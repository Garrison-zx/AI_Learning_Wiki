# 课程 6：知识检索专题（各种检索方法原理和对比应用）

> **学习时长**：60 分钟
> **先修知识**：课程 5（RAG 方法）
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 6.1 检索方法全景图

```
信息检索方法分类:

├── 关键词检索（Lexical）
│   ├── TF-IDF
│   ├── BM25（工业标准）
│   └── 倒排索引（Elasticsearch）
│
├── 语义检索（Semantic / Dense）
│   ├── 双塔模型（Bi-Encoder）
│   ├── 单塔模型（Cross-Encoder）
│   ├── 多向量检索（ColBERT）
│   └── 混合嵌入（Contriever, BGE）
│
├── 混合检索（Hybrid）
│   ├── 线性加权融合（RRF）
│   ├── 学习排序（Learning to Rank）
│   └── 自适应融合
│
└── 图检索（Graph-based）
    ├── 知识图谱检索
    └── GraphRAG
```

### 6.2 BM25 详解

**BM25（Best Matching 25）** 是搜索引擎的经典算法。

```
BM25 评分公式：
Score(Q, D) = Σ IDF(qᵢ) × (tf(qᵢ, D) × (k₁ + 1)) / (tf(qᵢ, D) + k₁ × (1 - b + b × |D|/avgdl))

其中：
- IDF(qᵢ): 逆文档频率（词越稀有，权重越高）
- tf(qᵢ, D): 词在文档中的频率
- k₁: 控制词频饱和度（通常 1.2-2.0）
- b: 控制文档长度归一化（通常 0.75）
- |D|: 文档长度
- avgdl: 平均文档长度
```

**为什么 BM25 仍然有效？**
- 精确匹配专业术语（如产品型号、人名）
- 不受 embedding 训练数据分布影响
- 计算效率极高

### 6.3 稠密检索（Dense Retrieval）

**双塔架构（Bi-Encoder）**：

```
问题 Q ──→ Encoder ──→ 向量 E_Q (768维) ─┐
                                         ├──→ 余弦相似度
文档 D ──→ Encoder ──→ 向量 E_D (768维) ─┘

优点：可以预计算所有文档向量，检索时只需做向量内积
缺点：问题和文档之间没有交互，细粒度匹配能力弱
```

**主流 Embedding 模型**：

| 模型 | 维度 | 特点 |
|------|------|------|
| **BGE (智源)** | 768/1024 | 中文最强，开源 |
| **text-embedding-3 (OpenAI)** | 256/1536 | 多语言，商用 |
| **GTE (阿里)** | 768 | 轻量高效 |
| **E5 (微软)** | 768 | 多任务优化 |

### 6.4 ColBERT：多向量检索

**核心思想**：不用一个向量表示整个文档，而是用每个 token 的向量。

```
传统稠密检索：
  文档 → [一个768维向量]

ColBERT：
  文档 → [token₁向量, token₂向量, ..., tokenₙ向量]

匹配时：
  对问题的每个 token，在文档的所有 token 向量中找最相似的，然后求和
  Score(Q,D) = Σ max_sim(q_token_i, all_d_tokens)
```

**优势**：细粒度 token 级匹配，精度接近 Cross-Encoder，速度快接近 Bi-Encoder。

### 6.5 Cross-Encoder（重排序）

```
问题 Q + 文档 D ──→ Transformer ──→ 相关性分数

与 Bi-Encoder 的区别：
- Bi-Encoder：分别编码，再算相似度 → 快
- Cross-Encoder：拼接后一起编码 → 慢但准

典型流程：
1. BM25/向量检索 → Top-100（快召回）
2. Cross-Encoder 重排 → Top-10（精排序）
```

**常用 Cross-Encoder 模型**：
- `cross-encoder/ms-marco-MiniLM-L-6-v2`
- `BAAI/bge-reranker-large`

### 6.6 GraphRAG

**传统 RAG 的问题**：切分文档破坏了全局信息（如人物关系、事件脉络）。

**GraphRAG 方案**：
1. 用 LLM 提取文档中的实体和关系
2. 构建知识图谱
3. 基于图谱进行多跳推理

```
文本 → LLM 提取 → (实体, 关系, 实体) 三元组 → 图数据库
问题 → 图查询 → 相关子图 → LLM 生成回答
```

---

## 二、代码实践（25 分钟）

### 6.1 混合检索实现（BM25 + 向量）

```python
import numpy as np
from sklearn.feature_extraction.text import TfidfVectorizer
from scipy.spatial.distance import cosine

documents = [
    "Transformer 使用自注意力机制处理序列数据",
    "BERT 是双向 Transformer 编码器",
    "GPT 是自回归 Transformer 解码器",
    "向量数据库用于存储和检索 embedding",
    "BM25 是经典的关键词检索算法",
]

# BM25 检索（用 TF-IDF 近似）
tfidf = TfidfVectorizer()
tfidf_matrix = tfidf.fit_transform(documents)

def bm25_search(query, top_k=3):
    query_vec = tfidf.transform([query])
    scores = (tfidf_matrix * query_vec.T).toarray().flatten()
    top_indices = np.argsort(scores)[::-1][:top_k]
    return [(documents[i], scores[i]) for i in top_indices if scores[i] > 0]

# 向量检索（用 sentence-transformers）
from sentence_transformers import SentenceTransformer

model = SentenceTransformer("BAAI/bge-small-zh-v1.5")
doc_embeddings = model.encode(documents)

def vector_search(query, top_k=3):
    query_embedding = model.encode([query])[0]
    similarities = [1 - cosine(query_embedding, d) for d in doc_embeddings]
    top_indices = np.argsort(similarities)[::-1][:top_k]
    return [(documents[i], similarities[i]) for i in top_indices]

# RRF（Reciprocal Rank Fusion）混合
def hybrid_search(query, top_k=3):
    bm25_results = bm25_search(query, top_k=20)
    vector_results = vector_search(query, top_k=20)
    
    # RRF 融合：score = Σ 1 / (k + rank)
    k = 60  # RRF 常数
    doc_scores = {}
    
    for rank, (doc, _) in enumerate(bm25_results):
        doc_scores[doc] = doc_scores.get(doc, 0) + 1 / (k + rank + 1)
    
    for rank, (doc, _) in enumerate(vector_results):
        doc_scores[doc] = doc_scores.get(doc, 0) + 1 / (k + rank + 1)
    
    sorted_docs = sorted(doc_scores.items(), key=lambda x: x[1], reverse=True)
    return sorted_docs[:top_k]

# 测试
query = "什么是自注意力"
print("BM25:", [d for d, s in bm25_search(query)])
print("向量:", [d for d, s in vector_search(query)])
print("混合:", [d for d, s in hybrid_search(query)])
```

### 6.2 Cross-Encoder 重排序

```python
from sentence_transformers import CrossEncoder

# 加载重排序模型
cross_encoder = CrossEncoder("cross-encoder/ms-marco-MiniLM-L-6-v2")

query = "什么是自注意力机制"
candidates = [
    "Transformer 使用自注意力机制处理序列数据",
    "BERT 是双向 Transformer 编码器",
    "自注意力允许模型关注序列中的所有位置",
    "注意力机制最早用于机器翻译",
    "GPT 使用因果掩码的自注意力",
]

# 计算相关性分数
pairs = [[query, doc] for doc in candidates]
scores = cross_encoder.predict(pairs)

# 排序
ranked = sorted(zip(candidates, scores), key=lambda x: x[1], reverse=True)
for i, (doc, score) in enumerate(ranked):
    print(f"  {i+1}. [{score:.3f}] {doc}")
```

---

## 三、作业与思考题（10 分钟）

1. **对比题**：BM25 和稠密检索各自擅长什么？为什么混合检索通常比单一检索好？

2. **分析题**：ColBERT 的 token 级匹配比文档级匹配好在哪里？代价是什么？

3. **设计题**：一个电商搜索系统需要同时支持精确搜索（商品型号）和语义搜索（"适合夏天的衣服"），你会怎么设计？

---

## 四、扩展阅读

- 📄 **ColBERT** (Khattab & Zaharia, 2020)
- 📄 **GraphRAG** (Microsoft, 2024)
- 🌐 **BGE Embedding 模型**：https://github.com/FlagOpen/FlagEmbedding
- 🌐 **Sentence Transformers 文档**：https://www.sbert.net
