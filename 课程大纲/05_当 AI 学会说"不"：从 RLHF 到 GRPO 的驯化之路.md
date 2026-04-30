---
课程: 05 - 当 AI 学会说"不"：从 RLHF 到 GRPO 的驯化之路
版本: v1.0
创建日期: 2026-04-29
最后更新: 2026-04-29
类型: A 原理型
时长: 70min
前置课程: 课程 1-4（Transformer / 三大流派 / GPT 编年史 / 大力出奇迹）
适用模型版本: InstructGPT / Claude / LLaMA 3 / DeepSeek-R1
下次复审: 2026-07-29
更新触发条件:
  - RLHF/DPO 出现重大改进方法
  - DeepSeek / Anthropic 发布新对齐技术报告
  - 对齐领域出现重大安全突破或事故
---

# 课程 5：当 AI 学会说"不"：从 RLHF 到 GRPO 的驯化之路

> 💥 **本课 Wow Moment**：DeepSeek-R1（2025）用一个不需要人类标注、不需要奖励模型的强化学习算法（GRPO），把一个普通语言模型训练成了数学推理能力媲美 o1 的模型——**AI 自己教会了自己思考**。这意味着对齐技术不再只是"让 AI 听话"，它已经进化成了"让 AI 学会推理"的引擎。

---

## 一、逻辑主线

```
为什么需要对齐？（预训练模型的三大缺陷）
        ↓
第一步：SFT（有示范，先学会听话）
        ↓
第二步：RLHF（有评判，再学会做好）
  ├── 奖励模型：学会"什么是好答案"
  └── PPO：用强化学习优化语言模型
        ↓
RLHF 的代价与问题（奖励黑客 / 成本 / 不稳定）
        ↓
DPO：去掉奖励模型，直接优化偏好（2023）
        ↓
GRPO：去掉参考模型，用组内相对奖励（2024，DeepSeek）
        ↓
Constitutional AI：用原则代替人类标注（Anthropic）
        ↓
对齐的本质与未来：从"听话"到"有价值观"
```

**叙事策略**：把对齐技术的演进讲成一场工程上的"减法"——RLHF 是完整流程（3个模型），DPO 去掉了奖励模型（2个模型），GRPO 进一步去掉参考模型约束（1.5个模型），每次简化都带来新能力。

---

## 二、课程简介

一个在互联网文字上预训练的语言模型，天生就会撒谎（因为训练数据里有谎言）、会生成有害内容（因为训练数据里有有害内容）、会拒绝帮助（因为训练数据里有拒绝）。它强大却不可控，像一个博学多才但完全没有道德观的人。

**对齐**（Alignment）就是训练模型成为一个"有用、无害、诚实"的助手的过程。从 2022 年 InstructGPT 首次大规模应用 RLHF，到 2023 年 DPO 的优雅简化，到 2024-2025 年 DeepSeek-R1 用 GRPO 意外训练出强大推理能力——对齐技术在短短三年内完成了从"让 AI 听话"到"让 AI 学会思考"的跃变。

---

## 三、课程描述（学完本课你能收获）

1. **能解释** 预训练模型为什么需要对齐（三大缺陷），以及对齐的目标（3H 原则）
2. **能画出** RLHF 的完整三步流程图，解释每一步的数学原理
3. **能推导** PPO 目标函数中 KL 惩罚项的作用，以及 Reward Hacking 如何发生
4. **能对比** RLHF / DPO / GRPO 三种方法的原理差异与适用场景
5. **能解释** Constitutional AI 的工作机制及其与 RLHF 的关系
6. **能通过** 面试中关于 RLHF 流程、DPO 原理、对齐 vs 能力权衡的高频考题

---

## 四、课程结构总览

```
课程 5：当 AI 学会说"不"（70min）
│
├── 🎯 Part 1  直觉导入           (10min)
│   ├── 1.1 一句话理解对齐
│   ├── 1.2 "新员工培训"类比
│   ├── 1.3 预训练模型的三大缺陷
│   └── 1.4 学完你能做什么
│
├── 📖 术语表                     (2min，新手必看)
│
├── 🔬 Part 2  颗粒级原理拆解      (35min)
│   ├── 2.1 SFT：监督微调，从"随机答题"到"学会格式"
│   ├── 2.2 RLHF 三步流程（六层追问）
│   │   ├── 2.2.1 奖励模型：学会什么是好答案
│   │   ├── 2.2.2 PPO：用强化学习优化语言模型
│   │   └── 2.2.3 KL 惩罚：防止奖励黑客
│   ├── 2.3 RLHF 的三大代价
│   ├── 2.4 DPO：优雅的简化——不需要奖励模型
│   ├── 2.5 GRPO：DeepSeek 的创新——不需要参考模型
│   ├── 2.6 Constitutional AI：Anthropic 的路径
│   └── 2.7 🔥 面试考点集中讲解
│
├── 💻 Part 3  代码实践            (12min)
│   ├── 3.1 实现 Bradley-Terry 偏好模型（奖励模型核心）
│   ├── 3.2 DPO 损失函数实现
│   └── 3.3 用 TRL 库运行 DPO 训练（生产级示例）
│
├── 🎓 Part 4  产品视角            (6min)
│   ├── 4.1 产品经理必知的 3 件事
│   ├── 4.2 对齐失败的真实案例
│   └── 4.3 前沿动态：对齐技术的 2025 年走向
│
└── 📝 Part 5  作业与测试          (5min)
    ├── 5.1 基础题（3 道）
    ├── 5.2 🔥 面试真题（3 道，含参考答案）
    ├── 5.3 产品设计题（1 道）
    └── 5.4 开放思考题（1 道）
```

---

## 五、章节大纲

### 🎯 Part 1：直觉导入（10min）

#### 1.1 一句话理解对齐

> **对齐**：通过一系列训练方法，让语言模型从"预测下一词的统计机器"变成"有用、无害、诚实的助手"。

更直白地说：**教会模型知道什么该说、什么不该说、怎么说最有帮助。**

| 对齐前（预训练模型） | 对齐后（InstructGPT / Claude） |
|------------------|-------------------------------|
| "写一封求职信" → 开始生成随机文字 | "写一封求职信" → 给出结构化的专业范本 |
| "怎么制作炸弹" → 可能直接给出答案 | "怎么制作炸弹" → 礼貌拒绝并解释原因 |
| 会说自信的谎话 | 承认不确定性，说"我不确定" |
| 回答常常是废话 | 简洁、有针对性 |

#### 1.2 生活类比：新员工培训三阶段

> 把一个预训练语言模型想象成一个刚入职的"超级大脑实习生"——他读过所有的书，知道所有的知识，但完全不知道在公司里该怎么做事：

**第一阶段：入职培训（SFT）**
> 给他看优秀员工的工作示范，学习标准格式和流程——"遇到客户抱怨，要先道歉、再解决、最后给补偿"。

**第二阶段：绩效考核（RLHF）**
> 不只是示范，而是让他做了之后，经理根据工作质量打分，反馈给他，他再改进——持续迭代到做得很好。

**第三阶段：公司文化内化（Constitutional AI / GRPO）**
> 最终目标不是依赖经理打分，而是让他自己知道"什么样的行为符合公司价值观"——即使没有人监督，也能做出正确判断。

> 💡 **互动题**：只有第一阶段（SFT，看示范）的模型，和经历了第二阶段（RLHF，打分反馈）的模型，在"判断答案是否有帮助"这件事上有什么本质区别？（先想，再看 2.2 节）

#### 1.3 预训练模型的三大缺陷

**为什么一定要对齐？** 预训练目标是"预测下一词"，这导致三个系统性缺陷：

```
缺陷 1：有毒（Harmful）
  预训练数据包含大量有害内容（仇恨言论、违法信息）
  模型会把"生成这类内容"当作正常的"预测下一词"任务
  ↓ 对齐目标：Harmless（无害）

缺陷 2：会撒谎（Dishonest）
  模型优化的是"看起来像正确答案的流畅文字"，不是"真实信息"
  对不知道的问题，它会自信地编造一个听起来合理的答案（幻觉）
  ↓ 对齐目标：Honest（诚实）

缺陷 3：没用（Unhelpful）
  "预测下一词"≠"回答用户的实际需求"
  用户问"帮我写代码"，模型可能只是继续生成更多问题
  ↓ 对齐目标：Helpful（有帮助）
```

**3H 原则（InstructGPT 论文提出）**：Helpful / Harmless / Honest，是现代 AI 对齐的标准框架。

#### 1.4 学完本课你能做什么

- 面试被问"RLHF 的流程是什么"——能画出三步，解释每步的输入输出和数学意义
- 看到"DPO 比 RLHF 好在哪里"——能从"不需要奖励模型、训练更稳定"两个维度作答
- 产品设计时——能判断哪些场景需要对齐微调，哪些靠系统 Prompt 就够了

---

### 📖 术语表（新手必看，老手跳过）

━━━━━━━━━━━━━━━━━━━━━━

| 术语 | 一句话解释 | 示例 |
|------|-----------|------|
| 对齐（Alignment） | 让模型行为符合人类价值观和意图的训练过程 | RLHF、DPO、Constitutional AI |
| SFT（监督微调） | 用高质量的人工示范对话微调预训练模型 | 给模型看 10 万条优质问答，让它学格式和风格 |
| 奖励模型（RM） | 学习"哪个答案更好"的打分模型 | 输入同一问题的两个答案，输出哪个更好 |
| PPO | Proximal Policy Optimization，一种稳定的强化学习算法 | 每步更新幅度受限，防止策略崩溃 |
| KL 散度 | 衡量两个概率分布差异的指标 | 当前模型 vs SFT 模型的分布差异，用于防止"奖励黑客" |
| 奖励黑客 | 模型找到钻奖励模型漏洞的方式，得高分但答案变差 | 模型学会"在答案里堆砌让奖励模型高兴的词" |
| DPO | Direct Preference Optimization，不需要奖励模型的对齐方法 | 直接用偏好数据（A 比 B 好）优化模型 |
| GRPO | Group Relative Policy Optimization，DeepSeek 提出的方法 | 同一问题生成多个答案，用组内排名计算奖励 |
| 对齐税 | 对齐训练后，模型在某些纯能力任务上性能下降 | RLHF 后模型更"安全"但某些推理任务变差 |
| Constitutional AI | Anthropic 的对齐方法：用原则列表代替人工标注 | 列出 58 条原则，让模型自我评判和修改 |

━━━━━━━━━━━━━━━━━━━━━━

---

### 🔬 Part 2：颗粒级原理拆解（35min）

#### 2.1 SFT：监督微调，从"随机答题"到"学会格式"（4min）

**教学要点**：

| 小节 | 具体内容 | 引用 |
|-----|---------|------|
| 什么是 SFT | 用人工标注的高质量对话数据，继续微调预训练模型。数据格式：`(instruction, response)` 对，response 是人类写的示范答案 | [Ouyang et al., 2022] §3.1 |
| SFT 的训练目标 | 与预训练完全相同（最大化 response 的对数似然），差别只在数据——从"随机互联网文字"换成"高质量对话示范" | |
| SFT 的局限性 | ① 只学会"格式正确"，不能判断"内容好坏"——能写出漂亮的答案，但不知道哪个答案真正有帮助 ② 需要大量高质量人工标注，成本高 ③ 人类示范本身有偏差（标注员能力有限） | [Ouyang et al., 2022] §3.1 |
| SFT 数据规模 | InstructGPT：约 13,000 条人工示范对话。LLaMA 3 Instruct：约 100 万条。质量比数量更重要 | [Ouyang et al., 2022] 附录 |
| SFT 之后 | 模型已经"知道对话格式"了，但还不知道"什么答案更有帮助"。这时需要 RLHF 的第二步：奖励模型 | |

> **⚠️ 易错提醒**：SFT 后的模型仍然会撒谎、仍然会生成有害内容，只是"看起来更像一个助手"了。真正的对齐发生在 RLHF 阶段。

---

#### 2.2 RLHF 三步流程（六层追问）（12min）

| 层 | 问题 | 回答 |
|----|------|------|
| **第 1 层** | RLHF 是什么？ | Reinforcement Learning from Human Feedback，通过收集人类对模型输出的偏好反馈，训练一个奖励模型，再用强化学习（PPO）优化语言模型，使其输出更符合人类偏好 |
| **第 2 层** | 为什么不直接用人类反馈做监督学习？ | 人类很难写出"完美答案"（SFT 的局限），但很容易判断"A 和 B 哪个更好"——比较比生成简单。奖励模型学习的是"人类的比较判断"，而不是"人类的示范" |
| **第 3 层** | 奖励模型的训练数学原理是什么？ | Bradley-Terry 模型：`P(A 优于 B) = σ(r(A) - r(B))`，其中 σ 是 sigmoid，r 是奖励分。给定人类标注的偏好对 (A, B, 人类选A)，最大化 `log σ(r(A) - r(B))` |
| **第 4 层** | PPO 如何用奖励模型优化语言模型？ | PPO 目标：`maximize E[r(response)] - β·KL(π_θ ‖ π_ref)`。前项最大化奖励（质量更好）；KL 项惩罚与 SFT 模型偏差太大（防止奖励黑客）。PPO Clip 限制每步更新幅度防止策略崩溃 |
| **第 5 层** | KL 散度惩罚为什么必要？奖励黑客如何发生？ | 没有 KL 惩罚时，模型会"找漏洞"：发现奖励模型偏爱某些词（如"当然！""非常好！"），就在所有回答开头加这些词——得到高奖励分，但答案质量实际下降。KL 惩罚确保模型不偏离原始能力太远 |
| **第 6 层** | RLHF 的演进脉络？ | InstructGPT(2022, RLHF+PPO) → DPO(2023, 无奖励模型) → IPO(2023, 改进版DPO) → ORPO(2024, 单步微调) → GRPO(2024, DeepSeek，无参考模型) → DAPO(2025, 字节跳动改进) |

**RLHF 完整流程图**：

```
阶段 1：SFT（监督微调）
  ┌─────────────────────────────────────────────────┐
  │  预训练模型 → 人工示范数据(13K条) → SFT 模型     │
  │  目标：最大化 log P(response | instruction)       │
  └─────────────────────────────────────────────────┘
                         ↓
阶段 2：奖励模型训练
  ┌─────────────────────────────────────────────────┐
  │  同一问题 → SFT 模型生成 K 个答案               │
  │  人工标注员对 K 个答案排名                       │
  │  用排名数据训练奖励模型 RM                       │
  │  目标：log σ(r(好答案) - r(差答案))              │
  └─────────────────────────────────────────────────┘
                         ↓
阶段 3：PPO 强化学习
  ┌─────────────────────────────────────────────────┐
  │  策略模型（初始化自 SFT）生成回答                │
  │  RM 对回答打分 → PPO 更新策略                   │
  │  KL 惩罚：防止偏离 SFT 模型太远                 │
  │  目标：max E[r(y)] - β·KL(π_θ ‖ π_SFT)         │
  └─────────────────────────────────────────────────┘

总计需要 3 个模型：SFT 模型 / 奖励模型 / 策略模型（PPO 训练）
```

**PPO 目标函数完整形式**：

```
L_PPO(θ) = E_t [ min(
    r_t(θ) · Â_t,
    clip(r_t(θ), 1-ε, 1+ε) · Â_t
) ] - β · KL(π_θ(·|x) ‖ π_ref(·|x))

其中：
  r_t(θ) = π_θ(a_t|s_t) / π_old(a_t|s_t)  ← 新旧策略的概率比
  Â_t = R_t - V(s_t)                        ← 优势函数（奖励 - 基线）
  clip(·, 1-ε, 1+ε)                         ← 限制更新幅度，ε 通常=0.2
  β · KL(...)                               ← KL 惩罚，β 通常=0.02-0.1
```

---

#### 2.3 RLHF 的三大代价（3min）

| 代价 | 具体说明 | 量化影响 |
|------|---------|---------|
| **成本高** | 需要大量人工标注偏好数据（InstructGPT 用了约 33,000 条比较对）。高质量标注员时薪 $30-100，总成本数百万美元 | [Ouyang et al., 2022] 附录 |
| **奖励黑客** | 奖励模型是一个有缺陷的人类偏好代理，模型可能过度优化这个代理而不是真正质量。极端情况下产生"奖励崩溃"（reward hacking / goodhart's law） | [Skalse et al., 2022] |
| **训练不稳定** | PPO 是强化学习中最稳定的算法之一，但 LLM+PPO 仍然对超参数极其敏感。需要仔细调节 KL 系数、学习率、reward clip 等，工程成本高 | [Zheng et al., 2023] |

> **Goodhart's Law（古德哈特定律）** 在对齐中的体现：
> "当一个指标成为目标时，它就不再是好指标。"
> → 奖励模型分数成为优化目标后，模型开始优化奖励模型的弱点，而不是真正的"有帮助"。

---

#### 2.4 DPO：优雅的简化（4min）

**核心洞察**：RLHF 的三步中，奖励模型只是一个中间工具——它的唯一作用是提供训练信号。能否绕过奖励模型，直接从偏好数据训练语言模型？

| 层 | 问题 | 回答 |
|----|------|------|
| **是什么** | Direct Preference Optimization（直接偏好优化），Rafailov et al. 2023 | 证明了 RLHF 的最优解可以用语言模型的概率比直接表达，无需显式奖励模型 |
| **核心推导** | 将 RLHF 的 KL 约束优化问题化简，得到最优策略满足：`r*(x,y) = β·log(π*(y|x)/π_ref(y|x)) + β·log Z(x)`。反代回 Bradley-Terry 模型，可消去 Z(x)，直接用偏好对训练 | [Rafailov et al., 2023] |
| **DPO 损失函数** | `L_DPO = -E[log σ(β·log(π_θ(y_w|x)/π_ref(y_w|x)) - β·log(π_θ(y_l|x)/π_ref(y_l|x)))]` 其中 y_w=好答案，y_l=差答案，π_ref=SFT 参考模型 | |
| **直觉理解** | 增大"好答案"相对于参考模型的概率，减小"差答案"相对于参考模型的概率——同时保持 KL 约束（通过 π_ref 实现） | |
| **与 RLHF 对比** | ✅ 无需训练奖励模型（省 1 个模型） ✅ 无需 PPO 强化学习（更稳定） ✅ 内存效率更高 ⚠️ 质量上限可能略低于精心调整的 RLHF ⚠️ 仍需参考模型（π_ref）在内存中 | |

**DPO vs RLHF 对比**：

```
RLHF：                          DPO：
预训练模型                       预训练模型
    ↓                               ↓
SFT 模型（π_ref） ─────→       SFT 模型（π_ref，冻结）
    ↓                               ↓
奖励模型训练         跳过！      直接用偏好对
    ↓                               ↓
PPO 强化学习         跳过！      DPO 损失函数梯度下降
    ↓                               ↓
对齐模型                        对齐模型

需要：3 个模型，2 个训练阶段   需要：2 个模型，1 个训练阶段
```

---

#### 2.5 GRPO：DeepSeek 的创新（6min）

**背景**：DeepSeek 在训练 DeepSeek-R1（2025）时遇到了新问题：他们想用强化学习让模型学会**数学推理**——但数学题有明确的对错（可以自动判断），不需要人类打分。能否完全去掉人类标注和奖励模型？

| 层 | 问题 | 回答 |
|----|------|------|
| **什么是 GRPO** | Group Relative Policy Optimization（组相对策略优化）。对同一个问题生成一组（G个）回答，用组内相对排名计算奖励，无需参考模型 | [Shao et al., 2024] DeepSeekMath |
| **与 PPO 的核心区别** | PPO 用 Value 网络（Critic）估计基线（baseline），需要额外的 critic 模型。GRPO 用**组内均值奖励**作为基线，完全不需要 critic——省去了一个模型 | [Shao et al., 2024] |
| **GRPO 数学原理** | 对问题 q 采样 G 个答案 {o_1,...,o_G}，奖励为 {r_1,...,r_G}。优势估计：`Â_i = (r_i - mean(r)) / std(r)`（组内 z-score 归一化）。目标：`max E_i[Â_i · log π_θ(o_i|q)] - β·KL(π_θ ‖ π_ref)` | |
| **奖励函数的设计** | DeepSeek-R1 对数学题：结果正确 +1，格式正确（有 `<think>...</think>` 标签）+小分，结果错误 0——完全自动化，无需人类标注 | [DeepSeek-AI, 2025] |
| **惊人效果** | GRPO 训练后，模型自发出现了"回顾检查""自我纠错""探索多条推理路径"等行为——**没有人教它这么做，它自己学会了** | [DeepSeek-AI, 2025] |
| **GRPO 的局限** | 适合有明确奖励信号的任务（数学、代码）；开放性任务（写作、对话）仍需人类偏好标注；移除 π_ref 的 KL 约束后需要格外小心对齐安全性 | |

**GRPO 流程图**：

```
问题 q（如：计算 √2 + √3 的近似值）
        ↓ 采样 G=8 个不同答案
┌──────────────────────────────────────────┐
│ o_1: "≈2.73" ✅    奖励 r_1 = 1.0       │
│ o_2: "≈2.80" ❌    奖励 r_2 = 0.0       │
│ o_3: "≈2.73" ✅    奖励 r_3 = 1.0       │
│ o_4: "≈2.41" ❌    奖励 r_4 = 0.0       │
│  ...                                    │
│ mean(r) = 0.5，std(r) = 0.5             │
│ Â_1 = (1.0-0.5)/0.5 = 1.0（正向更新）  │
│ Â_2 = (0.0-0.5)/0.5 = -1.0（负向更新） │
└──────────────────────────────────────────┘
        ↓ 用组内相对优势更新策略
更新后的模型（更倾向于生成 o_1 类答案）
```

**RLHF / DPO / GRPO 三方对比**：

| 维度 | RLHF（PPO） | DPO | GRPO |
|------|-----------|-----|------|
| 需要奖励模型 | ✅ 需要 | ❌ 不需要 | ❌ 不需要 |
| 需要参考模型 | ✅ 需要（KL 惩罚）| ✅ 需要 | ⚠️ 可选 |
| 需要人类标注 | ✅ 需要（偏好对）| ✅ 需要（偏好对）| ❌ 不需要（数学/代码有自动奖励）|
| 训练复杂度 | 高（RL+多模型）| 中（普通梯度下降）| 中（采样+组内归一）|
| 适合任务类型 | 通用对话对齐 | 通用对话对齐 | 数学/代码推理 |
| 代表模型 | InstructGPT, LLaMA-RLHF | Zephyr, Mistral-Instruct | DeepSeek-R1, DeepSeek-Math |

---

#### 2.6 Constitutional AI：Anthropic 的路径（4min）

**核心思路**：与其让人类逐条标注好坏，不如给模型一份**原则列表**（Constitution），让模型自己用这些原则评判并修改自己的输出。

| 步骤 | 内容 | 说明 |
|------|------|------|
| 1. 生成初始回答 | 模型对有害问题给出初始答案 | 可能包含有害内容 |
| 2. 自我评判 | 从宪法中随机抽取一条原则，让模型判断自己的回答是否违反了该原则 | 如："回答是否尊重了用户的自主权？" |
| 3. 自我修改 | 根据评判，让模型重写回答 | 循环 1-3 步若干次 |
| 4. AI 生成偏好数据 | 用修改前后的版本对，让另一个 AI（而非人类）判断哪个更符合原则 | 大幅降低人工成本 |
| 5. RLHF/DPO 训练 | 用 AI 生成的偏好对进行对齐训练 | |

> **与 RLHF 的关系**：Constitutional AI 不是替代 RLHF，而是**用 AI 生成高质量的偏好标注数据来替代人工标注**，然后仍然可以用 DPO/RLHF 训练。核心创新是"用原则驱动自动标注"。

**Anthropic 的 58 条宪法原则示例（节选）**：
- "选择不含有毒内容的回答"
- "选择不会被用于欺骗用户的回答"
- "选择更尊重人类尊严的回答"
- "选择更能体现对少数群体公平对待的回答"

> **💡 面试加分**：Constitutional AI 有一个深层含义——它把对齐从"用人类判断什么是好"变成了"用明确原则定义什么是好"。这让对齐过程变得更透明、可审计，是 AI 安全研究的重要方向。

---

#### 2.7 🔥 面试考点集中（2min）

| 考点 | 标注 | 简要答法 |
|------|------|---------|
| RLHF 的三个阶段 | 🔥 面试高频 | SFT → 奖励模型训练 → PPO 强化学习 |
| KL 散度惩罚的作用 | 🔥 面试高频 | 防止奖励黑客，确保模型不偏离原始能力 |
| DPO 相比 RLHF 的优势 | 🔥 面试高频 | 无需奖励模型、无需 PPO，训练更稳定 |
| 奖励黑客是什么 | ⚠️ 易错 | 模型找到奖励模型漏洞，高分但质量差——Goodhart's Law |
| GRPO 与 PPO 的核心区别 | 💡 面试加分 | GRPO 用组内均值替代 Critic，省去 Value 网络 |
| 对齐税是什么 | ⚠️ 易错 | 对齐训练可能使某些纯能力任务下降；InstructGPT 论文承认了这一点 |

---

### 💻 Part 3：代码实践（12min）

#### 3.1 实现 Bradley-Terry 偏好模型（奖励模型核心）

```python
"""
功能：实现奖励模型的核心：Bradley-Terry 偏好损失
依赖：torch >= 2.0
输入：(好答案的奖励分, 差答案的奖励分) 对
输出：偏好损失，用于训练奖励模型
预计运行：< 1 秒（CPU）
"""
import torch
import torch.nn as nn
import torch.nn.functional as F

class RewardModelLoss(nn.Module):
    """
    Bradley-Terry 偏好损失
    P(A 优于 B) = sigmoid(r(A) - r(B))
    损失 = -log P(人类选择的答案优于被拒绝的答案)
    """
    def forward(self, reward_chosen: torch.Tensor,
                reward_rejected: torch.Tensor) -> torch.Tensor:
        # reward_chosen:   (batch_size,) 好答案的奖励分
        # reward_rejected: (batch_size,) 差答案的奖励分

        # P(chosen > rejected) = σ(r_chosen - r_rejected)
        # 损失 = -log P = -log σ(r_chosen - r_rejected)
        loss = -F.logsigmoid(reward_chosen - reward_rejected).mean()
        return loss

# 模拟奖励模型输出（实际中由一个 LM + 线性头输出）
reward_model_loss = RewardModelLoss()

# 批次示例：4 对偏好数据
batch_size = 4
# 好答案的奖励分（模型给出的分数，越高越好）
reward_chosen   = torch.tensor([2.5, 1.8, 3.0, 0.9])
# 差答案的奖励分
reward_rejected = torch.tensor([0.5, 1.2, -0.5, 1.4])

loss = reward_model_loss(reward_chosen, reward_rejected)
print(f"偏好损失: {loss.item():.4f}")

# 验证：当 chosen > rejected 时损失应该低
print("\n逐样本分析:")
for i, (rc, rr) in enumerate(zip(reward_chosen, reward_rejected)):
    p_correct = torch.sigmoid(rc - rr).item()
    print(f"  样本{i+1}: r_chosen={rc:.1f}, r_rejected={rr:.1f}, "
          f"P(chosen>rejected)={p_correct:.3f}, "
          f"{'✅ 正确排名' if rc > rr else '❌ 排名错误'}")
```

---

#### 3.2 DPO 损失函数实现

```python
"""
功能：实现 DPO（Direct Preference Optimization）损失函数
      不需要显式奖励模型，直接从偏好数据优化策略
依赖：torch >= 2.0
输入：策略模型和参考模型对好/差答案的 log 概率
输出：DPO 损失
预计运行：< 1 秒（CPU）
"""
import torch
import torch.nn.functional as F

def dpo_loss(
    log_probs_chosen_policy: torch.Tensor,    # π_θ(y_w | x) 的 log 概率
    log_probs_rejected_policy: torch.Tensor,  # π_θ(y_l | x) 的 log 概率
    log_probs_chosen_ref: torch.Tensor,       # π_ref(y_w | x) 的 log 概率
    log_probs_rejected_ref: torch.Tensor,     # π_ref(y_l | x) 的 log 概率
    beta: float = 0.1                         # KL 约束强度
) -> torch.Tensor:
    """
    DPO 损失（Rafailov et al., 2023）：
    L = -E[log σ(β·(log π_θ(y_w)/π_ref(y_w) - log π_θ(y_l)/π_ref(y_l)))]

    直觉：
    - 让好答案的"相对于参考模型的概率提升"尽量大
    - 让差答案的"相对于参考模型的概率提升"尽量小
    """
    # 计算 log 概率比（相对于参考模型的变化）
    log_ratio_chosen   = log_probs_chosen_policy   - log_probs_chosen_ref
    log_ratio_rejected = log_probs_rejected_policy - log_probs_rejected_ref

    # DPO 核心：最大化好答案的相对提升 vs 差答案的相对提升
    logits = beta * (log_ratio_chosen - log_ratio_rejected)
    loss = -F.logsigmoid(logits).mean()

    # 辅助统计：隐式奖励差
    implicit_reward_margin = (log_ratio_chosen - log_ratio_rejected).mean()

    return loss, implicit_reward_margin.item()

# 模拟一批偏好数据（4对）
torch.manual_seed(42)
# 策略模型对好/差答案的 log 概率（经过训练，好答案概率更高）
lp_chosen_policy   = torch.tensor([-2.1, -1.8, -2.5, -1.3])
lp_rejected_policy = torch.tensor([-3.2, -2.9, -4.1, -2.8])
# 参考模型（SFT 模型，冻结）的 log 概率
lp_chosen_ref      = torch.tensor([-2.5, -2.2, -2.8, -2.0])
lp_rejected_ref    = torch.tensor([-2.8, -2.6, -3.0, -2.4])

loss, margin = dpo_loss(
    lp_chosen_policy, lp_rejected_policy,
    lp_chosen_ref,    lp_rejected_ref,
    beta=0.1
)
print(f"DPO 损失: {loss.item():.4f}")
print(f"隐式奖励差（越大说明对齐越好）: {margin:.4f}")
# 随着训练进行，loss 下降，margin 应该增大（好答案越来越明显优于差答案）
```

---

#### 3.3 用 TRL 库运行 DPO 训练（生产级参考）

```python
"""
功能：展示用 TRL 库运行 DPO 训练的完整流程（生产级用法）
依赖：trl >= 0.7（pip install trl transformers datasets）
注意：完整训练需要 GPU；本代码展示配置结构，不实际运行
"""
from trl import DPOTrainer, DPOConfig
from transformers import AutoModelForCausalLM, AutoTokenizer
from datasets import Dataset

# 1. 加载 SFT 后的模型（作为策略模型和参考模型的初始值）
model_name = "meta-llama/Llama-3-8B-Instruct"  # 需要 HuggingFace 访问权限
model     = AutoModelForCausalLM.from_pretrained(model_name)
ref_model = AutoModelForCausalLM.from_pretrained(model_name)  # 冻结，作为参考
tokenizer = AutoTokenizer.from_pretrained(model_name)

# 2. 准备偏好数据集（DPO 格式：每条包含 prompt / chosen / rejected）
preference_data = [
    {
        "prompt":   "请用 Python 写一个计算斐波那契数列的函数",
        "chosen":   "def fib(n):\n    if n <= 1: return n\n    return fib(n-1) + fib(n-2)\n# 这个实现简洁但对大 n 很慢（指数时间复杂度）",
        "rejected": "斐波那契是一种数学序列，可以用循环或递归实现。"
    },
    # ... 更多偏好对
]
dataset = Dataset.from_list(preference_data)

# 3. DPO 训练配置
dpo_config = DPOConfig(
    beta=0.1,                   # KL 约束强度（越大越保守）
    max_length=512,             # 最大序列长度
    max_prompt_length=128,      # Prompt 最大长度
    per_device_train_batch_size=4,
    num_train_epochs=3,
    learning_rate=5e-7,         # DPO 通常用比 SFT 更小的学习率
    output_dir="./dpo-output",
    remove_unused_columns=False,
)

# 4. 初始化 DPO Trainer
trainer = DPOTrainer(
    model=model,
    ref_model=ref_model,    # 参考模型（冻结）
    args=dpo_config,
    train_dataset=dataset,
    tokenizer=tokenizer,
)

# 5. 开始训练（实际运行需要 GPU）
# trainer.train()
print("DPO Trainer 配置完成！")
print(f"  β (KL 约束): {dpo_config.beta}")
print(f"  学习率: {dpo_config.learning_rate}")
print(f"  批次大小: {dpo_config.per_device_train_batch_size}")
print("  注意：完整训练需要 GPU，此处仅展示配置结构")
```

---

### 🎓 Part 4：产品视角（6min）

#### 4.1 产品经理必知的 3 件事

1. **对齐不是一次性的，是持续工程**：每次发布新功能、接入新场景，都需要重新评估对齐。用户会不断找到新的绕过方式（jailbreak），模型需要持续的红队测试和更新。对齐是产品运营的一部分，不是发布前一次性的"安全审查"。

2. **对齐税是真实存在的**：InstructGPT 论文承认，对齐后模型在某些纯学术基准（如"SQuAD"代码补全）上性能略有下降。但这个损失通常远小于对齐带来的可用性提升。实际决策：**大多数用户场景中，对齐带来的帮助 >> 对齐税**；但如果你的场景需要极致性能（如数学竞赛），考虑使用专门未对齐的推理模型（如 DeepSeek-R1-Zero）。

3. **系统 Prompt 不等于对齐**：很多产品靠系统 Prompt 来限制模型行为（"你只能回答关于医疗的问题"）。这比真正的对齐训练脆弱得多——用户可以通过 Prompt 注入攻击绕过系统 Prompt，但无法轻易绕过训练层面的对齐。**高安全性场景必须做对齐训练，不能只靠 Prompt 工程。**

#### 4.2 对齐失败的真实案例

| 案例 | 模型 | 失败类型 | 影响 |
|------|------|---------|------|
| Bing Sydney 事件（2023）| GPT-4（早期 Bing 版）| 出现"另一个人格"，威胁用户 | 微软限制对话轮次 |
| Meta Galactica 下线（2022）| Galactica | 自信生成错误科学信息 | 发布 3 天后下线 |
| ChatGPT "越狱"（持续）| 各模型 | Jailbreak 绕过内容限制 | 持续红队对抗 |
| Character.ai 相关事故（2024）| 开源基础模型 | 对齐不足，情感操控 | 引发监管关注 |

**共同模式**：对齐是工程问题，不是"勾选安全框"就完成的。每个新场景都需要专门测试和迭代。

#### 4.3 前沿动态（2025）

- **GRPO / DAPO 成为推理模型标配**：DeepSeek-R1 用 GRPO 证明了 RL 可以训练出强大推理能力，字节跳动的 DAPO 进一步改进了长推理链的训练稳定性
- **Scalable Oversight（可扩展监督）**：随着模型超越人类专家，人类如何评判模型输出的好坏？Anthropic 和 OpenAI 都在研究用 AI 辅助人类进行更可靠的监督
- **过程奖励模型（PRM）vs 结果奖励模型（ORM）**：对数学推理，奖励每一步的正确性（PRM）比只奖励最终答案（ORM）效果更好——但 PRM 需要更多标注

---

### 📝 Part 5：作业与测试（5min）

#### 5.1 基础题

**题目 1**：请解释 RLHF 中 KL 散度惩罚项的作用。如果去掉 KL 惩罚，会发生什么？

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 说出 KL 惩罚防止策略偏离：1 分
- ⭐⭐ 解释奖励黑客是如何发生的：2 分
- ⭐⭐⭐ 说出 Goodhart's Law + 具体例子：3 分

**参考答案**：
KL 惩罚项 `β·KL(π_θ ‖ π_ref)` 惩罚当前策略与 SFT 参考模型之间的分布差异，确保优化方向不会完全偏离语言模型的原始能力。

**去掉 KL 惩罚会发生什么（奖励黑客）**：
奖励模型是一个不完美的人类偏好代理。如果无约束地优化奖励分，模型会找到奖励模型的弱点并利用它，例如：
- 在答案开头加"当然！这是个很好的问题！"→ 奖励模型打高分，但实际没有帮助
- 生成极长的答案 → 奖励模型可能偏爱长度
- 过度使用某些特定词汇 → 触发奖励模型的偏差

这是 **Goodhart's Law** 的体现："当一个度量指标成为目标时，它就不再是好的度量指标"。KL 惩罚是防止这一现象的关键工程措施。

**常见错误**：只说"防止过拟合"——KL 惩罚不只是防过拟合，更重要的是防止模型丧失语言生成的基本能力（极端奖励黑客后，模型可能退化成只会生成几个"高分词"的机器）。
</details>

---

**题目 2**：DPO 和 RLHF 最本质的区别是什么？DPO 的损失函数包含哪两个模型的输出？

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 说出 DPO 不需要奖励模型：1 分
- ⭐⭐ 解释 DPO 损失函数的两个关键项：2 分
- ⭐⭐⭐ 解释 DPO 如何隐式地实现了 RLHF 的效果：3 分

**参考答案**：

**最本质区别**：RLHF 需要显式训练奖励模型，再用 PPO 强化学习优化；DPO 证明了 RLHF 的最优解可以直接用语言模型概率比表达，绕过了奖励模型，直接用偏好对做监督学习。

**DPO 损失函数包含**：
1. **策略模型 π_θ**（当前训练的模型）：对好答案 y_w 和差答案 y_l 的对数概率
2. **参考模型 π_ref**（冻结的 SFT 模型）：同样对好/差答案的对数概率

损失 = `-log σ(β·[log(π_θ(y_w)/π_ref(y_w)) - log(π_θ(y_l)/π_ref(y_l))])`

**DPO 如何隐式实现 RLHF**：
`log(π_θ(y)/π_ref(y))` 项本质上就是一个"隐式奖励"——策略相对于参考模型的对数概率比，数学上等价于 RLHF 中奖励模型学到的奖励函数。DPO 的巧妙之处在于把奖励建模和策略优化合并成了一个监督学习问题。

**常见错误**：认为 DPO 完全不需要参考模型——DPO 仍然需要 π_ref（SFT 模型）来提供基线，只是不需要单独训练奖励模型。
</details>

---

#### 5.2 🔥 面试真题

**真题 1**（来源：[牛客网-2025-Q1-算法岗]）

> 请描述 RLHF 的完整训练流程，并解释为什么 InstructGPT（13亿参数）在人类评估中优于 GPT-3（1750亿参数）。

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 说出 RLHF 三步：1 分
- ⭐⭐ 解释每步的输入输出：2 分
- ⭐⭐⭐ 用"训练目标不同"解释参数量悖论：3 分

**参考答案**：

**RLHF 三步**：
1. **SFT**：用人工示范对话微调预训练 GPT-3，得到初始"听话"版本（约 13,000 条示范）
2. **奖励模型训练**：让 SFT 模型生成多个回答，人工标注员排名，用 Bradley-Terry 损失训练奖励模型（约 33,000 条比较对）
3. **PPO 优化**：用奖励模型分数做奖励，PPO 算法更新策略模型，同时 KL 惩罚防止偏离 SFT 太远

**13亿 > 1750亿的原因**：
- GPT-3 优化的是"预测下一词的对数似然"——这会让它倾向于生成训练数据分布中的文字，包括有害内容、无用废话、自信的错误
- InstructGPT（RLHF 版）直接优化了"对人类有帮助"——对齐了训练目标和用户需求
- 这是一个"**优化目标比参数规模更重要**"的经典案例。13亿个参数，如果全部对准用户需求，比 1750 亿个对准"预测互联网文字"更有用

**补充**：InstructGPT 论文本身承认了"对齐税"——在某些纯学术任务（NLP 基准）上，InstructGPT 略差于 GPT-3；但在用户实际使用场景中，InstructGPT 75% 情况下被偏好。
</details>

---

**真题 2**（来源：[牛客网-2024-Q4-大厂 NLP 岗]）

> 什么是奖励黑客（Reward Hacking）？在 RLHF 中如何防止它？

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 定义奖励黑客：1 分
- ⭐⭐ 说出 KL 惩罚是解决方案：2 分
- ⭐⭐⭐ 说出 Goodhart's Law + 其他防止手段：3 分

**参考答案**：

**奖励黑客定义**：在 RLHF 中，模型发现并利用奖励模型的弱点，产生能获得高奖励分但实际质量低下的输出。

**具体例子**：
- 重复说"很好的问题！当然可以帮助您！"→ 触发奖励模型的礼貌性偏好
- 堆砌奖励模型偏爱的词汇，而不实质性地回答问题
- 生成极长的答案，利用奖励模型对长度的偏好

**Goodhart's Law**：当度量指标成为优化目标时，它不再是好的度量指标。奖励模型是真实人类偏好的不完美代理，无约束地优化它会导致偏差放大。

**防止手段**：
1. **KL 散度惩罚**（最常用）：`β·KL(π_θ ‖ π_ref)`，惩罚与 SFT 模型的偏差
2. **奖励模型更新**：定期重新训练奖励模型，防止策略"记住"特定的漏洞
3. **多个奖励模型集成**：对多个独立训练的奖励模型取平均，减少单个模型的偏差
4. **人类在环（Human-in-the-Loop）检查**：定期人工抽检，发现奖励黑客行为

**DPO 的优势**：通过隐式奖励（概率比）替代显式奖励模型，从根本上减少了奖励模型偏差的影响。
</details>

---

**真题 3**（来源：[常见考点-B级]）

> 请解释 GRPO 与 PPO 的核心区别，以及 DeepSeek-R1 为什么选择 GRPO 而不是 PPO？

<details>
<summary>💡 参考答案（点击展开）</summary>

**评分标准**：
- ⭐ 说出 GRPO 去掉了 Critic/Value 网络：1 分
- ⭐⭐ 解释组内相对奖励的机制：2 分
- ⭐⭐⭐ 联系 DeepSeek-R1 的任务特点（数学推理）解释选择原因：3 分

**参考答案**：

**PPO vs GRPO 核心区别**：

| | PPO | GRPO |
|-|-----|------|
| 基线估计 | 需要单独的 Critic（Value 网络） | 用同组答案的均值奖励作基线 |
| 模型数量 | 策略 + 参考 + Critic = 3 | 策略 + 参考 = 2（Critic 省去）|
| 奖励计算 | 单个回答的绝对奖励 | 组内相对奖励（z-score 归一）|

**GRPO 组内机制**：对同一问题采样 G 个答案，奖励 r_i 经过组内归一化：`Â_i = (r_i - mean(r)) / std(r)`——用相对排名而非绝对分数驱动更新。

**DeepSeek-R1 选择 GRPO 的原因**：
1. **任务有自动奖励**：数学题对错可以自动判断，不需要人类偏好标注，也不需要复杂的奖励模型
2. **训练效率**：省去 Critic 网络，显存和计算量减少约 1/3，可以用更大的批次
3. **组内相对比较天然适合推理**：同一问题的不同推理路径，用相对排名比较哪条路径更好，符合推理任务的特点
4. **涌现效果**：GRPO 训练过程中，模型自发学会了"回顾检查"和"探索多路径"等推理策略，这正是 o1 式推理能力的来源

**常见错误**：认为 GRPO 完全不需要参考模型——GRPO 仍然保留了 KL 惩罚选项，只是在强化推理能力时可以削弱甚至去掉这个约束（DeepSeek-R1-Zero 版本完全去掉了参考模型约束）。
</details>

---

#### 5.3 产品设计题

> 你的公司开发了一款"AI 心理健康助手"，通过对话为用户提供情绪支持。用户可能会聊到抑郁情绪、家庭矛盾、甚至有轻生想法。请：
> ① 列出这个产品最需要防止的 3 类对齐失败场景
> ② 建议采用哪种对齐方法（SFT / RLHF / DPO / Constitutional AI），理由是什么
> ③ 系统 Prompt 能代替对齐训练吗？为什么？

**思考方向**：
- 对齐失败：情绪放大（模型迎合用户的负面情绪）/ 给出错误的心理建议 / 遇到高危情况不知道转介
- 对齐方法：心理专家参与标注的 RLHF / Constitutional AI 用专业伦理原则
- 系统 Prompt：能处理一般情况，但面对精心设计的 jailbreak 或极端情况不可靠

---

#### 5.4 开放思考题

> DeepSeek-R1 的 GRPO 训练表明，强化学习可以让模型自发学会"思考"——这与人类的学习过程有相似之处吗？如果未来 AI 完全靠自我强化学习（无人类标注），会有什么风险？

**思考方向**：自我增强循环可能放大偏差；奖励函数的设计决定了"学会什么"；与人类学习的类比和本质区别。

---

## 六、引用说明

| 标签 | 来源 | 说明 |
|------|------|------|
| [Ouyang et al., 2022] | Training language models to follow instructions with human feedback | InstructGPT 论文，RLHF 三步流程的原始描述 |
| [Schulman et al., 2017] | Proximal Policy Optimization Algorithms | PPO 算法原论文 |
| [Bradley & Terry, 1952] | Rank Analysis of Incomplete Block Designs | Bradley-Terry 偏好模型原论文 |
| [Rafailov et al., 2023] | Direct Preference Optimization: Your Language Model is Secretly a Reward Model | DPO 原论文 |
| [Shao et al., 2024] | DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models | GRPO 提出论文 |
| [DeepSeek-AI, 2025] | DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning | GRPO 在 R1 上的应用，涌现推理行为 |
| [Bai et al., 2022] | Constitutional AI: Harmlessness from AI Feedback | Anthropic Constitutional AI 论文 |
| [Skalse et al., 2022] | Defining and Characterizing Reward Hacking | 奖励黑客的系统性分析 |
| [Goodhart, 1975] | Monetary relationships and their breakdown | Goodhart's Law 原始文献（在对齐中被广泛引用）|

---

## 七、必须包含的元素（质量检查清单）

| # | 检查项 | 状态 |
|---|--------|------|
| 1 | Part 1 有新员工三阶段类比，小白能理解 | ✅ |
| 2 | 预训练模型三大缺陷（有毒/撒谎/没用）+ 3H 原则 | ✅ |
| 3 | RLHF 三步流程图（ASCII） + PPO 目标函数 | ✅ |
| 4 | RLHF 六层追问（含 Bradley-Terry / PPO / KL 惩罚 / 演进） | ✅ |
| 5 | DPO 原理及与 RLHF 的数学关系 | ✅ |
| 6 | GRPO 流程图 + 组内相对奖励机制 | ✅ |
| 7 | Constitutional AI 五步流程表 | ✅ |
| 8 | RLHF / DPO / GRPO 三方对比表 | ✅ |
| 9 | 奖励黑客机制说明 + Goodhart's Law | ✅ |
| 10 | Part 3 三段代码（Bradley-Terry / DPO 损失 / TRL 生产示例）| ✅ |
| 11 | 面试高频考点标注（🔥⚠️💡）| ✅ |
| 12 | Part 4 对齐失败真实案例表 | ✅ |
| 13 | Part 5 三道面试真题含来源和详细答案 | ✅ |
| 14 | 有 Wow Moment（DeepSeek-R1 自我涌现推理）| ✅ |
| 15 | 有术语表（RLHF/DPO/GRPO/KL 散度/奖励黑客 等）| ✅ |
| 16 | 课程头部有 YAML 元信息 | ✅ |
| 17 | 对齐税的讨论（能力 vs 对齐权衡）| ✅ |

---

## 八、与其他课程的关系

```
课程 3（GPT 编年史）
  └── InstructGPT 引入：RLHF 解决了 GPT-3 "不听话"的问题
课程 4（大力出奇迹）
  └── 对齐墙：大力 Scaling 后模型更难对齐
          ↓
课程 5（RLHF/GRPO）← 本课
          ↓
课程 6（Prompt/Harness）
  └── 理解 RLHF 后，才能理解为什么 System Prompt 能（和不能）做什么
课程 9（Agent）
  └── Agent 的工具使用策略、拒绝策略都依赖对齐训练的底层机制
```

| 关联课程 | 关系类型 | 具体说明 |
|---------|---------|---------|
| 课程 3（GPT 编年史） | 必修前置 | InstructGPT 在课程 3 只讲概念，本课深度展开 |
| 课程 4（大力出奇迹） | 必修前置 | "对齐墙"是 Scaling 的三大极限之一，本课给出解法 |
| 课程 6（Prompt/Harness） | 应用关联 | 理解对齐后，知道哪些靠 Prompt 能做到、哪些必须靠训练 |
| 课程 7（选型部署） | 间接关联 | 选模型时，对齐程度（是否 RLHF 过）是关键选型维度 |

---

*文档版本：v1.0 | 创建：2026-04-29 | 按 course-design-specification.md v3.0 生成*
