# 课程 7：Agent 原理和应用（ReAct Agent、Harness、Hermes 架构等）

> **学习时长**：60 分钟
> **先修知识**：课程 1-6
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 7.1 什么是 Agent？

**定义**：Agent = 大模型 + 工具调用 + 自主决策循环

```
普通 LLM:   输入 → LLM → 输出（一次性）
Agent:      输入 → LLM → 决定用工具 → 执行工具 → 观察结果 → 循环 → 输出
```

**核心能力**：
- **思考**：分析问题，制定计划
- **行动**：调用外部工具（搜索、代码执行、API）
- **观察**：获取工具执行结果
- **迭代**：根据结果调整策略

### 7.2 ReAct（Reason + Act）

**核心思想**：将推理（Reasoning）和行动（Acting）交替进行。

```
Thought: 我需要先查询今天的天气
Action: search_weather{"location": "北京"}
Observation: 晴，25°C
Thought: 用户还问了是否适合户外运动，我需要结合天气判断
Action: none（已经有足够信息）
Answer: 今天北京晴天25度，非常适合户外运动
```

**ReAct 的标准格式**：
```
Thought → Action → Observation → Thought → Action → Observation → ... → Final Answer
```

### 7.3 主流 Agent 框架对比

| 框架 | 特点 | 适合场景 |
|------|------|---------|
| **LangGraph** | 图结构编排，可控性强 | 复杂多步骤流程 |
| **CrewAI** | 多 Agent 协作，角色分工 | 团队式任务 |
| **AutoGen** | 微软出品，对话式多 Agent | 代码协作、辩论 |
| **OpenClaw** | 个人 AI 助手框架，工具集成 | 个人自动化 |
| **Dify** | 可视化编排，低代码 | 快速搭建应用 |

### 7.4 Agent 架构模式

#### Harness 模式（监督者模式）

```
                    ┌──────────┐
                    │  Supervisor │
                    │  (调度中心)  │
                    └────┬─────┘
                         │
            ┌────────────┼────────────┐
            ▼            ▼            ▼
        ┌──────┐   ┌──────────┐  ┌──────┐
        │Agent A│   │Agent B   │  │Agent C│
        │(搜索) │   │(代码执行) │  │(总结) │
        └──────┘   └──────────┘  └──────┘

流程：
1. Supervisor 接收任务
2. 分配给合适的 Agent
3. 收集结果，决定是否继续
4. 所有 Agent 完成后输出最终答案
```

#### Hermes 模式（自主探索模式）

```
Agent 自主决定：
- 是否需要更多信息？
- 用哪个工具？
- 什么时候停止？

特点：
- 更灵活，适合开放性问题
- 但也可能陷入循环
- 需要设置最大步数
```

### 7.5 Agent 的关键组件

| 组件 | 作用 | 示例 |
|------|------|------|
| **LLM** | 推理引擎 | GPT-4, Claude, LLaMA |
| **工具集** | 执行能力 | 搜索、代码、数据库、API |
| **记忆** | 上下文保持 | 对话历史、知识库 |
| **规划** | 任务分解 | ReAct, ToT, Plan-and-Solve |
| **安全** | 防止滥用 | 工具调用限制、输出过滤 |

### 7.6 多步推理策略

| 策略 | 原理 | 优劣 |
|------|------|------|
| **ReAct** | 思考-行动-观察循环 | 简单有效，但可能冗长 |
| **ToT（Tree of Thoughts）** | 多路径探索，选择最优 | 更准但更贵 |
| **Plan-and-Solve** | 先规划再执行 | 减少错误行动 |
| **Reflexion** | 自我反思，修正错误 | 适合调试类任务 |

---

## 二、代码实践（25 分钟）

### 7.1 实现一个简单的 ReAct Agent

```python
import re
from typing import Dict, Callable

class Tool:
    """工具定义"""
    def __init__(self, name: str, description: str, func: Callable):
        self.name = name
        self.description = description
        self.func = func

class ReActAgent:
    """简单的 ReAct Agent 实现"""
    
    def __init__(self, tools: Dict[str, Tool], max_steps: int = 5):
        self.tools = tools
        self.max_steps = max_steps
    
    def run(self, query: str) -> str:
        """执行 Agent 循环"""
        history = []
        
        for step in range(self.max_steps):
            # 步骤1：构造 Prompt
            prompt = self._build_prompt(query, history)
            
            # 步骤2：调用 LLM（这里用模拟）
            thought, action, action_input = self._llm_decision(prompt)
            print(f"Step {step + 1}:")
            print(f"  Thought: {thought}")
            print(f"  Action: {action}({action_input})")
            
            # 步骤3：执行工具
            if action == "final_answer":
                return action_input
            
            observation = self._execute_tool(action, action_input)
            print(f"  Observation: {observation[:100]}...")
            
            history.append((thought, action, action_input, observation))
        
        return "达到最大步数，无法完成。"
    
    def _build_prompt(self, query, history):
        tools_desc = "\n".join(
            f"- {t.name}: {t.description}" for t in self.tools.values()
        )
        history_text = "\n".join(
            f"Thought: {t}\nAction: {a}\nObservation: {o[:100]}"
            for t, a, _, o in history
        )
        return f"""你是一个智能助手，可以使用工具来回答问题。

可用工具：
{tools_desc}

回答格式：
Thought: [你的思考]
Action: [工具名]
Action Input: [工具输入]
或者
Thought: 我有足够信息了
Final Answer: [最终答案]

历史：
{history_text}

问题：{query}"""
    
    def _execute_tool(self, action: str, action_input: str):
        if action in self.tools:
            return self.tools[action].func(action_input)
        return "未知工具"
    
    def _llm_decision(self, prompt):
        # 实际应用中这里调用 LLM API
        # 这里用规则模拟
        return "模拟思考", "模拟动作", "模拟输入"


# 定义工具
def search_weather(location: str) -> str:
    return f"{location} 今天是晴天，25°C，适合户外运动"

def search_knowledge(query: str) -> str:
    knowledge = {
        "Python": "Python 是一种高级编程语言，由 Guido van Rossum 创建",
        "Transformer": "Transformer 是 2017 年提出的深度学习架构",
    }
    return knowledge.get(query, f"未找到关于 '{query}' 的信息")

tools = {
    "search_weather": Tool("search_weather", "查询某地天气", search_weather),
    "search_knowledge": Tool("search_knowledge", "查询知识库", search_knowledge),
}

agent = ReActAgent(tools)
# agent.run("北京今天天气怎么样？适合跑步吗？")
```

### 7.2 LangGraph Agent 示例

```python
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool
from langgraph.prebuilt import create_react_agent

# 定义工具
@tool
def search_weather(location: str) -> str:
    """查询某个地方的天气"""
    return f"{location} 今天晴，25°C"

@tool
def get_time() -> str:
    """获取当前时间"""
    from datetime import datetime
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")

# 创建 Agent
llm = ChatOpenAI(model="gpt-4o-mini")
tools = [search_weather, get_time]
agent = create_react_agent(llm, tools)

# 执行
result = agent.invoke({
    "messages": [("user", "北京现在几点？天气怎么样？")]
})

print(result["messages"][-1].content)
```

---

## 三、作业与思考题（10 分钟）

1. **理解题**：ReAct 和普通 LLM 调用的区别是什么？为什么交替"思考-行动-观察"更有效？

2. **设计题**：设计一个"旅行规划 Agent"。它需要能搜索航班、酒店、景点，并根据预算制定行程。你会定义哪些工具？Agent 的执行流程是怎样的？

3. **分析题**：Hermes 模式的自主探索可能遇到什么问题？如何防止 Agent 陷入死循环？

---

## 四、扩展阅读

- 📄 **ReAct: Synergizing Reasoning and Acting in Language Models** (Yao et al., 2022)
- 📄 **Toolformer** (Schick et al., 2023) — 让模型学会使用工具
- 🌐 **LangGraph 文档**：https://langchain-ai.github.io/langgraph/
- 🌐 **CrewAI 文档**：https://docs.crewai.com
- 📄 **Hermes**（类人自主探索）：搜索 "Hermes autonomous agent"
