# A1: 介绍 (Introduction)

## 1.1 架构概述

- AMBA AXI 协议的历史和设计目标
- 高性能、高频率系统设计需求
- 内存映射（Memory-Mapped）接口概念

## 1.2 通道定义

AXI4 定义了 5 个独立通道：

| 通道 | 方向 | 用途 |
|------|------|------|
| Write Address (AW) | Manager → Subordinate | 写事务地址和控制信息 |
| Write Data (W) | Manager → Subordinate | 写数据 |
| Write Response (B) | Subordinate → Manager | 写完成应答 |
| Read Address (AR) | Manager → Subordinate | 读事务地址和控制信息 |
| Read Data (R) | Subordinate → Manager | 读数据和读完成应答 |

## 1.3 术语定义

| 术语 | AMBA 5 名称 | 说明 |
|------|-------------|------|
| Manager | (原 Master) | 发起读写事务的组件 |
| Subordinate | (原 Slave) | 响应读写事务的组件 |
| Interconnect | (原 Interconnect) | 连接多个 Manager 和 Subordinate 的互连结构 |
| Transaction | | 一次完整的读写操作（包含地址、数据、响应） |
| Transfer/Burst | | 一次 burst 中的单拍数据传输 |
| Register Slice | | 用于时序隔离的流水线寄存器 |

## 1.4 接口与互连

- 点对点 AXI 连接
- 多 Manager / 多 Subordinate 互连拓扑
- 互连功能：地址解码、路由、仲裁

## 1.5 协议层次

```
Transaction Layer    → 事务级操作（读写、burst）
Transfer Layer       → 单拍数据传输（VALID/READY 握手）
Link Layer           → 物理信号连接
```
