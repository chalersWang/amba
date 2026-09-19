# AXI 死锁全场景分析

> 生成日期：2026-05-29
> 适用范围：AMBA AXI3/AXI4/AXI4-Lite 协议验证

---

## 🔴 一、协议定义级死锁（AXI Spec 明确禁止）

### 场景 1：写响应依赖死锁（B-Read 循环）

**触发条件**：Master 必须等待 B 响应才能发 Read，但 Slave 必须等 Read 完成才能给 B。

```
Master:  AW → W → 等待 B → 才发 AR
Slave:   收到 AW/W → 先处理读请求 → 才回 B
```

**根本原因**：Master 的读事务发射依赖于写事务完成，而 Slave 的写事务完成依赖于读事务完成。

**测试方法**：
- 构造 Master 行为模型：发完 `AW+W` 后，不收到 `B` 就不发 `AR`
- 构造 Slave 行为模型：必须处理完所有 `R` 才给 `B`
- 检查 `BVALID` 和 `ARVALID` 是否同时拉低且不恢复（超时检测）
- **Spec 规避**：AXI 协议规定 Slave 不能因为有未完成的事务而阻塞读写请求

---

### 场景 2：Outstanding 计数死锁

**触发条件**：Slave 内部 Outstanding 计数器满，拒绝新 AR/AW，但当前 R/B 通道又被下游反压堵死。

```
Master:  发满 16 个 AR（Slave 的 outstanding 上限）
Slave:   正在等下游接收 R，但下游 Ready=0
         此时 Slave 不能再收新的 AR/AW（计数器满）
         又因为 R 发不出去 → 计数器无法释放 → 死锁
```

**测试方法**：
- 给 Slave 配置 `MAX_OUTSTANDING=4`
- 连续发 5 个 AR，前 4 个打入 Slave，第 5 个被阻塞
- 同时将下游 RREADY 拉低
- 观察 Slave 是否永久卡死
- **Spec 规避**：Slave 不得通过限制 Outstanding 来流控 AW/AR，只能用 READY 反压

---

### 场景 3：多通道循环依赖（4 通道死锁）

**触发条件**：AXI 5 通道虽然独立定义，但实现中共用 buffer 导致隐式依赖。

```
Master:  ARVALID=1 → 等 RREADY
Slave:   RVALID=1 → 等 RREADY  
Interconnect: AR 通过了，但 R 通道被下游反压
同时:    AWVALID=1 → 正在写，等 WREADY
         Interconnect buffer 被 W 数据占满 → AR 无法前进
```

**本质**：AXI 的 5 个通道虽然独立定义，但实现中共用 buffer 导致隐式依赖。

**测试方法**：
- 构造 4 个 VIP 并发场景：Master0 做 Read burst、Master1 做 Write burst
- Interconnect buffer 容量设为极小（如每个通道 2 个 entry）
- 注入随机反压，监控各通道 Ready/Valid 是否出现全低
- 用 SVA 断言：`not (ARREADY==0 && AWREADY==0 && WREADY==0 && RREADY==0 && BREADY==0)`

---

## 🔴 二、拓扑级死锁（Interconnect/NOC 内部）

### 场景 4：环形拓扑路由死锁

**触发条件**：多个 Master-Slave 路径在 Interconnect 内形成环形依赖。

```
M0 → S1: Read, 路径经过 Router A → B
M1 → S0: Write, 路径经过 Router B → A
Router A 的读通道 buffer 满, 等 Router B 释放
Router B 的写通道 buffer 满, 等 Router A 释放
```

**测试方法**：
- Mesh/Torus NoC 拓扑
- 构造 butterfly 流量模式：`M[i] → S[(i+1) mod N]`
- 长时间运行，监控每个 Router 的 buffer 占用
- 检查是否出现 buffer 满且无事务推进

**规避方案**：虚拟通道(VC)、逃逸通道、dateline 路由

---

### 场景 5：Outbound vs Inbound 方向依赖

**触发条件**：同一条 AXI 链路，Master 侧的 AR 通路和 Slave 侧的 B 通路共享物理 credit。

```
Master 发 AR → 占用 outbound credit
Interconnect 需要给主端回 B → 占用 inbound credit
如果两种 credit 共享同一 pool → 用完 → 死锁
```

**测试方法**：
- 构造单 Master 交替读写场景
- 限制 Interconnect 内部总 buffer = 4
- 先打满 4 个 Write → 所有 buffer 被 AW/W/B 占用
- 再发 AR → 无可用 buffer → 死锁

---

## 🔴 三、接口级死锁（组件间握手）

### 场景 6：AXI Register Slice 死锁

**触发条件**：AXI4 Register Slice（常用于跨时钟域）的寄存器级反压设计不当。

```
前级 RegSlice: AW 打入寄存器 → 等后级 AWREADY
后级 RegSlice: 被下游反压 → AWREADY=0
同时后级的 B 通道: BVALID=1, 但前级 BREADY=0（因为前级还没处理）
如果两级的 AW 和 B 共用同一组寄存器 → 循环等待
```

**测试方法**：
- 串联 2 个 AXI Register Slice
- 下游 AWREADY 随机拉低
- 同时发连续的 Write burst（让 B 通道反向压力大）
- 检查 `AWVALID && AWREADY` 同时 0 且不恢复
- **验证点**：各通道独立 Skid Buffer，不能有跨通道依赖

---

### 场景 7：Clock Domain Crossing (CDC) FIFO 死锁

**触发条件**：异步 FIFO 的满信号错误传播。

```
Write domain: 写满 FIFO → 停止发数据
Read domain:  因为格雷码同步延迟，读侧还没看到"非空"
→ 写侧已停，读侧在等 → 死锁
```

**测试方法**：
- AXI 主端 800MHz，从端 400MHz
- 主端连续 burst 写，撑满异步 FIFO
- 注入时钟相位抖动/额外延迟
- 检查满/空信号的格雷码 synced 版本是否赶得上

---

## 🔴 四、事务级死锁（协议违规）

### 场景 8：4KB 边界跨域死锁

**触发条件**：Master 发的 Burst 跨 4KB 边界，但 Slave 按单笔事务处理。

```
Master 发 AR: 起始地址 0xFFC, Burst Length=4, Size=4B → 跨 4KB
Interconnect 拆成两笔：0xFFC-0xFFF + 0x1000-0x100F
如果 Slave 的 R 重组逻辑把两笔当成一笔 → RLAST 永远不发 → Master 死等
```

**测试方法**：
- 所有 burst 类型 (INCR/WRAP/FIXED) x 跨 4KB 边界
- 检查 `RLAST` 是否正确拉高
- **关键验证**：2^Address × 2^BurstLength × 2^Size 的交叉覆盖

---

### 场景 9：Exclusive Access 死锁

**触发条件**：`Exclusive Read → Exclusive Write` 序列中，中间被其他 Master 插队。

```
M0: ARLOCK=1, Exclusive Read(addr A) → 拿到 EXOKAY
M1: 正常 Write(addr A) → 写入数据     → 获得 OKAY
M0: AWLOCK=1, Exclusive Write(addr A) → Slave 返回 OKAY（不是 EXOKAY）
M0 期望 EXOKAY → 重试 → 无限循环
```

**测试方法**：
- 多 Master 并发，其中一个用 Locked/Exclusive
- 验证 Exclusive 监视器(Monitor)正确跟踪每个地址的 Exclusive 状态
- 检查 `RRESP[1:0]=EXOKAY` → `BRESP[1:0]=EXOKAY` 必须是成对的

---

### 场景 10：Ordering Model 违规死锁

**触发条件**：发出 `AW+W` 后不等 `B` 就发 `AR` 到同一地址，且读到的数据与预期不一致。

```
M0: Write(addr A, Data=0x5A5A)
M0: Read(addr A) — 不等 B 就发 AR
Slave: 先处理 Read，返回旧数据 0x0000
M0 期望 0x5A5A → 死等或死循环重试
```

**测试方法**：
- 对同一地址先后发 Write + Read（不带 barrier）
- 验证 AXI Ordering Model：同一 ID 的事务保序，不同 ID 的不保序
- 检查 Slave 是否正确实现：AW 和 AR 到同一地址时，写必须先于读完成

---

### 场景 11：窄带传输(Narrow Transfer)重组死锁

**触发条件**：窄带传输中，Master/Slave 对 stride 计算不一致导致字节对齐错误。

```
Master: AW, Size=4B(32bit), Burst Length=2, Data Bus=64bit → 期望 2 拍数据
Slave:  数据总线 64bit，但错误地只返回 1 拍数据
→ WLAST(已拉) vs WSTRB 不匹配 → Slave 死等下一拍
```

**测试方法**：
- Master DataWidth=64, Slave DataWidth=32 (窄带)
- 所有 Burst 类型 × 所有 Burst Length × 所有 Byte Strobe 组合
- 验证 WSTRB 与 WLAST 的对齐关系

---

## 🔴 五、系统级死锁

### 场景 12：DMA Descriptor 环死锁

**触发条件**：DMA 的 descriptor 放在 DDR，DMA 读 descriptor 发读请求，但读请求也去同一 DDR Controller。

```
DMA:        读 Descriptor(AR → DDR Ctrl)
DDR Ctrl:   正在被其他 Master 占满 → 反压
DMA:        没读到 descriptor → 无法完成当前传输 → 不能释放给其他 Master
其他 Master: 等 DMA 释放 DDR → 死锁
```

**测试方法**：
- 单端口 DDR Controller
- DMA 优先级最低
- 其他 Master 持续占用 DDR
- 检查 DMA descriptor 读取是否被饿死

---

### 场景 13：QoS / 优先级反转死锁

**触发条件**：高优先级事务占满所有 buffer，低优先级事务拥有的资源无法释放。

```
Master_H(pri=高): 发满 WR burst, 占满 Interconnect buffer
Master_L(pri=低): 持有一个锁(lock)，需要写回释放
Interconnect:      只给高优先级的 forward，低优先级的被卡死
→ Master_L 无法释放锁 → 系统死锁
```

**测试方法**：
- 设置 2 个 Master，M0 pri=high, M1 pri=low
- M1 持有 exclusive lock
- M0 疯狂发事务打满 buffer
- 检查 M1 能否最终完成 lock release

---

## 🧪 通用测试方法论

### 1. SystemVerilog Assertions (SVA)

```systemverilog
// 核心：各通道独立反压不变性
property aw_channel_starvation_free;
    @(posedge aclk) disable iff (!aresetn)
    AWVALID |-> s_eventually AWREADY;
endproperty

property r_channel_no_loop_dep;
    @(posedge aclk) disable iff (!aresetn)
    (ARVALID && !ARREADY) && RVALID |-> ##[1:$] RREADY;
endproperty

// 事务完整性
property write_transaction_complete;
    @(posedge aclk) disable iff (!aresetn)
    (AWVALID && AWREADY) |-> ##[0:$] (WVALID && WREADY && WLAST) ##[0:$] (BVALID && BREADY);
endproperty

property read_transaction_complete;
    @(posedge aclk) disable iff (!aresetn)
    (ARVALID && ARREADY) |-> ##[0:$] (RVALID && RREADY && RLAST);
endproperty

// 4KB 边界检查
property burst_within_4kb;
    @(posedge aclk) disable iff (!aresetn)
    (ARVALID && ARREADY, (ARADDR >> 12) == ((ARADDR + (1<<ARSIZE) * (ARLEN+1) - 1) >> 12))
    or
    // 跨 4KB 必须被拆分
    (ARVALID && ARREADY, (ARADDR >> 12) != ((ARADDR + (1<<ARSIZE) * (ARLEN+1) - 1) >> 12))
    |-> AWVALID==0;
endproperty
```

### 2. VIP 随机化测试矩阵

| 参数 | 取值 |
|------|------|
| 地址范围 | 同一 4KB 内 / 跨 4KB / 全空间随机 |
| Burst 类型 | FIXED / INCR / WRAP |
| Burst 长度 | 1 / 4 / 8 / 16 / 256 |
| 数据位宽 | 与 Size 的组合：对齐 / 非对齐 / 跨 stride |
| ARID/AWID 复用 | 同 ID 保序 / 不同 ID 乱序 |
| 反压模式 | 无反压 / 随机反压 / 周期性反压 / 极限反压(1拍1停) |
| Outstanding | 1 / 4 / 16 / 最大 |
| Master 数量 | 1 / 2 / 4 / 全并发 |
| 窄带传输 | Master Width > Slave Width |
| 非对齐传输 | 起始地址未对齐 |

### 3. 关键验证 Checkers

```
✓ 超时检测：任何 Valid=1 && Ready=0 超过 N 周期 → FATAL
✓ AW/W/B 通道事务配对计数：AW_count == W_count == B_count
✓ Outstanding counter ≤ MAX_OTSD
✓ 跨 4KB burst 被正确拆分
✓ Exclusive 成对出现（Read→Write 原子性）
✓ RLAST 在最后一个 data beat 正确拉高
✓ 各通道 FIFO 的 full/empty 不会同时稳定为 1
✓ WSTRB 在数据 phase 内的对齐一致性
```

### 4. 形式验证

对于小规模 Interconnect/NOC，用 JasperGold/VC Formal 做属性检查，可以穷举所有死锁路径，是传统随机仿真的有力补充。

建议属性：
- `f_starvation_free`：每个通道最终一定前进
- `f_no_dead_cycle`：不会出现所有 Valid/Ready 全低
- `f_transaction_completion`：每笔事务最终一定完成

---

## 📊 死锁场景速查表

| # | 场景 | 层级 | 典型原因 | 检测方法 |
|---|------|------|----------|----------|
| 1 | B-Read循环 | 协议 | Master不等B不发AR, Slave不等Read不给B | 超时检测 |
| 2 | Outstanding满 | 协议 | Slave用计数反压取代READY反压 | 极限Outstanding+下游反压 |
| 3 | 4通道循环 | 协议 | 5通道共用buffer | 小buffer+4VIP并发 |
| 4 | 环形路由 | 拓扑 | NoC多跳路由缓存依赖 | Butterfly流量 |
| 5 | 方向依赖 | 拓扑 | 进出方向共享credit | 读写交替+小buffer |
| 6 | Register Slice | 接口 | Skid buffer跨通道依赖 | 串联Slice+随机反压 |
| 7 | CDC FIFO | 接口 | 格雷码同步延迟 | 异频时钟+满信号测试 |
| 8 | 4KB边界 | 事务 | 跨边界burst拆分/重组错误 | 边界地址枚举 |
| 9 | Exclusive | 事务 | 中间被其他Master破坏原子性 | 多Master并发Exclusive |
| 10 | Ordering | 事务 | 对同地址写后读不保序 | 单Master同地址Write+Read |
| 11 | 窄带传输 | 事务 | stride/字节对齐错误 | DataWidth不匹配 |
| 12 | DMA环 | 系统 | Descriptor与数据共享DDR通道 | 单口DDR+优先反转 |
| 13 | QoS反转 | 系统 | 高优占满buffer, 低优锁无法释放 | 优先级+锁持有 |

---

## 🔗 参考

- AMBA AXI and ACE Protocol Specification (ARM IHI 0022)
- AMBA AXI Protocol Specification (ARM IHI 0051)
- AMBA CHI Specification (ARM IHI 0050)
- On-Chip Networks, Second Edition (Natalie Enright Jerger et al.)
