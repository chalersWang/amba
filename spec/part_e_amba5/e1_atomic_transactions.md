# E1: 原子事务 (Atomic Transactions)

## 1. 原子操作类型

| 操作 | 说明 |
|------|------|
| AtomicLoad | 原子加载 |
| AtomicStore | 原子存储 |
| AtomicSwap | 原子交换 |
| AtomicCompare | 原子比较并交换 |

## 2. 地址通道信号

- AxATOP: 原子操作类型编码
- 在内存控制器/Subordinate 端执行
- 不需要独占访问协议

## 3. 数据通道

- 读返回旧值（如有）
- 写发送新值
- 操作在 Subordinate 端完成

## 4. 与 Exclusive Access 的对比

| 特性 | Exclusive | Atomic |
|------|-----------|--------|
| 执行位置 | Manager 端 | Subordinate 端 |
| 是否需要重试 | 是（可能失败） | 否（保证完成） |
| 适用场景 | 复杂同步 | 简单计数器/标志 |
