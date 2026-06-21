# AMBA (Advanced Microcontroller Bus Architecture) 文档与验证环境

本目录包含 AMBA 协议族的完整文档结构和 DV (Design Verification) 验证环境框架。

## 目录组织

```
amba/
├── spec/          # 协议规范文档（按 ARM IHI0022 章节组织）
├── axi4/          # AXI4 Full 协议 DV 验证环境
├── axi4lite/      # AXI4-Lite 精简协议 DV 验证环境
├── axi4stream/    # AXI4-Stream 流式协议 DV 验证环境
├── ace/           # ACE (AXI Coherency Extensions) DV 验证环境
└── common/        # 跨协议共享资源（文档、脚本、文件列表）
```

## 协议变体对比

| 特性 | AXI4 | AXI4-Lite | AXI4-Stream | ACE |
|------|------|-----------|-------------|-----|
| 通道数 | 5 (AR/R/AW/W/B) | 5 (简化信号) | 1 (单向) | 8 (5 AXI + AC/CR/CD) |
| Burst 支持 | 最多 256 beats | 无 (固定 1 beat) | 无 (TLAST 分界) | 同 AXI4 |
| 数据宽度 | 8-1024 bits | 32/64 bits | 任意 | 同 AXI4 |
| 缓存一致性 | 无 | 无 | 无 | 硬件缓存一致性 |
| 典型应用 | 高性能内存映射 | 控制寄存器 | 数据流 | 多核 SoC |

## 参考规范

- **ARM IHI 0022J** (2023-03): AMBA AXI and ACE Protocol Specification
- **ARM IHI 0051A**: AMBA AXI4-Stream Protocol Specification
- **ARM IHI 0061**: AMBA Low Power Interface Specification

## 相关资源

- 已有 AXI4-Lite UVC 参考: `canfd/canfd/uvc/axi4lite/`
- 已有 AXI4 VIP 参考: `agents/dv-flow/workspace/dv_env/uvc/axi/`
- AXI 死锁分析: `agents/claw-technology/workspace/axi_死锁场景与测试方法.md`
- AMBA 协议族知识库: `agents/claw-technology/workspace/memory.md`
