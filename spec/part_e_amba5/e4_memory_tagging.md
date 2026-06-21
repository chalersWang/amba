# E4: 内存标记 (Memory Tagging)

## 1. MTE (Memory Tagging Extension) 概述

- 每个 16 字节内存块关联一个 4 位 Tag
- 用于检测内存安全违规（use-after-free, buffer overflow）

## 2. AXI 信号扩展

- AxTAG: 地址通道的 Tag 信号
- 读数据返回时检查 Tag 匹配
- 写数据时携带 Tag 写入内存

## 3. Tag 检查流程

```
读事务:
  1. Manager 发送 ARADDR + ARTAG
  2. Subordinate 读取数据 + 存储的 Tag
  3. 比较 ARTAG 与存储 Tag，不匹配则标记 Data Poison
```

## 4. 使用场景

- 内存安全检测
- 动态内存分配跟踪
- 安全加固（防止内存攻击）
