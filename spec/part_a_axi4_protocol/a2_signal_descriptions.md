# A2: 信号描述 (Signal Descriptions)

## 2.1 全局信号

| 信号 | 方向 | 位宽 | 说明 |
|------|------|------|------|
| ACLK | - | 1 | 全局时钟 |
| ARESETn | - | 1 | 全局复位（低有效） |
| CSYSREQ | Subordinate → Manager | 1 | 低功耗系统请求 |
| CSYSACK | Manager → Subordinate | 1 | 低功耗系统应答 |
| CACTIVE | Subordinate → Manager | 1 | 时钟活跃指示 |

## 2.2 Write Address Channel (AW)

| 信号 | 方向 | 说明 |
|------|------|------|
| AWID | M → S | 写事务 ID 标签 |
| AWADDR | M → S | 写地址 |
| AWLEN | M → S | Burst 长度 (1-256) |
| AWSIZE | M → S | 每拍数据字节数 |
| AWBURST | M → S | Burst 类型 (FIXED/INCR/WRAP) |
| AWLOCK | M → S | 锁类型 |
| AWCACHE | M → S | 缓存属性 |
| AWPROT | M → S | 保护类型 |
| AWQOS | M → S | QoS 标识 |
| AWREGION | M → S | 区域标识 |
| AWVALID | M → S | 地址有效 |
| AWREADY | S → M | 地址就绪 |

## 2.3 Write Data Channel (W)

| 信号 | 方向 | 说明 |
|------|------|------|
| WDATA | M → S | 写数据 |
| WSTRB | M → S | 写字节选通 |
| WLAST | M → S | 最后一拍指示 |
| WVALID | M → S | 数据有效 |
| WREADY | S → M | 数据就绪 |

## 2.4 Write Response Channel (B)

| 信号 | 方向 | 说明 |
|------|------|------|
| BID | S → M | 响应 ID |
| BRESP | S → M | 写响应状态 |
| BVALID | S → M | 响应有效 |
| BREADY | M → S | 响应就绪 |

## 2.5 Read Address Channel (AR)

| 信号 | 方向 | 说明 |
|------|------|------|
| ARID | M → S | 读事务 ID 标签 |
| ARADDR | M → S | 读地址 |
| ARLEN | M → S | Burst 长度 |
| ARSIZE | M → S | 每拍数据字节数 |
| ARBURST | M → S | Burst 类型 |
| ARLOCK | M → S | 锁类型 |
| ARCACHE | M → S | 缓存属性 |
| ARPROT | M → S | 保护类型 |
| ARQOS | M → S | QoS 标识 |
| ARREGION | M → S | 区域标识 |
| ARVALID | M → S | 地址有效 |
| ARREADY | S → M | 地址就绪 |

## 2.6 Read Data Channel (R)

| 信号 | 方向 | 说明 |
|------|------|------|
| RID | S → M | 读数据 ID |
| RDATA | S → M | 读数据 |
| RRESP | S → M | 读响应状态 |
| RLAST | S → M | 最后一拍指示 |
| RVALID | S → M | 数据有效 |
| RREADY | M → S | 数据就绪 |

## 2.7 响应信号编码

| BRESP/RRESP | 值 | 说明 |
|-------------|-----|------|
| OKAY | 2'b00 | 正常访问成功 |
| EXOKAY | 2'b01 | 独占访问成功 |
| SLVERR | 2'b10 | Subordinate 错误 |
| DECERR | 2'b11 | 地址解码错误 |
