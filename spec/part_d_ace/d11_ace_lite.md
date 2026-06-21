# D11: ACE-Lite

## 1. ACE-Lite 定位

- ACE 的简化子集
- 单向一致性：可被 snoop，但不能发起 snoop
- 适用于 I/O 设备和加速器

## 2. 信号简化

- 不包含 AC/CR/CD Master 接口
- 仅包含 Slave snoop 接口（接收 snoop）

## 3. 典型应用

- DMA 控制器
- GPU/显示控制器
- PCIe 根端口
- 网络加速器

## 4. ACE vs ACE-Lite

| 特性 | ACE | ACE-Lite |
|------|-----|----------|
| 发起一致性事务 | ✓ | ✗ |
| 响应 snoop | ✓ | ✓ |
| 持有缓存 | ✓ | ✓（可选） |
| Snoop Filter 跟踪 | ✓ | ✓ |
| 实现复杂度 | 高 | 中 |
