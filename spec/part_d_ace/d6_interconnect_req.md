# D6: 互连需求 (Interconnect Requirements)

## 1. Snoop 排序

- 互连必须保证 snoop 事务与普通事务之间的排序
- 同一地址的访问必须先 snoop 后响应
- 保证缓存一致性的正确性

## 2. Snoop 发放策略

- 广播 snoop: 发送给所有缓存
- 定向 snoop: 通过 snoop filter 发送给特定缓存
- 多层互连的 snoop 路由

## 3. 内存交互

- snoop hit: 缓存提供数据，不访问内存
- snoop miss: 需要从内存读取
- 部分缓存行命中的处理

## 4. 互操作性

- ACE Manager + ACE Subordinate
- ACE Manager + ACE-Lite Subordinate
- ACE-Lite Manager 的限制
