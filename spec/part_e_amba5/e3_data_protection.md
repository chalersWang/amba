# E3: 数据保护 (Data Protection)

## 1. 数据奇偶校验

- AxDATA_PARITY: 数据通道的奇偶校验信号
- 读和写通道均可选支持

## 2. 数据中毒 (Data Poison)

- AxDATA_POISON: 标记数据为"中毒"状态
- 当数据在传输过程中被检测到不可纠正的错误时使用
- 传播中毒标记到消费者

## 3. 错误检测与报告

- 可纠正错误 (CE): 数据可用但记录错误
- 不可纠正错误 (UE): 数据不可用，触发异常
- 错误记录和报告机制

## 4. RAS (Reliability, Availability, Serviceability)

- 在线错误检测
- 错误隔离
- 错误恢复策略
