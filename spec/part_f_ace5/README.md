# Part F: ACE5 协议族

ACE5 是 AMBA 5 中的缓存一致性协议族，是 ACE 的演进版本。

## 章节列表

| 章节 | 标题 | 说明 |
|------|------|------|
| F1 | ACE5 | 完整 ACE5 协议 - 支持硬件缓存一致性 |
| F2 | ACE5-Lite | 简化版 - 仅支持被 snoop，不发起 snoop |
| F3 | ACE5-LiteDVM | Lite + 分布式虚拟内存支持 |
| F4 | ACE5-LiteACP | Lite + 加速器一致性端口 |
| F5 | ACE5 变更 | 从 ACE (AMBA 4) 到 ACE5 (AMBA 5) 的变更 |

## ACE5 关键增强

| 增强点 | 说明 |
|--------|------|
| Atomic Transactions | 原生原子操作支持 |
| Cache Stashing | 跨 cluster 的数据预推送 |
| Deallocating | 支持缓存行释放事务 |
| Improved DVM | 增强的分布式虚拟内存消息 |
| Protocol Simplification | 部分信号和状态机的简化 |
