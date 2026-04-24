# 课程 1：Transformer 架构原理和应用

> **学习时长**：60 分钟
> **先修知识**：基础深度学习概念（神经网络、反向传播）
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 1.1 为什么需要 Transformer？

在 Transformer 之前，NLP 领域的主流是 **RNN（循环神经网络）** 和 **LSTM（长短期记忆网络）**。

**RNN 的核心问题**：
- **序列依赖**：必须按顺序处理，无法并行计算
- **长程遗忘**：即使 LSTM 改善了，处理超长序列时仍然丢失早期信息

```
RNN 处理序列：
h₁ → h₂ → h₃ → ... → hₙ  （必须按顺序，无法并行）

如果第100个词依赖第1个词的信息，中间98步的传递会丢失细节
```

**Transformer 的突破**（2017 年，论文《Attention Is All You Need》）：
- 完全抛弃循环结构，只用 **Attention（注意力机制）**
- 支持**并行计算**，训练速度大幅提升
- 直接建模任意两个位置的关系，不受距离影响

### 1.2 Transformer 整体架构

```
                    Transformer
           ┌─────────────────────────┐
           │       Encoder-Decoder    │
           │                          │
  输入 ──→ │  Encoder (编码器)        │
  (源语言) │    × N 层                │
           │                          │
           │  Decoder (解码器)        │ ──→ 输出
           │    × N 层                │    (目标语言)
           └─────────────────────────┘
```

**核心组件**（从下往上）：

1. **输入嵌入（Input Embedding）**：将词转为向量
2. **位置编码（Positional Encoding）**：给序列添加位置信息
3. **多头注意力（Multi-Head Attention）**：核心机制
4. **前馈网络（Feed-Forward Network）**：每层内部的 MLP
5. **层归一化 + 残差连接（LayerNorm + Residual）**

### 1.3 自注意力机制（Self-Attention）—— 核心！

**一句话理解**：每个词都会"关注"序列中的所有其他词，根据相关性分配权重。

**计算过程**（这是面试/考试必考点）：

```
步骤1：为每个词生成三个向量
   Q (Query)  —— "我在找什么"
   K (Key)    —— "我有什么"
   V (Value)  —— "我的实际内容"

步骤2：计算注意力分数
   Attention(Q, K, V) = softmax(Q × K^T / √d_k) × V

   其中：
   - Q × K^T：计算每个词对之间的相关性
   - √d_k：缩放因子，防止点积过大导致 softmax 饱和
   - softmax：归一化为概率分布
   - × V：加权求和得到输出
```

**为什么要除以 √d_k？**
- 当 d_k 很大时，Q×K 的点积值会非常大
- 大值输入 softmax 会导致梯度趋近于 0（梯度消失）
- 除以 √d_k 使方差稳定在 1 附近

### 1.4 多头注意力（Multi-Head Attention）

**一句话理解**：把 Q、K、V 分成多组，分别计算注意力，最后拼接。

```
单头注意力 = 一个视角看关系
多头注意力 = 多个视角看关系（如语法关系、语义关系、指代关系...）

MultiHead(Q, K, V) = Concat(head₁, head₂, ..., headₕ) × W^O

其中 headᵢ = Attention(Q×Wᵢ^Q, K×Wᵢ^K, V×Wᵢ^V)
```

**为什么有效？**
- 不同头可以学到不同类型的关系
- 类似人类理解一句话时，同时关注语法、语义、情感等多个维度

### 1.5 位置编码（Positional Encoding）

**问题**：Transformer 没有 RNN 的顺序概念，怎么知道"我"在"你"前面？

**解决方案**：给每个位置添加一个唯一的编码向量。

原始 Transformer 使用**正弦/余弦函数**：

```
PE(pos, 2i)   = sin(pos / 10000^(2i/d_model))
PE(pos, 2i+1) = cos(pos / 10000^(2i/d_model))
```

**为什么用三角函数？**
- 可以让模型学习到相对位置关系：PE(pos+k) 可以表示为 PE(pos) 的线性函数
- 即使训练时没见过这么长的序列，也能泛化

### 1.6 Encoder vs Decoder 的区别

| 组件 | Encoder | Decoder |
|------|---------|---------|
| 自注意力 | 双向（看所有词） | 单向（Masked，只能看前面的词） |
| 交叉注意力 | 无 | 有（关注 Encoder 的输出） |
| 用途 | 理解/编码输入 | 生成/解码输出 |

**Decoder 的 Masked Attention**：
- 生成时只能"看到"已经生成的词，不能"偷看"未来
- 训练时通过 mask 矩阵实现（下三角矩阵）

### 1.7 现代演进

| 变体 | 年份 | 特点 |
|------|------|------|
| BERT | 2018 | 只用 Encoder，双向理解，擅长分类/问答 |
| GPT | 2018- | 只用 Decoder，单向生成，擅长创作/对话 |
| T5 | 2019 | Encoder-Decoder 统一框架，所有任务转为文本生成 |
| LLaMA | 2023 | Decoder-only，高效预训练，开源生态繁荣 |

---

## 二、代码实践（25 分钟）

### 2.1 用 PyTorch 从零实现 Self-Attention

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import math

class SelfAttention(nn.Module):
    """自注意力机制的 PyTorch 实现"""
    
    def __init__(self, d_model: int):
        super().__init__()
        self.d_model = d_model
        
        # 学习 Q、K、V 的投影矩阵
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)  # 输出投影
    
    def forward(self, x: torch.Tensor, mask: torch.Tensor = None):
        """
        Args:
            x: 输入张量，形状 (batch_size, seq_len, d_model)
            mask: 可选的 mask 矩阵
        
        Returns:
            输出张量，形状 (batch_size, seq_len, d_model)
            注意力权重，形状 (batch_size, seq_len, seq_len)
        """
        batch_size, seq_len, _ = x.shape
        
        # 步骤1：生成 Q、K、V
        Q = self.W_q(x)  # (batch, seq, d_model)
        K = self.W_k(x)
        V = self.W_v(x)
        
        # 步骤2：计算注意力分数
        # Q × K^T → (batch, seq, seq)
        scores = torch.matmul(Q, K.transpose(-2, -1))
        
        # 步骤3：缩放（除以 √d_k）
        scores = scores / math.sqrt(self.d_model)
        
        # 步骤4：应用 mask（如果有）
        if mask is not None:
            scores = scores.masked_fill(mask == 0, float('-inf'))
        
        # 步骤5：softmax 归一化
        attention_weights = F.softmax(scores, dim=-1)
        
        # 步骤6：加权求和 V
        output = torch.matmul(attention_weights, V)
        
        # 步骤7：输出投影
        output = self.W_o(output)
        
        return output, attention_weights


# ========== 动手测试 ==========
if __name__ == "__main__":
    # 创建测试数据：2个句子，每个5个词，每个词64维
    batch_size = 2
    seq_len = 5
    d_model = 64
    
    x = torch.randn(batch_size, seq_len, d_model)
    
    attention = SelfAttention(d_model)
    output, weights = attention(x)
    
    print(f"输入形状: {x.shape}")
    print(f"输出形状: {output.shape}")
    print(f"注意力权重形状: {weights.shape}")
    
    # 查看第一个样本第一个词的注意力分布
    print(f"\n第一个词对其他词的关注度:")
    print(f"  {weights[0, 0, :].detach().numpy().round(3)}")
    print(f"  总和: {weights[0, 0, :].sum().item():.4f}")
```

### 2.2 实现 Multi-Head Attention

```python
class MultiHeadAttention(nn.Module):
    """多头注意力"""
    
    def __init__(self, d_model: int, num_heads: int):
        super().__init__()
        assert d_model % num_heads == 0, "d_model 必须能被 num_heads 整除"
        
        self.d_model = d_model
        self.num_heads = num_heads
        self.d_k = d_model // num_heads  # 每个头的维度
        
        # 为所有头创建 Q、K、V 投影（用一个大矩阵然后分割）
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)
    
    def forward(self, x: torch.Tensor, mask: torch.Tensor = None):
        batch_size, seq_len, _ = x.shape
        
        # 步骤1：生成 Q、K、V 并 reshape 为多头
        Q = self.W_q(x).view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)
        K = self.W_k(x).view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)
        V = self.W_v(x).view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)
        # 现在形状: (batch, num_heads, seq, d_k)
        
        # 步骤2：计算注意力
        scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_k)
        if mask is not None:
            scores = scores.masked_fill(mask == 0, float('-inf'))
        attention_weights = F.softmax(scores, dim=-1)
        
        # 步骤3：加权求和
        output = torch.matmul(attention_weights, V)
        
        # 步骤4：拼接多头结果
        output = output.transpose(1, 2).contiguous().view(batch_size, seq_len, self.d_model)
        
        # 步骤5：输出投影
        output = self.W_o(output)
        
        return output, attention_weights
```

### 2.3 使用 Hugging Face Transformers 查看预训练模型

```python
from transformers import AutoModel, AutoTokenizer
import torch

# 加载一个小型预训练模型
model_name = "bert-base-uncased"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModel.from_pretrained(model_name)

# 输入文本
text = "The cat sat on the mat and looked at the dog."
inputs = tokenizer(text, return_tensors="pt")

print(f"分词结果: {tokenizer.tokenize(text)}")
print(f"输入 ID 形状: {inputs['input_ids'].shape}")

# 获取模型输出
with torch.no_grad():
    outputs = model(**inputs)

print(f"隐藏状态形状: {outputs.last_hidden_state.shape}")
# (batch_size=1, seq_len=14, hidden_size=768)
```

---

## 三、作业与思考题（10 分钟）

### 基础题

1. **计算题**：如果输入序列长度为 10，d_model=512，计算 Self-Attention 中 Q×K^T 的结果矩阵维度是多少？

2. **理解题**：为什么 Transformer 需要位置编码？如果去掉位置编码会发生什么？

3. **比较题**：RNN 和 Transformer 在处理长序列时各有什么优劣？

### 进阶题

4. **推导题**：解释为什么 Self-Attention 要除以 √d_k，如果不除会发生什么？请从梯度角度分析。

5. **设计题**：如果要实现一个处理 100 万字长文档的 Transformer，你会遇到什么问题？如何解决？

### 实践题

6. **代码题**：在上面的 SelfAttention 实现中，添加 Dropout 正则化，并实现 Layer Normalization。

---

## 四、扩展阅读

### 必读
- 📄 **Attention Is All You Need** (Vaswani et al., 2017) — Transformer 原始论文
  - 重点阅读第 3 节（Model Architecture）
- 🎥 **The Illustrated Transformer** (Jay Alammar) — 图解 Transformer
  - https://jalammar.github.io/illustrated-transformer/

### 选读
- 📄 **Annotated Transformer** — 带注释的 PyTorch 实现
  - https://nlp.seas.harvard.edu/2018-04-03-attention/
- 🎥 **Andrej Karpathy - Let's build GPT** (YouTube)
  - 从零实现 GPT 的视频教程，适合理解 Decoder-only 架构

---

## 附录：关键公式速查

| 公式 | 含义 |
|------|------|
| `Attention(Q, K, V) = softmax(QK^T/√d_k)V` | 缩放点积注意力 |
| `MultiHead(Q,K,V) = Concat(head₁,...,headₕ)W^O` | 多头注意力 |
| `headᵢ = Attention(QWᵢ^Q, KWᵢ^K, VWᵢ^V)` | 单头注意力 |
| `PE(pos,2i) = sin(pos/10000^(2i/d))` | 位置编码（正弦） |
| `PE(pos,2i+1) = cos(pos/10000^(2i/d))` | 位置编码（余弦） |
