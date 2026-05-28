---
课程系列: 具身智能系列
课程编号: EI-07
课程名称: 视觉语言动作模型（VLA）：RT-2、OpenVLA与π₀
版本: v1.0
创建日期: 2026-05-25
适用受众:
  - 算法工程师（大模型+机器人方向的核心课）
  - 机器人工程师（了解VLA与传统方法的关系）
  - AI产品经理（VLA是具身智能最前沿的商业方向）
前置课程: EI-04 模仿学习、EI-06 Sim-to-Real
预计学习时长: 4小时
适用机器人平台: 通用（机械臂/人形机器人均适用）
核心系统:
  - RT-2（Google DeepMind）
  - OpenVLA（Stanford，开源）
  - π₀（Physical Intelligence）
  - SayCan（Google）
  - Octo（UC Berkeley，开源）
---

# EI-07 视觉语言动作模型（VLA）：RT-2、OpenVLA与π₀

---

## Wow Moment

> **RT-2看到一块新颜色的积木（训练集中从未见过），没有任何额外训练，直接知道怎么抓——因为它从互联网上学到了"石榴石是一种红色"，因此能推断出"拿那块石榴石色的方块"。语言模型的泛化性注入了机器人。这一刻，具身智能和大模型真正融合了。**

这不是调了一下接口。这是范式的转变：机器人第一次能够**通过语言理解意图，通过视觉理解场景，并直接输出动作**——在一个统一的端到端模型中。

---

## Part 0：语言模型遇见机器人——为什么1+1>2？

### 各自的局限

**纯语言模型（GPT-4等）：**
- 知道"苹果是红色的"
- 知道"先把水烧开，再放茶叶"
- 但：没有手，不能做

**纯机器人策略（BC/RL训练的）：**
- 能抓物体
- 能执行动作序列
- 但：不理解"把红色的那个拿给我"
- 泛化能力差（换个物体就失败）

### VLA的融合逻辑

```
语言模型的贡献：
    ✓ 海量互联网数据的隐性物理知识
    ✓ 指令理解（"轻轻地" "先...再..."）
    ✓ 零样本泛化（见过描述，没见过物体）
    ✓ 常识推理（"杯子是易碎的"→用小力）

机器人数据的贡献：
    ✓ 真实动作轨迹
    ✓ 物理交互的感知反馈
    ✓ 任务成功/失败的标注

VLA = 两者的融合
    → 能理解指令 × 能生成动作 = 通用机器人操作
```

**ML背景学员**：VLA本质上是将机器人动作表示为token序列，然后用Transformer做多模态预测——和你熟悉的序列生成模型（GPT）是同一套框架。

**机器人背景学员**：VLA不是在传统控制器上加语言接口，而是**端到端替换整个决策层**。它输出的是原始关节角度，不是高层规划指令。

---

## Part 1：VLA的直觉

### 会说话的机器人 vs 会做事的机器人

**历史上的两个独立发展：**

```
NLP路线：Word2Vec → BERT → GPT → ChatGPT
    → 越来越好的语言理解和生成
    → 但没有身体，无法行动

机器人路线：经典控制 → RL → 模仿学习
    → 越来越好的动作控制
    → 但不理解语言，泛化差
```

**VLA：真正的融合**

```
不是"语言模型 + 规则接口"（SayCan的方式）
而是"在同一个Transformer中共同学习语言和动作"（RT-2的方式）
```

---

## Part 2：颗粒级原理拆解

### 2.1 演进路线：SayCan → RT-1 → RT-2

#### SayCan（Google 2022）：第一次融合，但不够深

```
架构：
    LLM（PaLM） → 生成可能的动作候选（自然语言描述）
    可供性函数（Affordance Function） → 每个动作的可行性评分
    组合：LLM概率 × 可供性评分 → 最终动作选择

例子：
    指令："帮我去厨房拿瓶水"
    LLM生成候选："走到厨房"/"拿起水瓶"/"找到冰箱"
    可供性评分：每个动作在当前观测下能执行的概率
    选择：概率最高的动作
```

**局限**：LLM和可供性函数是分离的，不是端到端训练的。

#### RT-1（Google DeepMind 2022）：大规模机器人数据基础

```
架构：
    EfficientNet（视觉编码）+ 语言编码
    Transformer解码器
    输出：离散化的动作token（11维，每维256个bucket）

训练数据：
    130,000个机器人操作episodes
    700+个不同任务
    13个UR5机械臂，在Google办公室连续收集数据

成就：
    在评估机器人上达到97%的成功率
    首次展示大规模机器人数据的价值
```

#### RT-2（Google DeepMind 2023）：互联网知识注入机器人

```
核心创新：用VLM（视觉语言模型）初始化机器人策略

架构：
    底座：PaLI-X（55B参数）或 PaLM-E
    输入：图像 + 语言指令
    输出：动作token（与语言token统一编码空间）

关键设计：
    动作表示：连续动作离散化为token
        关节角度 → 256个bucket → token
        与语言token在同一词汇表

训练策略：
    大量网络图文数据（VLM预训练）
    + 少量机器人操作数据（微调）
    两种数据混合训练（保持通用能力）

零样本泛化示例：
    "把石榴石色的积木放到纸上"
    → RT-2从未见过"石榴石"这个颜色
    → 但因为预训练学到了"石榴石=暗红色"
    → 成功识别并操作
```

**关键数字**：
- RT-2的零样本泛化能力是RT-1的**2倍**（62% vs 32%）
- 但推理速度慢（55B参数 → ~1 action/second）

### 2.2 VLA架构设计深度解析

#### 动作Tokenization（最关键的设计决策）

```
问题：如何将连续动作纳入离散token框架？

方案1：直接输出连续值（Octo/ACT的方式）
    优点：精度高，无信息损失
    缺点：与语言模型框架不兼容

方案2：均匀离散化（RT-1/RT-2的方式）
    关节角度范围 [θ_min, θ_max] → 256个等间距bucket
    优点：统一token空间
    缺点：精度受bucket数量限制

方案3：Flow Matching（π₀的方式）
    用连续流模型生成连续动作
    优点：精度高，分布表达能力强
    缺点：计算量大
```

**精度分析：**
```
6自由度机械臂，每个关节范围±3.14rad：
    256个bucket → 精度约 0.025 rad（约1.4°）
    对精密操作（<0.1°）不够用
    对一般抓取（>1°容差）够用
```

#### 行动头（Action Head）设计

```
主流设计1：Tokenization + 自回归解码（RT-2风格）
    动作 = 关节角度的token序列
    生成：自回归逐个生成每个关节的token

主流设计2：扩散头（Diffusion Head）
    视觉语言特征 → 扩散模型 → 连续动作序列
    优点：精度高，可建模多模态分布

主流设计3：流匹配头（Flow Matching Head，π₀）
    更高效的生成过程（比DDPM步数少）
    π₀的核心技术贡献
```

#### 数据混合（Data Mixing）

**VLA的训练数据由两部分组成：**

```
1. 通用VLM数据（大量）：
    图文对（问答/图像描述）
    比例：约80%
    作用：保持语言理解和视觉感知能力

2. 机器人操作数据（少量）：
    (图像序列, 语言指令, 关节角度序列) 三元组
    比例：约20%
    作用：学习动作生成

关键发现：
    直接用机器人数据微调（不混合通用数据）→ 语言能力灾难性遗忘
    必须持续混合通用数据 → 保持泛化能力
```

### 2.3 π₀：Flow Matching + VLA

**Physical Intelligence 2024，当前最先进的开放权重VLA**

```
π₀架构：
    基础：PaliGemma（视觉语言模型）
    动作头：Flow Matching

Flow Matching vs DDPM：
    DDPM：T=100步去噪，每步预测噪声
    Flow Matching：学习直接从噪声到动作的"流"
        → 可以用更少步数生成高质量动作
        → 推理更快（50ms vs 2000ms）

π₀的关键数字：
    训练数据：~10,000小时遥操作数据
    参数量：3B（PaliGemma 3B + 动作头）
    推理速度：50Hz（实时控制可行）
    学习新任务：3小时遥操作数据
```

**与前代的比较：**

| 指标 | RT-2 | OpenVLA | π₀ |
|------|------|---------|-----|
| 参数量 | 55B | 7B | 3B |
| 推理速度 | ~1Hz | ~10Hz | ~50Hz |
| 动作精度 | 中 | 中 | 高 |
| 开源 | 否 | 是 | 是（权重） |

### 2.4 OpenVLA：可实践的开源VLA

**Stanford/UC Berkeley 2024**

```
架构：
    视觉：DINOv2 + SigLIP（双编码器）
    语言：Llama 2-7B
    动作头：简单MLP（输出离散化关节角度）

训练数据：
    Open X-Embodiment数据集（970k episodes）
    涵盖20+机器人平台

优势（相比RT-2）：
    ✓ 完全开源（模型权重+训练代码）
    ✓ 参数量7B（vs RT-2的55B），可在单A100上运行
    ✓ 支持LoRA微调（新任务快速适应）

局限：
    × 动作精度低于π₀
    × 单摄像头输入（π₀支持多视角）
```

### 2.5 与模仿学习/RL的关系

**VLA在技术栈中的位置：**

```
技术层次：

高层（任务理解层）：
    输入：语言指令 + 视觉观测
    VLA：理解意图 → 生成高层动作

低层（执行控制层）：
    输入：VLA输出的关节角度序列
    传统控制器：执行轨迹，保证安全

VLA ≠ 替代模仿学习
VLA = 更通用的模仿学习（在大规模数据上）

VLA ≠ 替代RL
VLA = RL的上游（先VLA预训练，再RL微调）
```

**与模仿学习的关系：**
- Diffusion Policy/ACT = 小规模、任务专用模仿学习
- VLA = 大规模、通用模仿学习（用语言作为条件）
- VLA可以用少量任务数据fine-tune，相当于更通用的AC

**与RL的关系：**
- VLA通常用模仿学习训练（不用RL，因为数据收集成本）
- 但VLA可以作为RL的初始化策略（π₀在某些任务上用RL进一步优化）

### 2.6 面试考点

**Q：RT-2相比RT-1的核心创新是什么？为什么重要？**

核心创新：用大规模VLM（而非从头训练）初始化机器人策略。

重要性：
1. 互联网规模的视觉-语言知识被注入机器人策略
2. 实现零样本泛化（见过词义，没见过物体，能操作）
3. 数据效率提升（不需要为每个新概念收集机器人数据）

**Q：动作Tokenization的核心挑战是什么？π₀如何解决精度问题？**

核心挑战：连续动作的精度损失（256个bucket对精密操作不够）

π₀的解决方案：放弃Tokenization，用Flow Matching直接生成连续动作值，避免量化损失，同时保持与语言模型的融合。

**Q：为什么VLA训练时必须混合通用数据和机器人数据？**

防止灾难性遗忘（Catastrophic Forgetting）：
- 只训练机器人数据 → 覆盖预训练获得的语言/视觉能力
- 混合训练 → 保持通用能力（泛化）+ 获得机器人能力
- 这是VLA区别于纯机器人模型的关键工程考量

---

## Part 3：代码实践

### 目标：用OpenVLA推理演示（API调用）

```python
"""
OpenVLA推理演示
注意：运行此代码需要访问HuggingFace，并有足够GPU内存（≥16GB）
如果没有GPU，可以用CPU运行（速度较慢）
"""
import torch
from PIL import Image
import requests
from io import BytesIO

# =========================================
# 方案1：直接使用OpenVLA（需要GPU）
# =========================================
def demo_openvla_inference():
    """OpenVLA完整推理演示"""
    try:
        from transformers import AutoModelForVision2Seq, AutoProcessor

        # 加载模型（首次运行会下载~14GB）
        print("加载OpenVLA模型（首次需要下载，约14GB）...")
        processor = AutoProcessor.from_pretrained(
            "openvla/openvla-7b",
            trust_remote_code=True
        )
        vla = AutoModelForVision2Seq.from_pretrained(
            "openvla/openvla-7b",
            attn_implementation="flash_attention_2",
            torch_dtype=torch.bfloat16,
            low_cpu_mem_usage=True,
            trust_remote_code=True,
        ).to("cuda:0")

        # 准备输入：机器人摄像头图像 + 语言指令
        # 实际使用时替换为真实机器人摄像头图像
        image = Image.new('RGB', (224, 224), color=(128, 128, 128))

        instruction = "pick up the red cup and place it on the plate"

        # 构建输入
        prompt = f"In: What action should the robot take to {instruction}?\nOut:"

        inputs = processor(prompt, image).to("cuda:0", dtype=torch.bfloat16)

        # 推理（生成动作）
        print(f"指令: {instruction}")
        print("推理中...")

        with torch.no_grad():
            action = vla.predict_action(
                **inputs,
                unnorm_key="bridge_orig",  # 动作归一化键（与训练数据对应）
                do_sample=False
            )

        print(f"预测动作（7维关节角度+夹爪）: {action}")
        print(f"动作维度: {action.shape}")

    except ImportError:
        print("需要安装: pip install transformers accelerate")
        demo_openvla_mock()
    except Exception as e:
        print(f"GPU推理失败（{e}），使用模拟演示")
        demo_openvla_mock()


# =========================================
# 方案2：模拟VLA接口（无需GPU）
# =========================================
def demo_openvla_mock():
    """
    模拟OpenVLA接口，演示VLA的输入输出格式
    用于没有GPU环境的学习
    """
    import numpy as np

    print("=== OpenVLA接口模拟演示 ===\n")

    # VLA的标准输入格式
    class MockVLAInput:
        def __init__(self, image, instruction):
            self.image = image          # PIL Image，通常 224×224 或 256×256
            self.instruction = instruction  # 自然语言指令

    # VLA的标准输出格式
    def mock_vla_predict(input_data):
        """
        模拟VLA的输出：7维关节角度 + 1维夹爪状态
        真实OpenVLA输出的是归一化后的关节角度
        """
        action_dim = 7  # 6关节 + 1夹爪（WidowX250s构型）

        # 模拟：根据指令生成不同的动作倾向
        if "pick" in input_data.instruction.lower():
            # 抓取动作：末端向下，夹爪闭合
            action = np.array([0.0, 0.1, -0.2, 0.0, 0.3, 0.0, 1.0])
        elif "place" in input_data.instruction.lower():
            # 放置动作：末端移向目标，夹爪打开
            action = np.array([0.1, 0.0, -0.1, 0.0, 0.2, 0.0, -1.0])
        else:
            action = np.random.uniform(-0.1, 0.1, action_dim)

        return action

    # 演示不同指令的输出差异
    test_cases = [
        "pick up the red block",
        "place the block on the table",
        "push the object to the left",
        "open the drawer",
    ]

    print("指令 → VLA输出（7维动作）\n")
    for instruction in test_cases:
        mock_input = MockVLAInput(
            image=None,
            instruction=instruction
        )
        action = mock_vla_predict(mock_input)
        print(f"指令: '{instruction}'")
        print(f"动作: {action}")
        print(f"  关节1-6: {action[:6]}")
        print(f"  夹爪: {'闭合' if action[6] > 0 else '打开'} ({action[6]:.2f})")
        print()


# =========================================
# OpenVLA LoRA 微调示例（框架）
# =========================================
def demo_openvla_finetuning():
    """
    展示如何用LoRA微调OpenVLA到新任务
    实际运行需要GPU + 任务数据集
    """
    print("=== OpenVLA LoRA微调流程 ===\n")

    print("""
微调步骤：

1. 准备任务数据集（LeRobot格式）
   数据格式：
   {
     "image": [H, W, 3] numpy array,
     "instruction": "task description string",
     "action": [T, action_dim] numpy array  # T步动作序列
   }

2. 配置LoRA
   from peft import LoraConfig, get_peft_model
   lora_config = LoraConfig(
       r=32,                  # LoRA秩（越大越有表达力，但更慢）
       lora_alpha=32,
       target_modules=["q_proj", "v_proj"],  # 只微调注意力层
       lora_dropout=0.05,
       bias="none",
   )

3. 训练
   通常只需要：
   - 50-200个任务demos
   - 1-2小时训练（单A100）
   - 学习率 1e-4，batch_size=4

4. 评估
   在真实机器人上测试成功率
   对比：基础OpenVLA（无微调）vs 微调后
   通常微调后成功率提升20-40%
""")


if __name__ == "__main__":
    print("=== VLA推理演示 ===\n")

    # 优先尝试真实推理，失败则用模拟
    demo_openvla_inference()

    print("\n=== 微调流程演示 ===")
    demo_openvla_finetuning()
```

---

## Part 4：产品视角

### VLA的商业化格局

**当前可商业化的VLA路径：**

| 路径 | 代表 | 优势 | 劣势 |
|------|------|------|------|
| 自研VLA | Tesla, Physical Intelligence | 数据专有，能力领先 | 投入大，需要数据飞轮 |
| 基于OpenVLA微调 | 中小型机器人公司 | 门槛低，速度快 | 通用能力受限，精度有限 |
| API调用方式 | 未来的"机器人GPT API" | 使用方便 | 延迟/隐私/成本 |

**π₀的商业模式（Physical Intelligence）：**
```
商业模式：
    核心：自研VLA（π₀）作为平台
    收入来源：
        1. 机器人公司授权（让机器人集成π₀能力）
        2. 部署服务（提供部署和数据飞轮服务）
        3. 可能的API访问

护城河：
    数据飞轮（更多部署→更多数据→更好模型）
    算法领先（Flow Matching，当前最好的动作生成）
```

### VLA的技术成熟度评估（TRL）

| 维度 | 当前状态（2026） | 预计商业成熟（年份） |
|------|----------------|-------------------|
| 操作精度 | 中等（抓取成功率约80%） | 2027 |
| 指令理解 | 较好（日常语言） | 2026 |
| 任务多样性 | 50+任务同时 | 2028 |
| 鲁棒性 | 弱（环境变化敏感） | 2028 |
| 推理速度 | 50Hz（π₀）| 已满足 |
| 部署成本 | 高（需要GPU） | 边缘计算优化中 |

---

## Part 5：作业与测试

### 作业

**必做**：运行mock_vla_predict演示，并思考：
1. 为什么VLA的输出是连续的关节角度，而不是"向右移动"这样的语言指令？
2. 设计一个5步的测试用例：指令从简单到复杂，预测VLA对哪类指令更容易/困难
3. 阅读RT-2论文摘要，找出与OpenVLA的关键架构差异

**选做（ML背景）**：
- 用HuggingFace加载OpenVLA，在本地（CPU可以）运行推理
- 实现一个简单的LoRA微调demo（使用CartPole或其他简单任务作为代理数据）

**选做（机器人背景）**：
- 搭建一个简单的VLA部署框架：
  - 摄像头读取图像 → OpenVLA API → 动作输出 → PyBullet执行
  - 验证端到端延迟

### 测试题

1. RT-2的"动作Tokenization"是什么意思？为什么这个设计能让机器人动作和语言统一处理？

2. 为什么VLA训练时必须同时保留通用视觉语言数据？如果只用机器人数据微调会发生什么？

3. π₀使用Flow Matching而非DDPM，有什么实际的工程优势？（从推理速度、精度两个角度回答）

4. 如果你要用VLA开发一个"家庭收纳机器人"，你会选择直接使用OpenVLA还是自研VLA？请从数据、算法、成本三个维度说明你的判断。

---

## 质量检查清单

- [x] YAML元信息完整
- [x] Wow Moment：RT-2石榴石色积木，具体且有冲击力
- [x] Part 0：语言模型和机器人各自局限的清晰对比
- [x] 1+1>2的融合逻辑（分三段）
- [x] SayCan → RT-1 → RT-2演进路线完整
- [x] VLA架构三大设计：Tokenization/Action Head/Data Mixing
- [x] π₀的Flow Matching和关键数字
- [x] OpenVLA与RT-2的对比表
- [x] 与模仿学习/RL的关系厘清
- [x] 代码：真实API调用 + Mock演示（双备份）
- [x] 代码：LoRA微调流程框架
- [x] 面试考点：RT-2创新/Tokenization/混合训练
- [x] 产品视角：商业化路径/π₀商业模式/TRL评估
- [x] ML和机器人背景的差异化提示

---

*具身智能系列 · EI-07 · 上一课：EI-06 Sim-to-Real · 下一课：EI-08 通用具身智能*
