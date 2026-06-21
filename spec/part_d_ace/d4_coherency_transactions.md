# D4: 一致性事务 (Coherency Transactions)

## 1. 读事务的一致性属性

| ARSNOOP | 含义 |
|---------|------|
| 4'b0000 | 普通读（无一致性动作） |
| 4'b0001 | ReadOnce - 读取但不缓存 |
| 4'b0010 | ReadClean - 读取干净副本 |
| 4'b0011 | ReadNotSharedDirty - 读取且不共享脏数据 |
| 4'b0100 | ReadShared - 读取并共享 |
| 4'b0101 | ReadUnique - 将要写入，需要独占 |
| 4'b0110 | CleanUnique - 干净独占（用于存储操作） |
| 4'b0111 | MakeUnique - 获取独占所有权 |

## 2. 写事务的缓存策略

- WriteUnique: 写入独占副本
- WriteLineUnique: 写入完整缓存行
- WriteBack: 回写脏数据
- WriteClean: 写入干净数据
- WriteEvict: 逐出缓存行

## 3. 缓存行状态转换

```
  I ──(ReadShared)──→ S
  I ──(ReadUnique)──→ E
  S ──(MakeUnique)──→ M (通过 snoop 使其他副本失效)
  E ──(Write)───────→ M
  M ──(Snoop)───────→ O 或 S
  M ──(WriteBack)───→ I
```
