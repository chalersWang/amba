# D9: ACE 独占访问 (ACE Exclusive Accesses)

## 1. 一致性域内的独占访问

- LDREX/STREX 在一致性域内的行为
- 全局独占监视器 (Global Exclusive Monitor)
- 与 AXI4 独占访问的差异

## 2. 独占状态跟踪

- 互连维护每个共享地址的独占状态
- 多个 Manager 同时独占同一地址的限制
- 独占写失败的条件

## 3. 多核同步

- 自旋锁
- 信号量
- 无锁数据结构的实现
