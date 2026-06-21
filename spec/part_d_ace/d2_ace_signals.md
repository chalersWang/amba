# D2: ACE 信号描述 (ACE Signal Descriptions)

## 1. AXI4 基准信号变更

- AxDOMAIN: 一致性域选择
- AxSNOOP: Snoop 事务类型
- AxBAR: 屏障事务

## 2. Snoop Address Channel (AC)

| 信号 | 方向 | 说明 |
|------|------|------|
| ACADDR | IC → M | Snoop 地址 |
| ACSNOOP | IC → M | Snoop 类型 |
| ACPROT | IC → M | 保护属性 |
| ACVALID | IC → M | 地址有效 |
| ACREADY | M → IC | 地址就绪 |

## 3. Snoop Response Channel (CR)

| 信号 | 方向 | 说明 |
|------|------|------|
| CRRESP | M → IC | Snoop 响应状态 |
| CRVALID | M → IC | 响应有效 |
| CRREADY | IC → M | 响应就绪 |

## 4. Snoop Data Channel (CD)

| 信号 | 方向 | 说明 |
|------|------|------|
| CDDATA | M → IC | Snoop 数据 |
| CDLAST | M → IC | 最后一拍 |
| CDVALID | M → IC | 数据有效 |
| CDREADY | IC → M | 数据就绪 |
