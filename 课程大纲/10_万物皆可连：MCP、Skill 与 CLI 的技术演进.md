---
课程: 10 - 万物皆可连：MCP、Skill 与 CLI 的技术演进
版本: v1.0
创建日期: 2026-04-29
最后更新: 2026-04-29
适用模型版本: Claude 3.5 / GPT-4o / MCP SDK 1.x / Claude Code CLI
下次复审: 2026-07-29
更新触发条件:
  - MCP 协议发布新版本（规范变更/新原语）
  - 主流平台（GitHub/VSCode/JetBrains）对 MCP 的支持更新
  - Claude Code 发布重大功能更新（新 Skill 系统）
---

# 课程 10：万物皆可连——MCP、Skill 与 CLI 的技术演进

> **课程类型**：C 架构型 | **时长**：60 min | **前置课程**：课程 09（Agent 七种武器）

---

## 逻辑主线

```
"每接一个新工具就要写一套集成代码——这不是 Agent 该有的样子"
        ↓
[核心困境] N 个模型 × M 个工具 = N×M 个定制集成
           碎片化、不可复用、维护成本爆炸
        ↓
[解法演进]
  2023 年：Function Calling —— OpenAI 定义 JSON Schema 标准（模型侧统一）
  2024 年：MCP 协议 ——————— Anthropic 定义服务端标准（工具侧统一）
                              N×M → N+M，就像 USB 统一了设备接口
        ↓
[MCP 三层架构]
  Tools（工具）：AI 可以调用的函数接口
  Resources（资源）：AI 可以读取的上下文数据
  Prompts（提示模板）：可复用的任务提示片段
        ↓
[Skill 系统]
  比 Tool 更高层：将"提示模板 + 工具组合 + 执行逻辑"封装为可分发的能力单元
        ↓
[CLI 新范式]
  人类写命令 → AI 理解意图并执行命令 → AI 自主操作终端
  Claude Code = Agent + MCP + Skill 的完整体现
        ↓
"从工具碎片到能力生态：AI 工程的下一个基础设施层"
```

---

## 课程简介

本课聚焦一个正在发生的基础设施革命：**如何让 AI 工具连接从"手工定制"走向"标准化生态"？**

MCP（Model Context Protocol）是 Anthropic 于 2024 年 11 月开源的工具连接协议，2025 年已有 2000+ 个 MCP Server 社区实现，被称为"AI 时代的 USB 接口"。

本课不只讲 MCP 的 API 怎么用，而是讲清楚：
- 工具集成为什么会碎片化，以及协议化怎么解决这个问题
- MCP 的三层设计（Tools/Resources/Prompts）及其架构哲学
- Skill 系统如何在 MCP 之上封装更高阶的可复用能力
- CLI 交互范式从命令行→自然语言→Agent 自主的三代演进

---

## 课程描述：学完本课你能收获

| 能力 | 具体表现 |
|------|---------|
| **协议理解** | 解释 MCP 的 Client/Server 架构，画出一次工具调用的完整流程 |
| **对比分析** | 说清 MCP、Function Calling、OpenAPI 三者的定位差异 |
| **开发能力** | 用 Python MCP SDK 实现一个最小化的 MCP Server |
| **Skill 设计** | 设计并封装一个符合规范的 Claude Code Skill |
| **架构视野** | 描述 CLI→Agent 的交互范式演进，以及 MCP+Skill+CLI 的生态位 |
| **面试应答** | 回答"MCP 解决了什么问题"、"Skill 和 Tool 的区别"等高频题 |

---

## Wow Moment 🎯

> **MCP 出现之前：一个 AI 助手要同时支持 GitHub、Slack、数据库、文件系统四个工具，4 家公司需要为每个工具单独对接，共 4 个定制集成；10 个工具就需要 10 套；规模化后这是不可维护的 N×M 爆炸。MCP 出现之后：工具提供方只需实现一次 MCP Server，任意支持 MCP 的 AI 应用即可接入——N×M 变成 N+M，就像 USB 发明之前每种设备都有专属接口，USB 之后"万物皆可连"。**

引导设问（讲师口播）：
- "你有没有想过，为什么 GPT-4 能查天气、能搜代码，但换成另一个模型就不行了？"
- "如果有 100 个 AI 工具，你需要为每个模型写 100 遍集成代码吗？"
- "USB 发明之前，鼠标、键盘、打印机都有不同接口——AI 工具连接现在就处于这个阶段"

---

## 术语速查表

| 术语 | 一句话定义 | 首次出现位置 |
|------|-----------|------------|
| MCP（Model Context Protocol）| Anthropic 开源的 AI 工具连接标准协议，定义 Server/Client 架构 | Part 2.1 |
| MCP Server | 暴露 Tools/Resources/Prompts 的服务端，通常是一个独立进程 | Part 2.2 |
| MCP Client | 连接 MCP Server 并调用其能力的 AI 应用（如 Claude Desktop） | Part 2.2 |
| Tool（MCP 工具）| MCP 中 AI 可以执行的函数接口，有输入 Schema 和返回值 | Part 2.3 |
| Resource（MCP 资源）| MCP 中 AI 可以读取的数据（文件、数据库、API 返回）| Part 2.3 |
| Prompt（MCP 提示）| MCP 中可复用的提示模板，参数化定义常用任务 | Part 2.3 |
| Skill | 比 Tool 更高层的能力封装：提示模板 + 工具 + 执行逻辑的组合 | Part 3.1 |
| stdio transport | MCP 本地通信方式：通过标准输入输出（子进程）传输 | Part 2.4 |
| SSE transport | MCP 远程通信方式：基于 HTTP Server-Sent Events 传输 | Part 2.4 |
| Claude Code | Anthropic 的 AI 编程 CLI，是 MCP + Skill + Agent 的完整体现 | Part 4.1 |

---

## 课程结构总览

| Part | 主题 | 时长 | 核心交付 |
|------|------|------|---------|
| **Part 0** | 直觉导入：工具集成的碎片化困境 | 5 min | N×M 问题 + USB 类比 |
| **Part 1** | 演进背景：从 Function Calling 到 MCP | 10 min | 三代工具连接范式对比 |
| **Part 2** | MCP 深度解析：协议架构与三层原语 | 15 min | Client/Server + Tools/Resources/Prompts |
| **Part 3** | 代码实战：实现 MCP Server 与 Skill | 15 min | 三段代码（Server/Client/Skill）|
| **Part 4** | CLI 演进：从命令行到 Agent 操控终端 | 10 min | 三代 CLI 范式 + 生态全景 |
| **Part 5** | 总结与作业 | 5 min | 面试真题 + 实战挑战 |

---

## 章节大纲

### Part 0：直觉导入——工具集成的碎片化困境（5 min）

#### 0.1 一个真实的工程噩梦（3 min）

**2023 年的 AI 工具集成现场**：

假设你要构建一个能同时使用四个工具的 AI 助手：

```
你的 AI 助手
    ├── GitHub 集成
    │     → 阅读 OpenAPI 文档 → 写 GitHub API 调用代码 → 处理 token 认证
    │       → 适配模型的 Function Calling 格式（OpenAI 格式）
    │
    ├── Slack 集成
    │     → 阅读 Slack API 文档 → 写 Slack SDK 调用 → 处理 OAuth
    │       → 再次适配 Function Calling 格式
    │
    ├── PostgreSQL 集成
    │     → 写数据库连接池 → 实现查询接口 → 防 SQL 注入
    │       → 再次适配 Function Calling 格式
    │
    └── 文件系统集成
          → 封装 read/write/list 操作 → 处理权限
            → 再次适配 Function Calling 格式

共需编写：4 套独立集成代码 + 4 套 Function Calling 适配
```

**问题加剧**：
- 换成 Claude（Anthropic）：重写 4 套适配（格式不同）
- 换成 Gemini（Google）：再重写 4 套
- 新增一个工具：N 个模型都要更新

`N(模型) × M(工具) = N×M 个集成` → 指数级维护成本

#### 0.2 解题思路预告（2 min）

```
USB 统一前（1996年以前）：
  鼠标 → PS/2 接口
  键盘 → AT 接口
  打印机 → 并行口
  调制解调器 → 串口
  → 每台电脑有 7-8 种不同接口

USB 统一后（1996年至今）：
  所有设备 → USB 接口（一种标准）
  → "万物皆可连"

MCP 做的是同样的事：
  所有 AI 工具 → MCP Server（一种标准）
  所有 AI 应用 → MCP Client（对接一种标准）
  N×M → N+M
```

---

### Part 1：演进背景——从 Function Calling 到 MCP（10 min）

#### 1.1 三代工具连接范式（6 min）

**第一代：各自为政（2022 年之前）**

- 每个 AI 应用自己定义工具格式
- 工具描述、调用方式、结果处理全部不同
- 无法跨应用复用任何工具代码
- 代表：早期 LangChain Tool（每个版本格式不同）

**第二代：Function Calling 统一模型侧（2023 年）**

> OpenAI 于 2023 年 6 月发布 Function Calling，首次提供标准化的工具调用格式

- **统一了什么**：模型如何"请求调用工具"的格式（JSON Schema）
- **没解决什么**：工具提供方如何"暴露工具"依然各自为政
- **本质**：客户端统一了请求格式，但服务端（工具实现）仍然碎片化

```
Function Calling 解决的问题：
  模型 → [统一 JSON 格式] → 工具请求

Function Calling 没解决的问题：
  工具提供方 → ??? → 暴露给模型
  （每家仍然需要自己实现适配层）
```

**第三代：MCP 统一工具侧（2024 年）**

> Anthropic 于 2024 年 11 月开源 Model Context Protocol

- **统一了什么**：工具提供方如何"暴露工具"的标准（Server 协议）
- **核心思想**：让工具提供方实现一次 MCP Server，任何 MCP Client（AI 应用）均可接入
- **与 Function Calling 的关系**：互补，MCP 在传输层定义了工具描述和调用流程，模型内部的工具选择仍依赖 Function Calling 机制

**三代对比表**：

| 维度 | 各自为政 | Function Calling | MCP |
|------|---------|-----------------|-----|
| 统一层 | 无 | 模型侧（请求格式）| 工具侧（暴露格式）|
| 复用性 | 无 | 部分（同模型复用）| 高（跨模型复用）|
| 生态位 | 孤立 | 模型平台绑定 | 独立标准，跨平台 |
| 实现者 | 应用开发者 | 模型提供商 + 应用开发者 | 工具提供商 |
| 2026现状 | 历史遗留 | 仍广泛使用 | 快速增长（2000+ Server）|

#### 1.2 MCP 与相关概念的关系厘清（4 min）

**MCP vs Function Calling**：
- Function Calling：模型层协议（规定模型如何"点菜"）
- MCP：传输层协议（规定餐厅如何"报菜单"）
- 关系：**MCP Server 通过 MCP 协议暴露工具，MCP Client 调用这些工具时内部使用 Function Calling**

**MCP vs OpenAPI/REST**：
- OpenAPI：为人类开发者设计的 HTTP API 规范（swagger）
- MCP：为 AI 模型设计的工具调用规范，包含 AI 特有概念（Resources/Prompts）
- 关系：MCP 可以包装一个 OpenAPI 服务，对外暴露为 MCP Tool

**MCP vs LangChain Tool**：
- LangChain Tool：框架内部的工具抽象，依赖 LangChain 运行时
- MCP Tool：独立进程/服务，语言无关，跨框架复用
- 关系：LangChain 0.3+ 已支持接入 MCP Server

**⚠️ 面试考点**：面试时区分 MCP 和 Function Calling 是高频考点——MCP 是工具提供方的暴露标准（Server 协议），Function Calling 是模型如何声明"我要调用工具"的格式（模型输出格式）。两者在同一次工具调用中分别在不同层次发挥作用。

---

### Part 2：MCP 深度解析——协议架构与三层原语（15 min）

#### 2.1 Client-Server 架构全景（4 min）

```
┌────────────────────────────────────────────────────────────────┐
│                      MCP 架构全景                               │
│                                                                │
│  ┌─────────────────────────────┐                              │
│  │      Host Application        │                              │
│  │  (Claude Desktop / IDE 插件)  │                              │
│  │                             │                              │
│  │  ┌──────────────────────┐   │                              │
│  │  │    MCP Client         │   │    stdio / SSE              │
│  │  │  （协议连接管理）       │◄──┼────────────────────────────►│
│  │  └──────────────────────┘   │                              │
│  └─────────────────────────────┘    ┌────────────────────────┐│
│                                     │      MCP Server         ││
│  ┌───────────────────────────┐      │  （工具能力暴露方）       ││
│  │      LLM Provider          │      │                        ││
│  │  (Claude / GPT-4o 等)      │      │  ① Tools               ││
│  │  决定何时调用哪个工具        │      │  ② Resources           ││
│  └───────────────────────────┘      │  ③ Prompts              ││
│                                     └────────────────────────┘│
└────────────────────────────────────────────────────────────────┘
```

**四个核心角色**：
1. **Host Application**：AI 应用（如 Claude Desktop、Cursor、自建聊天系统）
2. **MCP Client**：Host 内嵌的 MCP 连接管理模块，负责发现和调用 Server
3. **LLM Provider**：实际的大模型，决定何时调用工具
4. **MCP Server**：独立进程，暴露 Tools/Resources/Prompts，可以是本地程序或远程服务

**一次完整工具调用的流程**：
```
① 用户发送消息 → Host 将消息送给 LLM
② LLM 决定需要工具 → 输出 Function Calling JSON
③ Host/Client 识别工具调用意图 → 通过 MCP 协议调用对应 Server
④ MCP Server 执行工具逻辑 → 返回结果
⑤ Host 将结果注入对话 → LLM 根据结果生成最终答案
⑥ 用户收到有工具支撑的答案
```

#### 2.2 三层核心原语（8 min）

**原语一：Tools（工具）——AI 能执行的动作**

Tool 是 MCP 中最核心的原语，对应"AI 能做什么"。

```
Tool 结构：
  name:        工具名称（唯一标识符）
  description: 自然语言描述（LLM 依赖这个决定何时调用）
  inputSchema: JSON Schema（定义输入参数类型和约束）
  → 调用后：返回结构化结果（文本/JSON/图片/错误）
```

Tool 的分类：
| 类别 | 描述 | 示例 |
|------|------|------|
| 读取操作 | 获取信息，无副作用 | `read_file`, `search_web`, `query_db` |
| 写入操作 | 修改状态，有副作用 | `write_file`, `create_issue`, `send_email` |
| 执行操作 | 运行代码或命令 | `run_python`, `execute_bash` |
| 转换操作 | 处理数据格式 | `parse_pdf`, `convert_image` |

**关键设计原则**：工具的 description 要为 LLM 而写，不是为人类——要说清楚"什么情况下应该用这个工具"，而不只是"这个工具做什么"。

**原语二：Resources（资源）——AI 能读取的上下文**

Resource 是 MCP 中专门为"上下文注入"设计的原语，对应"AI 能看到什么"。

```
Resource 结构：
  uri:      资源唯一标识（如 file:///path/to/doc.md）
  name:     可读名称
  mimeType: 内容类型（text/plain, application/json, image/png...）
  → 读取后：返回原始内容（直接注入 AI 上下文）
```

Resource vs Tool 的核心区别：
- **Tool**：执行动作，有输入参数，返回操作结果（适合"做事情"）
- **Resource**：读取数据，没有输入参数，返回静态/动态内容（适合"看数据"）

```
何时用 Tool：读取文件 → 用 read_file Tool（可以控制路径参数）
何时用 Resource：暴露项目文档 → 用 Resource（统一可见，无需参数）
```

**原语三：Prompts（提示模板）——可复用的任务定义**

Prompt 是 MCP 中最被低估的原语，对应"AI 的工作方式"。

```
Prompt 结构：
  name:        模板名称
  description: 描述这个模板适合什么任务
  arguments:   参数列表（可选，类似函数参数）
  → 展开后：返回一组具体的 Message（可直接发送给 LLM）
```

使用场景：
- 将复杂的多步骤任务标准化（如"代码审查"、"安全扫描"、"文档生成"）
- 在团队中统一 AI 的工作流程（所有人用同一个 Prompt 模板做代码审查）
- 减少"Prompt 工程"的重复工作

#### 2.3 两种传输方式（3 min）

**stdio Transport（本地标准输入输出）**：
- 原理：Host 启动 MCP Server 子进程，通过 stdin/stdout 通信
- 适用：本地工具（文件系统、本地数据库、命令行工具）
- 优点：零配置，无网络延迟，安全（不暴露端口）
- 典型配置：

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/me/projects"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {"GITHUB_TOKEN": "ghp_xxx"}
    }
  }
}
```

**SSE Transport（Server-Sent Events）**：
- 原理：MCP Server 作为 HTTP 服务运行，通过 SSE 推送消息
- 适用：远程工具（云服务、跨机器工具、需要持久连接的服务）
- 优点：可部署为独立服务，支持多 Client 共享
- 示例：公司统一部署一个内部知识库 MCP Server，所有 AI 工具接入

---

### Part 3：代码实战——实现 MCP Server 与 Skill（15 min）

#### 3.1 实现一个最小化 MCP Server（6 min）

```python
# 用 Python MCP SDK 实现一个项目管理 MCP Server
# pip install mcp

from mcp.server import Server
from mcp.server.stdio import stdio_server
from mcp.types import (
    Tool, TextContent, Resource, Prompt, PromptMessage,
    GetPromptResult, ListResourcesResult, ReadResourceResult
)
import json
import asyncio
from datetime import datetime

# 初始化 MCP Server
app = Server("project-manager")

# ── 模拟数据（实际应连接真实数据库）─────────────────────────
TASKS = [
    {"id": 1, "title": "设计 MCP Server 架构", "status": "done", "assignee": "Alice"},
    {"id": 2, "title": "实现 Tool 层", "status": "in_progress", "assignee": "Bob"},
    {"id": 3, "title": "编写测试用例", "status": "todo", "assignee": "Charlie"},
]

# ── 注册 Tools ────────────────────────────────────────────
@app.list_tools()
async def list_tools() -> list[Tool]:
    """声明所有可用工具（LLM 看到这些描述来决定何时调用）"""
    return [
        Tool(
            name="list_tasks",
            description="列出项目中的所有任务。当用户询问任务状态、进度或任务列表时使用。",
            inputSchema={
                "type": "object",
                "properties": {
                    "status": {
                        "type": "string",
                        "enum": ["todo", "in_progress", "done", "all"],
                        "description": "按状态筛选，默认返回全部"
                    }
                }
            }
        ),
        Tool(
            name="create_task",
            description="创建一个新任务。当用户想要添加新的工作项时使用。",
            inputSchema={
                "type": "object",
                "properties": {
                    "title":    {"type": "string", "description": "任务标题"},
                    "assignee": {"type": "string", "description": "负责人姓名"}
                },
                "required": ["title"]
            }
        ),
        Tool(
            name="update_task_status",
            description="更新任务状态。当用户完成任务或开始处理任务时使用。",
            inputSchema={
                "type": "object",
                "properties": {
                    "task_id": {"type": "integer", "description": "任务 ID"},
                    "status":  {"type": "string", "enum": ["todo", "in_progress", "done"]}
                },
                "required": ["task_id", "status"]
            }
        )
    ]

@app.call_tool()
async def call_tool(name: str, arguments: dict) -> list[TextContent]:
    """工具执行逻辑"""
    if name == "list_tasks":
        status_filter = arguments.get("status", "all")
        tasks = TASKS if status_filter == "all" else [t for t in TASKS if t["status"] == status_filter]
        return [TextContent(type="text", text=json.dumps(tasks, ensure_ascii=False, indent=2))]

    elif name == "create_task":
        new_task = {
            "id": max(t["id"] for t in TASKS) + 1,
            "title": arguments["title"],
            "status": "todo",
            "assignee": arguments.get("assignee", "未分配"),
            "created_at": datetime.now().isoformat()
        }
        TASKS.append(new_task)
        return [TextContent(type="text", text=f"✅ 任务已创建：{json.dumps(new_task, ensure_ascii=False)}")]

    elif name == "update_task_status":
        task = next((t for t in TASKS if t["id"] == arguments["task_id"]), None)
        if not task:
            return [TextContent(type="text", text=f"❌ 未找到 ID 为 {arguments['task_id']} 的任务")]
        old_status = task["status"]
        task["status"] = arguments["status"]
        return [TextContent(type="text", text=f"✅ 任务 #{arguments['task_id']} 状态从 {old_status} → {arguments['status']}")]

    return [TextContent(type="text", text=f"未知工具：{name}")]

# ── 注册 Resources ─────────────────────────────────────────
@app.list_resources()
async def list_resources() -> ListResourcesResult:
    return ListResourcesResult(resources=[
        Resource(
            uri="project://tasks/summary",
            name="项目任务总览",
            description="所有任务的汇总统计（实时更新）",
            mimeType="application/json"
        )
    ])

@app.read_resource()
async def read_resource(uri: str) -> ReadResourceResult:
    if uri == "project://tasks/summary":
        summary = {
            "total": len(TASKS),
            "by_status": {
                "todo": sum(1 for t in TASKS if t["status"] == "todo"),
                "in_progress": sum(1 for t in TASKS if t["status"] == "in_progress"),
                "done": sum(1 for t in TASKS if t["status"] == "done"),
            },
            "completion_rate": f"{sum(1 for t in TASKS if t['status']=='done')/len(TASKS)*100:.1f}%"
        }
        return ReadResourceResult(contents=[
            TextContent(type="text", text=json.dumps(summary, ensure_ascii=False, indent=2))
        ])

# ── 注册 Prompts ───────────────────────────────────────────
@app.list_prompts()
async def list_prompts():
    return [
        Prompt(
            name="daily_standup",
            description="生成每日站会报告，总结任务进度",
            arguments=[]
        )
    ]

@app.get_prompt()
async def get_prompt(name: str, arguments: dict) -> GetPromptResult:
    if name == "daily_standup":
        tasks_json = json.dumps(TASKS, ensure_ascii=False, indent=2)
        return GetPromptResult(
            description="每日站会报告提示",
            messages=[
                PromptMessage(role="user", content=TextContent(
                    type="text",
                    text=f"""请根据以下任务列表，生成一份简洁的每日站会报告（格式：已完成/进行中/待处理）：

{tasks_json}

报告格式：
✅ 已完成：[列举]
🔄 进行中：[列举，说明进度]
📋 待处理：[列举，标注优先级]"""
                ))
            ]
        )

# ── 启动 Server ────────────────────────────────────────────
async def main():
    async with stdio_server() as (read_stream, write_stream):
        await app.run(read_stream, write_stream, app.create_initialization_options())

if __name__ == "__main__":
    asyncio.run(main())
```

#### 3.2 连接 MCP Server：Client 调用示例（4 min）

```python
# 用 Claude API 调用 MCP Server（通过 Claude Desktop 配置）
# 注：实际生产中 MCP Client 通常是 IDE/Desktop 应用内置的
# 这里演示用 anthropic SDK 直接进行等效的工具调用

import anthropic
import subprocess, json, threading, queue

class SimpleMCPClient:
    """简化的 MCP Client，用于演示 MCP 通信流程"""

    def __init__(self, server_script: str):
        # 启动 MCP Server 子进程
        self.process = subprocess.Popen(
            ["python", server_script],
            stdin=subprocess.PIPE,
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
            text=True
        )
        self.request_id = 0

    def send_request(self, method: str, params: dict = None) -> dict:
        """发送 JSON-RPC 请求到 MCP Server"""
        self.request_id += 1
        request = {
            "jsonrpc": "2.0",
            "id": self.request_id,
            "method": method,
            "params": params or {}
        }
        # 通过 stdin 发送
        self.process.stdin.write(json.dumps(request) + "\n")
        self.process.stdin.flush()

        # 从 stdout 读取响应
        response_line = self.process.stdout.readline()
        return json.loads(response_line)

    def list_tools(self) -> list:
        """获取 Server 提供的所有工具"""
        response = self.send_request("tools/list")
        return response.get("result", {}).get("tools", [])

    def call_tool(self, name: str, arguments: dict) -> str:
        """调用指定工具"""
        response = self.send_request("tools/call", {
            "name": name,
            "arguments": arguments
        })
        contents = response.get("result", {}).get("content", [])
        return contents[0].get("text", "") if contents else ""

# 演示：Claude API + MCP 工具调用的完整流程
def demo_claude_with_mcp_tools():
    """
    实际生产中，这个流程由 Claude Desktop/IDE 自动完成。
    这里手动模拟，帮助理解 MCP 如何工作。
    """
    client = anthropic.Anthropic()

    # 1. 从 MCP Server 获取工具描述（实际由 MCP Client 自动完成）
    # mcp_client = SimpleMCPClient("project_server.py")
    # tools_from_mcp = mcp_client.list_tools()

    # 2. 将 MCP Tool 描述转换为 Claude API 的 tool 格式
    claude_tools = [
        {
            "name": "list_tasks",
            "description": "列出项目中的所有任务。当用户询问任务状态、进度或任务列表时使用。",
            "input_schema": {
                "type": "object",
                "properties": {
                    "status": {"type": "string", "enum": ["todo", "in_progress", "done", "all"]}
                }
            }
        }
    ]

    # 3. 调用 Claude，传入工具描述
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=1024,
        tools=claude_tools,
        messages=[{"role": "user", "content": "现在有哪些进行中的任务？"}]
    )

    # 4. 处理工具调用
    if response.stop_reason == "tool_use":
        tool_use = next(b for b in response.content if b.type == "tool_use")
        print(f"Claude 请求调用工具：{tool_use.name}({tool_use.input})")

        # 5. 实际执行工具（通过 MCP Client 调用 MCP Server）
        # result = mcp_client.call_tool(tool_use.name, tool_use.input)
        result = json.dumps([  # 模拟结果
            {"id": 2, "title": "实现 Tool 层", "status": "in_progress", "assignee": "Bob"}
        ], ensure_ascii=False)

        # 6. 将工具结果回传给 Claude，获取最终答案
        final_response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=1024,
            tools=claude_tools,
            messages=[
                {"role": "user", "content": "现在有哪些进行中的任务？"},
                {"role": "assistant", "content": response.content},
                {"role": "user", "content": [{"type": "tool_result",
                                               "tool_use_id": tool_use.id,
                                               "content": result}]}
            ]
        )
        print(f"\n最终答案：{final_response.content[0].text}")
```

#### 3.3 设计一个 Claude Code Skill（5 min）

```markdown
<!-- 示例：一个用于生成 Git Commit Message 的 Skill -->
<!-- 保存为 .claude/skills/commit-generator.md -->

---
name: commit-generator
description: >
  根据 git diff 自动生成符合 Conventional Commits 规范的提交信息。
  触发时机：当用户说"帮我写提交信息"、"生成 commit"、"commit message" 时使用。
  不要在用户只是询问代码修改内容时触发。
version: 1.0
author: team-platform
---

# Skill：Git Commit Message 生成器

## 触发指令
`/commit-msg` 或用户自然语言表达提交需求时

## 执行流程

### Step 1：获取变更内容
```bash
git diff --staged --stat
git diff --staged
```

### Step 2：分析变更类型
根据以下 Conventional Commits 类型分类：
- `feat`: 新功能
- `fix`: 修复 Bug
- `refactor`: 重构（无新功能/无 Bug 修复）
- `docs`: 文档更新
- `test`: 测试相关
- `chore`: 构建/工具链更新
- `perf`: 性能优化

### Step 3：生成提交信息
格式规范：
```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

约束：
- subject 不超过 72 字符
- 使用英文（代码注释和文档可用中文）
- 动词用一般现在时（add, fix, update），不用过去时
- subject 不以句号结尾
- body 说明"为什么"而非"做了什么"

### Step 4：输出格式
```
建议的提交信息：

feat(auth): add JWT refresh token support

- Implement /api/auth/refresh endpoint
- Add token expiry validation middleware
- Store refresh tokens in Redis with 7-day TTL

Closes #234
```

### Step 5：询问确认
"是否使用此提交信息执行 `git commit`？(y/n/edit)"
```

**Skill 与 Tool 的设计哲学对比**：

| 维度 | MCP Tool | Skill |
|------|---------|-------|
| 粒度 | 单一操作（如"读文件"）| 完整任务（如"代码审查"）|
| 实现方式 | 代码（Python/JS 函数）| 提示词 + 工具调用组合 |
| 输入 | 结构化参数 | 自然语言触发 + 上下文 |
| 可复用单元 | 工具函数 | 任务模板 |
| 典型场景 | API 集成、数据操作 | 工作流自动化、团队规范 |

---

### Part 4：CLI 演进——从命令行到 Agent 操控终端（10 min）

#### 4.1 CLI 交互范式的三代演进（6 min）

**第一代：传统 CLI（人类写命令，机器执行）**

```bash
# 1970s → 至今
$ git log --oneline --graph --all --decorate -20
$ find . -name "*.py" -newer setup.py -exec grep -l "import" {} \;
$ awk '{sum += $3} END {print sum}' sales.csv
```

特点：
- 人类需要精确记忆命令语法
- 强大但陡峭的学习曲线
- 效率极高（for 专家）但极不友好（for 新手）
- 命令是为机器设计的，不是为人设计的

**第二代：AI 增强 CLI（人类说意图，AI 翻译命令）**

```bash
# 2023-2024：GitHub Copilot CLI、fig.io 等
$ gh copilot suggest "删除30天前的日志文件"
# AI 输出：find /var/log -name "*.log" -mtime +30 -delete

$ gh copilot explain "git reset --soft HEAD~3"
# AI 解释：撤销最近3次提交但保留修改在暂存区...
```

特点：
- 自然语言输入，AI 输出精确命令
- 降低 CLI 学习门槛
- 人仍然是最终决策者（AI 建议，人确认）
- 本质：CLI 的"翻译器"

**第三代：Agent CLI（AI 自主规划并执行操作）**

```bash
# 2024-2026：Claude Code、OpenDevin、SWE-agent
$ claude "帮我找出这个仓库里所有 TODO 注释，整理成按优先级排序的 issue 列表，
         并在 GitHub 上创建对应的 issue"

# Agent 自主执行：
# 1. grep -r "TODO" . → 收集所有 TODO
# 2. LLM 分析优先级 → 分类整理
# 3. gh issue create ... → 批量创建 GitHub issue
# 4. 汇报执行结果
```

特点：
- 人类只说目标，Agent 自主规划并执行所有步骤
- Agent 可以使用所有 CLI 工具（git、bash、curl、python...）
- 错误时自动重试和修正
- 本质：CLI 的"自动驾驶"

**三代对比**：

```
第一代：Human → [自己写命令] → Shell → 结果
第二代：Human → [自然语言] → AI翻译 → [建议命令] → Human确认 → Shell → 结果
第三代：Human → [目标] → AI规划 → [自主执行多条命令] → 循环直至完成 → 结果汇报
```

#### 4.2 MCP + Skill + CLI 的生态全景（4 min）

**Claude Code 作为三者融合的典型案例**：

```
Claude Code = Agent CLI（第三代 CLI）
                │
                ├── 工具连接层：MCP（接入 GitHub/文件系统/数据库等）
                │   通过 .claude/mcp-config.json 配置 MCP Server
                │
                ├── 能力封装层：Skill（复用的任务模板）
                │   通过 .claude/skills/ 目录定义团队级 Skill
                │
                └── 执行层：bash + 本地工具
                    Agent 直接操作文件系统、运行测试、提交代码
```

**AI 工具生态的四层架构**：

```
┌────────────────────────────────────────────┐
│ Layer 4: 应用层（Agent/产品）                │
│  Claude Code / Cursor / Copilot / 自建系统   │
├────────────────────────────────────────────┤
│ Layer 3: 能力层（Skill）                     │
│  可复用任务模板，封装最佳实践工作流            │
├────────────────────────────────────────────┤
│ Layer 2: 工具层（MCP）                       │
│  标准化工具暴露协议，N+M 生态                 │
├────────────────────────────────────────────┤
│ Layer 1: 模型层（LLM）                       │
│  GPT-4o / Claude 3.5 / Gemini / 开源模型    │
└────────────────────────────────────────────┘
```

**2026 年 MCP 生态现状**：

| 类别 | 代表 MCP Server | 典型能力 |
|------|----------------|---------|
| 开发工具 | github, gitlab, jira | 代码/issue/PR 管理 |
| 数据库 | postgres, sqlite, mysql | SQL 查询和操作 |
| 文件系统 | filesystem, gdrive | 本地/云端文件读写 |
| 通信工具 | slack, email, calendar | 消息/日程管理 |
| 知识库 | notion, confluence, obsidian | 文档查询和更新 |
| 搜索 | brave-search, exa | 网页搜索 |
| 监控 | datadog, grafana | 指标查询和告警 |

**💡 面试考点**：MCP 生态的核心价值在于"一次实现，多处使用"——一个公司实现自己内部系统的 MCP Server 后，可以同时被 Claude Desktop、Cursor、自建 AI 系统接入，无需重复开发适配层。

---

### Part 5：总结与作业（5 min）

#### 5.1 本课核心知识地图

```
工具集成困境（N×M 问题）
        │
        ▼
MCP 协议（N+M 解法）
  ├── Tools（执行动作）
  ├── Resources（读取上下文）
  └── Prompts（复用任务）
        │
        ├── 传输方式：stdio（本地）/ SSE（远程）
        │
        ▼
Skill（高层能力封装）
  提示模板 + 工具组合 + 执行逻辑
        │
        ▼
CLI 三代演进
  命令行 → AI 翻译 → Agent 自主操控
        │
        ▼
Claude Code = MCP + Skill + Agent CLI 的完整体现
```

#### 5.2 三类人群的学习重点

| 人群 | 重点掌握 | 可略读 |
|------|---------|-------|
| 转行小白 | Part 0（N×M 类比）、Part 4.1（三代 CLI 演进）| Part 3 代码实现 |
| 算法工程师 | Part 2（三层原语设计）、Part 3（Server 代码）| Part 4.2 生态 |
| AI 产品经理 | Part 1.1（三代范式对比）、Part 4.2（四层架构+生态全景）| Part 3 代码 |

#### 5.3 面试真题与参考答案

**🔥 高频真题 1**：MCP 解决了什么问题？
> 解决了 AI 工具集成的 N×M 碎片化问题。在 MCP 之前，每个 AI 应用需要为每个工具单独写集成代码；MCP 通过标准化 Server 协议，让工具提供方只需实现一次，任何 MCP Client 均可接入，将 N×M 复杂度降为 N+M。类比 USB 统一设备接口。

**🔥 高频真题 2**：MCP 的三层原语分别解决什么问题？
> Tools 解决"AI 能做什么"——定义 AI 可以执行的动作接口；Resources 解决"AI 能看什么"——定义 AI 可以读取的上下文数据；Prompts 解决"AI 的工作方式"——提供可复用的任务提示模板，标准化团队工作流。

**⚠️ 中频真题 3**：MCP Tool 和 Function Calling 有什么区别？
> Function Calling 是模型侧协议，定义模型如何"声明要调用工具"（JSON Schema 输出格式）；MCP Tool 是服务端协议，定义工具提供方如何"暴露工具接口"（Server 协议）。两者在同一次工具调用中协同工作：MCP Server 暴露工具 → MCP Client 发现工具 → LLM 用 Function Calling 格式决定调用 → MCP Client 通过 MCP 协议执行。

**💡 进阶真题 4**：Skill 和 MCP Tool 的设计定位有什么不同？
> MCP Tool 是原子操作，粒度细，用代码实现，适合封装具体的外部系统调用（如"读文件"、"查数据库"）；Skill 是复合任务，粒度粗，用提示词+工具组合实现，适合封装完整的工作流（如"代码审查"、"生成提交信息"）。Skill 构建在 MCP Tool 之上，是更高层的能力抽象。

#### 5.4 实战作业

**Level 1（基础）**：配置你的第一个 MCP Server
1. 安装 Claude Desktop（或 Cursor）
2. 配置官方 `@modelcontextprotocol/server-filesystem` MCP Server，授权访问你的项目目录
3. 在 Claude Desktop 中让 AI 帮你分析项目的目录结构，验证 MCP 工具调用成功

**Level 2（进阶）**：实现自定义 MCP Server
1. 基于 Part 3.1 的代码，实现一个你自己设计的 MCP Server（至少包含 2 个 Tool）
2. 场景建议：Todo 管理 / 个人笔记搜索 / 简单数据库查询
3. 用 Claude Desktop 连接并验证工具可以被正确调用

**Level 3（挑战）**：设计并封装一个团队 Skill
1. 参考 Part 3.3 的 Skill 设计，为你的团队设计一个 Skill（如"PR 描述生成器"或"日志分析助手"）
2. 在 Claude Code 中测试该 Skill
3. 写出 Skill 的设计说明：解决什么问题、触发条件、执行步骤、输出格式

---

## 引用说明

| 编号 | 引用来源 | 引用位置 | 说明 |
|------|---------|---------|------|
| [1] | Anthropic, "Introducing the Model Context Protocol", Blog 2024.11 | Part 1.1 | MCP 正式发布博客 |
| [2] | MCP Specification, modelcontextprotocol.io, 2025 | Part 2 | MCP 官方协议规范 |
| [3] | OpenAI, "Function Calling", API Docs 2023 | Part 1.1 | Function Calling 发布说明 |
| [4] | Anthropic, "Claude Code: Deep Dive", Blog 2025 | Part 4.2 | Claude Code 技术博客 |
| [5] | Yang et al., "SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering", 2024 | Part 4.1 | Agent CLI 代表性研究 |
| [6] | MCP GitHub Organization, "awesome-mcp-servers", 2025 | Part 4.2 | MCP 生态服务器列表 |

---

## 必须包含的元素

- [x] YAML 元信息（版本/日期/复审时间）
- [x] Wow Moment（N×M → N+M，USB 类比）
- [x] 术语速查表（10 个核心术语）
- [x] 三代工具连接范式对比（各自为政/Function Calling/MCP）
- [x] MCP vs Function Calling / OpenAPI / LangChain Tool 关系厘清
- [x] MCP 四角色架构图（Host/Client/LLM/Server）
- [x] 三层原语深度解析（Tools/Resources/Prompts + 对比表）
- [x] 两种传输方式（stdio/SSE + 配置示例）
- [x] 三段代码（MCP Server 实现 / Client 调用演示 / Skill 设计示例）
- [x] Skill vs MCP Tool 设计哲学对比
- [x] CLI 三代演进（传统/AI增强/Agent 自主）
- [x] 四层 AI 工具生态架构图
- [x] MCP 生态全景表（7 类 Server）
- [x] 面试真题（4 道，含 🔥⚠️💡 标注）
- [x] 三层作业（基础/进阶/挑战）
- [x] 引用说明（6 条权威来源）

---

## 与其他课程的关系

```
课程09 Agent 七种武器
（武器① 函数调用 / 武器⑦ 多智能体）
        │
        │ MCP 是函数调用的标准化与生态化
        ▼
课程10（本课）
MCP / Skill / CLI 技术演进
        │
        ├────────────────────────────────┐
        ▼                                ▼
课程11 终端里的 AI 工程师             课程12 OpenClaw 架构
（Claude Code / Codex）               （企业 AI 管家）
Agent CLI 的典型产品实现              MCP 生态集成的系统工程实践
具体工具：MCP + Skill 的              多 MCP Server 编排 +
完整落地案例                          Skill 库的企业级管理
```

**前置依赖**：
- **课程 09**（必要）：Agent 的七种武器中，武器①（函数调用）是 MCP 的基础；理解 Function Calling 机制是理解 MCP 协议的前提

**后续影响**：
- **课程 11（Claude Code）**：Claude Code 是 MCP+Skill+Agent CLI 的完整产品体现，本课的所有概念在课程 11 均有具体实现案例
- **课程 12（OpenClaw）**：企业级 AI 管家架构需要管理多个 MCP Server、维护 Skill 库、协调多 Agent，本课是其工具层的理论基础
