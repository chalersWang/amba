# D13: 分布式虚拟内存 (Distributed Virtual Memory)

## 1. DVM 概述

- DVM 消息用于维护虚拟内存转换的一致性
- 跨多个 SMMU/MMU 的 TLB 维护

## 2. DVM 消息类型

| 消息 | 说明 |
|------|------|
| TLBI | TLB Invalidate - 使 TLB 条目无效 |
| DSB | Data Synchronization Barrier |
| DMB | Data Memory Barrier |
| DVM Complete | DVM 操作完成确认 |

## 3. DVM 事务流

```
CPU ──(DVM msg)──> Interconnect ──(snoop)──> SMMU
                     │                        │
                     │<──(DVM Complete)───────┘
                     │
                所有目标完成
```

## 4. 虚拟化支持

- VMID 过滤
- 第二阶段地址翻译
- 虚拟化感知的 TLB 维护
