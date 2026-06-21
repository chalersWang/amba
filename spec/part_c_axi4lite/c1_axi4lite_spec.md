# C1: AXI4-Lite 规范 (AXI4-Lite Specification)

## 1. 协议定位

AXI4-Lite 是 AXI4 的严格子集，设计目标是：
- 简单的控制寄存器访问接口
- 低门数的 Subordinate 实现
- 与 AXI4 的即插即用兼容

## 2. 信号子集

### Write Address Channel
- 保留: AWADDR, AWVALID, AWREADY, AWPROT
- 移除: AWID, AWLEN, AWSIZE, AWBURST, AWLOCK, AWCACHE, AWQOS, AWREGION

### Write Data Channel
- 保留: WDATA, WSTRB, WVALID, WREADY
- 移除: WLAST（隐含为 1）

### Write Response Channel
- 保留: BRESP, BVALID, BREADY
- 移除: BID

### Read Address Channel
- 保留: ARADDR, ARVALID, ARREADY, ARPROT (可选)
- 移除: ARID, ARLEN, ARSIZE, ARBURST, ARLOCK, ARCACHE, ARQOS, ARREGION

### Read Data Channel
- 保留: RDATA, RRESP, RVALID, RREADY
- 移除: RID, RLAST

## 3. 隐含默认值

| 移除信号 | 隐含值 |
|----------|--------|
| AxLEN | 0 (1 个 burst) |
| AxSIZE | 数据宽度/8 |
| AxBURST | INCR |
| AxLOCK | Normal |
| AxCACHE | Non-modifiable, Non-bufferable |
| AxPROT | Data, Secure, Unprivileged |
| AxQOS | 0 |
| AxREGION | 0 |

## 4. 互操作性

- AXI4-Lite Manager → AXI4 Subordinate: 直接兼容
- AXI4 Manager → AXI4-Lite Subordinate: 需要处理 burst 转换
- 互连负责桥接两端的协议差异

## 5. 典型应用

- GPIO、UART、I2C、SPI 等低速外设控制
- 配置和状态寄存器 (CSR) 访问
- 电源管理控制器 (PMC)
- 中断控制器 (GIC)
