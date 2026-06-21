# D7: Cache 维护操作 (Cache Maintenance Operations)

## 1. CMO 类型

| 操作 | 说明 |
|------|------|
| CleanInvalid | 清除脏数据并使无效 |
| MakeInvalid | 立即使无效 |
| CleanShared | 清除成共享状态 |
| CleanSharedPersist | 清除到持久化点 |

## 2. 传递和持久化

- 操作传播到 PoC (Point of Coherency)
- 持久化操作传播到 PoP (Point of Persistence)
- 跨一致性域的 CMO 传递

## 3. 虚拟化支持

- VMID 匹配的 CMO
- 虚拟机迁移场景的 CMO
