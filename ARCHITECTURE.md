# AMBA 总线架构总览

## 1. AMBA 协议族演进

```
AMBA 2 (1999)          AMBA 3 (2003)          AMBA 4 (2010)          AMBA 5 (2013)
    │                      │                      │                      │
    ├── AHB              ├── AHB-Lite           ├── AXI4              ├── AXI5
    ├── ASB              ├── APB3               ├── AXI4-Lite         ├── AXI5-Lite
    └── APB              ├── AXI3               ├── AXI4-Stream       ├── ACE5
                         └── ATB                ├── ACE               ├── ACE5-Lite
                                                ├── ACE-Lite          ├── ACE5-LiteDVM
                                                └── APB4              ├── ACE5-LiteACP
                                                                      └── CHI (分离规范)
```

## 2. AXI4 五通道架构

```
                    Write Address Channel (AW)
   Manager   ──────────────────────────────>  Subordinate
             AWID, AWADDR, AWLEN, AWSIZE,
             AWBURST, AWLOCK, AWCACHE,
             AWPROT, AWQOS, AWREGION,
             AWVALID ────────────────> AWREADY

                    Write Data Channel (W)
   Manager   ──────────────────────────────>  Subordinate
             WDATA, WSTRB, WLAST,
             WVALID ──────────────────> WREADY

                    Write Response Channel (B)
   Manager   <──────────────────────────────  Subordinate
             BID, BRESP,
             <────────────────── BVALID  BREADY

                    Read Address Channel (AR)
   Manager   ──────────────────────────────>  Subordinate
             ARID, ARADDR, ARLEN, ARSIZE,
             ARBURST, ARLOCK, ARCACHE,
             ARPROT, ARQOS, ARREGION,
             ARVALID ────────────────> ARREADY

                    Read Data Channel (R)
   Manager   <──────────────────────────────  Subordinate
             RID, RDATA, RRESP, RLAST,
             <────────────────── RVALID  RREADY
```

## 3. AXI 握手协议

所有通道使用相同的 VALID/READY 握手：

- **VALID**：发送方数据有效
- **READY**：接收方准备就绪
- 传输发生在 VALID && READY 的时钟沿
- 三阶段依赖规则确保无死锁

## 4. 关键特性

### AXI4 Full
- 5 个独立通道，支持全双工操作
- Burst 传输：FIXED / INCR / WRAP 三种类型
- 最多 256 beats 的 burst 长度
- 乱序事务完成（通过 ID 标签）
- QoS 信号（AxQOS）
- 区域标识（AxREGION）
- 保护单元支持（AxPROT）
- 原子操作：Exclusive Access / Locked Access
- 低功耗接口：CSYSREQ / CACTIVE

### AXI4-Lite
- AXI4 的严格子集
- 无 burst（固定 1 beat）
- 数据宽度限制为 32/64 bit
- 无 Cache / Exclusive / QoS 信号
- 与 AXI4 互操作无需协议转换器

### AXI4-Stream
- 独立协议，仅共享 VALID/READY 握手
- 单方向通道，无地址
- TLAST 信号标记包边界
- TKEEP / TSTRB 字节粒度的数据控制
- TID / TDEST 路由信息
- 适用于 FIFO、DMA、DSP 等数据流场景

### ACE (AXI Coherency Extensions)
- 在 AXI4 基础上增加 3 个 snoop 通道：
  - **AC** (Snoop Address Channel): 发送 snoop 请求
  - **CR** (Snoop Response Channel): 返回 snoop 结果
  - **CD** (Snoop Data Channel): 传输脏数据
- MOESI 缓存状态模型
- 支持 Barrier 事务和 DVM (Distributed Virtual Memory)
- ACE-Lite: 单向一致性（可被 snoop，不能发起 snoop）

## 5. UVM 验证架构

本目录下的 DV 环境遵循标准 UVM 验证架构：

```
Test Layer (testcase/)
    │
    ▼
Environment Layer (env/)
    │
    ├── UVC Agent (uvc/)
    │   ├── Driver  ─── 驱动协议信号
    │   ├── Monitor ─── 监控协议信号
    │   └── Sequencer ─ 管理事务序列
    │
    ├── Scoreboard (env/)
    │   └── Reference Model ─ 行为预测
    │
    ├── Coverage (coverage/)
    │   └── Functional Coverage ─ 功能覆盖率
    │
    └── Assertions (sva/)
        └── Protocol Checkers ─ 协议合规检查
```

## 6. 参考

- [ARM AMBA Specifications](https://developer.arm.com/architectures/system-architectures/amba)
- [AMBA AXI and ACE Protocol Specification (IHI0022J)](https://developer.arm.com/documentation/ihi0022/j/)
- [AMBA AXI4-Stream Protocol Specification (IHI0051A)](https://developer.arm.com/documentation/ihi0051/a/)
