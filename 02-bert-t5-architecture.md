# 课程 2：T5 和 BERT 架构

> **学习时长**：60 分钟
> **先修知识**：课程 1（Transformer 架构）
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 2.1 BERT 和 GPT 的分化

Transformer 原始架构是 Encoder-Decoder 结构，但后续两大分支走向了不同方向：

```
Transformer (2017)
    ├── Encoder-only → BERT 系列（理解任务）
    └── Decoder-only → GPT 系列（生成任务）
```

| 维度 | BERT（Encoder-only） | GPT（Decoder-only） |
|------|---------------------|---------------------|
| 预训练任务 | Masked LM（完形填空） | Next Token Prediction（续写） |
| 注意力 | 双向（每个词看所有词） | 单向（Masked，只能看前面） |
| 擅长任务 | 分类、NER、问答（抽取式） | 生成、翻译、创作 |
| 输出 | 最后一层 hidden states | 下一个 token 的概率分布 |

### 2.2 BERT（Bidirectional Encoder Representations from Transformers）

**核心思想**：让模型通过"完形填空"任务学习语言理解。

#### 预训练任务

**1. Masked Language Model (MLM)**
```
输入：The [MASK] sat on the [MASK].
目标：The cat sat on the mat.
```
- 随机掩盖 15% 的词
- 其中 80% 替换为 [MASK]
- 10% 替换为随机词（防止模型只记住 [MASK] 这个 token）
- 10% 保持不变（增加任务难度）

**2. Next Sentence Prediction (NSP)**
```
句子A：The cat sat on the mat.
句子B：It was a sunny day.
标签：IsNext / NotNext
```
- 判断两个句子是否是连续的文段
- 用于训练模型理解句子间关系

#### BERT 架构参数

| 版本 | 层数 | 隐藏维度 | 注意力头数 | 参数量 |
|------|------|---------|-----------|--------|
| BERT-Base | 12 | 768 | 12 | 110M |
| BERT-Large | 24 | 1024 | 16 | 340M |

#### BERT 的输入

```
[CLS] The cat sat on the mat . [SEP]
 │                          │
 └─ 分类任务的表示向量          └─ 句子分隔符
```

- `[CLS]` token 的最后隐藏状态用于分类任务
- `[SEP]` 分隔不同句子
- 添加了 **Token Type IDs** 区分两个句子
- 添加了 **Position IDs** 标记位置

### 2.3 T5（Text-To-Text Transfer Transformer）

**核心思想**：**所有 NLP 任务都可以统一为文本到文本的转换**。

#### Text-to-Text 框架

```
翻译：  "translate English to German: The cat sat on the mat" → "Die Katze saß auf der Matte"
分类：  "sst2 sentence: I love this movie" → "positive"
问答：  "question: What is the capital of France? context: Paris is the capital..." → "Paris"
摘要：  "summarize: The meeting discussed..." → "Key decisions made on budget..."
```

**统一的输入输出格式**：
- 输入 = 任务前缀 + 文本
- 输出 = 目标文本
- 一个模型解决所有任务

#### T5 架构

| 版本 | 层数 | 隐藏维度 | 头数 | 参数量 |
|------|------|---------|------|--------|
| T5-Small | 6 | 512 | 8 | 60M |
| T5-Base | 12 | 768 | 12 | 220M |
| T5-Large | 24 | 1024 | 16 | 770M |
| T5-3B | 24 | 1024 | 16 | 3B |
| T5-11B | 24 | 1024 | 16 | 11B |

**和原始 Transformer 的区别**：
1. **共享词嵌入**：Encoder 和 Decoder 共享同一个 Embedding 矩阵
2. **相对位置编码**：用相对位置替代绝对位置编码
3. **去掉 LayerNorm 的 bias 参数**
4. **预训练任务**：Span Corruption（文本损坏重建）

#### Span Corruption 预训练

```
原始文本：The cat sat on the mat and the dog barked loudly.
损坏后：  The <X> on the <Y> loudly.
输入：    The <X> on the <Y> loudly.
目标：    <X> cat sat <Y> mat and the dog barked
```
- 随机抽取连续文本片段替换为特殊 token
- 模型需要从上下文中恢复被移除的内容
- 比 BERT 的 MLM 更自然，更接近真实任务

### 2.4 BERT vs T5 对比

| 维度 | BERT | T5 |
|------|------|-----|
| 架构 | Encoder-only | Encoder-Decoder |
| 预训练 | MLM + NSP | Span Corruption |
| 任务范式 | 分类头/序列标注 | 统一 text-to-text |
| 适合场景 | 理解类任务（分类、抽取） | 生成类任务（翻译、摘要、问答） |
| 微调方式 | 添加分类头 | 统一用 text-to-text 格式 |

### 2.5 后续演进

**BERT 家族**：
- **RoBERTa**：去掉 NSP，更大 batch，更多训练数据
- **ALBERT**：参数共享 + 隐藏层维度压缩
- **DeBERTa**：解耦绝对位置和内容编码
- **ModernBERT**（2024）：最新改进，支持更长序列

**T5 家族**：
- **mT5**：多语言版本，覆盖 101 种语言
- **FLAN-T5**（2022）：指令微调版，零样本能力大幅提升
- **T0**：大规模多任务微调
- **UL2**（2022）：统一不同训练目标（MLM + 因果 + 前缀）

---

## 二、代码实践（25 分钟）

### 2.1 BERT 微调：文本分类

```python
from transformers import AutoTokenizer, AutoModelForSequenceClassification
import torch

# 加载预训练 BERT + 分类头
model_name = "bert-base-uncased"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForSequenceClassification.from_pretrained(
    model_name,
    num_labels=2  # 二分类：正面/负面
)

# 输入
texts = [
    "I love this product! It's amazing.",
    "Terrible experience, would not recommend."
]

# 编码
inputs = tokenizer(texts, padding=True, truncation=True, return_tensors="pt", max_length=128)

# 前向传播
with torch.no_grad():
    outputs = model(**inputs)

# 获取预测
logits = outputs.logits  # (batch_size, num_labels)
predictions = torch.argmax(logits, dim=1)
probabilities = torch.softmax(logits, dim=1)

for text, pred, probs in zip(texts, predictions, probabilities):
    label = "正面" if pred == 1 else "负面"
    print(f"{text} → {label} (置信度: {probs[pred].item():.2%})")
```

### 2.2 T5：多任务统一

```python
from transformers import T5Tokenizer, T5ForConditionalGeneration

# 加载 FLAN-T5（指令微调版）
model_name = "google/flan-t5-base"
tokenizer = T5Tokenizer.from_pretrained(model_name)
model = T5ForConditionalGeneration.from_pretrained(model_name)


def run_task(task_prefix, text, max_length=64):
    """统一的任务调用接口"""
    input_text = f"{task_prefix}: {text}"
    inputs = tokenizer(input_text, return_tensors="pt", max_length=512, truncation=True)
    
    outputs = model.generate(
        **inputs,
        max_length=max_length,
        num_beams=4,
        do_sample=False,
    )
    
    return tokenizer.decode(outputs[0], skip_special_tokens=True)


# 1. 翻译
print("翻译:", run_task("translate English to French", "The weather is beautiful today"))

# 2. 摘要
print("摘要:", run_task(
    "summarize",
    "The company reported record profits in Q4, with revenue increasing 25% year-over-year. "
    "The CEO attributed the success to strategic investments in AI and cloud computing."
))

# 3. 问答
print("问答:", run_task(
    "question: What year was Python created?",
    "context: Python was conceived in the late 1980s by Guido van Rossum at Centrum Wiskunde & Informatica (CWI) in the Netherlands."
))

# 4. 情感分类
print("情感:", run_task(
    "sst2 sentence",
    "This movie was a masterpiece of modern cinema"
))
```

### 2.3 对比 BERT 和 T5 的特征提取方式

```python
import torch
from transformers import BertModel, T5EncoderModel

# 同一句话，两种模型如何表示
text = "The quick brown fox jumps over the lazy dog"

# BERT: 双向编码
bert_tokenizer = BertTokenizerFast.from_pretrained("bert-base-uncased")
bert_model = BertModel.from_pretrained("bert-base-uncased")

bert_inputs = bert_tokenizer(text, return_tensors="pt")
with torch.no_grad():
    bert_outputs = bert_model(**bert_inputs)
    bert_hidden = bert_outputs.last_hidden_state  # (1, seq_len, 768)

# T5: 编码器表示
t5_tokenizer = T5TokenizerFast.from_pretrained("t5-base")
t5_model = T5EncoderModel.from_pretrained("t5-base")

t5_inputs = t5_tokenizer(text, return_tensors="pt")
with torch.no_grad():
    t5_outputs = t5_model(**t5_inputs)
    t5_hidden = t5_outputs.last_hidden_state  # (1, seq_len, 768)

print(f"BERT 隐藏状态: {bert_hidden.shape}")
print(f"T5 隐藏状态:  {t5_hidden.shape}")

# 比较 "fox" 这个词的表示
bert_fox_idx = bert_inputs['input_ids'][0].tolist().index(bert_tokenizer.convert_tokens_to_ids("fox"))
t5_fox_idx = t5_inputs['input_ids'][0].tolist().index(t5_tokenizer.convert_tokens_to_ids("fox"))

bert_fox_vec = bert_hidden[0, bert_fox_idx, :10]  # 取前10维
t5_fox_vec = t5_hidden[0, t5_fox_idx, :10]

print(f"\nBERT 'fox' 表示 (前10维): {bert_fox_vec.numpy().round(3)}")
print(f"T5   'fox' 表示 (前10维): {t5_fox_vec.numpy().round(3)}")
```

---

## 三、作业与思考题（10 分钟）

### 基础题

1. **理解题**：BERT 的 MLM 预训练中，为什么只有 80% 的掩盖词替换为 [MASK]？另外 20% 的作用是什么？

2. **对比题**：为什么说 T5 的 Text-to-Text 范式比 BERT 的"加分类头"范式更统一？请举一个 BERT 做起来麻烦但 T5 天然支持的任务。

3. **计算题**：BERT-Base 有 12 层，每层有 12 个头。如果一个输入序列有 512 个 token，每层自注意力的计算复杂度是多少？（用 O(n²·d) 表示）

### 进阶题

4. **分析题**：T5 的 Span Corruption 和 BERT 的 MLM 有什么本质区别？为什么说 Span Corruption 更适合生成任务？

5. **设计题**：如果你想做一个"代码审查助手"（输入代码 + 审查意见模板 → 输出审查结果），用 BERT 还是 T5 更合适？为什么？

---

## 四、扩展阅读

### BERT 系列
- 📄 **BERT: Pre-training of Deep Bidirectional Transformers** (Devlin et al., 2018)
- 📄 **RoBERTa: A Robustly Optimized BERT Pretraining Approach** (Liu et al., 2019)
- 📄 **ModernBERT** (2024) — 最新改进版 BERT

### T5 系列
- 📄 **Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer** (Raffel et al., 2019)
- 📄 **Finetuned Language Models Are Zero-Shot Learners (FLAN-T5)** (Wei et al., 2022)

### 工具
- 🌐 **Hugging Face Model Hub**：https://huggingface.co/models — 搜索所有 BERT/T5 变体
- 🌐 **Papers With Code**：https://paperswithcode.com/method/bert — 带代码的论文
