# A3: 单接口需求 (Single Interface Requirements)

## 3.1 时钟与复位

- ACLK: 全局时钟信号，所有通道信号在 ACLK 上升沿采样
- ARESETn: 低有效复位，复位期间所有 VALID 信号必须为低
- 复位退出序列和初始化要求

## 3.2 VALID/READY 握手

### 握手规则
- 发送方（Source）驱动 VALID 信号表示数据有效
- 接收方（Destination）驱动 READY 信号表示可以接收
- 传输发生在 ACLK 上升沿且 VALID && READY 同时为高时

### 握手时序
```
ACLK:  ─┐  ┌──┐  ┌──┐  ┌──┐  ┌──┐  ┌──
        └──┘  └──┘  └──┘  └──┘  └──┘
VALID:  ──────┐           ┌─────────────
              └───────────┘
READY:  ──────────┐     ┌─────────────
                  └─────┘
Xfer:                     ↑ (传输发生)
```

## 3.3 通道信号依赖关系

### 写事务通道依赖
1. Manager 不能等待 Subordinate 先给出 AWREADY 再拉高 AWVALID
2. Manager 可以在收到 AWREADY 之前就发送 WDATA
3. Subordinate 必须等待 WVALID && WREADY && WLAST 后才能给出 BVALID

### 读事务通道依赖
1. Manager 不能等待 Subordinate 先给出 ARREADY 再拉高 ARVALID
2. Subordinate 必须在收到 ARVALID && ARREADY 后才能给出 RVALID
3. Subordinate 可以在 AR 握手完成前就准备 R 数据

## 3.4 通道间依赖关系图

```
AW 通道 ──→ W 通道 ──→ B 通道
  │                      │
  │    (无依赖)          │   (可等待)
  │                      │
AR 通道 ──→ R 通道       │
  ↑         ↑            │
  └─────────┴────────────┘
  (在 Subordinate 端，R 可以与 B 任意排序)
```

## 3.5 死锁预防

- 单接口内无循环依赖
- Manager 必须能在不等待 Subordinate 的情况下发送 AW/AR
- Subordinate 必须能在不等待 Manager 的情况下接收 W 数据
