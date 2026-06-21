# A9: 默认信号与互操作性 (Default Signaling and Interoperability)

## 9.1 默认值要求

所有 Manager 必须正确处理以下默认信号值：

| 信号 | 默认值 | 说明 |
|------|--------|------|
| AxBURST | INCR | 默认增量 burst |
| AxLOCK | Normal | 默认非独占访问 |
| AxCACHE | Non-cacheable | 默认不缓存 |
| AxPROT | Data/Secure/Unprivileged | 默认数据、安全、非特权访问 |
| AxQOS | 4'b0000 | 默认最低优先级 |
| AxREGION | 4'b0000 | 默认区域 0 |

## 9.2 AXI4 与 AXI3 互操作性

- AXI4 Manager 可以连接 AXI3 Subordinate
- AXI3 Manager 可以连接 AXI4 Subordinate
- 需注意的差异：
  - AXI4 移除了 WID 信号
  - AXI4 增加了 AxQOS, AxREGION 信号
  - AXI4 对 AxLEN 的限制有差异

## 9.3 AXI4 与 AXI4-Lite 互操作性

- AXI4-Lite Manager → AXI4 Subordinate: 直接兼容
- AXI4 Manager → AXI4-Lite Subordinate: 需要协议转换器（处理 burst）
- AXI4-Lite 是 AXI4 的严格子集

## 9.4 未连接信号处理

- Manager 未驱动的输入信号应按默认值处理
- Subordinate 未驱动的输入信号应按默认值处理
- 互连负责处理缺失信号的默认赋值

## 9.5 接口宽度匹配

- Manager 和 Subordinate 数据宽度不一致时的处理
- 窄 Manager → 宽 Subordinate: 互连上转换
- 宽 Manager → 窄 Subordinate: 需要串行化（如支持）

## 9.6 协议检查清单

- [ ] 所有通道的 VALID/READY 握手符合规范
- [ ] AxSIZE 与数据宽度匹配
- [ ] WRAP burst 的边界计算正确
- [ ] Exclusive 访问的 Monitor 逻辑正确
- [ ] 默认信号值处理正确
