---
课程: WM-04 - JEPA架构：LeCun路线的核心与争议
系列: 世界模型系列课程（方案 A）
版本: v1.0
创建日期: 2026-05-25
最后更新: 2026-05-25
类型: A 原理型
时长: 70min
前置课程: WM-01（导论）、WM-02（潜空间表示）
适用受众: AI研究者、算法工程师
下次复审: 2026-08-25
更新触发条件:
  - LeCun 或 Meta AI 发布 V-JEPA v2 或新的 JEPA 变体
  - JEPA 在强化学习/规划任务上取得重大突破
  - JEPA 与生成式路线的对比出现定论性实验
---

# WM-04：JEPA架构：LeCun路线的核心与争议

> 💥 **本课 Wow Moment**："LeCun 用 5 年时间跟整个 AI 界唱反调，他可能是对的。"
>
> 2022 年，当 GPT-4、Stable Diffusion、DALL-E 如日中天时，Yann LeCun 发表了一篇 58 页的技术路线图，提出了一个反常识的论断：**生成式 AI（包括 LLM、图像生成）根本不是通往 AGI 的正确路径**。真正的智能需要一种新架构——JEPA（联合嵌入预测架构）。
>
> AI 界的反应从嘲笑到认真对待：2023 年，Meta 发布了基于 JEPA 的 I-JEPA，在没有任何像素级生成的情况下，学出了比 DINO 更好的视觉表示。2024 年，V-JEPA 把这个思想扩展到视频。
>
> LeCun 是对的吗？这个问题，你学完本课后自己回答。

---

## 一、逻辑主线

```
生成 vs 预测：两种世界观的哲学差异
    ↓ 核心问题：为什么生成像素是"浪费"？
LeCun 的批判：生成式 AI 的三个根本缺陷
    ↓
JEPA 框架：在表示空间中预测
    ↓ 理论
    ├── I-JEPA（2023）：图像域的实现
    └── V-JEPA（2024）：视频域的扩展
与对比学习的关系：Non-contrastive 方法
    ↓
与生成式路线的实证对比
    ↓ 争议
支持者论点 vs 反对者论点
    ↓
JEPA 的能力边界：视觉表示好，规划能力待验证
```

---

## 二、课程结构总览

```
WM-04：JEPA 架构（70min）
│
├── 🎯 Part 0  导入：预测 vs 生成的哲学差异  (6min)
│
├── 🌍 Part 1  直觉导入                      (8min)
│   ├── 1.1 "填空题" vs "写作文"类比
│   ├── 1.2 人类是用"生成"还是"预测"感知世界？
│   └── 1.3 能量模型的直觉
│
├── 📖 术语表                                 (3min)
│
├── 🔬 Part 2  颗粒级原理拆解                 (32min)
│   ├── 2.1 JEPA 框架的数学形式
│   ├── 2.2 I-JEPA：图像的联合嵌入预测
│   ├── 2.3 V-JEPA：视频的时序预测
│   ├── 2.4 与生成式路线的对比
│   ├── 2.5 争议：支持者 vs 反对者
│   └── 2.6 🔥 面试考点
│
├── 💻 Part 3  代码实践                       (10min)
│   └── 3.1 JEPA 核心思想：预测表示（简化 Demo）
│
├── 🎓 Part 4  产品视角                       (6min)
│
└── 📝 Part 5  作业与测试                     (5min)
```

---

## 三、章节大纲

### 🎯 Part 0：导入——预测 vs 生成的哲学差异（6min）

**核心对比**：

| 范式 | 做什么 | 代表模型 | LeCun 的评价 |
|------|--------|---------|------------|
| **生成式** | 给定部分输入，生成完整的输出（像素、文本、音频） | GPT、Stable Diffusion、DALL-E、VideoGPT | "浪费计算资源在无关细节上" |
| **判别式** | 给定输入，预测标签 | 分类器、检测器 | "有用，但需要大量标注" |
| **JEPA** | 给定部分输入，预测另一部分的**表示**（不是像素） | I-JEPA、V-JEPA | "正确的方向：预测有用信息，丢弃无关细节" |

**LeCun 的核心主张**：

> 如果你想预测"猫在沙发上后面会怎样"，你需要预测的是：
> - 猫可能跳走、蜷缩、伸懒腰（**语义预测**）
>
> 你不需要预测的是：
> - 阳光如何照在猫毛上、猫毛的每根毛的反光方向（**像素级细节**）
>
> 生成式模型花了大量计算力预测这些无关细节。JEPA 说：我们只预测**语义表示**，不预测像素。

---

### 🌍 Part 1：直觉导入（8min）

#### 1.1 "填空题" vs "写作文"类比

> **写作文（生成式）**：给你一段开头，让你写完整篇文章。你需要生成每一个词、每一个句子，包括很多"无聊但必须填满"的内容。
>
> **填空题（JEPA 式）**：给你一篇文章，遮住几个关键段落，让你预测"这里说的大概是什么意思"——你只需要预测**意义（表示）**，不需要逐字逐句还原。
>
> JEPA 就是让 AI 做"智能填空"：看到图像的一部分，预测其他部分的**语义表示**，而不是生成像素。

#### 1.2 人类感知：预测优先于生成

> 认知科学研究表明，人类大脑对世界的建模主要是**预测性**的，而非生成性的：
>
> - Karl Friston 的**预测编码理论（2005）**：大脑持续预测感官输入，只对"预测误差"（意外信息）做出反应
> - 当你走进一个熟悉的房间，你不会"生成"整个场景——你只更新你预期中不同的部分
>
> **LeCun 的论点**：JEPA 比生成式 AI 更接近大脑的工作方式。

#### 1.3 能量模型的直觉

> **能量模型（EBM）** 是 JEPA 的理论基础：
>
> 不问"P(y|x) 是多少"（生成式：需要归一化），而是问"E(x, y) 是多少"（能量：相容的配对能量低，不相容的配对能量高）。
>
> 类比：打分而不是生成答案。
> - 生成式：给定图片，**写出**描述（需要生成所有词）
> - 能量模型：给定图片和一个描述，打分"这描述对不对"（只需要判断）
>
> 为什么更高效：打分比生成更容易，不需要对所有可能的输出归一化。

---

### 📖 术语表

━━━━━━━━━━━━━━━━━━━━━━

| 术语 | 一句话解释 |
|------|-----------|
| **JEPA（联合嵌入预测架构）** | LeCun 提出的世界模型框架：在表示（嵌入）空间中预测，而非在原始输入空间（像素/文本）生成 |
| **联合嵌入（Joint Embedding）** | 两个（或多个）模态/视角的数据被编码到**同一**嵌入空间，相关的配对距离近 |
| **预测架构（Predictive Architecture）** | 给定上下文（x），预测目标（y）的表示——注意是预测表示，不是预测原始输入 |
| **能量模型（EBM）** | 直接建模输入-输出对的相容性分数，而非条件概率；相容的配对能量低 |
| **Non-Contrastive 方法** | 不需要负样本的自监督学习：BYOL、SimSiam、I-JEPA 等，通过架构设计（而非负样本）防止表示坍塌 |
| **Masking（遮挡预测）** | 训练策略：遮住输入的一部分，预测被遮住部分的表示（类似 MAE） |
| **上下文编码器** | JEPA 的输入端：编码可见的部分（上下文）→ 生成上下文表示 |
| **目标编码器** | JEPA 的目标端：编码被遮住的部分（目标）→ 生成目标表示（使用动量更新的慢速编码器） |
| **预测器** | JEPA 的中间件：基于上下文表示，预测目标表示 |
| **表示坍塌（Representation Collapse）** | 自监督学习中的病态情况：所有输入映射到同一向量，损失为 0 但表示无意义 |

━━━━━━━━━━━━━━━━━━━━━━

---

### 🔬 Part 2：颗粒级原理拆解（32min）

#### 2.1 JEPA 框架的数学形式（8min）

**JEPA 的核心公式**：

```
设：
  x：输入（上下文，可见部分）
  y：目标（被遮住的部分）
  f_x：上下文编码器（Context Encoder）
  f_y：目标编码器（Target Encoder，动量更新）
  g：预测器（Predictor）

训练目标：
  最小化 ‖g(f_x(x)) - f_y(y)‖²

即：
  上下文表示 经过预测器 → 预测目标表示
  目标编码器 直接编码目标 → 真实目标表示
  两者的 L2 距离应最小化
```

**与生成式模型的对比**：

```
生成式（如 MAE、DALL-E）：
  minimize ‖Decoder(f_x(x)) - y‖²  （重建原始输入 y）
  在像素空间衡量误差

JEPA：
  minimize ‖g(f_x(x)) - f_y(y)‖²  （预测目标的表示）
  在表示空间衡量误差
```

**关键区别**：JEPA 的目标是表示（经过编码器处理），而非原始像素。

**防止坍塌的机制（I-JEPA 的做法）**：
- 目标编码器使用**指数移动平均（EMA/动量更新）**：参数 = α × 旧参数 + (1-α) × 上下文编码器参数
- 类似 BYOL/MoCo 的动量编码器——慢速更新使目标编码器更稳定，两个编码器的不对称性防止坍塌
- 没有负样本，也没有 Batch Normalization 技巧——靠架构设计保证不坍塌

#### 2.2 I-JEPA：图像的联合嵌入预测（8min）

**I-JEPA（Assran et al., CVPR 2023）**：

| 小节 | 核心内容 |
|------|---------|
| 输入处理 | 图像切割为 N 个 patch（如 14×14 = 196 个 patch，ViT-H/14）。随机遮住部分 patch（目标 patch），剩余的作为上下文 |
| 遮挡策略的特殊性 | 关键创新：目标区域是**语义连贯的块**（4 个大块，每块占图像 ~15% 面积），而非 MAE 的随机散点遮挡。大块遮挡强迫模型做**语义级别的预测**，而非低层纹理补全 |
| 编码器架构 | 上下文编码器：ViT（只处理可见 patch）；目标编码器：EMA 动量更新的 ViT |
| 预测器 | 小型 Transformer：输入上下文表示 + 目标位置编码（告诉预测器"预测哪里"），输出目标位置的表示预测 |
| 实验结果 | ImageNet linear probing：超越 MAE、SimCLR、DINO，接近监督 ViT；训练速度比 MAE 快 1.5× （不需要解码器）；在 low-shot 分类上优势更大 |
| 为什么不需要解码器就能学好表示 | MAE 的解码器在训练后被丢弃——它学的是"像素重建"而非"语义表示"；JEPA 直接在表示空间优化，训练目标和最终使用目标一致 |

#### 2.3 V-JEPA：视频的时序预测（7min）

**V-JEPA（Bardes et al., 2024）**：

| 小节 | 核心内容 |
|------|---------|
| 从图像到视频 | 核心思想不变，从空间遮挡扩展到时空遮挡：遮住视频中某些时间步的某些空间区域 |
| 时空 Tube 遮挡 | 遮挡的是"时空管道"（tubelet masking）：在时间轴上连续的多帧，相同空间区域被遮住。这强迫模型预测**时间动态**，而非逐帧的静态模式 |
| 架构 | 基于 ViT 的视频编码器（Space-Time Factorized Attention）；目标编码器同样是 EMA 更新 |
| 下游任务 | 视频动作识别（Kinetics 400/600/700）：不需要微调，zero-shot 就有竞争力；Motion 理解任务优于 MAE 类方法 |
| 关键发现 | 时序遮挡预测（V-JEPA）比帧内遮挡预测（帧帧做 I-JEPA）学到更好的运动表示——时序信息对运动理解至关重要 |
| 局限性 | 当前 V-JEPA 主要在视觉表示学习任务上评估；在强化学习/规划任务上的有效性尚未大规模验证 |

#### 2.4 与生成式路线的实证对比（5min）

| 对比维度 | JEPA（I-JEPA/V-JEPA） | MAE（生成式遮挡预测） | Diffusion（生成式） |
|---------|----------------------|--------------------|--------------------|
| 训练目标 | 预测目标的**表示** | 重建目标的**像素** | 预测加噪过程 |
| 表示学习质量（ImageNet linear） | ★★★★★（超越 MAE） | ★★★☆☆ | ★★★★☆ |
| 生成质量 | ✗（不能生成图像） | ★★★★☆（视觉逼真） | ★★★★★（SOTA） |
| 计算效率 | 高（无解码器） | 中（有解码器） | 低（多步采样） |
| 下游分类（少样本） | ★★★★★ | ★★★☆☆ | ★★★★☆ |
| 视频动作理解 | ★★★★★ | ★★★☆☆ | 不适用 |
| 规划/RL 中的使用 | 尚未充分验证 | 少量验证 | 部分验证（DIAMOND） |

> **结论**：对视觉表示学习来说，JEPA 当前确实优于同类生成式方法；但对于需要生成可视化输出的任务（图像生成、视频生成），JEPA 无法胜任。两者适合不同的下游任务，不是简单的"谁更好"。

#### 2.5 争议：支持者 vs 反对者（4min）

这是本课最重要的批判性思考环节。

**支持 JEPA 路线的论点**：

| 论点 | 支持者 | 论据 |
|------|--------|------|
| 更高效的表示学习 | Yann LeCun、Meta AI 团队 | I-JEPA 的 linear probing 超越 MAE，且不需要解码器 |
| 更接近认知科学 | Karl Friston 的预测编码理论 | 大脑做预测，不做生成；JEPA 更符合神经科学证据 |
| 避免"世界的无聊维度" | LeCun（2022 路线图） | 生成背景纹理、光照细节不增加智能，是计算浪费 |
| 可以做不确定性建模 | EBM 理论支持者 | JEPA 可以对"哪些未来可能发生"保持多峰不确定性，而生成式倾向于均值 |

**质疑 JEPA 路线的论点**：

| 论点 | 质疑者 | 理由 |
|------|--------|------|
| 还没在规划/RL 中大规模验证 | Model-Free RL 研究者 | 好的表示只是必要条件，不是充分条件；DreamerV3 的成功是端到端的 |
| 无法生成意味着无法验证理解 | 生成式 AI 支持者 | 生成质量可以直接检验，预测表示质量难以直接评估"真正理解了什么" |
| 在 NLP/多模态中尚无类似成功 | LLM 研究者 | JEPA 目前主要在视觉领域验证，文本、语音、多模态的 JEPA 版本尚未成熟 |
| "坍塌问题"仍是隐患 | 理论研究者 | 动量编码器防坍塌在小规模有效，大规模是否稳定还有争议 |

> 💡 **面试建议**：呈现两方论点，展示自己理解了这个争议的深度，比单方面支持或反对更有说服力。

#### 2.6 🔥 面试考点（0min，嵌入上文）

| 考点 | 标注 | 简要答法 |
|------|------|---------|
| JEPA 和生成式模型的根本区别 | 🔥 面试高频 | 预测目标的表示（特征空间）vs 重建目标的原始输入（像素空间）；目标是语义而非像素 |
| 为什么 I-JEPA 的遮挡策略重要 | ⭐ 加分 | 大块语义区域遮挡 → 强迫语义级预测；散点遮挡 → 可以用纹理补全作弊 |
| EMA 动量目标编码器的作用 | ⚠️ 易错 | 防止表示坍塌：不对称的编码器参数更新使两个编码器有差异，防止模型学到"所有输出都一样"的平凡解 |
| LeCun 为什么反对生成式 AI | 🔥 面试高频 | 生成无关细节浪费计算；不适合多峰预测（平均会导致模糊）；训练不稳定（GAN/Diffusion 的通病） |
| JEPA 的当前局限性 | ⚠️ 易错 | 不能生成可视化输出；在 RL/规划中未充分验证；主要在视觉任务上有竞争力；NLP 的 JEPA 版本不成熟 |

---

### 💻 Part 3：代码实践（10min）

#### 3.1 JEPA 核心思想：预测表示（简化 Demo）

```python
"""
JEPA 核心思想的简化实现
功能：展示"预测表示"而非"重建像素"的区别
依赖：torch >= 2.0（pip install torch）
注意：这是教学级 Demo，真实 I-JEPA 使用 ViT-H，规模大得多
预计运行时间：< 30 秒（CPU）
"""
import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim


# ── 编码器：把输入映射到表示空间 ──────────────────────
class Encoder(nn.Module):
    def __init__(self, input_dim: int = 4, repr_dim: int = 16):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(input_dim, 32), nn.GELU(),
            nn.Linear(32, repr_dim),
        )

    def forward(self, x):
        return self.net(x)


# ── 预测器：从上下文表示预测目标表示 ──────────────────
class Predictor(nn.Module):
    """
    JEPA 的核心组件：给定上下文的表示，预测目标位置的表示
    不直接预测像素，而是预测表示
    """
    def __init__(self, repr_dim: int = 16):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(repr_dim, 32), nn.GELU(),
            nn.Linear(32, repr_dim),  # 输出同维度的表示预测
        )

    def forward(self, context_repr):
        return self.net(context_repr)


class JEPADemo:
    """
    JEPA vs 生成式（AE）对比演示
    数据：CartPole 观测（4维）
    任务：遮住后 2 维（如速度），预测/重建

    JEPA：预测被遮住部分的**表示**
    AE：重建被遮住部分的**原始值**（像素级）
    """

    def __init__(self):
        # JEPA 组件
        self.context_encoder = Encoder(input_dim=2, repr_dim=16)   # 编码可见部分
        self.target_encoder = Encoder(input_dim=2, repr_dim=16)    # 编码目标部分（EMA 更新）
        self.predictor = Predictor(repr_dim=16)

        # 生成式对照组（自编码器）
        self.ae_encoder = Encoder(input_dim=2, repr_dim=16)
        self.ae_decoder = nn.Sequential(
            nn.Linear(16, 32), nn.GELU(),
            nn.Linear(32, 2),  # 重建原始像素
        )

        self.jepa_optimizer = optim.Adam(
            list(self.context_encoder.parameters()) +
            list(self.predictor.parameters()), lr=1e-3
        )
        self.ae_optimizer = optim.Adam(
            list(self.ae_encoder.parameters()) +
            list(self.ae_decoder.parameters()), lr=1e-3
        )
        self.ema_momentum = 0.99

    def update_target_encoder(self):
        """EMA 动量更新目标编码器（防止表示坍塌）"""
        with torch.no_grad():
            for param_q, param_k in zip(
                self.context_encoder.parameters(),
                self.target_encoder.parameters()
            ):
                # 目标编码器 = α × 旧参数 + (1-α) × 上下文编码器参数
                param_k.data = (
                    self.ema_momentum * param_k.data +
                    (1 - self.ema_momentum) * param_q.data
                )

    def train_step(self, data):
        """
        一个训练步骤
        data：[N, 4] 的观测（CartPole）
        上下文：前 2 维（位置）；目标：后 2 维（速度）
        """
        context = data[:, :2]  # 可见：位置
        target = data[:, 2:]   # 目标：速度（被遮住的部分）

        # ── JEPA：在表示空间中预测 ────────────────────
        self.jepa_optimizer.zero_grad()
        context_repr = self.context_encoder(context)
        pred_repr = self.predictor(context_repr)
        with torch.no_grad():  # 目标编码器不需要梯度
            target_repr = self.target_encoder(target)
        jepa_loss = F.mse_loss(pred_repr, target_repr)
        jepa_loss.backward()
        self.jepa_optimizer.step()
        self.update_target_encoder()  # EMA 更新目标编码器

        # ── 生成式 AE：在像素空间中重建 ───────────────
        self.ae_optimizer.zero_grad()
        context_latent = self.ae_encoder(context)
        recon = self.ae_decoder(context_latent)
        ae_loss = F.mse_loss(recon, target)  # 直接对比原始像素
        ae_loss.backward()
        self.ae_optimizer.step()

        return jepa_loss.item(), ae_loss.item()


def run_jepa_demo():
    import gymnasium as gym

    env = gym.make("CartPole-v1")
    obs_list = []
    obs, _ = env.reset()
    for _ in range(1000):
        obs_list.append(obs)
        obs, _, done, truncated, _ = env.step(env.action_space.sample())
        if done or truncated:
            obs, _ = env.reset()
    env.close()

    data = torch.FloatTensor(obs_list)
    data = (data - data.mean(0)) / (data.std(0) + 1e-8)  # 归一化

    demo = JEPADemo()
    jepa_losses, ae_losses = [], []

    print("对比训练：JEPA（表示预测）vs AE（像素重建）\n")
    for epoch in range(100):
        jl, al = demo.train_step(data)
        jepa_losses.append(jl)
        ae_losses.append(al)
        if (epoch + 1) % 20 == 0:
            print(f"Epoch {epoch+1}: JEPA 损失={jl:.4f} | AE 重建损失={al:.4f}")

    print("\n关键观察：")
    print("  JEPA 损失度量的是：表示空间中的预测误差")
    print("  AE  损失度量的是：像素空间中的重建误差")
    print("  两者优化的目标不同，表示的语义质量也不同")
    print("\n  JEPA 的表示更适合下游任务（分类、RL），")
    print("  AE  的表示更适合需要生成/可视化的任务。")


if __name__ == "__main__":
    run_jepa_demo()
```

---

### 🎓 Part 4：产品视角（6min）

#### 4.1 JEPA 的实际应用现状（2025-2026）

| 场景 | 状态 | 说明 |
|------|------|------|
| 视觉表示预训练 | ✅ 生产可用 | I-JEPA/V-JEPA 作为视觉骨干网络，替代 DINO/MAE |
| 视频理解（动作识别） | ✅ 有竞争力 | V-JEPA 在 Kinetics 上超越同类自监督方法 |
| 强化学习/世界模型 | ⚠️ 早期探索 | MC-JEPA（运动控制 JEPA）是初步尝试，尚未成为主流 |
| NLP 中的 JEPA | ❌ 尚未突破 | 文本的 JEPA 变体（预测文本表示）还没有击败自回归 LLM |
| 多模态 JEPA | ⚠️ 研究阶段 | 图像-语言联合嵌入预测，Meta 内部有研究 |

#### 4.2 对算法工程师的建议

**什么时候选 JEPA（I-JEPA/V-JEPA）**：
- 需要视觉表示预训练，下游任务是分类/检测
- 计算资源有限，不想训练解码器
- 需要在少量标注数据上有好的 few-shot 性能

**什么时候不选 JEPA**：
- 需要生成图像/视频（JEPA 无法做）
- 需要在 RL 规划中使用（MBRL 路线更成熟）
- 任务在 NLP 领域（自回归 LLM 更成熟）

#### 4.3 产业界的态度

- **Meta AI**：LeCun 带队，持续投资 JEPA 路线，有明确的研究路线图
- **OpenAI**：没有公开支持 JEPA，继续投资生成式路线
- **DeepMind/Google**：两条路线都有布局（V-JEPA 类方法 + 生成式世界模型 Genie）
- **学术界**：JEPA 在 CV 顶会上有越来越多的后续工作，但与生成式方法的对比尚无定论

---

### 📝 Part 5：作业与测试（5min）

#### 5.1 基础题

1. JEPA 和生成式模型（如 MAE）在训练目标上有什么根本区别？预测"表示"而非"像素"有什么优势？

2. I-JEPA 使用"语义连贯的大块遮挡"而非"随机散点遮挡"，这个设计选择的理由是什么？

3. JEPA 使用 EMA 动量目标编码器来防止表示坍塌。请解释：① 什么是表示坍塌？② EMA 更新为什么能防止它？

#### 5.2 🔥 面试真题

**真题 1**

> 请解释 JEPA 框架的核心思想，并说明它与 BERT（Masked Language Model）的异同。

**参考答案要点**：
- 相同：都是遮挡预测的自监督学习范式
- BERT：预测被遮住 token 的**原始值**（词汇 ID）→ 等价于生成式
- JEPA：预测被遮住部分的**表示**（编码器输出）→ 在表示空间中优化
- 关键差异：JEPA 的预测目标是抽象表示，BERT 的预测目标是原始 token
- LeCun 的观点：BERT 是"生成式"在文本上的特例，JEPA 才是真正预测语义

**真题 2**

> LeCun 声称生成式 AI 无法通向 AGI，JEPA 是更正确的方向。你如何评价这个论断？

**参考答案要点**（高分需要呈现两方）：
- LeCun 的论点有实验支持（I-JEPA 表示质量超 MAE）
- 但 JEPA 在规划/RL 中尚未验证，而 DreamerV3（生成式路线）已经在大量任务上 SOTA
- NLP 中 JEPA 没有击败自回归 LLM，说明问题可能是领域特定的
- 结论建议：JEPA 在视觉表示学习上有优势，但"通向 AGI"的论断超出了当前实验证据的范围

#### 5.3 开放思考题

> 如果你要把 JEPA 的思想应用到机器人规划中（用世界模型预测未来状态的表示，而不生成像素），你会如何设计这个系统？面临的主要挑战是什么？与 DreamerV3 的 RSSM 路线相比，你的设计有什么优势和劣势？

---

## 六、引用说明

| 标签 | 来源 | 说明 |
|------|------|------|
| [LeCun, 2022] | A Path Towards Autonomous Machine Intelligence. OpenReview 2022 | JEPA 框架的完整提出，包含 EBM 基础和 LLM 批评 |
| [Assran et al., 2023] | Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture (I-JEPA). CVPR 2023 | I-JEPA 原论文 |
| [Bardes et al., 2024] | V-JEPA: Latent Video Prediction for Visual Representation Learning. arXiv 2024 | V-JEPA 原论文 |
| [He et al., 2022] | Masked Autoencoders Are Scalable Vision Learners (MAE). CVPR 2022 | 对比基线：生成式遮挡预测 |
| [Grill et al., 2020] | Bootstrap Your Own Latent (BYOL). NeurIPS 2020 | Non-contrastive 方法的奠基工作，EMA 动量编码器的来源 |
| [Friston, 2005] | A theory of cortical responses. Phil. Trans. R. Soc. B | 预测编码理论，JEPA 的认知科学基础 |

---

*文档版本：v1.0 | 创建：2026-05-25 | 世界模型系列课程（方案 A）第 4 课*
