# D14: Master 设计建议 (Master Design Recommendations)

## 1. 缓存控制器设计

- Tag RAM 组织
- 状态机设计（MOESI 状态转换）
- 缓存行分配/替换策略

## 2. Snoop 响应延迟

- snoop lookup 与正常访问的仲裁
- 避免 snoop 阻塞正常事务
- 使用 snoop buffer 或队列吸收瞬时 snoop

## 3. 一致性协议合规

- 正确处理所有 snoop 类型
- 正确处理屏障事务
- 正确处理独占访问

## 4. 性能优化建议

- 预取策略
- 写合并 (Write Combining)
- 临界区优化
- 缓存行填充和溢出处理
