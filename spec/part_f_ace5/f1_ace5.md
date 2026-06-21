# F1: ACE5

## 1. ACE5 概述

- ACE5 是 AMBA 5 中的完整缓存一致性协议
- 是 ACE (AMBA 4) 的演进版本
- 集成 AMBA 5 新特性（原子事务、Cache Stashing、Deallocating）

## 2. 与 ACE (AMBA 4) 的关系

- 向后兼容 ACE 大部分特性
- 新增信号支持 AMBA 5 特性
- 部分状态机优化和简化

## 3. 关键特性

- 完整的 MOESI 缓存状态模型
- 双向 snoop 能力
- 原子事务支持
- Cache Stashing 支持
- 数据保护（Parity / Poison）
