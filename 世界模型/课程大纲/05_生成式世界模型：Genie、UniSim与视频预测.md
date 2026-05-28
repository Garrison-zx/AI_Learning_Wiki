---
课程: WM-05 - 生成式世界模型：Genie、UniSim与视频预测
系列: 世界模型系列课程（方案 A）
版本: v1.0
创建日期: 2026-05-25
最后更新: 2026-05-25
类型: C 架构型
时长: 70min
前置课程: WM-01（导论）、WM-02（潜空间表示）、WM-03（动力学建模）
适用受众: 算法工程师、AI研究者、AI产品经理
下次复审: 2026-08-25
更新触发条件:
  - Genie 2 或 Genie 相关重大后续版本发布
  - Sora 开放 API 或发布技术报告
  - 生成式世界模型在具身智能/机器人中取得重大突破
---

# WM-05：生成式世界模型：Genie、UniSim与视频预测

> 💥 **本课 Wow Moment**：2024 年，Google DeepMind 发布了 Genie——你上传一张游戏截图，AI 不仅能生成这个场景，还能让你**用键盘控制角色在其中行走**。这个世界会对你的按键做出响应：左键走左，右键走右，跳跃键跳起。
>
> AI 从一张图片，造了一个可以玩的平行世界。
>
> 更令人震惊的是：Genie 没有在任何特定游戏上受过训练——它只看了大量互联网上的视频，就学会了"世界如何响应动作"的通用规律。

---

## 一、逻辑主线

```
视频预测：从预测下一帧到生成可交互的世界
    ↓
Genie（Google DeepMind，2024）：
  从视频中无监督学习交互码——生成可交互的世界模型
    ↓
UniSim（Stanford/Google，2023）：
  模拟真实世界的高保真视频生成世界模型
    ↓
DIAMOND（2024）：
  Diffusion 模型用于动力学预测——在扩散世界模型中训练 RL 智能体
    ↓
核心争议：Sora 是世界模型吗？
  支持者论点 vs 反对者论点 vs OpenAI 的官方立场
    ↓
生成式 vs MBRL vs JEPA 的最终对比
```

---

## 二、课程结构总览

```
WM-05：生成式世界模型（70min）
│
├── 🎯 Part 0  导入：AI 造了一个平行世界    (5min)
│
├── 🌍 Part 1  直觉导入                     (8min)
│   ├── 1.1 视频预测的演进：逐帧 → 生成 → 可交互
│   ├── 1.2 "电影 vs 游戏"类比
│   └── 1.3 生成式世界模型的三个关键能力
│
├── 📖 术语表                                (3min)
│
├── 🔬 Part 2  颗粒级原理拆解                (30min)
│   ├── 2.1 Genie：可交互世界生成的架构
│   ├── 2.2 UniSim：高保真物理仿真世界模型
│   ├── 2.3 DIAMOND：扩散世界模型 + RL
│   ├── 2.4 Sora 是世界模型吗？（争议）
│   └── 2.5 🔥 面试考点
│
├── 💻 Part 3  代码实践                      (10min)
│   └── 3.1 用 Diffusion 做简单视频帧预测（概念演示）
│
├── 🎓 Part 4  产品视角                      (8min)
│
└── 📝 Part 5  作业与测试                    (6min)
```

---

## 三、章节大纲

### 🎯 Part 0：导入——AI 造了一个平行世界（5min）

**时间线：生成式世界模型的演进**

```
2018  Ha & Schmidhuber：VAE+RNN 世界模型（低分辨率潜空间）
2022  DALL-E 2 / Stable Diffusion：静态图像生成爆发
2023  Sora 前驱：VideoGPT、CogVideo（视频生成，但不可控）
      UniSim：高保真物理世界仿真
2024  Genie：可交互世界生成（给一张图，造一个可玩的世界）
      DIAMOND：扩散模型 + RL（在扩散世界模型中训练智能体）
      Sora（OpenAI）：高质量视频生成（"世界模型"争议爆发）
2025  Genie 2（DeepMind）：更高保真度、更长时序、更多交互控制
      生成式世界模型进入具身智能预训练流水线
```

**核心问题**：生成式世界模型能做什么？
1. **数据增强**：为 RL/机器人生成训练数据（无需真实环境）
2. **交互仿真**：生成可以交互的虚拟世界（游戏、训练场景）
3. **规划辅助**：可视化展示动作的预期后果
4. **创意生成**：从单张图片生成游戏世界（Genie 的核心用途）

---

### 🌍 Part 1：直觉导入（8min）

#### 1.1 视频预测的演进

| 阶段 | 时间 | 能力 | 局限 |
|------|------|------|------|
| 帧预测 | 2016-2019 | 预测下一帧（SVG、VRNN） | 只能预测 1-5 帧，图像模糊 |
| 视频生成 | 2020-2022 | 生成连贯视频（VideoGPT、VGAN） | 固定场景，不可交互，不响应动作 |
| 条件视频生成 | 2022-2023 | 给定文字/图像条件生成视频 | 仍然不响应用户的实时控制 |
| 可交互世界模型 | 2024+ | 用户可以控制角色，世界实时响应 | Genie 的突破 |

#### 1.2 "电影 vs 游戏"类比

> **电影**（传统视频生成）：你只是观看者。AI 生成一段视频，你无法改变情节。就像看录制好的影片。
>
> **游戏**（可交互世界模型）：你是参与者。你按左键，角色往左走；你跳跃，AI 需要"知道"跳跃的后果，并生成相应的帧。
>
> **Genie 的革命**：从"电影"变成了"游戏"——AI 不再只是生成固定序列，而是在实时响应玩家输入的同时保持世界的一致性。

#### 1.3 生成式世界模型的三个关键能力

1. **一致性（Consistency）**：生成的世界在时间上应该一致——不能刚才还站在左边的柱子消失了
2. **可控性（Controllability）**：世界应该响应给定的动作——按右键，角色应该往右走
3. **泛化性（Generalizability）**：不只能生成训练数据中见过的场景，还能根据新输入生成全新的世界

---

### 📖 术语表

━━━━━━━━━━━━━━━━━━━━━━

| 术语 | 一句话解释 |
|------|-----------|
| **Tokenizer（视觉 tokenizer）** | 把视频帧（图像）转化为离散 token 序列（通常用 VQGAN），供 Transformer 在 token 上建模 |
| **LAM（潜动作模型）** | Genie 的核心组件：从视频中**无监督**地推断出潜在动作码，无需动作标注 |
| **STP（时空 Transformer）** | Genie 的动力学建模组件：在 token 序列上做时空自注意力，预测下一帧的 token |
| **World Simulator** | UniSim 的自称：建模真实物理世界，支持各种语言动作（如"推动桌子上的杯子"） |
| **Diffusion 世界模型** | 用扩散模型（Denoising Diffusion）做帧预测的世界模型，如 DIAMOND |
| **DIAMOND** | Diffusion as a world Model for ANd Decision-making；在扩散世界模型内部训练 RL 智能体 |
| **视频预测（Video Prediction）** | 给定前几帧，预测后续帧；生成式世界模型的基础任务 |
| **交互码（Latent Action）** | Genie 中从视频中提取的隐式动作表示，不需要真实动作标注 |
| **autoregressive video generation** | 自回归视频生成：逐帧（或 token by token）生成视频，每帧依赖前几帧 |

━━━━━━━━━━━━━━━━━━━━━━

---

### 🔬 Part 2：颗粒级原理拆解（30min）

#### 2.1 Genie：可交互世界生成的架构（10min）

**Genie（Bruce et al., ICML 2024）**——从互联网视频中无监督学习交互式世界模型

**核心挑战**：互联网上的游戏视频没有动作标注。怎么让 AI 学会"哪个按键导致了哪个变化"？

**Genie 的三组件架构**：

```
输入视频序列：[f_1, f_2, ..., f_T]（互联网游戏视频，无动作标注）

① 视觉 Tokenizer（VQGAN 类型）
   每帧 f_t → 离散 token 网格（如 16×16 个 token）

② LAM（潜动作模型）
   核心创新：从 (f_t, f_{t+1}) 的帧间差异中，
   无监督地推断出"潜在动作"a_t（一个离散码）
   方法：对比当前帧和下一帧的 token 变化，聚类为 K 个动作类别
   结果：即使没有按键标注，模型学会了"这类变化 = 向右移动"

③ STP（时空 Transformer 动力学模型）
   条件生成：给定 [f_1, ..., f_{t-1}] 和潜在动作 a_t，预测 f_t 的 token
   架构：因果 Transformer，处理时空 token 序列
```

**Genie 的关键实验结果**：
- 训练数据：约 200K 小时互联网游戏视频（2D 平台游戏为主）
- 能力：给定单张图片，生成可以用方向键控制的游戏世界
- 泛化：能处理从未见过的游戏视觉风格（素描、照片 → 生成对应风格的游戏）
- 局限：分辨率有限（64×64），长时序一致性较差，暂时主要在 2D 平台游戏风格上有效

**意义**：证明了可以从无标注视频中学到可控的世界模型——这可能是训练具身智能机器人的关键前置技术（机器人视频 → 无监督学习 → 机器人世界模型）

#### 2.2 UniSim：高保真物理仿真世界模型（7min）

**UniSim（Yang et al., 2023）**——"通用模拟器"

**核心定位**：不只是生成视频，而是模拟真实物理世界——机器人、人类、物体的交互。

**架构要点**：

| 组件 | 设计 | 作用 |
|------|------|------|
| 基础生成器 | 基于视频扩散模型（Video Diffusion）的大规模预训练 | 生成高保真视频帧 |
| 动作条件化 | 接收高层语言动作（"拿起桌上的杯子"）或低层控制（关节角度） | 把意图转化为视频预测 |
| 多层次控制 | 同时支持机器人控制指令和人类自然语言指令 | 泛化到不同类型的交互 |
| 物理一致性 | 通过大规模真实视频训练，隐式学习物理规律 | 生成物理上合理的结果 |

**UniSim 的核心贡献**：
- 展示了用生成式世界模型为机器人**生成训练数据**的可行性
- 在 UniSim 生成的数据上训练的策略，可以零样本迁移到真实环境
- 局限：需要大量真实视频预训练；推理速度慢（扩散模型的通病）；在精细物理操控上仍有误差

#### 2.3 DIAMOND：扩散世界模型 + RL（6min）

**DIAMOND（Alonso et al., 2024）**——在扩散世界模型内部训练 RL 智能体

**核心问题**：DreamerV3 在潜空间中规划（快，但不可视化）；能不能在像素空间的扩散模型中直接做 RL？

**DIAMOND 的设计**：

| 方面 | DIAMOND | DreamerV3 |
|------|---------|-----------|
| 动力学模型 | 去噪扩散网络（DDPM 类型） | RSSM（循环状态空间模型） |
| 规划空间 | 像素/帧空间 | 离散潜空间 |
| 可视化 | 直接生成可视化帧，人类可审查 | 潜向量不可直接可视化 |
| 计算效率 | 低（扩散多步推断） | 高（单次前向传播） |
| 生成质量 | 高（扩散模型的优势） | 低（不生成像素） |
| Atari 表现 | 超越 DreamerV2，接近 DreamerV3 | SOTA |

**关键发现**：扩散模型可以作为有效的动力学模型——不只用于生成，还可以用于 RL 训练。这打通了"图像生成"和"强化学习"两个领域。

#### 2.4 Sora 是世界模型吗？（争议）（7min）

这是 2024 年 AI 界最热门的争议之一。

**OpenAI 的官方立场**（2024 年 2 月 Sora 发布时）：

> "Sora 是一个世界模型。通过视频预测，Sora 学会了物理世界的动态规律，具备了对世界的理解。"

**为什么有人同意**：

| 论点 | 依据 |
|------|------|
| 物理一致性 | Sora 生成的视频中，物体运动符合基本物理规律（重力、碰撞） |
| 场景稳定性 | 长时序视频中，物体不会随机消失或变形 |
| 动作响应 | 可以根据文字指令生成相应的场景变化 |
| 大规模视频训练 | 在大量真实世界视频上预训练，隐式学习了世界规律 |

**为什么有人反对**：

| 论点 | 依据 |
|------|------|
| 没有状态表示 | Sora 没有显式的"世界状态"变量，只有像素级的条件生成 |
| 物理错误 | Sora 生成的视频中仍有物理不一致（人手的手指、液体流动） |
| 不可控 | 无法精确控制"在第 3 秒时角色的位置"，只能靠文字描述 |
| 不能规划 | Sora 无法用于规划——它生成视频，但不能在视频"想象"中优化决策 |
| LeCun 的反驳 | "Sora 只是在做时空插值，不理解物体持续性和因果关系" |

**比较中立的观点**：

> Sora 展示了视频生成模型可以隐式学习世界的视觉规律，这是真实的。但"世界模型"通常指可以用于规划、支持反事实推理、有显式状态表示的系统。Sora 做到了部分特征，但离完整的世界模型还有距离。
>
> 更准确的描述可能是：Sora 是"具有世界模型特性的视频生成模型"，而非严格意义上的世界模型。

**面试建议**：Sora 是世界模型这个问题没有标准答案，展示你对争议的理解、能说出具体论据，比简单说"是"或"否"更重要。

#### 2.5 🔥 面试考点

| 考点 | 标注 | 简要答法 |
|------|------|---------|
| Genie 如何在无动作标注的情况下学习可控世界模型 | 🔥 面试高频 | LAM（潜动作模型）：从帧间差异中无监督推断潜在动作码，然后把这个码作为动力学 Transformer 的条件 |
| 生成式世界模型 vs MBRL 世界模型的核心权衡 | 🔥 面试高频 | 生成式：可视化，可解释，适合数据增强；MBRL：计算效率高，适合在线规划。前者看图，后者做决策 |
| DIAMOND 和 DreamerV3 的主要区别 | ⭐ 加分 | 动力学模型：扩散网络 vs RSSM；规划空间：像素 vs 潜空间；可视化：直接可视 vs 抽象潜向量 |
| Sora 是否是世界模型 | 🔥 面试高频 | 无标准答案；关键是说出争议的两面：隐式学到物理规律（支持），但缺少显式状态/可规划性/精确控制（反对） |
| UniSim 的主要用途 | ⭐ 加分 | 为机器人生成训练数据；在生成数据上训练的策略可以零样本迁移到真实环境 |

---

### 💻 Part 3：代码实践（10min）

#### 3.1 视频预测的概念演示：帧差预测

```python
"""
生成式世界模型的概念演示
功能：① 简单帧差预测（MLP）；② 与逐像素对比，理解"预测表示 vs 预测像素"
依赖：torch >= 2.0, gymnasium, matplotlib
注意：这是概念演示，不是真实视频扩散模型
     真实 DIAMOND/Genie 需要 Diffusion Transformer，规模大数十倍

预计运行时间：< 2 分钟（CPU）
"""
import torch
import torch.nn as nn
import torch.optim as optim
import gymnasium as gym
import numpy as np
import matplotlib.pyplot as plt


class SimpleVideoPredictor(nn.Module):
    """
    简单帧预测器：给定前 K 帧，预测下一帧
    真实系统（DIAMOND）中，这里换成去噪扩散网络（U-Net + 多步去噪）
    """
    def __init__(self, obs_dim: int = 4, context_frames: int = 3):
        super().__init__()
        # 连接 context_frames 帧作为输入
        self.net = nn.Sequential(
            nn.Linear(obs_dim * context_frames, 128), nn.ELU(),
            nn.Linear(128, 64), nn.ELU(),
            nn.Linear(64, obs_dim),  # 预测下一帧
        )

    def forward(self, context: torch.Tensor):
        # context: [batch, context_frames, obs_dim]
        return self.net(context.flatten(1))


class ActionConditionedPredictor(nn.Module):
    """
    动作条件帧预测器：给定前 K 帧 + 当前动作，预测下一帧
    这是 Genie/UniSim 的核心：世界模型响应动作
    """
    def __init__(self, obs_dim: int = 4, action_dim: int = 2, context_frames: int = 3):
        super().__init__()
        input_dim = obs_dim * context_frames + action_dim
        self.net = nn.Sequential(
            nn.Linear(input_dim, 128), nn.ELU(),
            nn.Linear(128, 64), nn.ELU(),
            nn.Linear(64, obs_dim),
        )

    def forward(self, context: torch.Tensor, action: torch.Tensor):
        x = torch.cat([context.flatten(1), action], dim=-1)
        return self.net(x)


def collect_video_data(n_steps: int = 2000, context_len: int = 3):
    """收集 (帧序列, 动作, 下一帧) 数据"""
    env = gym.make("CartPole-v1")
    frames, actions, next_frames = [], [], []
    buffer = []
    obs, _ = env.reset()

    for _ in range(n_steps):
        buffer.append(obs.copy())
        action = env.action_space.sample()
        next_obs, _, done, truncated, _ = env.step(action)

        if len(buffer) >= context_len:
            context = np.array(buffer[-context_len:])
            frames.append(context)
            actions.append(action)
            next_frames.append(next_obs.copy())

        obs = next_obs
        if done or truncated:
            obs, _ = env.reset()
            buffer = []

    env.close()
    return (
        torch.FloatTensor(frames),
        torch.LongTensor(actions),
        torch.FloatTensor(next_frames),
    )


def compare_predictors():
    print("收集数据...")
    frames, actions, next_frames = collect_video_data(n_steps=2000)
    # 归一化
    m, s = frames.mean(), frames.std()
    frames = (frames - m) / (s + 1e-8)
    next_frames = (next_frames - m) / (s + 1e-8)
    action_onehot = torch.zeros(len(actions), 2)
    action_onehot.scatter_(1, actions.unsqueeze(1), 1.0)

    # 模型
    unconditional = SimpleVideoPredictor()
    conditional = ActionConditionedPredictor()
    opt_u = optim.Adam(unconditional.parameters(), lr=1e-3)
    opt_c = optim.Adam(conditional.parameters(), lr=1e-3)
    loss_fn = nn.MSELoss()

    print("训练对比：无动作条件 vs 有动作条件\n")
    for epoch in range(150):
        # 无动作条件
        pred_u = unconditional(frames)
        loss_u = loss_fn(pred_u, next_frames)
        opt_u.zero_grad(); loss_u.backward(); opt_u.step()

        # 有动作条件
        pred_c = conditional(frames, action_onehot)
        loss_c = loss_fn(pred_c, next_frames)
        opt_c.zero_grad(); loss_c.backward(); opt_c.step()

        if (epoch + 1) % 30 == 0:
            print(f"Epoch {epoch+1}: 无条件={loss_u.item():.4f} | 有动作条件={loss_c.item():.4f}")

    print("\n关键观察：")
    print("  有动作条件的预测器损失更低——因为动作提供了额外信息")
    print("  这正是 Genie/UniSim 的核心：世界模型必须响应动作")
    print("  真实系统：把 MLP 换成扩散 Transformer，把 CartPole 换成视频帧")


if __name__ == "__main__":
    compare_predictors()
```

---

### 🎓 Part 4：产品视角（8min）

#### 4.1 生成式世界模型的产品应用地图

| 应用场景 | 核心需求 | 适用系统 | 成熟度 |
|---------|---------|---------|--------|
| 游戏内容生成 | 从单张图生成可玩关卡 | Genie 及后续 | 研究→早期产品 |
| 机器人训练数据生成 | 模拟真实操控场景 | UniSim | 研究阶段 |
| 自动驾驶仿真 | 生成罕见/危险场景 | 扩散 + 驾驶先验 | 产业研究中 |
| 影视辅助制作 | AI 场景变化预览 | Sora 类 | 早期产品 |
| 教育/培训仿真 | 生成交互式学习场景 | 可交互世界模型 | 探索阶段 |
| 虚拟试衣/试装修 | 生成用户可控的效果 | 条件视频生成 | 有商业产品 |

#### 4.2 三大技术路线的最终对比（本系列课程的总结）

| 维度 | MBRL（DreamerV3） | 生成式（Genie/DIAMOND） | JEPA（V-JEPA） |
|------|-----------------|----------------------|---------------|
| 主要用途 | RL 策略学习（样本效率） | 视频生成/数据增强/交互世界 | 视觉表示预训练 |
| 可视化 | 无（潜空间） | 有（生成像素帧） | 无（表示向量） |
| 计算效率 | 高 | 低（扩散多步） | 高（无解码器） |
| 规划能力 | 强（RSSM + actor-critic） | 弱（主要用于生成） | 待验证 |
| 物理保真度 | 隐式（通过重建损失） | 显式（像素级） | 语义层面 |
| 在产业落地 | 游戏 AI、机器人（研究） | 自动驾驶、游戏生成（研究） | 视觉主干网络（已用） |

#### 4.3 未来展望（2025-2026 前沿）

- **Genie 2（2024 Q4）**：更高分辨率（128×128+）、3D 场景支持、更强的物理一致性
- **世界模型用于机器人预训练**：用互联网视频训练生成式世界模型，再迁移到机器人控制——可能是下一代机器人学习的核心范式
- **生成式 + MBRL 融合**：DIAMOND 的思路——扩散模型提供可视化，MBRL 提供规划能力，两者结合
- **实时交互世界模型**：当前 Genie 的推理速度不够实时（每帧 >1 秒），实时化是关键工程挑战

---

### 📝 Part 5：作业与测试（6min）

#### 5.1 基础题

1. Genie 如何从无动作标注的互联网视频中学习可控的世界模型？LAM（潜动作模型）的核心思想是什么？

2. UniSim 被称为"世界仿真器"，它与传统物理仿真软件（如 MuJoCo）有什么根本区别？各有什么适用场景？

3. DIAMOND 把扩散模型用于动力学建模，与 DreamerV3 的 RSSM 相比，在可视化和计算效率上各有什么优缺点？

#### 5.2 🔥 面试真题

**真题 1**

> 请从技术角度分析：Sora 是否是"世界模型"？用具体的特征来支持你的论点。

**参考答案框架**：
- 支持的特征：隐式物理规律学习、长时序视频一致性、多场景泛化
- 反对的特征：无显式状态表示、无法用于规划、精确控制不足、仍有物理错误
- 结论建议：Sora 具有部分世界模型特性（视觉预测），但缺少完整世界模型的核心要素（可规划性、状态分解）；"是否是世界模型"取决于定义

**真题 2**

> 生成式世界模型（如 Genie）和 MBRL 世界模型（如 DreamerV3）各适合哪些应用场景？它们能结合吗？

**参考答案要点**：
- Genie 适合：视觉内容生成、数据增强、交互式场景创建、人类可审查的仿真
- DreamerV3 适合：高效 RL 训练、需要多步规划、样本效率是瓶颈的控制任务
- 结合的可能性：DIAMOND 路线（扩散动力学 + RL）已证明可行；也可以 DreamerV3 在线规划，Genie 类系统生成可视化展示
- 关键权衡：像素级生成的计算成本 vs 潜空间规划的效率

#### 5.3 开放思考题

> 如果你要为游戏公司设计一个"AI 关卡生成器"：用户上传一张游戏截图，AI 生成一个可以在其中玩耍的完整关卡（包括碰撞、重力、敌人 AI 等）。你会选择哪种技术路线（Genie 风格 / DIAMOND 风格 / MBRL 风格）？请描述系统架构和主要技术挑战。

---

## 六、引用说明

| 标签 | 来源 | 说明 |
|------|------|------|
| [Bruce et al., 2024] | Genie: Generative Interactive Environments. ICML 2024 | Genie 原论文 |
| [Yang et al., 2023] | UniSim: Learning Interactive Real-World Simulators. ICLR 2024 | UniSim 原论文 |
| [Alonso et al., 2024] | Diffusion for World Modeling: Visual Details Matter in Atari (DIAMOND). NeurIPS 2024 | DIAMOND 原论文 |
| [Brooks et al., 2024] | Video generation models as world simulators. OpenAI Technical Blog, 2024 | Sora 的技术博客（无正式论文） |
| [Yan et al., 2021] | VideoGPT: Video Generation using VQ-VAE and Transformers. arXiv 2021 | 早期视频 GPT 方法 |
| [Ho et al., 2022] | Video Diffusion Models. NeurIPS 2022 | 视频扩散模型的奠基工作 |

---

*文档版本：v1.0 | 创建：2026-05-25 | 世界模型系列课程（方案 A）第 5 课*
