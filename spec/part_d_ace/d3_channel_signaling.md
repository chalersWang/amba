# D3: 通道信号 (Channel Signaling)

## 1. 读通道一致性扩展

- ARSNOOP: 在该读事务上请求的 snoop 动作
- ARDOMAIN: 事务所属的一致性域
- ARBAR: 屏障事务标识

## 2. 写通道一致性扩展

- AWSNOOP: 在该写事务上关联的 snoop 动作
- AWDOMAIN: 事务所属的一致性域
- AWBAR: 屏障事务标识

## 3. Snoop 通道握手

- AC 通道: 互连发放 snoop 请求
- CR 通道: Manager 响应 snoop 结果
- CD 通道: Manager 返回脏数据
- 三通道之间的依赖关系

## 4. 信号依赖

- AC 必须在 AR/AW 之前还是可以同时进行
- CR 与 R/B 的时序关系
- CD 与 CR 的关联
