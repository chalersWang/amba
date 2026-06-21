# Part E: AMBA 5 协议特性

AMBA 5 在 AXI4 基础上引入的新特性（可选）。

## 章节列表

| 章节 | 标题 | 说明 |
|------|------|------|
| E1 | 原子事务 | AtomicCompare / AtomicSwap / AtomicLoad / AtomicStore |
| E2 | Cache Stashing | 将数据预取到指定缓存 |
| E3 | 数据保护 | 数据奇偶校验、数据中毒（Data Poison） |
| E4 | 内存标记 | Memory Tagging Extension (MTE) 支持 |

## AMBA 5 特性总览

| 特性 | 说明 |
|------|------|
| Atomic Transactions | 在内存控制器端完成的原子操作 |
| Cache Stashing | 提前将数据推送到 Manager 缓存 |
| Deallocating Transactions | 释放缓存行的写事务 |
| Trace Signals | 事务跟踪信号 |
| QoS Accept | 接受/拒绝 QoS 请求 |
| Wake-up Signals | 异步唤醒信号 |
| Untranslated Transactions | 非地址翻译事务（SMMU 阶段 2） |
| Memory Tagging | 内存标记扩展 |
| Prefetch | 预取指令 |
| Write Zero | 零值写入操作 |
| Data Protection | 数据中毒和奇偶校验 |
