# B1: 低功耗接口 (Low Power Interface)

## 1. CSYSREQ / CACTIVE 握手

### 信号定义
| 信号 | 方向 | 说明 |
|------|------|------|
| CSYSREQ | Subordinate → Manager | 系统请求进入低功耗 |
| CSYSACK | Manager → Subordinate | 系统确认进入低功耗 |
| CACTIVE | Subordinate → Manager | 时钟活跃指示 |

### 低功耗进入流程
```
Subordinate                    Manager
  │                              │
  │ CSYSREQ=1 (请求低功耗) ──────> │
  │                              │ (完成待处理事务)
  │ <────────── CSYSACK=1 ────── │ (确认进入低功耗)
  │                              │
  │ <==== 进入低功耗状态 ======> │
  │                              │
  │ CACTIVE=0 (时钟可停止) ─────> │
```

### 低功耗退出流程
```
Subordinate                    Manager
  │                              │
  │ CACTIVE=1 (请求时钟恢复) ───> │
  │                              │ (恢复时钟)
  │ CSYSREQ=0 (退出低功耗) ──────> │
  │ <────────── CSYSACK=0 ────── │ (确认正常模式)
```

## 2. 时钟门控

- 当 CACTIVE=0 时，ACLK 可以停止
- 时钟门控策略：
  - 粗粒度：停止整个接口时钟
  - 细粒度：仅停止特定通道逻辑

## 3. 电源域

- 独立电源域可以完全断电
- 恢复时需要重新初始化接口
- 可选的保留寄存器在断电期间保持状态

## 4. 设计建议

- Manager 应在 CSYSREQ 有效后尽快停止新事务
- Subordinate 应在所有待处理事务完成后才停止时钟
- 低功耗转换期间的握手机制保证了数据传输的完整性
