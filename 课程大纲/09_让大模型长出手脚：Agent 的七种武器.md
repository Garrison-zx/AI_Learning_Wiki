---
课程: 09 - 让大模型长出手脚：Agent 的七种武器
版本: v1.0
创建日期: 2026-04-29
最后更新: 2026-04-29
适用模型版本: GPT-4o / Claude 3.5 / LangChain 0.3 / LangGraph 0.2
下次复审: 2026-07-29
更新触发条件:
  - 主流 Agent 框架重大版本更新（LangGraph/AutoGen/CrewAI）
  - OpenAI/Anthropic 发布新的 Function Calling 规范
  - 出现新的 Agent 规划范式（如 OpenAI o3 推理方式普及）
---

# 课程 09：让大模型长出手脚——Agent 的七种武器

> **课程类型**：C 架构型 | **时长**：70 min | **前置课程**：课程 06（Prompt Engineering）、课程 08（RAG）

---

## 逻辑主线

```
"大模型只会说话，怎么让它干活？"
        ↓
[核心转变] LLM → Agent
  语言引擎 + 规划能力 + 工具调用 + 记忆系统 = 自主行动的智能体
        ↓
[七种武器] 从简单到复杂，逐层装备
  ① 函数调用  — 连接外部世界的接口
  ② 代码执行  — 精确计算与数据处理
  ③ 记忆系统  — 短期 / 长期 / 语义记忆
  ④ 知识检索  — 私有知识的动态注入（RAG as Tool）
  ⑤ 规划引擎  — ReAct / CoT / Tree-of-Thought
  ⑥ 多模态感知 — 眼睛：处理图像、音频、文档
  ⑦ 多智能体  — 分工协作，超越单体能力上限
        ↓
[架构演进] 单 Agent → 监督型多 Agent → 层级式 Agent 网络
        ↓
"能在真实世界中完成端到端复杂任务的 AI 系统"
```

---

## 课程简介

本课解决一个核心问题：**如何把"只会说话"的大模型，变成"能干活"的智能体（Agent）？**

2025-2026 年，Agent 是 AI 工程领域最热的话题——从 OpenAI 的 Operator、Anthropic 的 Computer Use，到 AutoGPT 的百万用户，Agent 系统正在从实验走向生产。

本课不讲泛泛的 Agent 概念，而是：
- 拆解 Agent 的四个核心组成（感知/规划/记忆/行动）
- 逐一讲清七种武器的原理、代码、适用场景
- 重点讲清 ReAct 框架（当前主流 Agent 规划范式）
- 分析多 Agent 系统的架构模式与生产挑战

---

## 课程描述：学完本课你能收获

| 能力 | 具体表现 |
|------|---------|
| **概念清晰** | 准确解释 Agent 与 LLM 的区别，画出 Agent 的四层架构图 |
| **工具掌握** | 用 Function Calling 让 LLM 调用外部 API |
| **规划理解** | 解释 ReAct 的推理-行动循环，对比 CoT 和 ToT |
| **架构设计** | 设计简单的多 Agent 系统，分配角色和工具 |
| **生产意识** | 识别 Agent 系统的五大生产风险（幻觉工具调用/无限循环等）|
| **面试应答** | 回答"Agent 和 RAG 的关系"、"如何防止 Agent 跑偏"等高频题 |

---

## Wow Moment 🎯

> **GPT-4 单独回答 HotpotQA 多跳推理题（需要跨多个文档推理）的准确率约 33%；引入 ReAct（Reasoning + Acting）框架，让模型交替进行"推理"和"工具调用"后，准确率提升到 76%——Agent 不是让模型变聪明，而是给模型装上了"手"，让它能边思考边行动。**

引导设问（讲师口播）：
- "你有没有让 ChatGPT 帮你查今天的股价？它说'我没有实时数据'？"
- "你有没有想过，如果 AI 能自己搜索、自己写代码、自己验证答案，会发生什么？"
- "Agent 和 ChatGPT 对话有什么本质区别？"

---

## 术语速查表

| 术语 | 一句话定义 | 首次出现位置 |
|------|-----------|------------|
| Agent（智能体） | LLM + 规划 + 工具 + 记忆，能自主完成多步任务的系统 | Part 1.1 |
| Function Calling | LLM 输出结构化 JSON 调用预定义函数，实现外部工具集成 | Part 1.3 |
| ReAct | Reasoning + Acting 的交替循环：思考 → 行动 → 观察 → 再思考 | Part 2.1 |
| Tool Use | 模型使用工具（搜索/计算/API/代码执行）的能力统称 | Part 1.3 |
| 工作记忆（Working Memory） | Agent 当前对话/任务中的临时信息存储，对应 Context Window | Part 1.2 |
| 长期记忆（Long-term Memory） | 跨会话持久化的知识，通常通过向量数据库或数据库实现 | Part 1.2 |
| Tree-of-Thought（ToT） | 让模型并行探索多条推理路径，如树状搜索，选最优分支 | Part 2.2 |
| 计划-执行架构（Plan-and-Execute） | 先生成完整任务计划，再逐步执行，避免短视决策 | Part 2.3 |
| 多 Agent 系统 | 多个专门化 Agent 协作，分工完成复杂任务 | Part 4.1 |
| LangGraph | 基于有向图的 Agent 工作流框架，支持循环和条件分支 | Part 3.3 |

---

## 课程结构总览

| Part | 主题 | 时长 | 核心交付 |
|------|------|------|---------|
| **Part 0** | 直觉导入：从对话到行动 | 5 min | 类比 + Agent 四层架构预告 |
| **Part 1** | Agent 架构：四层解剖 | 15 min | 感知/规划/记忆/行动 + 七种武器地图 |
| **Part 2** | 规划范式：ReAct 与进化 | 10 min | ReAct 六层追问 + CoT/ToT 对比 |
| **Part 3** | 代码实战：三段逐层构建 | 15 min | Function Calling / ReAct Agent / LangGraph |
| **Part 4** | 架构视角：多 Agent 系统 | 20 min | 三种架构模式 + 生产五大风险 |
| **Part 5** | 总结与作业 | 5 min | 面试真题 + 实战挑战 |

---

## 章节大纲

### Part 0：直觉导入——从对话到行动（5 min）

#### 0.1 类比：助理 vs 行动者（2 min）

**纯 LLM（对话模式）**：
> 就像一个聪明的顾问——你问他"怎么订机票"，他给你一个详细的操作步骤。但他不会帮你打开网页、填写表单、点击确认按钮。

**Agent（行动模式）**：
> 就像一个能干的秘书——你说"帮我订明天北京到上海的机票"，他打开网页、对比价格、选择最优航班、填写信息、等你确认后完成支付，最后把确认邮件发给你。

**关键差异**：
```
LLM：感知输入 → 生成文字输出       （一步）
Agent：感知输入 → 规划 → 工具调用 → 观察结果 → 迭代 → 输出  （多步循环）
```

#### 0.2 Agent 的四层架构预告（3 min）

```
┌─────────────────────────────────────────────────┐
│                  Agent 四层架构                   │
│                                                 │
│  ① 感知层（Perception）                          │
│     文本 / 图像 / 音频 / 代码 / 文档 → 结构化输入  │
│                    ↓                            │
│  ② 规划层（Planning）                            │
│     LLM 核心大脑：任务分解 / 策略选择 / 步骤推理   │
│                    ↓                            │
│  ③ 记忆层（Memory）                              │
│     工作记忆（上下文）+ 长期记忆（向量DB）          │
│                    ↓                            │
│  ④ 行动层（Action）                              │
│     工具调用 / 代码执行 / API 请求 / 文件操作      │
└─────────────────────────────────────────────────┘
```

---

### Part 1：Agent 架构——四层解剖与七种武器（15 min）

#### 1.1 Agent 的正式定义（3 min）

**学术定义**（Wooldridge & Jennings, 1995，沿用至今）：
> An agent is a computer system situated in some environment, that is capable of **autonomous action** in this environment in order to meet its design objectives.

**工程定义**（2024 年实践共识）：
> Agent = LLM（大脑）+ 感知（眼耳）+ 记忆（海马体）+ 工具（手脚）+ 规划（前额叶）

**Agent 的本质特征**：
1. **自主性（Autonomy）**：不需要每一步都等待人类指令
2. **反应性（Reactivity）**：能感知环境变化并做出响应
3. **主动性（Pro-activity）**：能主动采取行动追求目标
4. **社会性（Social Ability）**：能与其他 Agent 或人类协作

**与 LLM 调用的核心区别**：

| 维度 | 单次 LLM 调用 | Agent 系统 |
|------|-------------|-----------|
| 步骤数 | 1 步（输入→输出） | N 步（循环迭代） |
| 工具 | 无 | 有（搜索/代码/API等）|
| 记忆 | 仅当次上下文 | 跨步骤、跨会话记忆 |
| 决策 | 无 | LLM 自主决策下一步 |
| 目标 | 完成当前请求 | 完成长期目标 |

#### 1.2 记忆系统的四种类型（5 min）

类比人类记忆系统：

```
人类记忆            Agent 记忆             技术实现
────────────       ────────────          ──────────
感觉记忆             输入缓冲              Token 窗口限制
（毫秒级）          （当前请求）

工作记忆             上下文窗口             Context Window
（秒-分钟）         （本次对话）           （4K-200K tokens）

情节记忆             对话历史               消息历史列表
（具体事件）        （记得说过什么）        （数据库持久化）

语义记忆             知识记忆               向量数据库（RAG）
（事实概念）        （了解什么）            + Embedding 检索

程序记忆             技能/工具记忆          预定义 Tool Schema
（如何做事）        （会用什么工具）        + Few-shot Examples
```

**记忆的工程挑战**：
- **上下文窗口有限**：长任务必须压缩或外化记忆
- **跨会话记忆**：默认情况下每次对话独立，需要显式设计
- **记忆一致性**：更新知识时避免矛盾（向量数据库版本控制）

#### 1.3 七种武器详解（7 min）

**武器① 函数调用（Function Calling）**

OpenAI 在 2023 年 6 月引入，现已成为事实标准。

原理：LLM 输出一个结构化 JSON，描述"调用哪个函数 + 传什么参数"，然后由外部代码实际执行。

```
用户: "北京明天天气怎么样？"
LLM 输出: {"name": "get_weather", "arguments": {"city": "北京", "date": "tomorrow"}}
系统执行函数 → 返回结果 → LLM 生成自然语言答案
```

适用场景：数据库查询、外部 API、实时数据获取、业务操作

**武器② 代码执行（Code Interpreter）**

原理：LLM 生成 Python/JS 代码 → 在沙箱环境执行 → 返回执行结果（输出/图表/文件）

优势：绕过 LLM 的计算局限（如精确数学、数据分析、文件处理）

案例：ChatGPT 的 Code Interpreter 能处理 Excel 文件、绘制图表、做统计分析

**武器③ 记忆系统（Memory）**

（详见 1.2，此处略，强调工程实现）：
- 短期：滑动窗口 + 摘要压缩
- 长期：写入向量DB + 按需检索

**武器④ 知识检索（RAG as Tool）**

将 RAG 封装为一个工具：Agent 自主决定"何时检索"、"检索什么"，而不是每次都检索。
这是 Naive RAG（总是检索）→ Agentic RAG（按需检索）的关键跨越。

**武器⑤ 规划引擎（Planning）**

核心是 **ReAct**（详见 Part 2），让模型交替 Reasoning 和 Acting。

**武器⑥ 多模态感知（Perception）**

视觉输入：GPT-4V / Claude 3.5 可以看截图、图表、UI 界面
- Computer Use（Anthropic 2024）：Agent 直接控制计算机屏幕
- Document Understanding：处理 PDF、扫描件、表格

**武器⑦ 多智能体协作（Multi-Agent）**

单个 Agent 的瓶颈：上下文窗口有限、专业能力有限、串行执行慢
解决方案：多个专门化 Agent 并行协作（详见 Part 4）

**七种武器适用场景速查**：

| 武器 | 解决什么问题 | 典型工具 |
|------|------------|---------|
| 函数调用 | 连接外部系统（API/DB/服务）| OpenAI Function Calling |
| 代码执行 | 精确计算、数据处理、文件操作 | Code Interpreter / E2B |
| 记忆系统 | 长任务状态保持、跨会话一致性 | LangChain Memory / Redis |
| 知识检索 | 私有知识注入、动态信息获取 | RAG + 向量DB |
| 规划引擎 | 多步任务分解与执行 | ReAct / Plan-and-Execute |
| 多模态感知 | 处理非文本输入（图、音、屏幕）| GPT-4V / Computer Use |
| 多智能体 | 复杂并行任务、专业分工 | LangGraph / AutoGen |

---

### Part 2：规划范式——ReAct 与进化（10 min）

#### 2.1 ReAct：当前主流规划范式（6 min）

> 论文：Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models", ICLR 2023

**直觉类比**：
> 想象你在做一道复杂的菜。纯"思考"模式是：想好所有步骤再开始做，中间不看菜谱。ReAct 模式是：**边想边做**——想一步，做一步，看结果，根据结果调整下一步。

**ReAct 的三元素循环**：

```
[Thought]（推理）：分析当前情况，决定下一步行动
[Action]（行动）：调用特定工具（搜索/计算/API）
[Observation]（观察）：接收工具返回的结果
     ↓ 循环直到任务完成 ↓
[Final Answer]（答案）：综合所有中间步骤给出最终答案
```

**ReAct 完整示例**（多跳推理问题）：

```
问题：苹果公司成立时所在城市的市长，现在是谁？

Thought 1: 我需要知道苹果公司成立时在哪个城市。苹果公司1976年成立于加利福尼亚州库比蒂诺市。
Action 1: Search["库比蒂诺市长 2026"]
Observation 1: 库比蒂诺市长 Sheila Mohan，任期至 2026 年 12 月。

Thought 2: 我已经找到了答案。苹果公司成立于库比蒂诺，现任市长是 Sheila Mohan。
Final Answer: 苹果公司成立时所在城市（加州库比蒂诺）的现任市长是 Sheila Mohan。
```

**ReAct 的六层追问**：

| 层次 | 问题 | 答案 |
|------|------|------|
| **是什么** | ReAct 是什么？ | 交替进行推理（Reasoning）和行动（Acting）的 Agent 规划框架 |
| **为什么** | 为什么需要 ReAct？ | 纯推理（CoT）无法获取外部信息；纯行动（工具调用）缺乏推理，容易迷失目标 |
| **怎么做** | ReAct 如何实现？ | Prompt 中包含 Thought/Action/Observation 的格式范例，LLM 按格式输出 |
| **局限性** | ReAct 有什么问题？ | 串行执行慢；步骤数不受控（可能无限循环）；每步都依赖前一步输出 |
| **改进方向** | 怎么改进 ReAct？ | Plan-and-Execute（先规划后执行）+ Reflection（反思纠错）|
| **实测效果** | 效果数据？ | HotpotQA 多跳推理：CoT 47% → ReAct 76%（+29pp）；幻觉率降低 |

**🔥 面试考点**：ReAct 和 CoT 的区别——CoT 是纯思维链（不调用工具，在模型内部推理），ReAct 在推理步骤中插入真实的外部工具调用，能获取实时信息和精确计算结果。

#### 2.2 规划范式进化树（4 min）

**第一代：链式推理（Chain-of-Thought）**
> 论文：Wei et al., 2022 - "Chain-of-Thought Prompting Elicits Reasoning in LLMs"
- 原理：让模型输出中间推理步骤
- 局限：无工具，纯内部推理，不能获取外部信息

**第二代：ReAct（推理-行动循环）**
> 论文：Yao et al., ICLR 2023
- 改进：推理步骤中插入实际工具调用
- 局限：串行，无回溯，错了一步全错

**第三代：Tree-of-Thought（思维树）**
> 论文：Yao et al., 2023 - "Tree of Thoughts: Deliberate Problem Solving with LLMs"
- 原理：并行生成多条推理路径（树状），用 BFS/DFS 搜索最优解
- 优势：能回溯错误路径，全局最优
- 局限：计算成本 5-10x，适合高价值问题
- 适用：数学证明、代码调试、游戏策略

**第四代：Plan-and-Execute（规划-执行分离）**
- 原理：阶段一（规划）：LLM 生成完整任务计划；阶段二（执行）：按计划逐步执行
- 优势：整体目标清晰，不会"只见树木不见森林"
- 适用：多步骤复杂任务（数据分析报告生成、代码重构等）

**四种规划范式对比**：

| 范式 | 工具 | 回溯 | 并行 | 成本 | 推荐场景 |
|------|------|------|------|------|---------|
| CoT | ✗ | ✗ | ✗ | 低 | 纯推理问题 |
| ReAct | ✓ | ✗ | ✗ | 中 | 大多数 Agent 任务 |
| Tree-of-Thought | ✓ | ✓ | ✓ | 高 | 复杂搜索/优化问题 |
| Plan-and-Execute | ✓ | 有限 | 部分 | 中高 | 长流程复杂任务 |

---

### Part 3：代码实战——三段逐层构建（15 min）

#### 3.1 Function Calling：让 LLM 调用外部工具（5 min）

```python
# Function Calling：最基础的 Agent 能力
# 场景：天气 + 计算器 + 股票查询三合一助手
import json
from openai import OpenAI

client = OpenAI()

# ── 定义工具（Tool Schema）────────────────────────────────
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "获取指定城市的当前天气信息",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "城市名，如'北京'、'上海'"},
                    "unit": {"type": "string", "enum": ["celsius", "fahrenheit"],
                             "description": "温度单位，默认 celsius"}
                },
                "required": ["city"]
            }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "calculate",
            "description": "执行数学计算，返回精确结果",
            "parameters": {
                "type": "object",
                "properties": {
                    "expression": {"type": "string",
                                   "description": "Python 数学表达式，如 '2**10 + 3*7'"}
                },
                "required": ["expression"]
            }
        }
    }
]

# ── 工具的实际实现 ─────────────────────────────────────────
def get_weather(city: str, unit: str = "celsius") -> dict:
    """模拟天气 API（实际应替换为真实 API 调用）"""
    mock_data = {
        "北京": {"temp": 22, "condition": "晴", "humidity": 45},
        "上海": {"temp": 18, "condition": "阴有小雨", "humidity": 78},
    }
    weather = mock_data.get(city, {"temp": 20, "condition": "未知", "humidity": 60})
    return {**weather, "unit": unit, "city": city}

def calculate(expression: str) -> dict:
    """安全执行数学计算（仅允许数学运算）"""
    import ast, operator
    # 白名单安全计算
    allowed_names = {k: v for k, v in vars(__builtins__).items()
                     if k in ['abs', 'round', 'min', 'max', 'sum', 'pow']}
    try:
        result = eval(expression, {"__builtins__": allowed_names})
        return {"result": result, "expression": expression}
    except Exception as e:
        return {"error": str(e)}

TOOL_FUNCTIONS = {"get_weather": get_weather, "calculate": calculate}

# ── Agent 循环（工具调用 → 执行 → 回复）──────────────────────
def run_agent(user_message: str) -> str:
    """完整的 Function Calling 循环"""
    messages = [{"role": "user", "content": user_message}]

    # 最多循环 5 次（防止无限循环）
    for step in range(5):
        response = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=messages,
            tools=tools,
            tool_choice="auto"  # auto：LLM 自主决定是否调用工具
        )

        message = response.choices[0].message
        finish_reason = response.choices[0].finish_reason

        # 不需要工具：直接返回答案
        if finish_reason == "stop":
            return message.content

        # 需要工具：执行工具调用
        if finish_reason == "tool_calls":
            messages.append(message)  # 把 assistant 的工具调用请求加入历史

            for tool_call in message.tool_calls:
                func_name = tool_call.function.name
                func_args = json.loads(tool_call.function.arguments)

                print(f"  [Step {step+1}] 调用工具: {func_name}({func_args})")
                result = TOOL_FUNCTIONS[func_name](**func_args)
                print(f"  [Step {step+1}] 工具结果: {result}")

                # 把工具结果加入消息历史
                messages.append({
                    "role": "tool",
                    "tool_call_id": tool_call.id,
                    "content": json.dumps(result, ensure_ascii=False)
                })

    return "任务未完成（超过最大步骤数）"

# 测试
print(run_agent("北京今天天气怎么样？如果气温是摄氏度，换算成华氏度是多少？"))
# 输出：
#   [Step 1] 调用工具: get_weather({"city": "北京"})
#   [Step 1] 工具结果: {"temp": 22, "condition": "晴", ...}
#   [Step 2] 调用工具: calculate({"expression": "22 * 9/5 + 32"})
#   [Step 2] 工具结果: {"result": 71.6, ...}
# 最终回复：北京今天晴天，气温22°C，换算成华氏度是71.6°F。
```

#### 3.2 ReAct Agent：推理-行动循环（5 min）

```python
# 基于 LangChain 的 ReAct Agent（带搜索和计算工具）
# pip install langchain langchain-openai langchain-community duckduckgo-search

from langchain_openai import ChatOpenAI
from langchain.agents import create_react_agent, AgentExecutor
from langchain_community.tools import DuckDuckGoSearchRun
from langchain.tools import tool
from langchain import hub

# ── 定义工具 ──────────────────────────────────────────────
search_tool = DuckDuckGoSearchRun(name="web_search",
    description="搜索互联网获取最新信息，输入搜索关键词")

@tool
def python_calculator(expression: str) -> str:
    """执行 Python 数学表达式计算。示例输入：'2**10 + sqrt(144)'"""
    import math
    safe_globals = {k: v for k, v in vars(math).items() if not k.startswith('_')}
    safe_globals['__builtins__'] = {}
    try:
        return str(eval(expression, safe_globals))
    except Exception as e:
        return f"计算错误：{e}"

@tool
def get_current_date(_: str = "") -> str:
    """获取今天的日期"""
    from datetime import datetime
    return datetime.now().strftime("%Y年%m月%d日，星期%A")

tools = [search_tool, python_calculator, get_current_date]

# ── 加载 ReAct Prompt 模板（hub 提供标准模板）─────────────────
# hwchase17/react 是 LangChain Hub 上最常用的 ReAct prompt
prompt = hub.pull("hwchase17/react")
# 模板核心格式：
# "Thought: [推理]"
# "Action: [工具名]"
# "Action Input: [工具输入]"
# "Observation: [工具返回结果]"
# ... 循环 ...
# "Final Answer: [最终答案]"

# ── 构建 Agent ────────────────────────────────────────────
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
agent = create_react_agent(llm=llm, tools=tools, prompt=prompt)

agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,          # 显示每一步的 Thought/Action/Observation
    max_iterations=8,      # 最多 8 步，防止无限循环
    handle_parsing_errors=True  # 格式出错时自动恢复
)

# 测试多跳推理：需要搜索 + 计算
result = agent_executor.invoke({
    "input": "Claude Code 最新版本是什么？如果每天有 10 万用户，每人平均使用 50 分钟，"
             "一年总计多少人/小时的使用时长？"
})

print(f"\n最终答案：{result['output']}")
# 中间过程（verbose=True 会显示）：
# Thought: 我需要先搜索 Claude Code 的最新版本
# Action: web_search
# Action Input: "Claude Code latest version 2026"
# Observation: [搜索结果...]
# Thought: 现在我需要计算使用时长
# Action: python_calculator
# Action Input: "100000 * 50 / 60 * 365"
# Observation: 304166666.67
# Final Answer: ...
```

#### 3.3 LangGraph：有状态的 Agent 工作流（5 min）

```python
# LangGraph：用状态图管理复杂 Agent 流程
# 场景：带人工确认节点的文件处理 Agent
# pip install langgraph

from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, AIMessage
from typing import TypedDict, Annotated, List
import operator

# ── 定义 Agent 状态 ──────────────────────────────────────
class AgentState(TypedDict):
    messages: Annotated[List, operator.add]   # 消息历史（追加式）
    task: str                                  # 任务描述
    approved: bool                             # 是否通过人工审核
    result: str                                # 最终结果

# ── 定义节点函数 ──────────────────────────────────────────
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
llm_with_tools = llm.bind_tools(tools)  # 绑定工具

def agent_node(state: AgentState) -> AgentState:
    """LLM 决策节点：根据当前状态决定下一步行动"""
    response = llm_with_tools.invoke(state["messages"])
    return {"messages": [response]}

def human_approval_node(state: AgentState) -> AgentState:
    """人工审核节点：暂停执行，等待人工确认"""
    last_message = state["messages"][-1]
    print(f"\n⚠️  需要人工确认：{last_message.content[:200]}")
    approval = input("是否批准？(y/n): ").strip().lower()
    return {"approved": approval == "y"}

def should_continue(state: AgentState) -> str:
    """条件路由：决定下一个节点"""
    last_message = state["messages"][-1]

    # 有工具调用 → 执行工具
    if hasattr(last_message, "tool_calls") and last_message.tool_calls:
        # 检查是否是高风险操作（如删除文件）
        for tool_call in last_message.tool_calls:
            if tool_call["name"] in ["delete_file", "send_email"]:
                return "human_review"  # 需要人工确认
        return "tools"                 # 普通工具直接执行

    # 没有工具调用 → 任务完成
    return END

def after_approval(state: AgentState) -> str:
    """审核后路由：批准则继续，拒绝则终止"""
    return "tools" if state.get("approved") else END

# ── 构建状态图 ────────────────────────────────────────────
workflow = StateGraph(AgentState)

# 添加节点
workflow.add_node("agent", agent_node)
workflow.add_node("tools", ToolNode(tools))
workflow.add_node("human_review", human_approval_node)

# 设置入口
workflow.set_entry_point("agent")

# 添加条件边（路由逻辑）
workflow.add_conditional_edges("agent", should_continue,
    {"tools": "tools", "human_review": "human_review", END: END})
workflow.add_edge("tools", "agent")        # 工具执行完 → 回到 agent
workflow.add_conditional_edges("human_review", after_approval,
    {"tools": "tools", END: END})           # 审核后路由

# 编译为可执行图
app = workflow.compile()

# 运行 Agent
initial_state = {
    "messages": [HumanMessage(content="搜索今天 AI 领域的最新新闻，生成一份简报")],
    "task": "生成 AI 新闻简报",
    "approved": False,
    "result": ""
}

for step in app.stream(initial_state):
    node_name = list(step.keys())[0]
    print(f"\n[节点：{node_name}]")
    if "messages" in step[node_name]:
        last_msg = step[node_name]["messages"][-1]
        print(f"消息：{str(last_msg)[:150]}...")
```

---

### Part 4：架构视角——多 Agent 系统（20 min）

#### 4.1 为什么需要多 Agent？（4 min）

**单 Agent 的三大天花板**：

**天花板一：上下文窗口限制**
- 长任务会超出上下文窗口（即使 200K 也不够写一个完整的软件项目）
- 解决：将任务分配给多个 Agent，每个 Agent 处理一个子任务

**天花板二：专业能力瓶颈**
- 单个 Agent 既要写代码又要做测试又要写文档，什么都会但都不精
- 解决：专门化 Agent——CodeAgent（专门写代码）、TestAgent（专门测试）、DocAgent（专门文档）

**天花板三：串行速度慢**
- 单 Agent 必须顺序执行所有步骤
- 解决：并行 Agent——多个子任务同时进行，最后汇总

**案例：AI 写作助手的单/多 Agent 对比**：
```
单 Agent 方式（串行）：
  搜索资料(30s) → 整理大纲(20s) → 写初稿(60s) → 润色(30s) = 总耗时 140s

多 Agent 方式（部分并行）：
  [ResearchAgent 搜索(30s)]
  [OutlineAgent 整理(20s)]   ← 可以并行
       ↓
  [WriterAgent 写初稿(60s)]
       ↓
  [EditorAgent 润色(30s)]
  总耗时：约 90s（节省 35%）
```

#### 4.2 三种多 Agent 架构模式（8 min）

**模式一：监督者-工作者（Supervisor-Worker）**

```
                    ┌──────────────┐
                    │  Supervisor   │
                    │  (调度 Agent)  │
                    └──────┬───────┘
              ┌────────────┼────────────┐
              ▼            ▼            ▼
        ResearchAgent  WriterAgent  FactCheckAgent
        （信息收集）    （内容生成）   （事实核查）
```

- **Supervisor 职责**：接收用户目标 → 分解任务 → 分配给专门 Worker → 收集结果 → 综合输出
- **适用**：任务可以明确分解，各子任务相对独立
- **代表框架**：LangGraph Supervisor、AutoGen

```python
# 简化的 Supervisor 模式实现
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate

supervisor_llm = ChatOpenAI(model="gpt-4o", temperature=0)

SUPERVISOR_PROMPT = """你是一个任务调度 Agent。根据用户的需求，决定下一步应该由哪个 Worker 来处理。

可用 Workers：
- researcher：负责搜索和收集信息
- writer：负责撰写和生成内容
- fact_checker：负责验证事实准确性
- FINISH：所有任务已完成，输出最终结果

当前任务进度：
{history}

用户需求：{objective}

请输出下一步应该调用的 Worker 名称（researcher/writer/fact_checker/FINISH）："""

def supervisor_agent(objective: str, history: list) -> str:
    """Supervisor 决定下一步调用哪个 Worker"""
    prompt = SUPERVISOR_PROMPT.format(
        objective=objective,
        history="\n".join(history) if history else "（刚开始）"
    )
    response = supervisor_llm.invoke(prompt)
    return response.content.strip()

# 简化演示（实际需要接入真实 Worker 实现）
objective = "写一篇关于 Agent 技术趋势的 300 字文章"
history = []
workers_called = []

for step in range(6):
    next_worker = supervisor_agent(objective, history)
    workers_called.append(next_worker)
    print(f"Step {step+1}: Supervisor → 调用 {next_worker}")

    if next_worker == "FINISH":
        print("任务完成！")
        break

    # 模拟 Worker 执行（实际实现中每个 Worker 是独立的 Agent）
    mock_results = {
        "researcher": "已收集到5篇相关论文和3个案例",
        "writer":     "已生成300字草稿",
        "fact_checker":"已核实所有数据，发现1处需更正"
    }
    result = mock_results.get(next_worker, "Unknown worker")
    history.append(f"{next_worker}: {result}")
    print(f"         {next_worker} 完成：{result}")
```

**模式二：对等协作（Peer-to-Peer）**

```
CodeAgent ←──────────────→ ReviewAgent
    ↑                           ↑
    └──────→ TestAgent ─────────┘
```

- 特点：Agent 之间平等交流，互相提供反馈
- 典型案例：ChatDev（用多 Agent 模拟软件公司角色）
- 适用：需要多轮辩论和相互验证的场景（代码审查、论文评审）

**模式三：流水线（Pipeline）**

```
InputAgent → ExtractAgent → AnalyzeAgent → ReportAgent → OutputAgent
```

- 特点：严格顺序，每个 Agent 的输出是下一个的输入
- 适用：ETL 处理、报告生成、标准化工作流
- 优势：可预测、易调试、易监控

**三种架构选型建议**：

| 场景 | 推荐架构 | 理由 |
|------|---------|------|
| 开放式复杂任务（写项目计划、做调研）| Supervisor-Worker | 任务边界不清晰，需要灵活调度 |
| 代码生成 + 审查 + 测试 | Peer-to-Peer | 需要多轮相互验证 |
| 固定流程数据处理 | Pipeline | 流程确定，效率优先 |

#### 4.3 生产环境的五大风险（5 min）

**风险一：幻觉工具调用（Hallucinated Tool Calls）**
- 表现：模型调用不存在的函数，或传入错误参数格式
- 危害：直接报错或产生错误结果
- 防御：严格的 JSON Schema 验证 + 工具调用结果检查 + Fallback 处理

**风险二：无限循环（Infinite Loop）**
- 表现：Agent 反复调用同一工具，无法收敛
- 触发：工具返回无法满足 Agent 期望的结果，Agent 持续重试
- 防御：`max_iterations` 硬限制（通常 10-20 步）+ 循环检测（连续相同 Action 触发终止）

**风险三：目标漂移（Goal Drift）**
- 表现：Agent 在完成子任务过程中偏离了原始目标
- 触发：多步任务中，中间步骤的细节干扰了主线目标
- 防御：每 N 步重新注入原始目标 + Plan-and-Execute 架构（锚定整体计划）

**风险四：权限越界（Privilege Escalation）**
- 表现：Agent 执行了超出授权范围的操作（删除文件、发送邮件、修改系统配置）
- 危害：不可逆的破坏性操作
- 防御：工具白名单 + 人工审核节点（如 LangGraph 的 interrupt）+ 沙箱执行环境

**风险五：成本失控（Cost Explosion）**
- 表现：Agent 生成过多步骤，或频繁调用昂贵 API
- 案例：某 Agent 系统因无限循环，一次请求消耗了 $500 的 API 费用
- 防御：Token 预算上限 + 步骤数上限 + 实时成本监控告警

```python
# 生产级 Agent 保护措施
class SafeAgentExecutor:
    """带安全保护的 Agent 执行器"""

    def __init__(self, agent, tools, max_iterations=10,
                 max_tokens_budget=5000, blocked_tools=None):
        self.agent = agent
        self.tools = tools
        self.max_iterations = max_iterations
        self.max_tokens_budget = max_tokens_budget
        self.blocked_tools = blocked_tools or []
        self.iteration_count = 0
        self.tokens_used = 0
        self.action_history = []

    def validate_tool_call(self, tool_name: str, tool_args: dict) -> tuple[bool, str]:
        """工具调用前验证"""
        # 检查黑名单
        if tool_name in self.blocked_tools:
            return False, f"工具 '{tool_name}' 已被禁用"

        # 检查循环调用（连续3次相同工具+参数）
        recent = self.action_history[-3:] if len(self.action_history) >= 3 else []
        if all(a == (tool_name, str(tool_args)) for a in recent):
            return False, f"检测到循环调用，终止执行"

        return True, "OK"

    def run(self, user_input: str) -> dict:
        """执行 Agent，带完整安全保护"""
        for step in range(self.max_iterations):
            self.iteration_count = step + 1

            # Token 预算检查
            if self.tokens_used > self.max_tokens_budget:
                return {"status": "budget_exceeded", "iterations": step,
                        "message": f"已超出 Token 预算（{self.tokens_used} > {self.max_tokens_budget}）"}

            # （实际 Agent 执行逻辑简化）
            # action = self.agent.step(...)
            # valid, reason = self.validate_tool_call(action.tool, action.args)
            # if not valid: return {"status": "blocked", "reason": reason}
            # self.action_history.append((action.tool, str(action.args)))

        return {"status": "max_iterations_reached", "iterations": self.max_iterations}
```

#### 4.4 2026 Agent 技术前沿（3 min）

**Computer Use（计算机控制）**：
- Anthropic Claude 3.5（2024.10）：可以操控鼠标和键盘，直接使用电脑
- 场景：自动填表、网页操作、软件测试
- 挑战：视觉定位精度、操作速度、安全边界

**OpenAI Operator（2025.01）**：
- 在浏览器中自主完成网购、预订、表单填写
- 关键进展：商业化落地的 Agent 产品

**Agent Protocol 标准化**：
- 2025 年出现多个 Agent 通信协议（A2A Protocol、MCP）
- 目标：不同公司的 Agent 能互相调用，形成 Agent 生态
- 与课程 10（MCP）深度相关

---

### Part 5：总结与作业（5 min）

#### 5.1 本课核心知识地图

```
Agent = LLM + 四层架构
              │
    ┌─────────┼──────────┐
    ▼         ▼          ▼
  记忆层    规划层      行动层
  4类记忆   ReAct/ToT   7种武器
              │
    生产风险（5大）
              │
    多Agent架构（3种模式）
```

#### 5.2 三类人群的学习重点

| 人群 | 重点掌握 | 可略读 |
|------|---------|-------|
| 转行小白 | Part 0（类比）、Part 1.1（定义）、Part 1.3（七种武器概览）| Part 3 代码细节 |
| 算法工程师 | Part 2（ReAct六层追问）、Part 3（三段代码）、Part 4.3（生产风险）| Part 0 类比 |
| AI 产品经理 | Part 1.3（七种武器速查）、Part 4.1-4.2（多Agent架构）、Part 4.4（前沿）| Part 3 代码 |

#### 5.3 面试真题与参考答案

**🔥 高频真题 1**：Agent 和普通 LLM 调用有什么区别？
> 三点核心区别：① 步骤数：单次 LLM 是一问一答，Agent 是多步循环迭代；② 工具：Agent 可以调用外部工具（搜索/代码/API），LLM 只能依赖训练知识；③ 自主性：Agent 自主决定下一步行动，LLM 只被动响应。

**🔥 高频真题 2**：ReAct 的 Thought/Action/Observation 分别是什么？
> Thought（推理）：LLM 分析当前情况，决定下一步用什么工具；Action（行动）：实际调用特定工具（指定工具名+参数）；Observation（观察）：接收工具执行的返回结果。三者交替循环，直到得出 Final Answer。

**⚠️ 中频真题 3**：如何防止 Agent 陷入无限循环？
> 三道防线：① 硬性限制 `max_iterations`（推荐 10-20 步）；② 循环检测（连续 N 次相同工具+参数则终止）；③ 监控预算（Token 用量或时间超出阈值则强制终止）。同时设计合适的 Prompt 让模型在不确定时选择放弃而非重试。

**💡 进阶真题 4**：多 Agent 系统的 Supervisor-Worker 模式是如何工作的？
> Supervisor 是一个 LLM Agent，负责接收目标、分解任务、选择合适的 Worker Agent 执行子任务、收集结果、评估是否完成。Worker 是专门化的 Agent，只负责单一职责（如研究/写作/测试）。与微服务架构类似，通过职责分离提升整体能力上限。

#### 5.4 实战作业

**Level 1（基础）**：Function Calling 初探
1. 用 Part 3.1 的代码搭建一个两工具 Agent（天气 + 计算器）
2. 提 5 个需要同时用到两个工具的问题（如"北京今天气温的平方是多少？"）
3. 观察并记录 Agent 的 Thought 推理过程

**Level 2（进阶）**：ReAct Agent 实战
1. 基于 Part 3.2，搭建一个带网页搜索的 ReAct Agent
2. 提 3 个多跳推理问题（需要搜索 + 计算的组合）
3. 设置 `max_iterations=5`，观察步骤超限时的行为

**Level 3（挑战）**：简易多 Agent 系统
1. 设计一个两 Agent 系统：Researcher（负责搜索）+ Writer（负责写作）
2. 用 LangGraph 实现 Supervisor 调度这两个 Worker
3. 测试：用这个多 Agent 系统生成一篇 200 字的技术简报，记录总 Token 消耗

---

## 引用说明

| 编号 | 引用来源 | 引用位置 | 说明 |
|------|---------|---------|------|
| [1] | Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models", ICLR 2023 | Part 2.1 | ReAct 原始论文 |
| [2] | Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in LLMs", NeurIPS 2022 | Part 2.2 | CoT 原始论文 |
| [3] | Yao et al., "Tree of Thoughts: Deliberate Problem Solving with LLMs", NeurIPS 2023 | Part 2.2 | ToT 原始论文 |
| [4] | Qian et al., "ChatDev: Communicative Agents for Software Development", ACL 2024 | Part 4.2 | 多 Agent 软件开发案例 |
| [5] | Wooldridge & Jennings, "Intelligent agents: Theory and practice", 1995 | Part 1.1 | Agent 经典定义 |
| [6] | Anthropic, "Computer Use (Beta)", API Docs 2024 | Part 4.4 | Claude Computer Use 文档 |
| [7] | LangChain, "LangGraph Documentation", 2025 | Part 3.3 | LangGraph 状态图框架 |

---

## 必须包含的元素

- [x] YAML 元信息（版本/日期/复审时间）
- [x] Wow Moment（ReAct 使多跳推理准确率 33% → 76%）
- [x] 术语速查表（10 个核心术语）
- [x] Agent 四层架构图（感知/规划/记忆/行动）
- [x] 七种武器详解（含适用场景速查表）
- [x] 记忆系统四类型（工作/情节/语义/程序记忆类比）
- [x] ReAct 六层追问
- [x] 规划范式进化树（CoT→ReAct→ToT→Plan-and-Execute）
- [x] 三段完整代码（Function Calling / ReAct / LangGraph）
- [x] 三种多 Agent 架构模式 + 选型建议
- [x] 生产五大风险 + 安全保护代码
- [x] 面试真题（4 道，含 🔥⚠️💡 标注）
- [x] 三层作业（基础/进阶/挑战）
- [x] 引用说明（7 条权威来源）

---

## 与其他课程的关系

```
课程06 Prompt Engineering      课程08 RAG
（System Prompt / CoT）        （知识检索工具）
        │                            │
        │ Agent 的大脑设计依赖 Prompt  │ RAG 是 Agent 武器④
        └──────────────┬─────────────┘
                       ▼
              课程09（本课）
          Agent 的七种武器
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
    课程10             课程11        课程12
  MCP/Skill/CLI     AI 编程助手    OpenClaw 架构
  （工具协议标准）   （代码 Agent）  （企业 Agent 平台）
  武器①的标准化     七种武器的    多 Agent 系统的
  与生态化          综合应用       完整工程实现
```

**前置依赖**：
- **课程 06**（必要）：Agent 的 System Prompt 设计、工具描述、输出格式控制都依赖 Prompt Engineering
- **课程 08**（推荐）：RAG 是 Agent 七种武器之一，理解 RAG 有助于掌握 Agentic RAG 设计

**后续影响**：
- **课程 10（MCP）**：MCP 协议是 Function Calling（武器①）的标准化和生态化，解决工具描述的互操作性问题
- **课程 11（AI 编程助手）**：Claude Code、Codex 是代码执行（武器②）+ 多模态感知（武器⑥）的专门化 Agent
- **课程 12（OpenClaw）**：OpenClaw 是本课多 Agent 架构在企业级的完整工程实现，所有七种武器均有对应
