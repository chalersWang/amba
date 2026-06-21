# A8: AMBA 4 附加信号 (Additional Signaling)

## 8.1 QoS 信号 (AxQOS)

| AxQOS | 优先级 |
|-------|--------|
| 4'b0000 | 最低优先级（默认） |
| 4'b0001 ~ 4'b1110 | 递增优先级 |
| 4'b1111 | 最高优先级 |

- 用于互连中的仲裁优先级控制
- 允许低延迟流量（如实时视频）获得更高的互连优先级

## 8.2 区域标识 (AxREGION)

- 4 位区域标识符
- 允许 Subordinate 将物理地址空间划分为多个区域
- 典型用途：
  - 普通内存 vs 外设空间
  - 多物理内存区域
  - 安全/非安全区域

## 8.3 保护信号 (AxPROT)

| AxPROT[2:0] | 含义 |
|-------------|------|
| [0] | 0: 非特权访问; 1: 特权访问 |
| [1] | 0: 安全访问; 1: 非安全访问 |
| [2] | 0: 数据访问; 1: 指令访问 |

组合示例：
- 3'b001: 特权 + 安全 + 数据
- 3'b010: 非特权 + 非安全 + 数据
- 3'b110: 用户模式指令访问

## 8.4 缓存信号 (AxCACHE)

| AxCACHE[3:0] | 含义 |
|-------------|------|
| [0] | Bufferable (B) |
| [1] | Modifiable (M) |
| [2] | Read-Allocate (RA) |
| [3] | Write-Allocate (WA) |

常见编码：
- 4'b0000: Non-cacheable, non-bufferable
- 4'b0011: Normal memory, non-cacheable, bufferable
- 4'b1111: Write-back, Read-allocate, Write-allocate

## 8.5 用户侧信号

- AxUSER: 用户自定义信号，可扩展为特定应用场景传递元数据
- 典型用途：安全标签、数据着色、事务跟踪
