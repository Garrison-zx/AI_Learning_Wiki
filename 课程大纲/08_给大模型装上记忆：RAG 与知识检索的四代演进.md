---
课程: 08 - 给大模型装上记忆：RAG 与知识检索的四代演进
版本: v1.0
创建日期: 2026-04-29
最后更新: 2026-04-29
适用模型版本: GPT-4o / Claude 3.5 / LangChain 0.3 / LlamaIndex 0.10
下次复审: 2026-07-29
更新触发条件:
  - 新一代 RAG 框架发布（LangChain/LlamaIndex 重大版本）
  - 主流向量数据库有重大更新（Chroma/Pinecone/Milvus）
  - 出现新的检索范式（如 GraphRAG 普及、长上下文替代检索）
---

# 课程 08：给大模型装上记忆——RAG 与知识检索的四代演进

> **课程类型**：B 应用型 | **时长**：70 min | **前置课程**：课程 06（Prompt Engineering）、课程 07（选型部署）

---

## 逻辑主线

```
"我的 AI 不知道公司内部知识怎么办？训练太贵，微调太慢……"
        ↓
[核心矛盾] LLM 的知识是静态的，现实世界是动态的
           参数知识 ≠ 私有知识 ≠ 实时知识
        ↓
[解法] RAG：检索增强生成
       不改模型参数，给模型插上"可更新的外部记忆"
        ↓
[四代演进]
  第一代 Naive RAG：固定 Chunk + 向量召回 → 能用，但粗糙
  第二代 Advanced RAG：HyDE + 多路召回 + 重排序 → 质量提升
  第三代 Modular RAG：查询路由 + 迭代检索 + Self-RAG → 灵活组合
  第四代 Agentic RAG：主动检索 + 反思循环 + 多步推理 → 接近通用
        ↓
"同等成本，知识覆盖率从 20% → 90%+"
```

---

## 课程简介

本课解决一个企业 AI 落地的核心痛点：**如何让大模型"知道"它本来不知道的东西？**

RAG（Retrieval-Augmented Generation）是 2023-2026 年企业 AI 最高频的工程实践，没有之一。本课不仅讲"RAG 是什么"，更讲清楚：
- 为什么 Fine-tuning 不是答案，RAG 为什么是更务实的选择
- 向量检索的数学原理（Embedding 空间中的语义相似度）
- 四代 RAG 架构的演进逻辑，每一代解决了什么具体问题
- 生产级 RAG 的五大质量陷阱与调优策略

---

## 课程描述：学完本课你能收获

| 能力 | 具体表现 |
|------|---------|
| **理论框架** | 解释 RAG 的 Indexing/Retrieval/Generation 三阶段，画出完整架构图 |
| **四代演进** | 说清每代 RAG 解决什么问题，推荐对应技术组件 |
| **代码实操** | 用 LangChain 从零搭建能跑的 RAG 系统（PDF → 问答） |
| **质量诊断** | 识别 RAG 系统的五大常见故障并给出调优方向 |
| **选型决策** | 针对不同业务场景选择向量数据库和检索策略 |
| **面试应答** | 回答"RAG 和 Fine-tuning 怎么选"、"如何提升召回率"等高频题 |

---

## Wow Moment 🎯

> **GPT-4 在没有任何检索增强的情况下，回答私有企业知识库问题的准确率不足 20%（幻觉率超过 60%）；接入 RAG 后，同等模型、同等成本，准确率提升到 85%+，幻觉率下降 80%——这不是微调，而是给模型"装上了可插拔的外部记忆"。**

引导设问（讲师口播）：
- "你有没有问过 ChatGPT 你们公司的产品规格，然后它一本正经地编出一个假答案？"
- "为什么 ChatGPT 不知道今天的新闻？它的知识截止日期是什么意思？"
- "如果你是 AI，你怎么'查资料'再回答？"

---

## 术语速查表

| 术语 | 一句话定义 | 首次出现位置 |
|------|-----------|------------|
| RAG（检索增强生成） | 在生成前先检索相关文档，将检索结果作为上下文注入 LLM | Part 1.1 |
| Embedding（向量嵌入） | 将文本映射到高维向量空间，语义相似的文本向量接近 | Part 2.1 |
| Chunk（文档分块） | 将长文档切分为适合检索的片段（通常 256-1024 tokens） | Part 2.2 |
| 向量数据库 | 专门存储和检索高维向量的数据库（Chroma/Pinecone/Milvus） | Part 2.3 |
| 余弦相似度 | 两个向量夹角的余弦值，衡量语义相似程度，范围[-1, 1] | Part 2.1 |
| HyDE（假设文档嵌入） | 先让 LLM 生成假设答案，再用假设答案做检索，提升召回率 | Part 4.2 |
| 重排序（Reranking） | 对初步召回的候选文档用更强模型重新评分排序 | Part 4.2 |
| BM25 | 基于词频的稀疏检索算法，适合关键词精确匹配 | Part 4.2 |
| Self-RAG | 模型自主判断何时检索、是否使用检索结果，含反思标记 | Part 4.3 |
| GraphRAG | 基于知识图谱的 RAG，适合实体关系密集的查询 | Part 4.4 |

---

## 课程结构总览

| Part | 主题 | 时长 | 核心交付 |
|------|------|------|---------|
| **Part 0** | 直觉导入：LLM 的记忆困境 | 5 min | 三大知识缺陷 + RAG 类比 |
| **Part 1** | 为什么是 RAG？不是 Fine-tuning？ | 10 min | RAG vs Fine-tuning 决策框架 |
| **Part 2** | 向量检索基础：语义怎么变成数字？ | 10 min | Embedding 原理 + 相似度计算 |
| **Part 3** | 代码实战：从零搭建 RAG 系统 | 20 min | 三段代码（基础/混合检索/评测） |
| **Part 4** | 四代演进：从 Naive 到 Agentic | 20 min | 每代核心改进 + 技术选型建议 |
| **Part 5** | 总结与作业 | 5 min | 面试真题 + 实战挑战 |

---

## 章节大纲

### Part 0：直觉导入——LLM 的记忆困境（5 min）

#### 0.1 三大知识缺陷（3 min）

**缺陷一：知识截止日期（Knowledge Cutoff）**
> "GPT-4o 的训练数据截止到 2024 年 4 月——它不知道今天发生了什么"
- 影响：无法回答最新事件、实时数据、近期政策
- 规模：企业信息更新周期平均 72 小时，训练周期 6-12 个月

**缺陷二：私有知识盲区（Private Knowledge Gap）**
> "互联网上没有你们公司的产品手册、内部流程、客户合同"
- 影响：企业 AI 最核心的应用场景，模型完全不知道
- 无法解决：公开互联网上的训练数据根本不包含这些

**缺陷三：幻觉（Hallucination）**
> "模型在不确定时倾向于'创作'而非说'我不知道'"
- 影响：给出听起来合理但完全错误的答案，危害性极高
- 根源：参数记忆的压缩性导致信息失真

**类比**：LLM 就像一个读了海量书籍但毕业后被锁在房间里的学者——博学但与世隔绝，问他公司内部的事他只能猜。**RAG 给他一个能实时查阅资料的能力**。

#### 0.2 RAG 的本质（2 min）

```
传统 LLM（纯参数记忆）：
  问题 ──→ LLM 脑内检索（参数） ──→ 答案（可能幻觉）

RAG（检索增强生成）：
  问题 ──→ [检索器] 外部知识库 ──→ 相关文档片段
            └──→ [LLM] 问题 + 文档片段 ──→ 有依据的答案
```

---

### Part 1：为什么是 RAG？不是 Fine-tuning？（10 min）

#### 1.1 三种知识注入方式的比较（6 min）

**方式一：参数微调（Fine-tuning）**
- 原理：用私有数据继续训练，将知识"烧录"到模型权重
- 优点：推理时无需检索，速度快；模型"懂"这些知识
- 缺点：
  - 成本高：70B 模型 LoRA 微调需要 2-4 块 A100，数天时间
  - 知识静态：微调后知识固定，更新需要重新训练
  - 灾难性遗忘：新知识可能覆盖旧能力
  - 不可溯源：无法知道答案来自哪条训练数据

**方式二：上下文注入（Context Stuffing）**
- 原理：把所有知识塞进 System Prompt
- 优点：简单直接，无需额外系统
- 缺点：
  - 上下文长度有限（即使 200K 也不够企业知识库）
  - 成本巨大：每次请求都要发送全量知识
  - 注意力稀释：文档太多，模型"看不全"

**方式三：RAG（检索增强生成）**
- 原理：只检索最相关的片段注入上下文
- 优点：
  - 知识动态可更新（修改知识库即可）
  - 可溯源（答案来自哪个文档一目了然）
  - 成本可控（只注入相关片段）
  - 无需重训模型
- 缺点：
  - 系统复杂度增加
  - 检索质量决定最终效果（"垃圾进，垃圾出"）

**三方式选型决策框架**：

```
知识是否需要频繁更新？
    │ 是（每天/每周）          │ 否（稳定知识）
    ▼                         ▼
    RAG（首选）           知识规模？
                        │ <100K tokens    │ >100K tokens
                        ▼                 ▼
                   上下文注入          RAG 或 Fine-tuning
                   （一次性塞进去）

                   需要提升模型
                   「能力风格」？
                        │ 是（如特定领域写作风格）
                        ▼
                    Fine-tuning
                    （改变行为模式）
```

#### 1.2 RAG 三阶段架构（4 min）

```
┌─────────────────────────────────────────────────────────────┐
│                      RAG 系统架构                            │
│                                                             │
│  [离线阶段 - Indexing]                                       │
│  原始文档 → 文档切分(Chunking) → 向量化(Embedding) → 存入向量DB │
│                                                             │
│  [在线阶段 - Retrieval]                                      │
│  用户问题 → 问题向量化 → 向量相似度搜索 → Top-K 文档片段         │
│                                                             │
│  [生成阶段 - Generation]                                     │
│  问题 + Top-K 片段 → Prompt 组装 → LLM → 带引用的答案          │
└─────────────────────────────────────────────────────────────┘
```

**三阶段各自的质量瓶颈**：
- Indexing：切分粒度太粗 → 上下文丢失；太细 → 语义不完整
- Retrieval：向量检索遗漏关键词精确匹配；Top-K 选多 → 干扰，选少 → 遗漏
- Generation：上下文矛盾时模型不知所措；答案无法溯源

**🔥 面试考点**：被问"RAG 的工作流程"时，务必分三阶段回答（Indexing/Retrieval/Generation），并能说出每阶段的核心挑战。

---

### Part 2：向量检索基础——语义怎么变成数字？（10 min）

#### 2.1 Embedding 的数学直觉（4 min）

**从词向量到句向量**：
- 词向量（Word2Vec, 2013）："king - man + woman ≈ queen"——类比关系编码在向量方向中
- 句向量（Sentence Transformer）：整句话映射为单个向量，保留句子级语义

**余弦相似度**：

```
cos(θ) = (A · B) / (|A| × |B|)

其中：
  A · B = Σ(aᵢ × bᵢ) （内积）
  |A|   = √(Σaᵢ²)     （L2 范数）

含义：
  cos(θ) = 1.0  → 完全相同语义
  cos(θ) = 0.0  → 语义不相关
  cos(θ) = -1.0 → 语义完全相反（中文场景少见）
```

**可视化类比**：
> 想象所有句子被投影到一个巨大的球面上。语义相似的句子挨在一起，"苹果手机的价格"和"iPhone多少钱"在球面上相邻，而"今天天气不错"在很远的地方。RAG 的检索就是：找到用户问题在球面上的位置，然后找最近的几个文档片段。

#### 2.2 Chunking 策略（4 min）

**为什么要切分？**
- Embedding 模型有最大 Token 限制（通常 512-8192 tokens）
- 太长的 chunk → Embedding 向量"稀释"，无法精确定位语义
- 太短的 chunk → 上下文不足，LLM 无法生成高质量答案

**五种切分策略**：

| 策略 | 原理 | 适用场景 | 典型 Chunk 大小 |
|------|------|---------|----------------|
| 固定长度切分 | 按字符数切分，有重叠（overlap） | 快速原型，均匀文档 | 512 tokens，50 overlap |
| 语义切分 | 按句子边界切分，保持语义完整 | 新闻、文章 | 变长，平均 300-600 tokens |
| 递归字符切分 | 按段落→句子→词语层级递归切分 | 通用文档（LangChain 默认） | 1000 chars，200 overlap |
| 文档结构切分 | 按 Markdown/HTML 标题层级切分 | 有结构的技术文档、Wiki | 一个小节为一 chunk |
| 语义滑动窗口 | 计算相邻句子语义差异，差异大处切断 | 话题转换明显的长文档 | 变长 |

**💡 实战经验**：
- 技术文档 + 代码 → 按标题层级切分（保持代码块完整）
- 法律合同 → 按条款切分（不能跨条款）
- 对话记录 → 按会话轮次切分

#### 2.3 向量数据库选型（2 min）

| 数据库 | 特点 | 推荐场景 |
|--------|------|---------|
| **Chroma** | 本地优先，零配置，Python 原生 | 开发阶段、原型验证 |
| **Pinecone** | 全托管云服务，自动扩缩容 | 快速上线，无运维 |
| **Milvus** | 开源，支持亿级向量，功能最全 | 生产大规模部署 |
| **Weaviate** | 内置混合检索，GraphQL API | 需要混合检索的场景 |
| **pgvector** | PostgreSQL 插件，融合结构化查询 | 已有 PG 数据库的团队 |
| **FAISS** | Meta 开源，纯内存，速度极快 | 离线批量检索，研究场景 |

---

### Part 3：代码实战——从零搭建 RAG 系统（20 min）

#### 3.1 基础 RAG：PDF 问答系统（8 min）

```python
# 完整基础 RAG 系统（约 50 行核心代码）
# 依赖：pip install langchain langchain-openai chromadb pypdf

from langchain_community.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_community.vectorstores import Chroma
from langchain.chains import RetrievalQA
from langchain.prompts import PromptTemplate
import os

# ── Step 1: 文档加载与切分（Indexing 阶段）────────────────────
loader = PyPDFLoader("company_handbook.pdf")  # 加载公司手册 PDF
documents = loader.load()
print(f"原始文档：{len(documents)} 页")

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,        # 每块约 1000 字符
    chunk_overlap=200,      # 相邻块重叠 200 字符（防止切断语义）
    separators=["\n\n", "\n", "。", "！", "？", " ", ""]  # 中文优先按段落切
)
chunks = splitter.split_documents(documents)
print(f"切分后：{len(chunks)} 个 chunks，平均 {sum(len(c.page_content) for c in chunks)//len(chunks)} 字符/chunk")

# ── Step 2: 向量化并存入向量数据库（Indexing 阶段）──────────────
embedding_model = OpenAIEmbeddings(model="text-embedding-3-small")  # 1536 维向量

# 首次构建（约需 30s-2min，取决于文档大小）
vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=embedding_model,
    persist_directory="./chroma_db",  # 持久化到本地磁盘
    collection_name="company_knowledge"
)
print(f"向量库已构建，共 {vectorstore._collection.count()} 条向量")

# ── Step 3: 构建检索器（Retrieval 阶段）──────────────────────
retriever = vectorstore.as_retriever(
    search_type="similarity",   # 余弦相似度检索
    search_kwargs={"k": 4}      # 返回最相关的 4 个 chunks
)

# ── Step 4: 构建问答链（Generation 阶段）──────────────────────
# 自定义 Prompt，要求模型基于上下文回答并标注依据
qa_prompt = PromptTemplate(
    template="""你是一个专业的企业知识库助手。请仅根据以下上下文回答问题。
如果上下文中没有相关信息，请明确说"根据现有文档无法回答此问题"，不要猜测。

上下文：
{context}

问题：{question}

回答（请标注信息来源的页码或章节）：""",
    input_variables=["context", "question"]
)

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)  # temperature=0 减少幻觉

qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    chain_type="stuff",           # "stuff" = 将所有检索结果塞入一次请求
    chain_type_kwargs={"prompt": qa_prompt},
    return_source_documents=True  # 返回检索到的原始文档
)

# ── Step 5: 测试问答 ───────────────────────────────────────
query = "公司年假政策是什么？入职满一年可以休几天？"
result = qa_chain.invoke({"query": query})

print(f"\n问题：{query}")
print(f"\n答案：{result['result']}")
print(f"\n引用来源（{len(result['source_documents'])} 个片段）：")
for i, doc in enumerate(result['source_documents']):
    print(f"  [{i+1}] 第 {doc.metadata.get('page', '?')} 页：{doc.page_content[:100]}...")
```

#### 3.2 混合检索：向量 + BM25（7 min）

**为什么纯向量检索不够？**
- 向量检索擅长：语义相似（"iPhone 多少钱" ↔ "苹果手机价格"）
- 向量检索不擅长：精确关键词（产品型号、法律条款编号、专有名词）
- 解决方案：**混合检索 = 稀疏检索（BM25）+ 稠密检索（向量）**

```python
# 混合检索系统（BM25 + 向量，使用 RRF 融合排序）
from langchain_community.retrievers import BM25Retriever
from langchain.retrievers import EnsembleRetriever
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings

# ── 稀疏检索器（BM25 - 关键词匹配）──────────────────────────
# BM25 核心公式（了解即可）：
# Score(q, d) = Σ IDF(qᵢ) × (tf(qᵢ,d) × (k1+1)) / (tf(qᵢ,d) + k1 × (1-b+b×|d|/avgdl))
# - IDF：词的稀缺性（越罕见分越高）
# - TF：词在文档中出现频率
# - k1, b：调节参数（通常 k1=1.5, b=0.75）

bm25_retriever = BM25Retriever.from_documents(chunks)
bm25_retriever.k = 4  # 返回 Top-4

# ── 稠密检索器（向量 - 语义匹配）────────────────────────────
vector_retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

# ── 混合检索器（RRF 融合排序）──────────────────────────────
# RRF（Reciprocal Rank Fusion）公式：
# RRF(d) = Σ 1 / (k + rank_i(d))  其中 k=60（平滑参数）
# 融合来自不同检索器的排名，不依赖分数归一化
ensemble_retriever = EnsembleRetriever(
    retrievers=[bm25_retriever, vector_retriever],
    weights=[0.4, 0.6]  # BM25 权重 0.4，向量权重 0.6（可根据任务调整）
)

# ── 测试：精确匹配 vs 语义匹配 ───────────────────────────────
queries = [
    "HR-2024-003 号文件的内容是什么？",  # 精确编号 → BM25 擅长
    "入职后待遇怎么样？",                # 语义查询 → 向量擅长
]

for q in queries:
    # 对比两种检索器的结果
    bm25_results = bm25_retriever.get_relevant_documents(q)
    vector_results = vector_retriever.get_relevant_documents(q)
    hybrid_results = ensemble_retriever.get_relevant_documents(q)

    print(f"\n查询：{q}")
    print(f"BM25 Top-1：{bm25_results[0].page_content[:80]}...")
    print(f"向量 Top-1：{vector_results[0].page_content[:80]}...")
    print(f"混合 Top-1：{hybrid_results[0].page_content[:80]}...")
```

#### 3.3 RAG 质量评测（5 min）

**RAG 的五大评测维度**（RAGAS 框架）：

| 指标 | 定义 | 计算方式 |
|------|------|---------|
| **Context Recall** | 检索到的相关文档是否覆盖了问题需要的全部信息 | 需标注答案 |
| **Context Precision** | 检索到的文档中有多少是真正相关的（噪声比例） | 需标注答案 |
| **Faithfulness** | 生成答案是否完全基于检索内容（不产生幻觉） | LLM 自动评估 |
| **Answer Relevancy** | 生成答案是否回答了用户的实际问题 | LLM 自动评估 |
| **Answer Correctness** | 生成答案与参考答案的一致性 | 需标注答案 |

```python
# 使用 RAGAS 评测 RAG 系统质量
# pip install ragas

from ragas import evaluate
from ragas.metrics import (
    faithfulness,          # 忠实度（无幻觉）
    answer_relevancy,      # 答案相关性
    context_recall,        # 上下文召回率
    context_precision,     # 上下文精确率
)
from datasets import Dataset

# 构建评测数据集（需要人工标注的"金标准"）
eval_data = {
    "question": [
        "公司年假政策是什么？",
        "报销流程需要哪些审批步骤？",
        "试用期是多长时间？",
    ],
    "answer": [            # RAG 系统生成的答案
        qa_chain.invoke({"query": q})["result"]
        for q in ["公司年假政策是什么？", "报销流程需要哪些审批步骤？", "试用期是多长时间？"]
    ],
    "contexts": [          # 检索到的上下文（列表）
        [doc.page_content for doc in retriever.get_relevant_documents(q)]
        for q in ["公司年假政策是什么？", "报销流程需要哪些审批步骤？", "试用期是多长时间？"]
    ],
    "ground_truth": [      # 人工标注的标准答案
        "入职满1年享有5天年假，满3年10天，满5年15天",
        "员工提交→直属上级审批→财务审核→3个工作日到账",
        "标准试用期为3个月，可协商缩短至1个月",
    ]
}

dataset = Dataset.from_dict(eval_data)
scores = evaluate(
    dataset=dataset,
    metrics=[faithfulness, answer_relevancy, context_recall, context_precision],
)

print(scores.to_pandas()[["faithfulness", "answer_relevancy",
                           "context_recall", "context_precision"]])

# 理想结果（生产系统目标）：
# faithfulness:     > 0.90（允许 10% 的轻微改写）
# answer_relevancy: > 0.85
# context_recall:   > 0.80（召回率最难优化）
# context_precision:> 0.75

# 📊 常见问题与诊断：
# context_recall 低  → chunk 太大或 k 太小 → 增大 k 或缩小 chunk
# context_precision低 → 检索了太多无关内容 → 改用 MMR 去重或提高阈值
# faithfulness 低    → 模型在编造内容 → 收紧 Prompt 或换更准确的模型
```

---

### Part 4：四代演进——从 Naive 到 Agentic（20 min）

#### 4.1 第一代 Naive RAG（3 min）

**架构特征**：固定 Chunk 大小 → 向量检索 Top-K → 直接拼接注入 LLM

**核心问题**：
1. **Chunking 边界割裂语义**：一段话被切断，关键信息在两个 chunk 里
2. **单一检索维度**：只有语义相似，缺失关键词精确匹配
3. **无相关性过滤**：Top-K 中可能包含完全无关的结果
4. **无引用追踪**：用户不知道答案从哪来

**适用场景**：知识库简单、问题直接、原型验证阶段

**工程评价**：能跑，但在复杂问题上表现不稳定，"召回率低，噪声多"。

---

#### 4.2 第二代 Advanced RAG（6 min）

**核心改进**：在检索前和检索后各加一步处理

**改进一：查询增强（Pre-retrieval）**

```
原始查询 → [查询增强] → 增强后的查询 → 向量检索
```

**HyDE（Hypothetical Document Embedding）**：
> 论文：Gao et al., "Precise Zero-Shot Dense Retrieval without Relevance Labels", ACL 2023

- 原理：先让 LLM 生成一个假设性答案，再用假设答案做检索
- 为什么有效：用户问题往往是简短的问句，而知识库里是描述性段落——两者向量距离天然偏大
- 效果：在某些任务上召回率提升 15-30%

```python
# HyDE 实现
def hyde_retrieval(question: str, retriever, llm) -> list:
    """假设文档嵌入检索"""
    # Step 1：让 LLM 先"猜"一个答案
    hypothetical_answer = llm.invoke(
        f"请用 2-3 句话回答以下问题（即使不确定也要给出猜测）：\n{question}"
    ).content

    # Step 2：用假设答案（而非原始问题）做检索
    docs = retriever.get_relevant_documents(hypothetical_answer)

    print(f"原始问题：{question}")
    print(f"假设答案：{hypothetical_answer[:100]}...")
    print(f"检索到 {len(docs)} 个相关片段")
    return docs
```

**查询分解（Query Decomposition）**：
- 原理：复杂问题先拆分为多个子问题，分别检索，再合并答案
- 适用：多跳推理问题（"A 公司 CEO 毕业的大学的校训是什么？"）

**改进二：重排序（Post-retrieval Reranking）**

```
初步召回 Top-20 → [Cross-Encoder Reranker] → 精选 Top-4 → 注入 LLM
```

- **为什么需要重排序**：向量检索（Bi-Encoder）速度快但精度低；Cross-Encoder 精度高但太慢（O(n²) 复杂度），两阶段结合取长补短
- **主流重排序模型**：`BAAI/bge-reranker-v2-m3`、Cohere Rerank API
- **效果**：精确率通常提升 10-25%

```python
# 两阶段检索：粗召回 + 精排
from langchain.retrievers.contextual_compression import ContextualCompressionRetriever
from langchain_cohere import CohereRerank

# Step 1: 粗召回（速度优先，召回更多候选）
base_retriever = vectorstore.as_retriever(search_kwargs={"k": 20})  # 召回20个

# Step 2: 精排（精度优先，筛选最相关）
reranker = CohereRerank(model="rerank-multilingual-v3.0", top_n=4)  # 精选4个

compression_retriever = ContextualCompressionRetriever(
    base_compressor=reranker,
    base_retriever=base_retriever
)

# 对比效果
query = "公司对于远程办公有什么具体规定？"
coarse_docs = base_retriever.get_relevant_documents(query)
refined_docs = compression_retriever.get_relevant_documents(query)

print(f"粗召回 Top-1（相关性分：未知）：{coarse_docs[0].page_content[:100]}")
print(f"精排  Top-1（已优化）：{refined_docs[0].page_content[:100]}")
```

---

#### 4.3 第三代 Modular RAG（5 min）

**核心理念**：RAG 不是一个固定流水线，而是**可组合的模块**——根据查询类型动态选择检索策略。

> 论文：Gao et al., "Modular RAG: Transforming RAG Systems into LEGO-like Reconfigurable Frameworks", 2024

**三大模块化改进**：

**改进一：查询路由（Query Routing）**
```
用户问题
    ├── 事实性问题 → 向量检索
    ├── 精确编号查询 → BM25 检索
    ├── 跨文档推理 → 多跳检索
    └── 不需要检索 → 直接 LLM 回答（"你叫什么名字？"）
```

**改进二：迭代检索（Iterative Retrieval）**
- 场景：第一次检索结果不完整时，自动生成补充查询
- 流程：检索 → 评估充分性 → 不充分则生成新查询 → 再检索（最多 3 轮）

**改进三：Self-RAG（自反思 RAG）**
> 论文：Asai et al., "Self-RAG: Learning to Retrieve, Generate, and Critique through Self-Reflection", NeurIPS 2023

- 原理：训练 LLM 在生成时自动插入特殊反思标记（Reflection Token）
- 四种标记：
  - `[Retrieve]`：是否需要检索？（Yes/No）
  - `[ISREL]`：检索到的文档是否相关？（Relevant/Irrelevant）
  - `[ISSUP]`：答案是否被文档支持？（Fully/Partially/No）
  - `[ISUSE]`：这个回答对用户有用吗？（1-5分）
- 效果：在多个 benchmark 上超过 RAG + ChatGPT，且无需检索时不检索（节省成本）

```python
# 模拟迭代检索逻辑（实际 Self-RAG 需要微调模型）
def iterative_rag(question: str, retriever, llm, max_iterations=3):
    """迭代检索：评估充分性后决定是否继续检索"""
    context_docs = []
    queries = [question]

    for iteration in range(max_iterations):
        # 检索当前查询
        new_docs = retriever.get_relevant_documents(queries[-1])
        context_docs.extend(new_docs)

        # 让 LLM 评估是否有足够信息回答
        context_text = "\n\n".join([d.page_content for d in context_docs[:6]])
        sufficiency_check = llm.invoke(
            f"""基于以下上下文，你是否有足够信息完整回答问题？
问题：{question}
上下文：{context_text[:1000]}...
只回答"充分"或"不充分，需要补充查询：[补充查询]"""
        ).content

        if "充分" in sufficiency_check:
            print(f"第 {iteration+1} 轮：信息充分，停止检索")
            break
        else:
            # 提取补充查询
            follow_up = sufficiency_check.split("补充查询：")[-1].strip()
            queries.append(follow_up)
            print(f"第 {iteration+1} 轮：需要补充查询 → '{follow_up}'")

    return context_docs, queries
```

---

#### 4.4 第四代 Agentic RAG（4 min）

**核心理念**：RAG 不再是被动的"查了再说"，而是**主动规划的 Agent 行为**——检索是 Agent 众多工具之一。

**与第三代的关键区别**：

| 维度 | Modular RAG | Agentic RAG |
|------|------------|------------|
| 检索触发 | 规则路由 | LLM 自主决策 |
| 检索工具 | 向量数据库 | 向量DB + SQL + API + 搜索引擎 |
| 规划能力 | 固定流程 | 动态多步规划 |
| 反思能力 | 规则判断 | LLM 自我批评 |
| 代表框架 | LangChain | LangGraph / AutoGen |

**Agentic RAG 典型流程**：
```
用户问题
    ↓
[计划] LLM 分解问题，规划检索路径
    ↓
[执行] 调用多个工具：
       → 向量检索（内部知识库）
       → SQL 查询（结构化数据库）
       → Web Search（实时信息）
    ↓
[反思] 评估检索结果质量和完整性
    ↓
[迭代] 不满意则调整策略，重新检索
    ↓
[合成] 综合多源信息，生成有引用的答案
```

**GraphRAG（图增强 RAG）**：
> 论文：Edge et al., "From Local to Global: A Graph RAG Approach to Query-Focused Summarization", Microsoft, 2024

- 问题：传统向量 RAG 对"全局性问题"（"整个文档的主要主题是什么？"）效果差
- 方案：预处理阶段构建知识图谱（实体 + 关系），图遍历代替向量检索
- 适用：文档实体关系密集（如法律文书、医学文献、企业知识图谱）

**2026 技术趋势**：
- 长上下文模型（1M+ tokens）能否取代 RAG？
  > 部分简单场景可以，但成本仍是 10-100 倍；复杂知识库（GB 级）仍需 RAG
- RAG + Agent 融合：RAG 成为 Agent 的核心工具，而非独立系统
- 实时 RAG：流式检索，在生成过程中动态补充上下文

---

#### 4.5 生产级 RAG 的五大质量陷阱（2 min）

| 陷阱 | 表现 | 诊断指标 | 解决方案 |
|------|------|---------|---------|
| **切分粒度错误** | 答案被截断，语义不完整 | Context Recall 低 | 调整 chunk_size，加大 overlap |
| **Embedding 模型不匹配** | 中文查询检索到英文内容 | 手动抽样检查 | 换多语言模型（bge-m3, e5-mistral）|
| **噪声文档污染** | 答案混入无关信息 | Context Precision 低 | 两阶段检索 + 重排序 |
| **LLM 忽视上下文** | 检索正确但答案错误 | Faithfulness 低 | 收紧 Prompt，强制引用约束 |
| **实体消解失败** | "他"/"该公司"检索不到 | 直接问 vs 代词问对比测试 | 查询改写 + 实体链接预处理 |

---

### Part 5：总结与作业（5 min）

#### 5.1 四代 RAG 演进总结

```
第一代 Naive RAG    → 能跑，精度差
  固定Chunk + 向量检索 + 直接注入
        ↓ 加入查询增强 + 重排序
第二代 Advanced RAG  → 精度显著提升
  HyDE/查询分解 + 粗召回 + 精排
        ↓ 模块化，按需组合
第三代 Modular RAG   → 灵活，接近生产
  查询路由 + 迭代检索 + Self-RAG
        ↓ 融入 Agent，主动规划
第四代 Agentic RAG   → 最强，最复杂
  多工具 + 动态规划 + 反思循环
```

#### 5.2 三类人群的学习重点

| 人群 | 重点掌握 | 可略读 |
|------|---------|-------|
| 转行小白 | Part 0-1（动机）、Part 2.1（Embedding 直觉）、Part 4.1（Naive RAG）| Part 3 代码细节 |
| 算法工程师 | Part 2（向量检索数学）、Part 3（评测代码）、Part 4.2-4.3（Advanced/Modular）| Part 0 类比 |
| AI 产品经理 | Part 1.1（RAG vs Fine-tuning）、Part 4.4（趋势）、Part 4.5（陷阱）| Part 2-3 代码 |

#### 5.3 面试真题与参考答案

**🔥 高频真题 1**：RAG 和 Fine-tuning 怎么选？
> 知识需要频繁更新 → RAG；需要改变模型行为风格（如特定领域写作）→ Fine-tuning；两者不互斥，有时组合使用（Fine-tuning 提升指令跟随，RAG 注入私有知识）。

**🔥 高频真题 2**：如何提升 RAG 系统的召回率？
> 五板斧：① 调小 chunk_size（提升语义精度）② 加大 k（多召回候选）③ 混合检索（向量 + BM25）④ HyDE 查询增强 ⑤ 迭代检索。先用 RAGAS 的 context_recall 指标定量评估，确认瓶颈后针对性优化。

**⚠️ 中频真题 3**：向量数据库和传统数据库有什么区别？
> 传统数据库（MySQL）基于精确匹配（where id=123），向量数据库基于近似最近邻（ANN）搜索（找最相似的向量）。向量数据库用 HNSW、IVF 等索引结构在高维空间实现亚线性时间复杂度检索，牺牲少量精度换取速度。

**💡 进阶真题 4**：Self-RAG 和普通 RAG 的核心区别是什么？
> 普通 RAG 无论如何都会检索；Self-RAG 训练模型输出反思标记（Reflection Tokens），模型自主判断"是否需要检索"、"检索结果是否相关"、"答案是否有文档支持"。效果更好，不需要检索时节省成本，但需要专门微调模型。

#### 5.4 实战作业

**Level 1（基础）**：搭建你的第一个 RAG
1. 找一份 PDF（公司介绍、课程大纲、任意英文论文）
2. 用 Part 3.1 的代码跑通基础 RAG
3. 提 10 个问题，记录：有几个答案是正确的？有几个有幻觉？

**Level 2（进阶）**：评测与优化
1. 用 RAGAS 评测你的 RAG 系统（至少 10 个问题的评测集）
2. 尝试修改 chunk_size（分别试试 200/500/1000 字符）
3. 记录 context_recall 和 faithfulness 的变化，写出调优报告

**Level 3（挑战）**：混合检索 + 重排序
1. 实现 BM25 + 向量的混合检索（Part 3.2）
2. 接入 Cohere Rerank 或使用 `BAAI/bge-reranker-v2-m3` 本地重排
3. 对比 Naive RAG vs Advanced RAG 在你的评测集上的分数差异

---

## 引用说明

| 编号 | 引用来源 | 引用位置 | 说明 |
|------|---------|---------|------|
| [1] | Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks", NeurIPS 2020 | Part 1.2 | RAG 原始论文（Facebook AI） |
| [2] | Gao et al., "Precise Zero-Shot Dense Retrieval without Relevance Labels", ACL 2023 | Part 4.2 | HyDE 方法原论文 |
| [3] | Asai et al., "Self-RAG: Learning to Retrieve, Generate, and Critique through Self-Reflection", NeurIPS 2023 | Part 4.3 | Self-RAG 原论文 |
| [4] | Gao et al., "Modular RAG: Transforming RAG Systems into LEGO-like Reconfigurable Frameworks", arXiv 2024 | Part 4.3 | Modular RAG 综述 |
| [5] | Edge et al., "From Local to Global: A Graph RAG Approach to Query-Focused Summarization", Microsoft 2024 | Part 4.4 | GraphRAG 论文 |
| [6] | Es et al., "RAGAS: Automated Evaluation of Retrieval Augmented Generation", EACL 2024 | Part 3.3 | RAGAS 评测框架论文 |
| [7] | Robertson & Zaragoza, "The Probabilistic Relevance Framework: BM25 and Beyond", 2009 | Part 3.2 | BM25 算法原论文 |

---

## 必须包含的元素

- [x] YAML 元信息（版本/日期/复审时间）
- [x] Wow Moment（RAG 让准确率从 20% → 85%）
- [x] 术语速查表（10 个核心术语）
- [x] RAG vs Fine-tuning 决策框架（含流程图）
- [x] Embedding + 余弦相似度数学公式
- [x] Chunking 五种策略对比表
- [x] 向量数据库选型表（6 种）
- [x] 三段完整代码（基础RAG / 混合检索 / RAGAS 评测）
- [x] 四代 RAG 演进（Naive→Advanced→Modular→Agentic）
- [x] HyDE 代码实现
- [x] 生产级五大质量陷阱表
- [x] 面试真题（4 道，含 🔥⚠️💡 标注）
- [x] 三层作业（基础/进阶/挑战）
- [x] 引用说明（7 条权威来源）

---

## 与其他课程的关系

```
课程06 Prompt Engineering
（Prompt 模板 / Context 管理）
        │
        │ RAG 的 Generation 阶段本质是 Prompt Engineering
        ▼
课程07 选型与部署 ──────→ 课程08（本课）
（Embedding 模型选型）     RAG 与知识检索四代演进
（vLLM/Ollama 部署）              │
                      ┌───────────┼──────────┐
                      ▼           ▼          ▼
                课程09 Agent  课程11      课程12
                （Agent使用   AI编程助手  OpenClaw
                RAG作为工具） (代码检索   （系统集成
                             增强）       RAG服务）
```

**前置依赖**：
- **课程 06**（必要）：RAG 的 Generation 阶段本质是精心设计的 Prompt，理解 Prompt Engineering 直接影响 RAG 答案质量
- **课程 07**（推荐）：Embedding 模型的选型和量化部署，影响 RAG 系统的检索速度和成本

**后续影响**：
- **课程 09（Agent）**：RAG 是 Agent 最核心的工具之一，Agent 通过 ReAct 框架动态决定何时调用 RAG
- **课程 11（AI 编程助手）**：代码库检索是 RAG 的重要变体，Cursor/GitHub Copilot 的上下文填充本质是代码 RAG
- **课程 12（OpenClaw）**：企业级 AI 管家系统中，RAG 是知识管理层的核心组件
