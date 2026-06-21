# Part C: AXI4-Lite 精简协议

AXI4-Lite 是 AXI4 的简化子集，适用于不需要完整 AXI4 功能的外设寄存器访问场景。

## 与 AXI4 Full 的关键差异

| 特性 | AXI4 Full | AXI4-Lite |
|------|-----------|-----------|
| Burst 长度 | 1-256 | 固定为 1 |
| 数据宽度 | 8-1024 bits | 32 或 64 bits |
| AxCACHE | 4 位 | 无 |
| AxLOCK | 有 | 无 |
| AxQOS | 4 位 | 无 |
| AxREGION | 4 位 | 无 |
| Exclusive Access | 支持 | 不支持 |
| WSTRB | 可选部分选通 | 通常全选通 |

## 章节列表

| 章节 | 标题 | 说明 |
|------|------|------|
| C1 | AXI4-Lite 规范 | 信号子集、时序要求、互操作性 |
