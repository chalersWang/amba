# A4: 事务属性 (Transaction Attributes)

## 4.1 Burst 类型 (AWBURST / ARBURST)

| 类型 | 编码 | 说明 |
|------|------|------|
| FIXED | 2'b00 | 固定地址，每拍使用同一地址（FIFO 模式） |
| INCR | 2'b01 | 增量地址，地址按传输大小递增 |
| WRAP | 2'b10 | 回环地址，在边界内循环 |

## 4.2 Burst 长度 (AWLEN / ARLEN)

- 编码: AxLEN[7:0]，实际长度 = AxLEN + 1
- 范围: 1 ~ 256 拍
- AXI4-Lite 固定为 1 拍
- WRAP 类型仅支持 2, 4, 8, 16 拍

## 4.3 Burst 大小 (AWSIZE / ARSIZE)

| AxSIZE | 字节数 | 说明 |
|--------|--------|------|
| 3'b000 | 1 | 8-bit |
| 3'b001 | 2 | 16-bit |
| 3'b010 | 4 | 32-bit |
| 3'b011 | 8 | 64-bit |
| 3'b100 | 16 | 128-bit |
| 3'b101 | 32 | 256-bit |
| 3'b110 | 64 | 512-bit |
| 3'b111 | 128 | 1024-bit |

## 4.4 传输地址

- 起始地址: AxADDR
- 地址对齐要求：burst 起始地址应对齐到传输大小
- WRAP 边界计算：Wrap_Boundary = INT(Start_Address / (Number_Bytes × Burst_Length))

## 4.5 写选通 (WSTRB)

- 每 bit 对应一个字节通道
- WSTRB[n] = 1 表示 WDATA[8n+7:8n] 有效
- 支持部分写入（窄传输）
- AXI4-Lite 通常使用全选通

## 4.6 窄传输 (Narrow Transfer)

- 当数据宽度小于总线宽度时的传输方式
- 字节不变性（Byte Invariance）：多字节传输中，每个字节保持其位置
- Write strobe 控制实际写入的字节

## 4.7 非对齐传输 (Unaligned Transfer)

- AXI4 支持非对齐 burst 起始地址
- 使用 WSTRB 实现首拍和尾拍的部分字节写入
- 中间拍为对齐传输

## 4.8 事务响应 (BRESP / RRESP)

| 响应 | 说明 |
|------|------|
| OKAY (2'b00) | 正常访问成功 |
| EXOKAY (2'b01) | 独占访问成功（仅 RRESP） |
| SLVERR (2'b10) | Subordinate 检测到错误条件 |
| DECERR (2'b11) | 互连或默认 Subordinate 报告地址未映射 |
