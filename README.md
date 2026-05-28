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
| **梯度范数柱状图** | 逐层分组柱状图：蓝色 GD + 橙色 SGD（‖∇‖），直观对比两种模式在各层的梯度衰减差异 |
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
| **GD vs SGD 损失曲线** | 梯度面板底部实时绘制 GD 和 SGD 的损失-epoch 折线图，直观对比收敛速度与稳定性 |
| **损失景观双轨迹** | 蓝色 GD + 橙色 SGD 同时绘制在等高线曲面上，当前点双标记 |
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

---

## 公式附录

### 一、损失景观函数

可视化中 2D 等高线图的损失曲面定义：

$$
L(x, y) = x^2 + 2y^2 + 0.5 \cdot \sin(3x) \cdot \cos(2y) + 0.3 \cdot \cos(4x) \cdot \sin(3y)
$$

梯度：

$$
\frac{\partial L}{\partial x} = 2x + 1.5 \cos(3x) \cos(2y) - 1.2 \sin(4x) \sin(3y)
$$

$$
\frac{\partial L}{\partial y} = 4y - \sin(3x) \sin(2y) + 0.9 \cos(4x) \cos(3y)
$$

### 二、梯度下降与随机梯度下降

**批梯度下降 (GD)**：

$$
\theta_{t+1} = \theta_t - \eta \nabla L(\theta_t)
$$

**随机梯度下降 (SGD)**：

$$
\theta_{t+1} = \theta_t - \eta \left( \nabla L(\theta_t) + \epsilon_t \right), \quad \epsilon_t \sim \mathcal{N}(0, \sigma^2)
$$

其中 $\eta$ 为学习率，SGD 在梯度上叠加随机噪声以模拟小批量采样的随机性。

### 三、Transformer 反向传播 —— 链式法则逐层展开

**反向传播顺序**（从 Loss 向 Embedding 逆向）：

#### 1. Cross-Entropy Loss

$$
\frac{\partial \mathcal{L}}{\partial \hat{y}} = \mathrm{softmax}(\hat{y}) - y
$$

整个反向传播的起点，Loss 对输出 logits 的梯度。

#### 2. Output Projection

$$
\frac{\partial \mathcal{L}}{\partial W_{\text{out}}} = z_2^{\top} \cdot \frac{\partial \mathcal{L}}{\partial \hat{y}}
$$

输出投影层的权重梯度，通过链式法则从上游梯度计算。

#### 3. Add & LayerNorm₂

$$
\frac{\partial z_2}{\partial x_2} = \mathrm{LayerNorm}'(x_2 + \mathrm{FFN}(z_1))
$$

残差连接 + LayerNorm 层的梯度，同时分流至 FFN 输出和残差路径。

#### 4. FFN (Linear)

$$
\frac{\partial \mathrm{FFN}}{\partial W_2} = h_1^{\top} \cdot \frac{\partial \mathcal{L}}{\partial \mathrm{FFN}}
$$

前馈网络第二层线性变换的权重梯度。

#### 5. FFN (Linear + ReLU)

$$
\frac{\partial h_1}{\partial W_1} = z_1^{\top} \cdot \mathrm{ReLU}'(W_1 z_1) \odot \frac{\partial \mathcal{L}}{\partial h_1}
$$

ReLU 激活函数处部分神经元梯度为零（稀疏梯度），$\odot$ 表示逐元素乘法。

#### 6. Add & LayerNorm₁

$$
\frac{\partial z_1}{\partial x_1} = \mathrm{LayerNorm}'(x_1 + \mathrm{Attn}(x))
$$

第一个残差+归一化层，梯度向 Attention 和残差路径分流。

#### 7. Self-Attention

$$
\frac{\partial \mathrm{Attn}}{\partial W_o} = \mathrm{concat}(\mathrm{head}_i)^{\top} \cdot \frac{\partial \mathcal{L}}{\partial \mathrm{Attn}}
$$

多头注意力输出投影的梯度，softmax 产生平滑梯度分布。

#### 8. Q/K/V 投影

$$
\frac{\partial Q}{\partial W_q}, \quad \frac{\partial K}{\partial W_k}, \quad \frac{\partial V}{\partial W_v}
$$

Q/K/V 三个投影矩阵的梯度分别通过注意力图回传。

#### 9. Embedding

$$
\frac{\partial \mathcal{L}}{\partial E} = \sum \frac{\partial \mathcal{L}}{\partial x}
$$

嵌入层梯度，稀疏更新 — 仅被使用的 token 嵌入获得梯度。

### 四、MLP 前向传播与反向传播

**网络结构**：Input(2) → Hidden₁(4, ReLU) → Hidden₂(3, ReLU) → Output(1)

#### 前向传播

$$
z^{(l)} = W^{(l)} a^{(l-1)} + b^{(l)}, \quad a^{(l)} = \mathrm{ReLU}(z^{(l)})
$$

最后一层不使用激活函数：$a^{(L)} = z^{(L)}$。

#### MSE 损失

$$
\mathcal{L} = \frac{1}{2} \sum_{i} (a_i^{(L)} - y_i)^2
$$

#### 反向传播

**输出层**：

$$
\delta^{(L)} = a^{(L)} - y
$$

$$
\frac{\partial \mathcal{L}}{\partial W^{(l)}} = \delta^{(l)} \cdot (a^{(l-1)})^{\top}, \quad \frac{\partial \mathcal{L}}{\partial b^{(l)}} = \delta^{(l)}
$$

**链式法则（隐藏层）**：

$$
\delta^{(l)} = \left( (W^{(l+1)})^{\top} \cdot \delta^{(l+1)} \right) \odot \mathrm{ReLU}'(z^{(l)})
$$

其中 $\mathrm{ReLU}'(z) = \begin{cases} 1 & z > 0 \\ 0 & z \leq 0 \end{cases}$。

### 五、Momentum 优化器

**动量累积**：

$$
v_t = \beta \cdot v_{t-1} + \eta \cdot \nabla L(\theta_t)
$$

**参数更新**：

$$
\theta_{t+1} = \theta_t - v_t
$$

**参数说明**：
- $\beta$：动量衰减系数（默认 0.9）
- $\eta$：学习率（默认 0.3）
- $v_t$：动量累积项，起始为 0

Momentum 将历史梯度方向累积为速度矢量，使参数更新更平滑并加速收敛。

### 六、Adam 优化器

**一阶矩（动量）估计**：

$$
m_t = \beta_1 \cdot m_{t-1} + (1 - \beta_1) \cdot \nabla L(\theta_t)
$$

**二阶矩（方差）估计**：

$$
v_t = \beta_2 \cdot v_{t-1} + (1 - \beta_2) \cdot (\nabla L(\theta_t))^2
$$

**偏差校正**：

$$
\hat{m}_t = \frac{m_t}{1 - \beta_1^t}, \quad \hat{v}_t = \frac{v_t}{1 - \beta_2^t}
$$

**参数更新**：

$$
\theta_{t+1} = \theta_t - \eta \cdot \frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \varepsilon}
$$

**参数说明**：
- $\beta_1$：一阶矩衰减系数（默认 0.9）
- $\beta_2$：二阶矩衰减系数（默认 0.999）
- $\varepsilon = 10^{-8}$：数值稳定小量
- $\eta$：学习率（默认 0.3）

Adam 结合了 Momentum 的动量思想与 RMSProp 的自适应学习率，通过二阶矩 $\hat{v}_t$ 对每个参数的自适应缩放，使不同层能够获得不同量级的更新步长。

### 七、梯度范数计算

用于可视化中柱状图的梯度度量：

$$
\|\nabla W^{(l)}\|_F = \sqrt{\sum_{i,j} \left(\frac{\partial \mathcal{L}}{\partial W_{ij}^{(l)}}\right)^2}
$$

即 Frobenius 范数，衡量每层梯度矩阵的整体大小，用于观察梯度从深层到浅层的衰减效应。