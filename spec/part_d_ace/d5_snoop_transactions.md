# D5: Snoop 事务 (Snoop Transactions)

## 1. Snoop 事务类型

| ACSNOOP | 含义 | 说明 |
|---------|------|------|
| 4'b0000 | ReadOnce | 读取当前状态，不修改 |
| 4'b0001 | ReadClean | 读取并转干净 |
| 4'b0010 | ReadNotSharedDirty | 读取且不再共享 |
| 4'b0011 | ReadShared | 读取并保持共享 |
| 4'b0100 | ReadUnique | 转移独占副本给请求方 |
| 4'b0101 | CleanInvalid | 清除并使无效 |
| 4'b0110 | MakeInvalid | 立即使无效 |
| 4'b0111 | CleanShared | 清除并保持共享 |

## 2. Snoop 响应

| CRRESP | 含义 |
|--------|------|
| 3'b000 | PassDirty - 数据已转移 |
| 3'b001 | IsShared - 数据被共享 |
| 3'b010 | WasUnique - 之前是独占的 |
| 3'b011 | PassDirty + IsShared |
| 3'b100 | DataTransfer - 脏数据已传输 |
| 3'b101 | RetryAck - 重试确认 |

## 3. Snoop 与事务的关系

- 互连在收到一致性读请求时发放 snoop
- snoop 的目标是根据 snoop 过滤器选定的缓存
- snoop 完成后，原始请求可以继续
