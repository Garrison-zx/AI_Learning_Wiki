# 课程 5：主流 RAG 方法

> **学习时长**：60 分钟
> **先修知识**：课程 1-4
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 5.1 为什么需要 RAG？

**大模型的核心问题**：
1. **知识截止**：训练数据有截止日期，不知道最新信息
2. **幻觉问题**：对不确定内容也会自信地编造
3. **私有数据**：模型没见过的企业/个人数据
4. **可溯源性**：需要知道答案的来源

**RAG（Retrieval-Augmented Generation）解决方案**：
- 先从外部知识库检索相关信息
- 再把检索结果 + 用户问题一起喂给大模型
- 模型基于检索到的事实回答

```
用户问题 → 检索模块 → Top-K 相关文档 → 生成模块 → 回答（带引用来源）
```

### 5.2 Naive RAG（基础版）

```
流程：
1. 文档切分（Chunking）
2. 向量化（Embedding）
3. 存入向量数据库
4. 用户问题 → Embedding → 向量检索 → Top-K
5. Prompt: "根据以下文档回答问题：\n{文档}\n问题：{query}"
6. LLM 生成回答
```

**问题**：
- 检索质量依赖 embedding 质量
- 切分粒度不好把握
- 多跳问题（需要多个文档组合）处理不好

### 5.3 Advanced RAG 技术

| 技术 | 解决的问题 | 原理 |
|------|----------|------|
| **重排序（Re-ranking）** | 向量检索不够精准 | 用 Cross-Encoder 对 Top-K 重新排序 |
| **混合检索** | 纯向量检索遗漏关键词匹配 | 向量检索 + BM25 关键词检索融合 |
| **HyDE** | 问题和文档表达不一致 | 先用 LLM 生成假设文档，再检索 |
| **查询改写** | 用户问题表述不清 | 将问题改写为更适合检索的形式 |
| **上下文压缩** | 检索结果包含无关信息 | 从检索结果中提取与问题相关的片段 |
| **Parent-Child** | 切分太细丢上下文，太粗不精准 | 用小块检索，返回大块（父文档） |
| **Self-RAG** | 无法判断检索结果是否相关 | 模型自主决定是否检索、是否使用检索结果 |

### 5.4 Re-ranking 详解

**两阶段检索**：

```
阶段1（召回）：向量检索 → Top-100（快但粗糙）
阶段2（精排）：Cross-Encoder 重排序 → Top-5（慢但精准）
```

**Bi-Encoder vs Cross-Encoder**：
- **Bi-Encoder**：问题和文档分别编码，计算余弦相似度（快）
- **Cross-Encoder**：问题和文档拼接后一起编码（慢但更准）

### 5.5 主流 RAG 框架

| 框架 | 特点 | 适用场景 |
|------|------|---------|
| **LangChain** | 最流行，组件丰富 | 快速原型开发 |
| **LlamaIndex** | 数据索引专精 | 文档问答、知识库 |
| **RAGFlow** | 深度文档解析 | PDF/表格等复杂文档 |
| **Haystack** | 生产级，可观测性好 | 企业级部署 |

---

## 二、代码实践（25 分钟）

### 5.1 构建一个简单的 RAG 系统

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain.prompts import ChatPromptTemplate

# 1. 文档加载
documents = [
    "Transformer 是 Vaswani 等人在 2017 年提出的架构，完全基于自注意力机制。",
    "BERT 是 Google 在 2018 年提出的，使用 Masked Language Model 进行预训练。",
    "GPT 是 OpenAI 的系列模型，采用自回归方式训练，擅长文本生成。",
    "T5 将所有 NLP 任务统一为 text-to-text 格式，是 Encoder-Decoder 架构。",
    "RAG 通过检索外部知识库增强大模型的知识，减少幻觉。",
]

# 2. 文档切分
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=200,
    chunk_overlap=50,
)
splits = text_splitter.create_documents(documents)

# 3. 向量化 + 存入向量库
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
vectorstore = FAISS.from_documents(splits, embeddings)

# 4. 检索
def retrieve(query: str, top_k: int = 2):
    """检索相关文档"""
    docs = vectorstore.similarity_search(query, k=top_k)
    return [doc.page_content for doc in docs]

# 5. 生成回答
prompt_template = ChatPromptTemplate.from_messages([
    ("system", "你是一个 AI 助手。根据以下文档内容回答问题。如果文档中没有相关信息，请说明不知道。"),
    ("user", "文档内容：\n{context}\n\n问题：{question}")
])

llm = ChatOpenAI(model="gpt-3.5-turbo", temperature=0)

def rag_query(question: str):
    context = retrieve(question)
    context_text = "\n\n".join(context)
    
    chain = prompt_template | llm
    response = chain.invoke({"context": context_text, "question": question})
    
    return response.content, context

# 测试
question = "BERT 和 GPT 的主要区别是什么？"
answer, sources = rag_query(question)
print(f"问题: {question}")
print(f"答案: {answer}")
print(f"来源:\n" + "\n".join(f"  - {s}" for s in sources))
```

### 5.2 混合检索（向量 + BM25）

```python
from langchain.retrievers import EnsembleRetriever
from langchain_community.retrievers import BM25Retriever

# 向量检索器
faiss_retriever = FAISS.from_documents(splits, embeddings).as_retriever(search_kwargs={"k": 3})

# BM25 关键词检索器
bm25_retriever = BM25Retriever.from_documents(splits)
bm25_retriever.k = 3

# 集成检索（Reciprocal Rank Fusion）
ensemble_retriever = EnsembleRetriever(
    retrievers=[bm25_retriever, faiss_retriever],
    weights=[0.5, 0.5],  # BM25 和向量各占一半权重
)

# 测试
docs = ensemble_retriever.invoke("2017 年提出的注意力架构")
for i, doc in enumerate(docs):
    print(f"{i+1}. {doc.page_content}")
```

### 5.3 Parent-Child 检索

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter
import uuid

# 大块（父文档）：保持完整上下文
parent_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
# 小块（子文档）：用于精准检索
child_splitter = RecursiveCharacterTextSplitter(chunk_size=200, chunk_overlap=50)

# 假设有一篇长文档
long_doc = "LLaMA 3 是 Meta 在 2024 年发布的最新一代开源大语言模型..."
# 实际应用中从文件加载

parent_docs = parent_splitter.create_documents([long_doc])

# 为每个父文档生成子文档
child_docs = []
for parent in parent_docs:
    children = child_splitter.split_text(parent.page_content)
    for child in children:
        child_docs.append({
            "content": child,
            "parent_id": parent.page_content  # 关联父文档
        })

# 检索时：用子文档匹配，返回父文档内容
def parent_child_retrieval(query: str):
    # 用子文档检索
    child_embeddings = [OpenAIEmbeddings().embed_query(d["content"]) for d in child_docs]
    query_embedding = OpenAIEmbeddings().embed_query(query)
    
    # 计算相似度并排序
    import numpy as np
    similarities = [np.dot(q, c) / (np.linalg.norm(q) * np.linalg.norm(c)) 
                    for c in child_embeddings]
    
    # 返回对应父文档
    top_indices = np.argsort(similarities)[-3:][::-1]
    return [child_docs[i]["parent_id"] for i in top_indices]
```

---

## 三、作业与思考题（10 分钟）

1. **理解题**：RAG 解决了大模型的哪些问题？又有哪些局限？

2. **设计题**：设计一个企业内部知识库 RAG 系统。你的文档包括 PDF、Word、数据库记录，你会怎么做文档处理和检索？

3. **分析题**：为什么"两阶段检索"（召回 + 精排）比单一检索更精准？

---

## 四、扩展阅读

- 📄 **Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks** (Lewis et al., 2020) — RAG 原始论文
- 🌐 **LlamaIndex 文档**：https://docs.llamaindex.ai
- 🌐 **LangChain RAG 教程**：https://python.langchain.com
- 📄 **Self-RAG** (Asai et al., 2023) — 自主检索
- 🌐 **Pinecone RAG 教程**：https://www.pinecone.io/learn/rag/
