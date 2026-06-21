# B2: 时序参数 (Timing Parameters)

## 1. 建立/保持时间

- 所有 AXI 信号相对于 ACLK 上升沿的建立/保持时间要求
- 典型值（取决于工艺节点和目标频率）

## 2. 组合逻辑延迟

- Manager 输出延迟：Tov_max（最大输出有效时间）
- Subordinate 输出延迟：Tov_max（最大输出有效时间）
- 互连组合延迟：路径相关的传播延迟

## 3. 流水线寄存器 (Register Slice)

### 用途
- 隔离长组合逻辑路径，提高时序收敛能力
- 位置选择：
  - Manager 端：缓解 Manager 输出压力
  - Subordinate 端：缓解 Subordinate 输出压力
  - 互连中间：均衡路径延迟

### VALID/READY 流水线
```
Source ──VALID──> [Reg Slice] ──VALID──> Dest
Dest   ──READY──>              ──READY──> Source

- Reg Slice 完整流水线化 VALID/READY/PAYLOAD
- 不改变协议语义，仅增加一拍延迟
```

## 4. 频率目标

| 协议 | 典型频率 | 常见工艺 |
|------|----------|----------|
| AXI4 | 200-800 MHz | 28nm / 16nm |
| AXI4-Lite | 100-400 MHz | 通用 |
| AXI4-Stream | 400 MHz - 1 GHz+ | 高性能数据路径 |

## 5. 时序收敛建议

- 使用寄存器片（Register Slice）打破长路径
- 限制 Manager-Subordinate 之间的逻辑级数
- 考虑使用浅的 FIFO 缓冲来平衡吞吐量和延迟
