# 课程 10：OpenClaw 架构原理和应用

> **学习时长**：60 分钟
> **先修知识**：课程 1-9
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 10.1 OpenClaw 是什么？

**定义**：OpenClaw 是一个开源的**个人 AI 助手框架**，让你的 AI 拥有持久的记忆、工具调用能力、和多渠道通信能力。

```
OpenClaw = AI 网关（Gateway）+ 技能系统（Skills）+ 记忆系统（Memory）+ 渠道集成（Channels）+ 代理系统（Agents）

核心价值：
- 7×24 小时运行，不像 ChatGPT 需要主动打开
- 有持久记忆（跨会话）
- 有工具能力（日历、邮件、代码执行等）
- 多渠道接入（飞书、Telegram、WhatsApp、Discord...）
```

### 10.2 整体架构

```
┌─────────────────────────────────────────────────────┐
│                   OpenClaw Gateway                   │
│                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────┐ │
│  │   Channels    │  │    Agents     │  │  Tools     │ │
│  │  ┌──────────┐ │  │  ┌────────┐ │  │ ┌────────┐ │ │
│  │  │ 飞书     │ │  │  │ Main   │ │  │ │ Skills │ │ │
│  │  │ Telegram │ │  │  │ Cron   │ │  │ │ Memory │ │ │
│  │  │ WhatsApp │ │  │  │ Sub    │ │  │ │ Browser│ │ │
│  │  │ Discord  │ │  │  └────────┘ │  │ │ Exec   │ │ │
│  │  └──────────┘ │  └──────────────┘  │ └────────┘ │ │
│  └──────────────┘                     └────────────┘ │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │              记忆系统 (Memory)                  │    │
│  │  MEMORY.md（长期记忆）+ memory/（日记）        │    │
│  └──────────────────────────────────────────────┘    │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │              工作空间 (Workspace)               │    │
│  │  AGENTS.md / SOUL.md / USER.md / TOOLS.md     │    │
│  └──────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────┘
```

### 10.3 核心组件详解

#### 1. Gateway（网关）

```
Gateway 是 OpenClaw 的核心守护进程：
- 监听各渠道的消息
- 路由到对应的 Agent
- 管理会话状态
- 处理心跳和定时任务

启动命令：
openclaw gateway start

配置文件：~/.openclaw/openclaw.json
```

#### 2. Agent 系统

```
Agent 类型：
├── Main Agent（主代理）
│   - 处理用户直接对话
│   - 有完整的工作空间上下文
│   - 可以派生子代理
│
├── Cron Agent（定时代理）
│   - 定时执行任务
│   - 独立会话，不污染主上下文
│   - 适合日报、监控等
│
└── Sub-Agent（子代理）
    - 主代理派生出来执行特定任务
    - 隔离的执行环境
    - 完成后自动汇报结果
```

#### 3. 记忆系统

```
短期记忆：会话内的对话历史
长期记忆：MEMORY.md（跨会话持续更新）
日记记忆：memory/YYYY-MM-DD.md（每日详细记录）

记忆更新原则：
- 重要决策 → 写入 MEMORY.md
- 每日事件 → 写入 memory/日记
- 定期从日记提炼到 MEMORY.md
```

#### 4. 技能系统（Skills）

```
每个 Skill 是一个独立的目录，包含：
- SKILL.md：技能描述和使用指南
- 可选的参考文档、脚本等

触发方式：
- 自动匹配：根据任务描述自动选择合适的 Skill
- 手动指定：用户明确要求使用某个 Skill

示例：
- feishu-calendar：飞书日历管理
- feishu-bitable：多维表格操作
- weather：天气查询
- coding-agent：代码任务委托
```

### 10.4 Harness 在 OpenClaw 中的体现

OpenClaw 本身就是 Harness 的一个实现：

```
Tool Use Pattern 在 OpenClaw 中的体现：

1. Agent 分析用户消息
2. 决定是否调用工具（Skill）
3. 调用工具并获取结果
4. 根据结果生成回复
5. 更新记忆系统

关键设计：
- 工具调用是结构化的（JSON Schema）
- Agent 有自己的"人格"（SOUL.md）
- Agent 了解自己的用户（USER.md）
- Agent 有行为准则（AGENTS.md）
```

### 10.5 OpenClaw vs 其他框架

| 特性 | OpenClaw | LangChain | AutoGen |
|------|----------|-----------|---------|
| 定位 | 个人 AI 助手 | 应用开发框架 | 多 Agent 协作 |
| 持久化 | ✅ 记忆系统 | 需自己实现 | 需自己实现 |
| 多渠道 | ✅ 内置 | 需自己集成 | 不支持 |
| 技能系统 | ✅ SKILL.md | Agent 工具 | Agent 工具 |
| 心跳/定时 | ✅ 内置 | 需外部调度 | 不支持 |
| 复杂度 | 中 | 高 | 高 |

---

## 二、代码实践（25 分钟）

### 10.1 OpenClaw 配置结构

```bash
# 查看 OpenClaw 状态
openclaw status

# 配置文件位置
ls ~/.openclaw/
# ├── openclaw.json          # 主配置
# ├── memory/                 # 记忆目录
# └── skills/                 # 技能目录

# 查看配置
cat ~/.openclaw/openclaw.json | python3 -m json.tool
```

### 10.2 创建一个自定义 Skill

```bash
# 创建技能目录
mkdir -p ~/.openclaw/skills/my-skill

# 创建 SKILL.md
cat > ~/.openclaw/skills/my-skill/SKILL.md << 'EOF'
---
name: my-skill
description: 我的自定义技能
---

# My Skill

## 用途
当用户要求 XXX 时使用此技能。

## 使用方法
1. 第一步...
2. 第二步...
EOF
```

### 10.3 记忆系统实践

```python
from pathlib import Path
from datetime import datetime

class MemorySystem:
    """OpenClaw 风格的记忆系统"""
    
    def __init__(self, workspace: str = "/workspace"):
        self.workspace = Path(workspace)
        self.memory_file = self.workspace / "MEMORY.md"
        self.memory_dir = self.workspace / "memory"
        self.memory_dir.mkdir(exist_ok=True)
    
    def today_entry(self) -> Path:
        """获取今天的日记文件"""
        today = datetime.now().strftime("%Y-%m-%d")
        return self.memory_dir / f"{today}.md"
    
    def append_to_diary(self, content: str):
        """追加到今天的日记"""
        entry = self.today_entry()
        if not entry.exists():
            entry.write_text(f"# {entry.stem}\n\n")
        
        with open(entry, "a") as f:
            f.write(f"\n## {datetime.now().strftime('%H:%M')}\n")
            f.write(content + "\n")
    
    def update_long_term(self, section: str, content: str):
        """更新长期记忆"""
        if self.memory_file.exists():
            current = self.memory_file.read_text()
        else:
            current = "# MEMORY.md - Long-Term Memory\n\n"
        
        # 简单追加（实际实现应该做智能合并）
        with open(self.memory_file, "a") as f:
            f.write(f"## {section}\n{content}\n\n")
    
    def get_recent_context(self, days: int = 3) -> str:
        """获取最近 N 天的日记作为上下文"""
        context = []
        for i in range(days):
            d = datetime.now()
            if i > 0:
                from datetime import timedelta
                d = d - timedelta(days=i)
            path = self.memory_dir / f"{d.strftime('%Y-%m-%d')}.md"
            if path.exists():
                context.append(f"\n--- {d.strftime('%Y-%m-%d')} ---\n")
                context.append(path.read_text())
        
        return "\n".join(context)


# 使用示例
mem = MemorySystem()
mem.append_to_diary("完成了 Transformer 课程的内容准备")
mem.update_long_term("学习进度", "- 课程 1-10 内容已准备完毕")
print(mem.get_recent_context())
```

### 10.4 心跳系统理解

```
心跳（Heartbeat）是 OpenClaw 的主动性机制：

每隔一段时间（如 30 分钟），Agent 收到心跳消息：
"Read HEARTBEAT.md if it exists. Follow it strictly. 
If nothing needs attention, reply HEARTBEAT_OK."

Agent 可以：
1. 检查邮件、日历等
2. 做记忆整理
3. 如果无事可做 → 回复 HEARTBEAT_OK

HEARTBEAT.md 内容示例：
- 检查是否有未回复的消息
- 检查 MEMORY.md 是否需要整理
- 检查今天的工作计划是否完成
```

---

## 三、作业与思考题（10 分钟）

1. **理解题**：OpenClaw 的记忆系统解决了传统 ChatGPT 的什么问题？

2. **分析题**：Agent 的 SOUL.md、USER.md、AGENTS.md 各自的作用是什么？它们如何共同塑造 Agent 的行为？

3. **设计题**：如果你想让 OpenClaw 每天自动检查你的 GitHub 仓库有没有新 Issue，你会怎么做？（提示：Cron Agent + 自定义 Skill）

---

## 四、扩展阅读

- 🌐 **OpenClaw 官方文档**：/workspace/openclaw/docs
- 🌐 **OpenClaw 在线文档**：https://docs.openclaw.ai
- 🌐 **GitHub 仓库**：https://github.com/openclaw/openclaw
- 🌐 **社区**：https://discord.com/invite/clawd
- 🌐 **技能市场**：https://clawhub.ai

---

## 附录：10 门课程总结

| # | 课程 | 核心内容 | 文件 |
|---|------|---------|------|
| 1 | Transformer 架构 | 自注意力、多头注意力、位置编码、Encoder/Decoder | 01-transformer-architecture.md |
| 2 | T5 和 BERT | MLM、Text-to-Text、预训练范式 | 02-bert-t5-architecture.md |
| 3 | 大模型基础知识 | 架构演进、Scaling Laws、训练三阶段、Tokenization | 03-llm-fundamentals.md |
| 4 | 大模型应用选型 | 模型分类、选型矩阵、部署方式、量化推理 | 04-llm-application-selection.md |
| 5 | 主流 RAG 方法 | Naive RAG、Advanced RAG、主流框架 | 05-rag-methods.md |
| 6 | 知识检索专题 | BM25、稠密检索、ColBERT、Cross-Encoder、GraphRAG | 06-knowledge-retrieval.md |
| 7 | Agent 原理和应用 | ReAct、Harness、Hermes、多 Agent | 07-agent-principles.md |
| 8 | Claude Code 架构 | 模型训练、Constitutional AI、Harness 方案 | 08-claude-code-architecture.md |
| 9 | OpenAI Codex 架构 | 代码训练、Codex CLI、插件系统、沙箱 | 09-codex-architecture.md |
| 10 | OpenClaw 架构 | 网关、Agent、记忆系统、技能系统、心跳 | 10-openclaw-architecture.md |
