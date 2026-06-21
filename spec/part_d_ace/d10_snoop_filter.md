# D10: 外部 Snoop 过滤 (External Snoop Filtering)

## 1. Snoop Filter 的功能

- 跟踪哪些缓存持有哪些地址的副本
- 仅向持有副本的缓存发送 snoop
- 减少不必要的 snoop 广播

## 2. Snoop Filter 实现

- 基于 Tag RAM 的 snoop 过滤器
- 基于 Bloom Filter 的近似过滤器
- 溢出和回退机制

## 3. 可选的实现

- Snoop 过滤器是可选组件
- 无 filter 时使用广播 snoop
- filter 命中率对性能的影响
