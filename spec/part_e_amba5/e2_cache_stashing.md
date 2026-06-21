# E2: Cache Stashing

## 1. 概念

- 允许 Subordinate 主动将数据推送到 Manager 的缓存中
- 减少 Manager 首次访问的延迟（缓存预热）

## 2. Stash 事务类型

- StashOnce: 推送数据到目标缓存一次
- StashOnceShared: 推送共享状态数据
- StashOnceUnique: 推送独占状态数据

## 3. 使用场景

- 网络数据包预取到处理器缓存
- DMA 完成后通知 CPU 并推送数据
- 异构计算的缓存预热

## 4. 设计考虑

- Stash 目标地址由互连决定
- 接收方可以接受或拒绝 stash
- 不影响功能正确性，仅优化性能
