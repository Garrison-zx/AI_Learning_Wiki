# 课程 3：大模型基础知识（架构、底层原理）

> **学习时长**：60 分钟
> **先修知识**：课程 1-2（Transformer + BERT/T5）
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 3.1 什么是"大模型"？

**定义**：基于 Transformer 架构，参数规模 ≥ 1B（10 亿）的语言模型。

**三个核心特征**：
1. **规模大**：参数从 B 级到 T 级
2. **数据多**：训练数据从数百 GB 到数十 TB
3. **涌现能力（Emergent Abilities）**：规模达到临界点后出现的小模型没有的能力

### 3.2 大模型的核心架构演进

```
Decoder-only 架构演进路线（主流大模型均采用此架构）

GPT (2018)          → GPT-2 (2019)       → GPT-3 (2020)
117M 参数             1.5B                  175B
│                     │                     │
▼                     ▼                     ▼
LayerNorm 前置        ↓                     ↓
(Pre-Norm)            ▼                     ▼
                      GPT-NeoX → LLaMA (2023) → LLaMA 2/3 (2023-2024)
                      开源大模型              7B/13B/70B → 8B/70B/405B
                                               │
                                               ▼
                                          Mistral, Qwen, DeepSeek...
```

### 3.3 现代大模型的关键改进

#### 改进 1：Pre-Norm vs Post-Norm

```
Post-Norm（原始 Transformer）:     Pre-Norm（现代大模型）:
x → Attention → Add → Norm → ...  x → Norm → Attention → Add → ...
                                    │
                                    更稳定，不易梯度爆炸
                                    可以训练更深的网络
```

#### 改进 2：RoPE（Rotary Position Embedding）

- 替代原始正弦位置编码
- 通过旋转矩阵注入位置信息
- **优势**：支持外推（训练时没见过的位置也能处理）

```
RoPE 的核心思想：
Q 和 K 做点积时，天然包含相对位置信息
不需要显式添加位置编码向量
```

#### 改进 3：SwiGLU 激活函数

```
传统：ReLU(x) = max(0, x)
现代：SwiGLU(x) = x ⊗ σ(βx) ⊗ Wx
                  （门控机制，表达能力更强）
```

#### 改进 4：RMSNorm

```
替代 LayerNorm，去掉均值中心化，只保留方差归一化
计算更快，效果相当
```

#### 改进 5：GQA（Grouped-Query Attention）

```
MQA（Multi-Query Attention）: 所有头共享一组 K、V（快但精度降）
GQA（Grouped-Query Attention）: 头分组，组内共享 K、V（折中方案）

LLaMA 2/3、Mistral 均采用 GQA
```

### 3.4 训练过程三阶段

```
阶段1：预训练（Pre-training）
   输入：海量无标注文本（数 TB）
   目标：Next Token Prediction
   产出：基础语言模型（Base Model）
   算力：数千 GPU × 数月

阶段2：有监督微调（SFT）
   输入：高质量指令-回答对
   目标：学会遵循指令
   产出：指令跟随模型（Instruct Model）
   算力：数十 GPU × 数天

阶段3：人类对齐（Alignment）
   方法：RLHF / DPO / ORPO
   目标：符合人类偏好，减少有害输出
   产出：对齐模型（Chat Model）
   算力：数十 GPU × 数天
```

### 3.5 Scaling Laws

**核心发现**：模型性能遵循幂律关系

```
Loss ≈ (N₀/N)^α + (D₀/D)^β + L∞

其中：
- N = 参数量
- D = 训练数据量
- α, β ≈ 0.3-0.5（经验值）
- 结论：参数和数据都要按比例扩大
```

**Chinchilla 最优比例**（DeepMind, 2022）：
- 最优数据量 ≈ 20 × 参数量（以 token 计）
- 例如：70B 参数 → 需要 ~1.4T token

### 3.6 Tokenization（分词）

**为什么需要分词**：模型处理的是离散 token，不是原始文本。

| 方法 | 原理 | 词表大小 | 示例 |
|------|------|---------|------|
| Word | 按词分割 | ~50K | "hello" → "hello" |
| BPE | 合并最常见字符对 | ~50K | "running" → "run" + "ning" |
| WordPiece | 类似 BPE，用似然比 | ~30K | "playing" → "play" + "ing" |
| SentencePiece | 无监督，支持多语言 | ~32K-256K | "你好" → "▁你" + "▁好" |

**现代大模型的词表**：
- LLaMA 3：128K
- Qwen 2.5：152K
- GPT-4：~100K（估计）

---

## 二、代码实践（25 分钟）

### 3.1 手动实现 RMSNorm

```python
import torch
import torch.nn as nn

class RMSNorm(nn.Module):
    """Root Mean Square Layer Normalization"""
    
    def __init__(self, dim: int, eps: float = 1e-6):
        super().__init__()
        self.eps = eps
        self.weight = nn.Parameter(torch.ones(dim))  # 可学习的缩放参数
    
    def forward(self, x: torch.Tensor):
        # 计算均方根
        rms = torch.sqrt(x.pow(2).mean(-1, keepdim=True) + self.eps)
        # 归一化 + 缩放
        return x / rms * self.weight


# 对比 LayerNorm 和 RMSNorm
x = torch.randn(2, 4, 8)  # (batch, seq, dim)

layer_norm = nn.LayerNorm(8)
rms_norm = RMSNorm(8)

out_ln = layer_norm(x)
out_rms = rms_norm(x)

print(f"LayerNorm 输出均值: {out_ln.mean().item():.6f}")  # 接近 0
print(f"RMSNorm 输出均值: {out_rms.mean().item():.6f}")   # 不一定为 0（无均值中心化）
print(f"LayerNorm 输出方差: {out_ln.var().item():.6f}")
print(f"RMSNorm 输出方差: {out_rms.var().item():.6f}")
```

### 3.2 RoPE（旋转位置编码）的简化实现

```python
import torch

def apply_rotary_pos_emb(q, k, freqs):
    """
    应用旋转位置编码
    
    Args:
        q: Query 向量 (batch, seq, heads, dim)
        k: Key 向量 (batch, seq, heads, dim)
        freqs: 预计算的旋转频率
    """
    # 分离实部和虚部
    q_real, q_imag = q[..., ::2], q[..., 1::2]
    k_real, k_imag = k[..., ::2], k[..., 1::2]
    
    # 旋转
    cos, sin = freqs.cos(), freqs.sin()
    
    q_rot = torch.stack([
        q_real * cos - q_imag * sin,
        q_real * sin + q_imag * cos
    ], dim=-1).flatten(-2)
    
    k_rot = torch.stack([
        k_real * cos - k_imag * sin,
        k_real * sin + k_imag * cos
    ], dim=-1).flatten(-2)
    
    return q_rot, k_rot


def precompute_freqs(dim: int, seq_len: int, base: float = 10000.0):
    """预计算旋转频率"""
    freqs = 1.0 / (base ** (torch.arange(0, dim, 2).float() / dim))
    t = torch.arange(seq_len).float()
    freqs = torch.outer(t, freqs)  # (seq_len, dim/2)
    return freqs


# 测试
dim = 64
seq_len = 10
heads = 8

freqs = precompute_freqs(dim // heads, seq_len)
print(f"频率矩阵形状: {freqs.shape}")

q = torch.randn(1, seq_len, heads, dim // heads)
k = torch.randn(1, seq_len, heads, dim // heads)

q_rot, k_rot = apply_rotary_pos_emb(q, k, freqs)
print(f"RoPE 后 Q 形状: {q_rot.shape}")

# 验证：不同位置的 token 有不同的旋转角度
print(f"位置 0 的旋转角度 (前4维): {freqs[0, :4].numpy().round(4)}")
print(f"位置 5 的旋转角度 (前4维): {freqs[5, :4].numpy().round(4)}")
```

### 3.3 计算模型参数量和 FLOPs

```python
def count_parameters(model_name: str, config: dict):
    """计算 Transformer 模型的参数量"""
    n_layers = config['n_layers']
    n_heads = config['n_heads']
    d_model = config['d_model']
    d_ff = config.get('d_ff', 4 * d_model)  # FFN 中间层维度
    vocab_size = config.get('vocab_size', 32000)
    
    # Embedding 层
    embed_params = vocab_size * d_model
    
    # 每层的参数
    per_layer = (
        4 * d_model * d_model  # Q, K, V, O 投影
        + d_model * d_ff * 2   # FFN 两层
        + 2 * d_model          # RMSNorm 参数
    )
    
    # 总参数
    total = embed_params + n_layers * per_layer
    
    print(f"=== {model_name} ===")
    print(f"  层数: {n_layers}")
    print(f"  隐藏维度: {d_model}")
    print(f"  注意力头数: {n_heads}")
    print(f"  Embedding: {embed_params / 1e6:.1f}M")
    print(f"  每层参数: {per_layer / 1e6:.1f}M")
    print(f"  总参数量: {total / 1e9:.2f}B")
    print()
    return total


# 计算几个经典模型
count_parameters("LLaMA-3-8B", {
    'n_layers': 32, 'n_heads': 32, 'd_model': 4096,
    'vocab_size': 128256, 'd_ff': 14336  # SwiGLU 用更大的 FFN
})

count_parameters("LLaMA-3-70B", {
    'n_layers': 80, 'n_heads': 64, 'd_model': 8192,
    'vocab_size': 128256, 'd_ff': 28672
})

count_parameters("GPT-3-175B", {
    'n_layers': 96, 'n_heads': 96, 'd_model': 12288,
    'vocab_size': 50257, 'd_ff': 49152
})
```

### 3.4 Tokenizer 实践

```python
from transformers import AutoTokenizer

# 对比不同分词器
models = {
    "LLaMA 3": "meta-llama/Meta-Llama-3-8B",
    "Qwen 2.5": "Qwen/Qwen2.5-7B",
    "GPT-2": "gpt2",
}

test_texts = [
    "Hello world!",
    "你好，世界！",
    "def fibonacci(n):\n    if n <= 1: return n\n    return fibonacci(n-1) + fibonacci(n-2)",
    "The 🚀 launched at 10:30 AM.",
]

for name, model_id in models.items():
    try:
        tokenizer = AutoTokenizer.from_pretrained(model_id, trust_remote_code=True)
        print(f"\n=== {name} ===")
        print(f"词表大小: {tokenizer.vocab_size}")
        
        for text in test_texts:
            tokens = tokenizer.encode(text)
            decoded = [tokenizer.decode([t]) for t in tokens]
            print(f"  '{text[:40]}' → {len(tokens)} tokens: {decoded}")
    except Exception as e:
        print(f"\n=== {name} === (加载失败: {e})")
```

---

## 三、作业与思考题（10 分钟）

### 基础题

1. **理解题**：为什么现代大模型都转向 Decoder-only 架构，而不是 Encoder-Decoder？

2. **计算题**：假设一个 LLaMA 模型有 32 层、d_model=4096、d_ff=14336，估算其总参数量。

3. **理解题**：Scaling Laws 告诉我们什么？如果只有 100B token 的训练数据，最大应该训练多大的模型？

### 进阶题

4. **分析题**：GQA 相比 MQA 和 MHA 各自的优劣是什么？为什么 LLaMA 2 选择了 GQA？

5. **设计题**：如果要训练一个面向工业场景的中文大模型，你会如何设计词表大小和训练数据配比？

---

## 四、扩展阅读

- 📄 **Attention Is All You Need** (Vaswani et al., 2017)
- 📄 **Training Compute-Optimal Large Language Models (Chinchilla)** (Hoffmann et al., 2022)
- 📄 **LLaMA: Open and Efficient Foundation Language Models** (Touvron et al., 2023)
- 📄 **RoFormer: Enhanced Transformer with Rotary Position Embedding** (Su et al., 2021)
- 📖 **The LLaMA 3 Herd of Models** (2024) — 详细架构文档
- 🌐 **LLaMA 技术报告解读**：搜索 "LLaMA 3 technical report 中文解读"
