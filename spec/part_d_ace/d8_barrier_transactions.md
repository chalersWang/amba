# D8: 屏障事务 (Barrier Transactions)

## 1. Barrier 类型

| AxBAR | 含义 |
|-------|------|
| 3'b000 | 非屏障事务 |
| 3'b001 | Memory Barrier (DMB) |
| 3'b010 | Synchronization Barrier (DSB) |
| 3'b011 | 保留 |
| 3'b100 | 保留 |

## 2. 屏障语义

- DMB: 确保屏障前的内存访问在屏障后之前完成（观测顺序）
- DSB: 确保屏障前的所有访问完成，然后才继续（同步点）

## 3. 域边界

- 内部域 (Inner Domain)
- 外部域 (Outer Domain)
- 系统域 (System Domain)
- Barrier 响应的传播和完成
