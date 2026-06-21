# Part D: ACE 缓存一致性协议 (AXI Coherency Extensions)

ACE 在 AXI4 基础上增加了硬件缓存一致性支持，通过 3 个额外的 snoop 通道实现 MOESI 协议。

## 章节列表 (D1-D14)

| 章节 | 标题 | 关键内容 |
|------|------|----------|
| D1 | ACE 概述 | 系统级一致性、缓存模型、协议错误 |
| D2 | 信号描述 | AC/CR/CD 通道信号、AxDOMAIN/AxSNOOP |
| D3 | 通道信号 | 一致性通道握手和依赖关系 |
| D4 | 一致性事务 | 读写通道上的一致性事务类型 |
| D5 | Snoop 事务 | ReadOnce/ReadClean/ReadUnique/MakeInvalid 等 |
| D6 | 互连需求 | 排序要求、snoop 发放、内存交互 |
| D7 | Cache 维护操作 | CMO 类型、传播和持久化 |
| D8 | 屏障事务 | Barrier 信号、域边界 |
| D9 | ACE 独占访问 | 一致性域内的 LDREX/STREX |
| D10 | 外部 Snoop 过滤 | 可选的 snoop 过滤机制 |
| D11 | ACE-Lite | 单向一致性（仅可被 snoop） |
| D12 | 接口控制 | 接口状态机和配置 |
| D13 | 分布式虚拟内存 | TLBI/DSB/DMB 维护操作 |
| D14 | Master 设计建议 | 缓存控制器的实现指南 |

## 三通道扩展

```
AXI4 5 通道          ACE 扩展 3 通道
─────────────        ───────────────
AW ─────────────────>
W  ─────────────────>
B  <─────────────────
AR ─────────────────>
R  <─────────────────
                     AC (Snoop Address) ──>
                     CR (Snoop Response) <──
                     CD (Snoop Data)     <──
```
