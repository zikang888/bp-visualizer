# Backpropagation Visualizer — Transformer 架构梯度传播 & 优化器对比演示

> 基于链式法则的反向传播算法交互式可视化教学工具，包含 Transformer 架构梯度传播和 MLP 优化器对比两大模块

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](#license)
[![Pure HTML/CSS/JS](https://img.shields.io/badge/stack-vanilla-ff69b4.svg)](#技术栈)

## 概述

本项目是一个面向教学用途的**反向传播算法可视化演示网站**，包含两大模块：

1. **Transformer 反向传播**：聚焦于 Transformer 架构中**梯度逐层传播**的完整过程，支持**批梯度下降 (GD)** 和**随机梯度下降 (SGD)** 两种模式的对比演示。
2. **优化器对比**：基于具体 3 层 MLP 网络的**Momentum vs Adam** 逐层优化对比，展示两种优化器在各层的梯度范数差异和收敛轨迹。

### 核心理念

Transformer 的反向传播遵循经典的链式法则（Chain Rule），但因其多层堆叠的架构（Self-Attention → Add & LayerNorm → FFN → Add & LayerNorm）和残差连接的存在，梯度传播路径较为复杂。本工具通过**逐层动画**直观展示梯度从 Loss 层逆向传播至 Embedding 层的全过程。

优化器对比模块进一步使用具体的 MLP 模型（2→4→3→1）实现完整的 forward/backward pass，在相同初始权重下对比 Momentum 和 Adam 对每层参数的不同更新策略 — Momentum 累加速度平滑更新，Adam 自适应缩放每层学习率。

## 功能特性

| 模块 | 说明 |
|---|---|
| **Transformer 架构图** | 四列式布局（输入层 → 注意力子层 → 前馈子层 → 输出层），包含残差连接的虚线绕行路径，反向传播时逐层高亮脉冲 |
| **损失景观 (Loss Landscape)** | 基于 Marching Squares 算法的 2D 等高线图，优化轨迹在曲面上同时绘制，全局最优点带发光标记 |
| **梯度范数柱状图** | 反向传播/逐层顺序排列的各层梯度范数 ‖∇‖，直观展示梯度衰减效应 |
| **公式推导面板** | MathJax 渲染的 LaTeX 数学公式，逐层展示链式法则及优化器更新量的具体展开 |
| **GD vs SGD 对比** | 双轨迹同时绘制在损失景观上（蓝色 GD + 橙色 SGD），**损失曲线对比图**实时追踪每 epoch 收敛差异，面板同时显示双方 Loss 数值 |
| **MLP 优化器对比** | 基于具体 3 层 MLP (2→4→3→1) 的 Momentum vs Adam 对比 — D3 网络拓扑图反向传播动画、逐层梯度范数柱状图、损失景观双轨迹 |

## 交互操作

| 操作 | 说明 |
|---|---|
| **Tab 切换** | Transformer 反向传播 / 优化器对比（Momentum vs Adam） |
| **▶ 开始传播** | 自动连续运行动画，逐层显示反向传播 |
| **⏭ 单步** | 手动控制，每次前进一层/一步，方便课堂讲解 |
| **↺ 重置** | 清空所有状态回到初始画面 |
| **GD / SGD 切换** | （Transformer 模式）切换优化模式，两种模式同时计算并对比 |
| **GD vs SGD 损失曲线** | 梯度面板底部实时绘制 GD 和 SGD 的损失-epoch 折线图，直观对比收敛速度与稳定性 |
| **学习率 η** | 滑块调节 0.01 ~ 0.50 |
| **β, β₂** | （优化器模式）Momentum β / Adam β₂ 参数调节 |
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
├── index.html          # 单文件完整应用（HTML + CSS + JS），含两个 Tab 模块
├── README.md           # 项目文档
└── .gitignore
```

### 技术栈

- **D3.js v7** — Transformer 架构图 & MLP 网络拓扑的 SVG 渲染、梯度范数柱状图
- **MathJax 3** — LaTeX 数学公式渲染（`tex-mml-chtml`）
- **Canvas API** — 损失景观等高线图（Marching Squares 算法）
- **原生 CSS** — 暗色科研风格主题，CSS Grid 响应式布局
- **纯前端，零依赖构建工具** — 浏览器直接打开即可运行

### 关键算法

1. **Marching Squares** — 在 `drawLossLandscape()` 中实现，3px 步长网格扫描，线性插值计算等高线穿越点，生成平滑连续的等高线
2. **分段颜色映射** — 四段 colormap 函数（深蓝 → 青绿 → 翠绿 → 金 → 暗红），按损失值归一化映射
3. **链式法则逐层展开** — 9 层节点的梯度计算公式覆盖了 Transformer 的核心算子：Softmax、LayerNorm、ReLU、残差连接
4. **MLP Forward/Backward Pass** — 基于矩阵运算的前向传播和反向传播，支持对 3 层全连接网络的实际参数进行 Momentum 和 Adam 优化

## 快速开始

### 方式一：拉取项目

```bash
git clone https://github.com/zikang888/bp-visualizer.git
cd bp-visualizer
```

### 方式二：浏览器直接打开

```bash
# 直接双击 index.html 即可运行
open index.html
```

### 方式三：本地服务器

```bash
# Python 3
python3 -m http.server 8080

# 然后访问 http://localhost:8080
```

### 方式四：在线预览

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

### Transformer 模式
```
Loss → Output Proj → Add & LayerNorm₂ → FFN (Linear) → FFN (Linear+ReLU)
  → Add & LayerNorm₁ → Self-Attention → Q/K/V 投影 → Embedding
```

### 优化器模式 (MLP)
```
Output (MSE Loss) → W₃(bias₃) → Hidden₂ (ReLU) → W₂(bias₂) → Hidden₁ (ReLU) → W₁ → Input
```

## License

MIT

---

*本项目为教学演示用途，Transformer 模式的梯度数值基于启发式函数，优化器模式使用真实 MLP 模型的前向/反向传播计算。*