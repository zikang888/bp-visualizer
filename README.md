# Backpropagation Visualizer — Transformer 架构梯度传播演示

> 基于链式法则的 Transformer 反向传播算法交互式可视化教学工具

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Pure HTML/CSS/JS](https://img.shields.io/badge/stack-vanilla-ff69b4.svg)]()

![Screenshot](./screenshot.png)

## 概述

本项目是一个面向教学用途的**反向传播算法可视化演示网站**，聚焦于 Transformer 架构中**梯度逐层传播**的完整过程。支持**批梯度下降 (GD)** 和**随机梯度下降 (SGD)** 两种模式的对比演示。

### 核心理念

Transformer 的反向传播遵循经典的链式法则（Chain Rule），但因其多层堆叠的架构（Self-Attention → Add & LayerNorm → FFN → Add & LayerNorm）和残差连接的存在，梯度传播路径较为复杂。本工具通过**逐层动画**直观展示梯度从 Loss 层逆向传播至 Embedding 层的全过程。

## 功能特性

| 模块 | 说明 |
|---|---|
| **Transformer 架构图** | 四列式布局（输入层 → 注意力子层 → 前馈子层 → 输出层），包含残差连接的虚线绕行路径，反向传播时逐层高亮脉冲 |
| **损失景观 (Loss Landscape)** | 基于 Marching Squares 算法的 2D 等高线图，GD（蓝色）与 SGD（橙色）优化轨迹在曲面上同时绘制，全局最优点带发光标记 |
| **梯度范数柱状图** | 反向传播顺序排列的各层梯度范数 ‖∇‖，直观展示梯度从深层向浅层递减的衰减效应 |
| **公式推导面板** | 使用 MathJax 渲染的标准 LaTeX 数学公式，逐层展示链式法则的具体展开（偏导数、ReLU 稀疏性、Softmax 平滑性） |
| **GD vs SGD 对比** | 一键切换优化模式，SGD 路径带有随机噪声，体现"震荡但更快"的特性 |

## 交互操作

| 操作 | 说明 |
|---|---|
| **▶ 开始传播** | 自动连续运行动画（最多 40 轮 epoch），逐层显示反向传播 |
| **⏭ 单步** | 手动控制，每次前进一层，方便课堂讲解 |
| **↺ 重置** | 清空所有状态回到初始画面 |
| **GD / SGD 切换** | 切换优化模式 |
| **学习率 η** | 滑块调节 0.01 ~ 0.50 |
| **速度调节** | 1 ~ 5 档动画速度 |

### 快捷键

| 按键 | 功能 |
|---|---|
| `Space` | 开始 / 停止传播 |
| `→` | 单步前进 |
| `R` | 重置 |

## 技术架构

```
backprop-visualizer/
├── index.html          # 单文件完整应用（HTML + CSS + JS）
├── README.md           # 项目文档
└── .gitignore
```

### 技术栈

- **D3.js v7** — Transformer 架构图的 SVG 渲染、梯度范数柱状图
- **MathJax 3** — LaTeX 数学公式渲染（`tex-mml-chtml`）
- **Canvas API** — 损失景观等高线图（Marching Squares 算法）
- **原生 CSS** — 暗色科研风格主题，CSS Grid 响应式布局
- **纯前端，零依赖构建工具** — 浏览器直接打开即可运行

### 关键算法

1. **Marching Squares** — 在 `drawLossLandscape()` 中实现，3px 步长网格扫描，线性插值计算等高线穿越点，生成平滑连续的等高线
2. **分段颜色映射** — 四段 colormap 函数（深蓝 → 青绿 → 翠绿 → 金 → 暗红），按损失值归一化映射
3. **链式法则逐层展开** — 9 层节点的梯度计算公式覆盖了 Transformer 的核心算子：Softmax、LayerNorm、ReLU、残差连接

## 快速开始

### 方式一：浏览器直接打开

```bash
# 直接双击 index.html 即可运行
open index.html
```

### 方式二：本地服务器

```bash
# Python 3
python3 -m http.server 8080

# 然后访问 http://localhost:8080
```

### 方式三：在线预览

通过 GitHub Pages 部署后直接访问。

## 可视化架构图

```
  输入层            注意力子层                      前馈子层              输出层
┌─────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────┐
│Embedding│────▶│  Q/K/V 投影   │     │FFN(Linear    │     │  Output  │
│         │     │     │        │     │  +ReLU)     │────▶│   Proj   │
│         │     │  ┌──▼──────┐ │     │     │        │     │     │    │
│         │     │  │  Self-   │ │     │  ┌──▼──────┐│     │  ┌──▼──┐ │
│         │     │  │Attention │─┼────▶│  │  FFN    ││     │  │Loss │ │
│         │─────│─▶│          │ │     │  │(Linear) ││     │  │     │ │
│         │ 残差 │  └────┬─────┘ │     │  └────┬────┘│     │  └─────┘ │
│         │     │  ┌────▼─────┐ │     │  ┌────▼────┐│     └──────────┘
│         │     │  │Add &     │─┼────▶│  │Add &    ││
│         │     │  │LayerNorm │ │ 残差 │  │LayerNorm││
│         │     └──│          │─┼────▶│  │         ││
└─────────┘        └──────────┘ │     └──────────┘│
                                └──────────────┘
```

## 反向传播顺序

```
Loss → Output Proj → Add & LayerNorm₂ → FFN (Linear) → FFN (Linear+ReLU)
  → Add & LayerNorm₁ → Self-Attention → Q/K/V 投影 → Embedding
```

## License

MIT

---

*本项目为教学演示用途，模拟的梯度数值基于启发式函数，不连接真实模型训练。*