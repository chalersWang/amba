# A7: 原子访问 (Atomic Accesses)

## 7.1 独占访问 (Exclusive Access)

### 原理
- 用于实现信号量（Semaphore）和自旋锁（Spinlock）
- 使用 AxLOCK 信号指示独占序列
- Manager 先读后写，硬件保证其间数据未被修改

### 独占读 (Exclusive Read)
- AxLOCK = 2'b01（独占访问）
- Subordinate 记录 Monitor 地址（如支持）
- 返回 EXOKAY 表示独占访问被接受

### 独占写 (Exclusive Write)
- AxLOCK = 2'b01
- 如果 Monitor 地址上的数据自上次独占读以来未被修改，写入成功
- 如果数据已被修改（另一个 Manager 写入），写入失败，返回 OKAY
- 成功时返回 EXOKAY

### 独占访问流程
```
Manager                    Subordinate
  │                            │
  │ ARLOCK=1 (独占读) ─────────> 记录监控地址
  │ <───────── RRESP=EXOKAY ─── │
  │                            │
  │ AWLOCK=1 (独占写) ─────────> 检查地址是否仍被独占
  │ <───────── BRESP ────────── │
  │  EXOKAY = 成功              │
  │  OKAY   = 失败（需重试）     │
```

## 7.2 锁定访问 (Locked Access)

- AxLOCK = 2'b10（锁定访问）
- AXI3 支持，AXI4 已移除
- 锁定整个互连，阻止其他事务
- AXI4 仅保留用于兼容性，新设计应使用独占访问

## 7.3 独占访问限制

- Burst 长度限制：Exclusive burst 最大 16 拍
- 地址对齐：必须与事务总字节数对齐
- 写数据量必须等于读数据量

## 7.4 多核场景

- 独占访问是 SoC 中实现多核同步的基础
- 配合原子操作指令（LDREX/STREX 等）
- ACE 扩展了独占访问以支持缓存一致性域内的原子操作
