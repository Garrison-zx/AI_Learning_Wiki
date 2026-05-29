---
课程: 13 - 炼出一个大脑：LLM、多模态与世界模型的训练框架全解
版本: v1.0
创建日期: 2026-05-28
最后更新: 2026-05-28
适用模型版本: LLaMA 3 / Qwen2.5 / LLaVA-1.6 / InternVL2 / GAIA-1 / Cosmos
下次复审: 2026-08-28
更新触发条件:
  - 主流训练框架（DeepSpeed / Megatron / FSDP）发布重大版本
  - 多模态或世界模型出现新的训练范式
  - 面试出现新的分布式训练高频考点
---

> 💥 **本课的 Wow Moment**
> 大语言模型、多模态模型、世界模型——听起来是三种完全不同的 AI——但它们的训练底层逻辑惊人地相似：**把一切信号 Token 化，然后预测下一个 Token**。文字是 Token，图片切成 196 个 Token，机器人手臂的关节角度也可以是 Token。懂了这一点，你就懂了现代 AI 训练的本质。

---

## 🎯 Part 1：直觉导入

### 1.1 一句话理解

> 训练一个大模型，本质上是在用海量数据反复做一件事：**给模型看一段内容，让它预测下一个词（Token），做错了就惩罚它，做对了就鼓励它**——重复千亿次，模型就学会了语言、图像，乃至物理世界的规律。

### 1.2 生活中的类比

想象你在训练一个刚出生的孩子学语言：

1. **LLM（大语言模型）**：给孩子看无数本书，每次遮住最后一个字让他猜。猜错了就纠正。读了 3 万亿个字之后，他不仅会说话，还能写代码、做数学。
2. **多模态模型**：除了书，还给孩子看带图的绘本。他要学会把"图里那只动物"和"猫"这个字对应起来——视觉信息和语言信息打通了。
3. **世界模型**：进一步，让孩子在模拟世界里操控一个机器人，看视频、感受力道、听语言指令，学会预测"下一帧世界是什么样"——他开始理解物理规律。

三个阶段，难度递增，但核心动作相同：**看 → 预测 → 纠错**。

### 1.3 它在产品/产业中的位置

```
原始数据（文本/图片/传感器信号）
        ↓
  Token 化（切成模型能读的碎片）
        ↓
  预训练（学习世界的通用规律）
        ↓
  指令微调 / 对齐（学会听人话）
        ↓
  部署（GPT-4、Claude、Gemini、特斯拉 FSD）
```

你每天用的 ChatGPT、Claude、Sora 视频生成、特斯拉自动驾驶，都走过这条流水线。

### 1.4 本课学完你能做什么

- 能描述 LLM 预训练的完整数据流：原始文本 → Token → (input_ids, labels) → loss
- 能解释多模态模型如何把 224×224 图片变成 196 个 Token，与文本 Token 拼接训练
- 能说清世界模型的物理信号（深度图/IMU/关节角）如何被 Token 化
- 能区分 DeepSpeed、Megatron-LM、FSDP 三大训练框架的适用场景
- 能搭建一个最小可运行的多模态训练 Demo（LLaVA 风格）

---

## 📖 本课术语表（新手必看，老手跳过）

━━━━━━━━━━━━━━━━━━━━━━

| 术语 | 通俗理解 | 专业定义 | 示例 |
|------|---------|---------|------|
| Token | 模型读文字/图片时的"最小碎片" | 文本经分词器或图片经 ViT 切割后的最小处理单元 | "你好" = 2 个文本 Token；224×224 图 = 196 个图像 Token |
| 预训练（Pretraining） | 用海量数据让模型学通用知识 | 在大规模无标注数据上以自监督方式训练模型 | GPT-3 用 3000 亿 Token 的文本预训练 |
| 分词器（Tokenizer） | 把句子切成碎片的工具 | 将原始文本转化为 Token ID 序列的算法 | BPE、SentencePiece、tiktoken |
| ViT（视觉 Transformer） | 把图片切成小格子，每格变成一个向量 | Vision Transformer，将图片切成固定大小 Patch 后用 Transformer 处理 | 16×16 Patch，224×224 图 → 196 个 Patch |
| 投影层（Projector） | 把图像"翻译"成语言模型能读的格式 | 将视觉编码器输出的特征映射到 LLM 词嵌入空间的线性层或 MLP | LLaVA 的 Linear Projection 或 MLP |
| VQVAE | 把连续图像压缩成离散 Token 的工具 | Vector Quantized Variational Autoencoder，学习离散码本 | 图片 → 256 维码本中最近的 Token ID |
| 张量并行（Tensor Parallelism） | 把一层网络切开，分给多张 GPU 算 | 将模型的权重矩阵按列/行切分到多个设备上并行计算 | Megatron-LM 的列并行/行并行 |
| 梯度检查点（Gradient Checkpointing） | 用算力换内存，重新计算中间结果 | 前向时不保存中间激活值，反向时重新计算，节省显存 | 节省约 50% 显存，增加约 30% 训练时间 |
| bf16 / fp16 | 用低精度浮点数训练，快而省显存 | 16 位浮点数格式；bf16 动态范围大于 fp16，更适合大模型训练 | A100/H100 原生支持 bf16 |

━━━━━━━━━━━━━━━━━━━━━━

---

## 🔬 Part 2：颗粒级原理拆解

### 2.1 三代模型的统一视角：一切皆 Token 预测

现代 AI 训练有一个统一范式，无论模型处理的是文字、图片还是传感器信号：

```
输入序列: [T₁, T₂, T₃, ... Tₙ]
目标:     [T₂, T₃, T₄, ... Tₙ₊₁]  ← 每个位置预测下一个
损失:     Cross-Entropy(预测分布, 真实 Token)
```

三代模型的区别只在于 **Token 的来源和种类**：

| 模型 | Token 种类 | Token 来源 |
|------|-----------|-----------|
| LLM | 文本 Token | BPE 分词器 |
| 多模态模型 | 文本 Token + 图像 Token | BPE + ViT Patch |
| 世界模型 | 文本 + 图像 + 物理信号 Token | BPE + ViT + VQVAE + 传感器投影 |

---

### 2.2 LLM 训练全流程

#### 2.2.1 数据准备：从原始文本到训练样本

**原始数据来源**：

| 数据集 | 来源 | 规模 | 特点 |
|--------|------|------|------|
| Common Crawl | 全网爬虫 | 数十 TB | 噪声多，需清洗 |
| Books3 / OpenWebText | 书籍/Reddit | 数百 GB | 质量高 |
| StarCoder / The Stack | GitHub 代码 | 数 TB | 代码专项 |
| Wikipedia / Wikidata | 百科 | 数十 GB | 高质量知识 |

**数据预处理流水线**：

```
原始 HTML 网页
    ↓ 文本提取（BeautifulSoup / Trafilatura）
清洁文本
    ↓ 质量过滤（语言检测、困惑度过滤、去重）
高质量文本
    ↓ Tokenizer 编码（BPE，词表大小 32K-128K）
Token ID 序列
    ↓ 打包（Pack，填满 4096 长度，减少 padding 浪费）
训练样本 (input_ids, labels, attention_mask)
```

**🔥 面试高频**：为什么要"打包（Pack）"而不是补零（Padding）？

> Padding 会浪费大量算力在 `<pad>` Token 上，而打包将多个短文本首尾相接填满序列长度，GPU 利用率提升 30-50%。代价是需要用 attention mask 隔断不同文档之间的注意力（避免跨文档信息泄露）。

**⚠️ 易错**：labels 不是 input_ids 的复制！labels 中，Padding 位置和 Prompt 位置（指令微调时）需要设为 -100，使 Cross-Entropy 忽略这些位置的损失。

#### 2.2.2 模型架构：Decoder-only Transformer

现代 LLM 统一采用 Decoder-only 结构（GPT 系列、LLaMA 系列）：

```
输入 Token IDs [B, T]
      ↓
Token Embedding [B, T, d_model]    ← 词嵌入表，d_model = 4096（7B 模型）
      ↓
+ RoPE 位置编码（旋转位置编码，无额外参数）
      ↓
N × Transformer Block：
  ├── RMSNorm（Pre-Norm）
  ├── GQA 注意力（Grouped Query Attention）[B, T, d_model]
  │     ├── Q: [B, n_heads, T, d_head]      ← n_heads = 32
  │     ├── K: [B, n_kv_heads, T, d_head]   ← n_kv_heads = 8（GQA 减少 KV 缓存）
  │     └── V: [B, n_kv_heads, T, d_head]
  ├── RMSNorm
  └── SwiGLU FFN [B, T, d_model]            ← 隐藏层 = 4 × d_model × 2/3
      ↓
RMSNorm
      ↓
LM Head（线性层，映射到词表大小 V）[B, T, vocab_size]
      ↓
Cross-Entropy Loss（与 labels 对比）
```

**关键设计决策及为什么**：

| 组件 | 为什么这么选 | 演进脉络 |
|------|------------|---------|
| RMSNorm 替代 LayerNorm | 省去均值计算，速度快 5-10%，效果相当 | LN(2016) → RMSNorm(2019) → 无归一化探索(2024) |
| Pre-Norm 替代 Post-Norm | 梯度直接绕过残差分支传播，深层网络训练更稳定 | Post-Norm 在 100 层以上容易梯度消失 |
| GQA 替代 MHA | KV Cache 内存减少 4-8×，推理速度显著提升 | MHA → MQA(2019) → GQA(2023) |
| SwiGLU 替代 ReLU-FFN | 门控机制带来更强表达能力，PaLM/LLaMA 均采用 | ReLU → GELU → SwiGLU → Mish |
| RoPE 替代绝对位置编码 | 天然编码相对位置，外推能力强 | 正弦(2017) → 可学习(2018) → RoPE(2021) → YaRN(2023) |

#### 2.2.3 训练策略

**学习率调度（Cosine Schedule + Warmup）**：

```
阶段1（Warmup）: 0 → lr_max，线性升温（约 1-2% 总步数）
阶段2（Decay）:  lr_max → lr_min，余弦退火
典型值: lr_max = 3e-4，lr_min = 3e-5，Warmup = 2000 步
```

**为什么需要 Warmup**：初始参数随机，直接用大学习率会导致梯度爆炸。Warmup 让模型先"试探"合适的方向。

**混合精度训练（bf16 + fp32 主权重）**：

```
前向/反向: bf16（快、省显存）
梯度累积: fp32（精度高，防止梯度下溢）
优化器状态（Adam m/v）: fp32
```

**梯度检查点（Gradient Checkpointing）**：

训练 7B 模型时，激活值内存占用约 40GB（batch=8, seq=4096）。梯度检查点只保存每个 Transformer Block 的输入，反向传播时重新计算中间激活值，内存减少约 50%，训练时间增加约 30%。

---

### 2.3 多模态模型训练：文本 Token + 图像 Token

#### 2.3.1 图像如何变成 Token

**ViT（Vision Transformer）Patch 化流程**：

```
输入: RGB 图片 [3, 224, 224]
      ↓
Patch 切割（16×16 像素一格）: [(224/16)², 16×16×3] = [196, 768]
      ↓
线性 Patch Embedding: [196, 768] → [196, d_model]
      ↓
+ [CLS] Token + 位置编码: [197, d_model]
      ↓
ViT Transformer Blocks（CLIP ViT-L/14: 24 层）
      ↓
图像特征: [197, 1024]（取 [CLS] 或全部 Patch 特征）
```

**一张 224×224 图 = 196 个图像 Token**（14×14 或 16×16 Patch 均常用）

**⚠️ 易错**：不同分辨率会产生不同数量的 Token。InternVL2 等模型支持动态分辨率，1024×1024 的图可能产生 1000+ Token，直接影响上下文长度和显存占用。

#### 2.3.2 图像 Token 如何与文本 Token 对齐

三种主流对齐架构：

| 架构 | 代表模型 | 对齐方式 | 优缺点 |
|------|---------|---------|--------|
| **线性投影/MLP** | LLaVA-1.5 | ViT 输出 → Linear/MLP → LLM embedding space | 简单高效，对齐效果一般 |
| **Q-Former** | BLIP-2 | 可学习 Query Token 通过交叉注意力提取图像信息 | 压缩图像信息，节省 Token 数 |
| **交叉注意力层** | Flamingo | 在 LLM 每层插入视觉交叉注意力层 | 视觉融合深入，参数量大 |

**LLaVA 风格训练流程（两阶段）**：

```
阶段一：视觉-语言对齐预训练
  - 数据：595K 图文对（CC3M 子集）
  - 训练：冻结 ViT + 冻结 LLM，只训练 Projection Layer
  - 目标：让 Projection Layer 把图像特征映射到 LLM 能理解的空间
  - 耗时：约 4h（8×A100）

阶段二：视觉指令微调
  - 数据：150K 视觉问答对话数据
  - 格式：{"image": img, "conversations": [
             {"from": "human", "value": "<image>\n这张图里有什么？"},
             {"from": "gpt", "value": "图中是一只橘猫，..."}
           ]}
  - 训练：冻结 ViT，同时训练 Projection Layer + LLM
  - 目标：让模型能按指令回答图像相关问题
  - 耗时：约 8h（8×A100）
```

**训练样本的实际数据格式**：

```python
# 多模态训练样本的张量形状
{
  "input_ids":      [B, T],           # 文本+图像占位符的 Token ID
  "pixel_values":   [B, C, H, W],     # 原始图像像素 [B, 3, 224, 224]
  "labels":         [B, T],           # -100 表示不计算 loss 的位置
  "attention_mask": [B, T],           # 1=有效 Token，0=padding
}
# 图像 Token 在 input_ids 中用特殊 ID（如 <image>=32000）占位
# 模型前向时替换为 ViT 提取的图像特征
```

**🔥 面试高频**：多模态模型训练时，图像部分的 loss 需要 mask 掉吗？

> 是的。图像 Token 是输入，不是预测目标，对应位置的 labels 设为 -100。只对模型生成的文字回答计算 loss，否则模型会"学着重建图像"而非"学着回答问题"。

---

### 2.4 世界模型训练：文本 + 图像 + 物理世界信号

#### 2.4.1 什么是世界模型

世界模型（World Model）的核心目标：给定当前状态 $s_t$ 和动作 $a_t$，预测下一个状态 $s_{t+1}$：

$$\hat{s}_{t+1} = f_\theta(s_t, a_t)$$

实现"真正理解物理世界"，而非单纯模式匹配。

#### 2.4.2 物理世界信号的种类与 Token 化

| 信号类型 | 来源 | Token 化方法 | Token 形状 |
|---------|------|------------|-----------|
| RGB 图像 / 视频帧 | 摄像头 | ViT Patch Embedding | [T_frames × 196, d] |
| 深度图（Depth Map） | 深度相机/激光雷达 | 同 ViT，单通道 | [196, d] |
| 点云（Point Cloud） | 激光雷达 | PointNet++ 提取特征 → 线性投影 | [N_points, d] |
| IMU 数据（加速度/陀螺仪） | 惯性传感器 | 归一化 → MLP 投影，或离散化分桶 | [T_imu, d] |
| 关节角度 / 末端位置 | 机器人编码器 | 离散化（200 个桶）或连续 MLP 投影 | [N_joints, d] |
| 力矩 / 触觉 | 力矩传感器 | 归一化 → MLP 投影 | [N_sensors, d] |
| 语言指令 | 文本 | BPE 分词器 | [T_text, d] |

**视频帧的离散 Token 化（VQVAE 路线）**：

```
视频帧 [T, 3, H, W]
    ↓ VQVAE 编码器（CNN）
连续特征 [T, h, w, C]
    ↓ 向量量化（找最近的码本向量）
离散 Token IDs [T × h × w]   ← 每帧约 16×16=256 个 Token
    ↓（展平）
视频 Token 序列 [T_video_tokens]
```

**💡 面试加分**：VQVAE vs ViT 特征，世界模型训练哪个更常用？

> 取决于任务。VQVAE 离散化后可以用统一的 next-token prediction loss，与语言模型完全统一（如 VideoGPT、GAIA-1）；ViT 连续特征则需要额外的重建损失或扩散模型头（如 Sora、VideoPoet）。离散路线工程简单，连续路线生成质量更高。

#### 2.4.3 世界模型的训练数据格式

以自动驾驶世界模型 GAIA-1 为例，一个训练样本是一段驾驶序列：

```python
{
  "video_tokens":   [T_frames × 256],  # VQVAE 离散化的视频 Token
  "text_tokens":    [T_text],          # 场景描述/指令 Token
  "action_tokens":  [T_frames],        # 离散化方向盘角度 + 油门/刹车
  "labels":         [T_total],         # 预测目标：下一帧的 video Token
}
# 序列拼接格式：[文本指令 | 帧1视频Token | 动作1 | 帧2视频Token | 动作2 | ...]
```

以机器人操作世界模型为例（π₀、RT-2 等）：

```python
{
  "rgb_tokens":      [T_frames × 196],   # ViT 图像特征（连续）
  "language_tokens": [T_text],           # 任务指令
  "proprio_tokens":  [T_steps × 7],      # 机器人关节角（7 自由度）→ 线性投影
  "action_labels":   [T_steps × 7],      # 预测目标：下一步关节角
}
```

**训练目标的差异**：

| 模型类型 | 主要损失函数 | 说明 |
|---------|------------|------|
| LLM | Cross-Entropy（文本 Token） | 预测下一个词 |
| 多模态模型 | Cross-Entropy（文本 Token，图像位置 mask） | 预测文字回答 |
| 离散 Token 世界模型 | Cross-Entropy（下一帧的离散 Token） | 预测下一帧 |
| 连续特征世界模型 | MSE / Diffusion Loss（下一帧连续特征） | 重建下一帧 |

---

### 2.5 分布式训练框架：DeepSpeed vs Megatron vs FSDP

训练百亿参数模型，单张 GPU 装不下，需要分布式训练。三种主流框架：

#### 并行策略全景

```
分布式训练策略
├── 数据并行（Data Parallelism）
│   ├── DDP：每个 GPU 完整模型，梯度同步
│   └── FSDP：模型参数分片存储，通信时聚合
│
├── 模型并行（Model Parallelism）
│   ├── 流水线并行（Pipeline Parallelism）：不同层放不同 GPU
│   └── 张量并行（Tensor Parallelism）：同一层权重矩阵切分
│
└── 专家并行（Expert Parallelism）：MoE 架构专用
```

#### 三大框架对比

| 框架 | 核心优势 | 典型场景 | 接入难度 |
|------|---------|---------|---------|
| **DeepSpeed** (微软) | ZeRO 优化（参数/梯度/优化器状态分片），显存效率极高 | 单机多卡或小规模集群，7B-70B 模型 | 低，加几行配置即可 |
| **Megatron-LM** (NVIDIA) | 张量并行+流水线并行，通信效率最优 | 大规模集群（1000+ GPU），100B+ 模型 | 高，需改造模型代码 |
| **FSDP** (PyTorch 原生) | 与 PyTorch 深度集成，无需额外依赖 | 需要 PyTorch 原生支持的场景 | 中，API 类似 DDP |

**DeepSpeed ZeRO 三个阶段**：

| ZeRO Stage | 分片内容 | 显存节省 | 通信开销 |
|-----------|---------|---------|---------|
| ZeRO-1 | 优化器状态（Adam m/v） | ~4× | 低 |
| ZeRO-2 | 优化器状态 + 梯度 | ~8× | 中 |
| ZeRO-3 | 优化器状态 + 梯度 + 参数 | ~64× | 高 |

**🔥 面试高频**：ZeRO-3 能把 70B 模型训练在 8×A100 上吗？

> 理论上可以。70B 参数 × 2（bf16）= 140GB 参数，8×A100（80GB）= 640GB 总显存，ZeRO-3 把参数分散到 8 卡，每卡约 17.5GB 参数；加上梯度、优化器状态（fp32，约 4×参数）实际更紧张，需配合梯度检查点。

**🔥 面试高频**：张量并行和流水线并行的区别？

> **张量并行**：把同一层的矩阵按行/列切分到多个 GPU，所有 GPU 同步处理同一个 batch 的同一层，通信发生在层内（需要 All-Reduce）。延迟低，适合层内维度大的情况。
> **流水线并行**：不同层分到不同 GPU，数据像流水线一样流动，通信发生在层间（P2P Send/Recv）。通信量小，但存在"bubble"（等待问题）。实践中两者结合使用（3D 并行 = 数据并行 × 张量并行 × 流水线并行）。

---

### 2.6 关键公式速查表

| 公式 | 含义 |
|------|------|
| $\mathcal{L} = -\frac{1}{T}\sum_{t=1}^{T} \log P(x_t \mid x_{<t})$ | 语言模型预训练损失（负对数似然） |
| $\text{Patch Token 数} = \left(\frac{H}{p}\right)^2$ | 图像 Patch 数，H=图片尺寸，p=Patch 大小 |
| $\text{GPU 显存} \approx 2N + 16N/n$ | 训练显存估算（bf16 参数 + ZeRO-1 优化器），N=参数量（字节），n=GPU 数 |
| $\text{Tokens/GPU/s} = \frac{6ND}{T_{wall} \cdot n_{GPU}}$ | 训练吞吐量，N=参数，D=数据量，T=总训练时间 |

---

## 💻 Part 3：代码实践

### 3.1 LLM 训练最小 Demo（从零实现训练循环）

```python
"""
功能：LLM 预训练核心训练循环（最小可运行版本）
依赖：torch >= 2.0, transformers >= 4.40
输入：随机生成的 token 序列，模拟真实训练数据
输出：每步 loss 值
预计运行：< 5 秒（CPU），展示核心逻辑
"""
import torch
import torch.nn.functional as F
from transformers import AutoTokenizer, AutoModelForCausalLM

# ─── 配置 ───────────────────────────────────────────────
model_name = "Qwen/Qwen2.5-0.5B"  # 0.5B，CPU 可运行
device = "cuda" if torch.cuda.is_available() else "cpu"

# ─── 加载模型与分词器 ─────────────────────────────────────
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(
    model_name, torch_dtype=torch.bfloat16
).to(device)

# ─── 构造一批训练数据 ─────────────────────────────────────
texts = [
    "深度学习是人工智能的核心技术之一，",
    "Transformer 架构在 2017 年被提出，",
]
# Tokenize：输入和标签错开一位
batch = tokenizer(texts, return_tensors="pt", padding=True, truncation=True,
                  max_length=128).to(device)
input_ids = batch["input_ids"]
labels = input_ids.clone()
labels[batch["attention_mask"] == 0] = -100  # 忽略 padding 位置的 loss

# ─── 单步训练 ─────────────────────────────────────────────
optimizer = torch.optim.AdamW(model.parameters(), lr=3e-4)
model.train()

outputs = model(input_ids=input_ids, labels=labels)
loss = outputs.loss   # CrossEntropy，内部已做 next-token shift

loss.backward()
optimizer.step()
optimizer.zero_grad()

print(f"Loss: {loss.item():.4f}")
# 正常初始 loss ≈ ln(vocab_size) ≈ ln(151936) ≈ 11.9
```

### 3.2 图像 Token 化 Demo（ViT Patch 提取）

```python
"""
功能：演示图片如何被 ViT 切分成 196 个 Patch Token
依赖：torch >= 2.0, transformers >= 4.40, Pillow
输入：任意 224×224 图片（代码内生成随机图）
输出：图像特征 shape = [1, 197, 1024]（含 CLS Token）
预计运行：< 10 秒（CPU）
"""
import torch
from transformers import CLIPVisionModel, CLIPImageProcessor
from PIL import Image
import numpy as np

# 用随机图代替真实图片（避免依赖外部文件）
fake_image = Image.fromarray(
    np.random.randint(0, 255, (224, 224, 3), dtype=np.uint8)
)

# 加载 CLIP ViT（openai/clip-vit-large-patch14）
processor = CLIPImageProcessor.from_pretrained("openai/clip-vit-large-patch14")
vision_model = CLIPVisionModel.from_pretrained("openai/clip-vit-large-patch14")

# 图片预处理：归一化、转 Tensor
pixel_values = processor(images=fake_image, return_tensors="pt").pixel_values
# pixel_values.shape = [1, 3, 224, 224]

# ViT 前向：切 196 个 Patch + 1 个 CLS Token
with torch.no_grad():
    outputs = vision_model(pixel_values=pixel_values)

image_features = outputs.last_hidden_state
# image_features.shape = [1, 197, 1024]
# ├── [:, 0, :]    → CLS Token（全局图像表示）
# └── [:, 1:, :]   → 196 个 Patch Token（14×14 网格的局部特征）

print(f"图像特征 shape: {image_features.shape}")
print(f"Patch 数量: {image_features.shape[1] - 1}")  # 196
print(f"每个 Patch 特征维度: {image_features.shape[2]}")  # 1024
```

### 3.3 DeepSpeed ZeRO 配置（生产级用法）

```python
"""
功能：DeepSpeed ZeRO-2 训练配置文件（JSON格式）
用途：在多 GPU 训练时启用，减少显存占用
使用方法：
  deepspeed --num_gpus 8 train.py --deepspeed ds_config.json
"""
ds_config = {
    "bf16": {"enabled": True},           # bf16 混合精度
    "zero_optimization": {
        "stage": 2,                       # ZeRO-2：分片优化器状态+梯度
        "overlap_comm": True,             # 梯度通信与计算重叠，加速训练
        "contiguous_gradients": True,     # 连续梯度内存，减少碎片
        "allgather_partitions": True,
        "reduce_scatter": True,
        "reduce_bucket_size": 5e8,
    },
    "gradient_checkpointing": True,      # 节省激活值显存
    "gradient_clipping": 1.0,            # 梯度裁剪，防止爆炸
    "train_micro_batch_size_per_gpu": 4,
    "gradient_accumulation_steps": 8,   # 等效 batch = 4×8×n_gpu
    "optimizer": {
        "type": "AdamW",
        "params": {"lr": 3e-4, "betas": [0.9, 0.95], "weight_decay": 0.1}
    },
    "scheduler": {
        "type": "WarmupCosineAnnealing",
        "params": {"warmup_num_steps": 2000, "total_num_steps": 100000}
    }
}
```

### 3.4 常见训练报错与排查

| 报错 | 原因 | 解决方法 |
|------|------|---------|
| `CUDA out of memory` | batch size 或序列长度过大 | 减小 batch，开启梯度检查点，用 ZeRO-3 |
| `loss = nan` | 学习率过大或梯度爆炸 | 降低 lr，加梯度裁剪（clip_grad_norm=1.0） |
| `labels shift error` | labels 与 input_ids 未错开一位 | 检查 labels 是否正确左移；使用模型内置 loss 计算 |
| `NCCL timeout` | 多卡通信超时 | 检查网络带宽，增大 NCCL_TIMEOUT 环境变量 |
| `tokenizer padding side` | 左 padding vs 右 padding 混用 | 训练时用右 padding（`padding_side='right'`） |

---

## 🎓 Part 4：产品视角

### 4.1 产品经理必知的 3 件事

1. **训练成本决定市场格局**：GPT-4 预训练成本约 $1 亿，LLaMA 3 70B 约 $500 万，7B 级别约 $20-50 万。成本门槛决定了谁能参与竞争——大公司做预训练，中小公司做微调和应用。

2. **多模态数据质量是瓶颈**：图文对数据的质量（而非数量）是多模态模型好坏的核心。LLaVA 用 150K 条高质量指令数据，表现超过用千万条低质量数据训练的模型——这对产品侧的数据标注策略有重要启示。

3. **世界模型是具身智能的基础设施**：特斯拉 FSD、Figure 人形机器人、Google DeepMind Robotics 都在构建世界模型。懂训练框架才能判断哪些具身 AI 产品声称的"通用能力"是真正可落地的，哪些是 PPT。

### 4.2 行业案例

| 产品 | 模型类型 | 训练框架 | 核心数据 |
|------|---------|---------|---------|
| ChatGPT / GPT-4 | LLM | 内部框架（基于 Megatron） | 45TB 文本 + RLHF 标注 |
| Claude 3.5 | LLM | 内部框架 | 未披露 + Constitutional AI |
| Gemini 1.5 | 多模态 | TPU + JAX | 文本+图+视频+音频 |
| LLaVA-1.6 | 多模态 | DeepSpeed + transformers | CC3M + LLaVA-Instruct |
| GAIA-1 (Wayve) | 世界模型 | 内部框架 | 驾驶视频 + 文本 |
| Cosmos (NVIDIA) | 世界模型 | Megatron + 物理仿真数据 | 视频+传感器融合 |

### 4.3 技术选型的商业考量

| 场景 | 推荐框架 | 理由 |
|------|---------|------|
| 初创公司微调 7B/13B | Hugging Face Trainer + DeepSpeed ZeRO-2 | 上手快，社区资源丰富 |
| 中型团队训练 30B-70B | FSDP + torch.compile | PyTorch 原生，无额外依赖 |
| 大型团队预训练 100B+ | Megatron-LM + DeepSpeed 3D 并行 | 极致通信效率，千卡扩展性最优 |
| 多模态快速 PoC | LLaVA 开源代码 + LoRA | 开箱即用，单卡 A100 可跑 |

### 4.4 前沿动态（2025-2026）

- **Token 效率革命**：Qwen2.5-VL 的动态分辨率支持，高分辨率图片直接处理无需预裁剪，多模态理解能力大幅提升。
- **视频世界模型走向实用**：NVIDIA Cosmos 开源物理 AI 世界模型，支持机器人模拟数据生成，直接降低具身 AI 训练数据成本。
- **统一多模态架构（Any-to-Any）**：GPT-4o、Gemini 2.0 原生多模态（语音、图像、视频统一在一个模型内），不再是"LLM + 外挂视觉编码器"的拼接方式。
- **训练框架整合**：Megatron-LM 与 DeepSpeed 的 Mcore 架构融合趋势明显，未来可能统一成一套框架。

---

## 📝 Part 5：作业与测试

### 5.1 基础题

**题目 1**：一张 448×448 的图片，用 ViT-L/14（Patch size = 14）处理后，会产生多少个图像 Token？

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 知道用 (H/p)² 公式：1 分
- ⭐⭐ 正确计算结果：2 分
- ⭐⭐⭐ 能说明这对上下文长度的影响：3 分

**参考答案**：
$(448/14)^2 = 32^2 = 1024$ 个图像 Token。
加上 CLS Token 共 1025 个 Token。若 LLM 上下文窗口是 4096，仅一张图就占用了 25%。这是多模态模型对高分辨率图处理的核心瓶颈，也是为什么 InternVL2 等模型要专门优化动态分辨率的原因。

**常见错误**：
- 只计算一个维度（448/14=32），忘记平方。
- 忘记 CLS Token（面试中加上会显得更熟悉细节）。
</details>

---

**题目 2**：LLM 训练时，为什么 labels 中 Prompt 部分要设为 -100？

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 知道 -100 表示忽略该位置的 loss：1 分
- ⭐⭐ 能解释为什么忽略 Prompt：2 分
- ⭐⭐⭐ 能举例说明不忽略的后果：3 分

**参考答案**：
Cross-Entropy Loss 在计算时，会对 labels 中所有位置求平均（或求和）。如果 Prompt（指令部分）也参与 loss 计算，模型会既优化"生成正确指令"又优化"生成正确回答"，导致模型学会"原样复述指令"，而非学会"根据指令生成回答"。
设为 -100 后，PyTorch 的 CrossEntropyLoss 会自动跳过这些位置（`ignore_index=-100`），模型只学习"给定完整上下文，预测回答部分的每个词"。

**常见错误**：
- 误以为 -100 是某个特殊 Token ID，实际上这是 PyTorch loss 函数的约定值，与词表无关。
</details>

---

### 5.2 🔥 面试真题

**真题 1** `[牛客网-2025-Q2-算法岗]`：请解释 DeepSpeed ZeRO-1、ZeRO-2、ZeRO-3 的区别，以及各自的通信开销？

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 能区分三个 Stage 分片的内容：1 分
- ⭐⭐ 能给出显存节省的大致倍数：2 分
- ⭐⭐⭐ 能分析通信开销差异及实际使用建议：3 分

**参考答案**：
| Stage | 分片内容 | 每 GPU 显存节省 | 额外通信 |
|-------|---------|--------------|---------|
| ZeRO-1 | 优化器状态（Adam m、v、fp32主权重） | ~4× | All-Reduce 梯度（与 DDP 相同） |
| ZeRO-2 | ZeRO-1 + 梯度 | ~8× | Reduce-Scatter 梯度，All-Gather 参数 |
| ZeRO-3 | ZeRO-2 + 模型参数 | ~64× | 每次前向/反向均需 All-Gather 参数 |

实际工程选择：7B/13B 模型优先 ZeRO-2（显存够用时避免 ZeRO-3 的高通信开销）；30B 以上模型使用 ZeRO-3 + 梯度检查点；100B 以上需要 3D 并行（数据×张量×流水线）。

**常见错误**：
- 将 ZeRO 与模型并行混淆：ZeRO 属于数据并行范畴，所有 GPU 都保有完整的计算，只是存储分片；模型并行是不同 GPU 计算不同层/不同部分。
</details>

---

**真题 2** `[常见考点-多模态训练]`：多模态模型中，图像特征和文本特征如何对齐？为什么需要"对齐"这一步骤？

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 知道 ViT 特征维度与 LLM 嵌入维度不同：1 分
- ⭐⭐ 能说明主流对齐方式（Linear/MLP/Q-Former）：2 分
- ⭐⭐⭐ 能解释为什么需要"对齐预训练"而非直接端到端训练：3 分

**参考答案**：
"对齐"是必要的，因为 ViT（如 CLIP ViT-L）的输出特征空间和 LLM（如 LLaMA）的词嵌入空间是完全独立训练的，维度不同（1024 vs 4096），语义空间也不重叠。直接拼接就像把英文词向量塞进中文模型，模型无法理解。

主流对齐方式：
1. **Linear Projection**（LLaVA-1.5）：简单线性层，参数量小，训练快
2. **MLP Projector**（LLaVA-1.6）：两层 MLP，表达能力更强
3. **Q-Former**（BLIP-2）：用可学习 Query Token 通过交叉注意力"主动提取"图像信息，能压缩 Token 数量

为什么要两阶段训练（先对齐、再指令微调）：第一阶段用大量低质量图文对"打通"视觉-语言通道；第二阶段用少量高质量对话数据教模型"怎么用"视觉信息回答问题。直接混合训练会导致模型既没学好视觉特征提取，也没学好指令遵循。
</details>

---

### 5.3 💼 产品设计题

**题目**：你是一家自动驾驶公司的技术产品经理。公司计划训练一个能够"理解驾驶场景并预测未来 3 秒"的世界模型，用于改善 L4 级自动驾驶的规划模块。请设计数据采集和训练方案的概要（不需要写代码），重点说明：需要采集哪些传感器信号？如何 Token 化？训练目标是什么？

<details>
<summary>💡 思考方向（点击展开）</summary>

**思考维度**：
- 传感器选择：摄像头（多视角 RGB）、激光雷达（点云）、毫米波雷达、IMU、GPS、高精地图
- Token 化方案：视频帧 → VQVAE 离散化；点云 → 体素化后 PointNet；IMU/GPS → 归一化后线性投影；高精地图 → 图像化后 ViT
- 训练目标：给定当前帧 + 未来动作，预测未来 3 秒的视频帧（感知预测）；或预测未来轨迹点（规划预测）
- 数据规模估算：1 小时驾驶数据 ≈ 10Hz × 6 摄像头 × 3600 秒 = 21.6 万帧，约 500GB 原始数据
- 工程权衡：离散 Token 路线（工程简单）vs 连续特征路线（生成质量高）；是否需要人工标注（感知标签）
</details>

---

### 5.4 开放思考题

**思考题**：三种模型类型（LLM、多模态、世界模型）的"Token 化"策略越来越统一，有人认为未来会有一个"万物皆 Token"的统一大模型，训练一次解决所有问题。你认为这条路的核心挑战是什么？技术和工程上各有哪些障碍？

> 💡 **提示**：可以从 Token 词表对齐、不同模态的数据规模差异、训练目标冲突、计算成本、评估标准等维度展开思考。没有标准答案，重在逻辑连贯性。

---

**前置知识**：课程 01（Transformer 架构）、课程 04（大力出奇迹：大模型基础）

**后续知识**：课程 05（RLHF 与对齐）、世界模型专题（具身智能）

**关联知识**：课程 07（选型与部署，训练后如何高效推理）、课程 06（提示词与 Harness）
