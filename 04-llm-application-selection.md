# 课程 4：大模型应用选型（模型架构 → 应用匹配 → 部署方法）

> **学习时长**：60 分钟
> **先修知识**：课程 1-3
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 4.1 模型分类：按架构和能力

```
按架构分类:
├── Decoder-only (生成型)
│   ├── GPT 系列（闭源）: GPT-3.5, GPT-4, GPT-4o
│   ├── LLaMA 系列（开源）: LLaMA-3, LLaMA-3.1
│   ├── Qwen 系列（开源）: Qwen2, Qwen2.5
│   ├── Mistral 系列（开源）: Mistral-7B, Mixtral
│   └── Claude 系列（闭源）: Claude 3, Claude 3.5
│
├── Encoder-only (理解型)
│   ├── BERT 系列
│   └── RoBERTa / DeBERTa
│
└── Encoder-Decoder (理解+生成)
    ├── T5 系列
    └── BART
```

### 4.2 选型决策矩阵

| 场景 | 推荐类型 | 具体建议 | 原因 |
|------|---------|---------|------|
| 聊天机器人/对话 | 大参数 Decoder | GPT-4o / Claude / LLaMA-3-70B | 需要强生成和推理能力 |
| 文本分类 | 小参数 Encoder | BERT / RoBERTa / DeBERTa | 理解为主，不需要生成 |
| 翻译 | Encoder-Decoder | T5 / mT5 / NLLB | 序列到序列转换 |
| 信息抽取 | Encoder + 轻量头 | BERT + NER 头 | 序列标注任务 |
| RAG 检索增强 | Decoder + 嵌入模型 | LLaMA + BGE/ColBERT | 需要检索+生成 |
| 代码生成 | 代码专用 Decoder | Codex / StarCoder / DeepSeek-Coder | 代码预训练 |
| 摘要生成 | Decoder | LLaMA / Qwen | 长文本压缩 |

### 4.3 关键指标解读

| 指标 | 含义 | 如何判断好坏 |
|------|------|------------|
| 参数量 | 模型大小 | 越大通常越强，但边际递减 |
| 上下文窗口 | 最大输入长度 | 8K（基础）→ 128K+（增强） |
| MMLU | 多学科知识测试 | >70% 为优秀 |
| 推理速度 | tokens/s | 受硬件、量化影响 |
| 成本 | 推理费用 | API 调用 or 自建成本 |

### 4.4 部署方式对比

| 方式 | 优点 | 缺点 | 适用场景 |
|------|------|------|---------|
| API 调用（闭源） | 即开即用、最强能力 | 成本高、数据出域 | 快速验证、非敏感场景 |
| 私有化部署（开源） | 数据可控、无持续费用 | 运维成本高、算力需求大 | 敏感数据、大规模使用 |
| 混合部署 | 灵活切换 | 架构复杂 | 分层服务（核心私有+边缘API） |
| 边缘部署 | 离线可用、低延迟 | 模型能力受限 | 移动端、IoT |

### 4.5 推理优化技术

```
量化（Quantization）:
  FP32 (4字节) → FP16/BF16 (2字节) → INT8 (1字节) → INT4 (0.5字节)
  精度损失: 0%          → ~1%         → ~2-3%      → ~5-10%

推理框架:
  - vLLM: 高吞吐，PagedAttention
  - TensorRT-LLM: NVIDIA 优化，极低延迟
  - Ollama: 简单易用，适合个人开发
  - llama.cpp: CPU/GPU 混合推理

并行策略:
  - Tensor Parallel: 大矩阵拆分到多卡
  - Pipeline Parallel: 层拆分到多卡
  - 混合并行: 同时使用以上两种
```

---

## 二、代码实践（25 分钟）

### 4.1 用 Ollama 本地部署模型

```bash
# 安装 Ollama
curl -fsSL https://ollama.com/install.sh | sh

# 下载模型（以 LLaMA 3.1 8B 为例）
ollama pull llama3.1

# 运行对话
ollama run llama3.1

# API 调用
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.1",
  "prompt": "解释什么是 Transformer",
  "stream": false
}'
```

### 4.2 用 vLLM 部署高性能服务

```python
from vllm import LLM, SamplingParams

# 加载模型（自动多卡并行）
llm = LLM(
    model="meta-llama/Meta-Llama-3-8B-Instruct",
    tensor_parallel_size=2,  # 2 卡并行
    gpu_memory_utilization=0.9,
)

# 批量推理
prompts = [
    "解释量子计算",
    "写一首关于 AI 的诗",
    "总结 Python 的核心特点",
]

sampling_params = SamplingParams(
    temperature=0.7,
    max_tokens=256,
    top_p=0.95,
)

outputs = llm.generate(prompts, sampling_params)

for prompt, output in zip(prompts, outputs):
    print(f"输入: {prompt}")
    print(f"输出: {output.outputs[0].text}")
    print("-" * 40)
```

### 4.3 模型量化推理

```python
from transformers import AutoModelForCausalLM, AutoTokenizer, BitsAndBytesConfig
import torch

# 配置 4-bit 量化
quantization_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.float16,
    bnb_4bit_use_double_quant=True,
)

# 加载量化模型
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Meta-Llama-3-8B",
    quantization_config=quantization_config,
    device_map="auto",
)

tokenizer = AutoTokenizer.from_pretrained("meta-llama/Meta-Llama-3-8B")

# 推理
text = "Transformer 的核心思想是"
inputs = tokenizer(text, return_tensors="pt").to(model.device)

outputs = model.generate(**inputs, max_new_tokens=100, do_sample=False)
print(tokenizer.decode(outputs[0], skip_special_tokens=True))

# 对比显存占用
print(f"量化后显存: {model.get_memory_footprint() / 1e9:.2f} GB")
# FP16 ~16GB, INT4 ~5GB
```

### 4.4 成本估算工具

```python
def estimate_cost(model_params_b: float, prompt_tokens: int, 
                  gen_tokens: int, n_requests: int = 1):
    """估算不同部署方式的成本"""
    
    # API 价格参考（2024 年）
    api_prices = {
        "GPT-4o": {"input": 5/1e6, "output": 15/1e6},     # $/token
        "Claude 3.5 Sonnet": {"input": 3/1e6, "output": 15/1e6},
        "GPT-3.5-Turbo": {"input": 0.5/1e6, "output": 1.5/1e6},
    }
    
    print(f"=== {n_requests} 次请求 ===")
    print(f"  输入: {prompt_tokens} tokens, 输出: {gen_tokens} tokens\n")
    
    for name, price in api_prices.items():
        total = (prompt_tokens * price["input"] + 
                 gen_tokens * price["output"]) * n_requests
        print(f"  {name}: ${total:.4f}")
    
    # 自建成本估算
    print("\n=== 自建部署 ===")
    vram_gb = model_params_b * 2  # FP16 需要参数量 × 2 GB
    print(f"  需要显存: ~{vram_gb} GB (FP16)")
    print(f"  4-bit 量化显存: ~{vram_gb / 4:.0f} GB")
    print(f"  推荐 GPU: {'A100 80G' if vram_gb > 40 else 'A10 24G' if vram_gb > 12 else 'T4 16G'}")


estimate_cost(8, 2000, 500, 10000)
```

---

## 三、作业与思考题（10 分钟）

1. **选型题**：公司要做一个内部知识库问答系统，要求数据不出域，每天约 1000 次查询，每次平均 3000 字输入 + 500 字输出。你会推荐什么方案？

2. **计算题**：如果用 FP16 部署一个 70B 参数的模型，至少需要多少显存？如果用 INT4 量化呢？

3. **分析题**：什么场景下应该用闭源 API？什么场景下必须私有化部署？

---

## 四、扩展阅读

- 🌐 **OpenAI API 定价**：https://openai.com/pricing
- 🌐 **vLLM 文档**：https://docs.vllm.ai
- 🌐 **Ollama**：https://ollama.com
- 📄 **GPTQ**（4-bit 量化）：https://arxiv.org/abs/2210.17323
- 🌐 **Hugging Face 模型排行榜**：https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard
