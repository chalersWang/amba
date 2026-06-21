# F5: ACE5 变更 (Changes in ACE5 and ACE5-Lite)

## 1. 从 ACE (AMBA 4) 到 ACE5 的变更

| 变更 | 说明 |
|------|------|
| 原子事务 | 新增强大的原子操作 |
| Cache Stashing | 新增缓存预热功能 |
| Deallocating | 新增缓存行释放事务 |
| 数据保护 | 增强的 Parity/Poison 支持 |
| 内存标记 | MTE 信号扩展 |
| 写零操作 | 优化的零数据写入 |

## 2. 信号变更

- 新增 AxATOP（原子操作类型）
- 新增 AxTAG（内存标记）
- 新增 Deallocating 事务信号
- 数据保护信号的标准化

## 3. 向后兼容性

- ACE5 Subordinate 可以与 ACE Manager 互操作
- ACE5 Manager 的向后兼容策略
- 兼容模式下的信号默认值
