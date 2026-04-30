---
课程: 11 - 终端里的 AI 工程师：Claude Code、Codex 与编程助手
版本: v1.0
创建日期: 2026-04-29
最后更新: 2026-04-29
适用模型版本: Claude 3.5 Sonnet / GPT-4o / Claude Code CLI / GitHub Copilot
下次复审: 2026-07-29
更新触发条件:
  - Claude Code 发布重大版本更新（新工具/新权限模型）
  - SWE-bench 出现新 SOTA 结果
  - GitHub Copilot / Cursor 发布重大功能
  - 新的 AI 编程 Agent 产品发布
---

# 课程 11：终端里的 AI 工程师——Claude Code、Codex 与编程助手

> **课程类型**：C 架构型 | **时长**：70 min | **前置课程**：课程 09（Agent）、课程 10（MCP/Skill/CLI）

---

## 逻辑主线

```
"AI 能帮我写代码了，但它真的能解决真实的软件工程问题吗？"
        ↓
[演进脉络] 三代 AI 编程助手
  第一代：代码补全（2021）—— Copilot，低侵入，如影随形的自动完成
  第二代：对话编程（2023）—— ChatGPT + Code Interpreter，问答式生成
  第三代：Agent 编程（2024）—— Claude Code/Devin，自主读写文件、跑测试
        ↓
[核心跨越] SWE-bench 基准
  AI 解决真实 GitHub Issue 的能力：
  2023年最强：~3% → Devin（2024.3）：13.86% → Claude 3.5（2025）：49%+
        ↓
[Claude Code 解剖]
  = Agent（规划执行）
  + 文件系统工具（读写删改）
  + Shell 工具（运行命令/测试）
  + 搜索工具（代码语义检索）
  + Git 工具（diff/commit/PR）
  + MCP 扩展（连接外部系统）
  + Skill 系统（复用任务模板）
  + CLAUDE.md（项目记忆）
        ↓
[工程实践] 如何让 AI 编程助手真正提升团队效率
  CLAUDE.md 设计 / 任务描述技巧 / 权限边界 / CI/CD 集成
        ↓
"从'AI 写代码片段'到'AI 解决真实软件工程问题'"
```

---

## 课程简介

本课聚焦软件工程师最关心的问题：**AI 编程助手已经能做到什么？怎么用好它？**

从 GitHub Copilot 的自动补全，到 Claude Code 的 Agent 编程，AI 辅助编程在三年内经历了三代跃迁。本课不只讲"怎么用"，更讲：
- 三代 AI 编程助手的架构差异与能力边界
- Claude Code 的完整技术栈（Agent + 工具集 + MCP + Skill）
- CLAUDE.md 这个被低估的工程神器如何构建"项目记忆"
- SWE-bench 基准怎么衡量 AI 解决真实工程问题的能力
- 将 AI 编程助手集成到团队工作流的实战策略

---

## 课程描述：学完本课你能收获

| 能力 | 具体表现 |
|------|---------|
| **演进理解** | 说清三代 AI 编程助手的架构差异和能力边界 |
| **原理掌握** | 解释 Claude Code 的工具集组成和执行循环 |
| **基准认知** | 解读 SWE-bench 结果，合理评估 AI 编程能力 |
| **工程实践** | 设计高质量 CLAUDE.md，编写有效的任务描述 |
| **选型决策** | 针对不同场景选择合适的 AI 编程工具 |
| **面试应答** | 回答"AI 编程助手的局限性"、"如何评估 AI 写代码的质量"等题 |

---

## Wow Moment 🎯

> **2023 年底，最好的 AI 系统在 SWE-bench（用真实 GitHub Issue 测试 AI 解决软件 Bug 的能力）上只能解决 3% 的问题；2024 年 3 月，Devin 横空出世，达到 13.86%，震惊业界；到 2025 年，Claude 3.5 Sonnet 在 SWE-bench Verified 上达到 49%——不到两年，AI 解决真实软件工程问题的能力提升了 16 倍。这不是"写代码"，这是"做软件工程"。**

引导设问（讲师口播）：
- "你有没有让 ChatGPT 帮你写代码，但它给的代码跑不起来？"
- "GitHub Copilot 的自动补全，和让 AI 直接修 bug，有多大区别？"
- "如果 AI 能做 50% 的软件工程工作，开发者的价值在哪里？"

---

## 术语速查表

| 术语 | 一句话定义 | 首次出现位置 |
|------|-----------|------------|
| SWE-bench | 用真实 GitHub Issue 测试 AI 解决软件 Bug 能力的基准，2023 年发布 | Part 1.3 |
| Claude Code | Anthropic 的 AI 编程 CLI，完整 Agent 架构，在终端中自主完成编程任务 | Part 2.1 |
| CLAUDE.md | 项目根目录的 AI 指令文件，定义项目规范、架构约定、禁止操作 | Part 2.4 |
| GitHub Copilot | GitHub/微软的 IDE 内嵌代码补全工具，基于 Codex 模型 | Part 1.1 |
| Codex | OpenAI 专门针对代码训练的模型系列（已演进为 GPT-4o 代码能力）| Part 1.1 |
| SWE-agent | CMU 开源的软件工程 Agent，Agent-Computer Interface 的研究实现 | Part 1.2 |
| Devin | Cognition AI 发布的 AI 软件工程师，首个商业化 AI 编程 Agent | Part 1.3 |
| 代码 RAG | 将代码库向量化，供 AI 检索相关代码上下文，是 AI 编程的核心技术 | Part 2.3 |
| 沙箱执行（Sandbox） | 隔离的代码运行环境，防止 AI 执行危险操作影响真实系统 | Part 2.2 |
| agentic loop | Agent 的执行循环：感知→规划→行动→观察→再规划，直到任务完成 | Part 2.1 |

---

## 课程结构总览

| Part | 主题 | 时长 | 核心交付 |
|------|------|------|---------|
| **Part 0** | 直觉导入：从"补全"到"工程师" | 5 min | 三代跃迁 + SWE-bench 数字冲击 |
| **Part 1** | 三代 AI 编程助手演进 | 12 min | 架构对比 + 能力边界 + 基准分析 |
| **Part 2** | Claude Code 架构深度解析 | 18 min | 工具集解剖 + 执行循环 + CLAUDE.md |
| **Part 3** | 代码实战：三段工程实践 | 15 min | CLAUDE.md / 任务描述 / MCP 扩展 |
| **Part 4** | 产品视角：选型与工作流集成 | 15 min | 四款产品对比 + CI/CD 集成 + 局限性 |
| **Part 5** | 总结与作业 | 5 min | 面试真题 + 实战挑战 |

---

## 章节大纲

### Part 0：直觉导入——从"补全"到"工程师"（5 min）

#### 0.1 一个工程师的三天（3 min）

**2021 年（第一代）**：
> 小明在 VSCode 里写一个 API 函数。敲了函数名，Copilot 自动补全了参数和实现。小明检查了一下，按 Tab 接受，继续写下一行。
> **AI 是副驾驶：提建议，人决策**

**2023 年（第二代）**：
> 小明遇到一个复杂的 bug。他把错误信息贴给 ChatGPT，ChatGPT 分析出可能的原因，给出修复代码。小明复制代码，粘贴到编辑器，运行——还是有问题。他再贴给 ChatGPT 继续问。
> **AI 是同事：你描述问题，它给建议，你来执行**

**2025 年（第三代）**：
> 小明打开终端：`claude "找出所有 N+1 查询问题，优化并补全测试"`。
> Claude Code 自动扫描代码库，识别出 7 个 N+1 查询，逐一修复，运行测试，提交 PR。
> 小明审查 PR，合并。
> **AI 是初级工程师：你说目标，它自主完成**

#### 0.2 SWE-bench 数字带来的冲击（2 min）

```
SWE-bench：从 GitHub 收集 2294 个真实 Issue + 对应 PR
           测试 AI 能否独立解决这些 Bug（不给解法，只给问题描述）

时间线：
  2023.11  SWE-bench 发布：最强 AI 解决率 1.96%（Claude 2）
  2024.03  Devin 发布：13.86%（业界震惊）
  2024.06  SWE-agent（开源）：12.5%
  2024.10  Claude 3.5 Sonnet：49%（SWE-bench Verified 子集）
  2025     多个系统突破 50%

意义：不是"写出正确代码"，而是"理解真实软件系统，定位并修复 Bug"
这是软件工程能力的量化，不是语言能力的量化。
```

---

### Part 1：三代 AI 编程助手演进（12 min）

#### 1.1 第一代：代码补全（2021-2022）（3 min）

**代表产品**：GitHub Copilot（2021.6）、Tabnine、Amazon CodeWhisperer

**技术架构**：
```
用户输入（光标位置上下文）
       ↓
Codex / Starcoder（代码专向大模型）
       ↓
补全建议（1-10 行，多候选）
       ↓
用户接受/拒绝
```

**核心特征**：
- **范式**：实时补全，低延迟（< 500ms）
- **上下文**：仅当前文件（最多 2048 tokens）
- **交互**：Tab 接受，Esc 拒绝，零打断
- **能力上限**：函数级补全，不理解跨文件依赖

**真实价值**：
> GitHub 2022 年研究：使用 Copilot 的开发者编码速度提升 55%，接受率约 30%
> 真正的价值不在于"AI 写代码"，而在于**消除了"打字"这个摩擦点**

**局限性**：不理解项目意图，无法跨文件推理，不能运行代码验证。

#### 1.2 第二代：对话编程（2023）（3 min）

**代表产品**：ChatGPT（2022.11）、Cursor（2023）、Codeium

**技术架构**：
```
用户用自然语言描述需求
       ↓
GPT-4 / Claude（通用大模型，代码能力增强）
       ↓
生成代码 + 解释
       ↓
用户复制粘贴 / Cursor 直接 Apply
       ↓
用户运行验证（人工）
```

**核心特征**：
- **范式**：问答式，多轮对话，可以解释和修改
- **上下文**：用户手动提供（复制粘贴代码片段）
- **交互**：自然语言描述需求，AI 生成完整实现
- **能力提升**：可处理复杂算法，可解释代码逻辑

**Cursor 的关键创新**：
- 在 IDE 内嵌 LLM，无需切换工具
- `Cmd+K`：内联编辑，`Cmd+L`：Chat 面板
- **自动填充上下文**：当前文件 + 打开的文件 → AI 感知项目局部

**局限性**：用户必须手动管理上下文；AI 不能运行代码；不能自主迭代直到成功。

#### 1.3 第三代：Agent 编程（2024-2026）（6 min）

**代表产品**：Claude Code（2025.2）、Devin（2024.3）、OpenDevin、SWE-agent

**技术架构**：
```
用户描述目标（"修复这个 bug 并补充测试"）
       ↓
[规划] LLM 理解任务，制定执行计划
       ↓
[执行循环]
  读代码 → 理解上下文 → 写修改 → 运行测试
  → 观察结果 → 如果失败：分析错误 → 继续修改
  → 直到测试通过
       ↓
[汇报] 向用户汇报完成情况 + 展示变更
       ↓
用户审查并确认
```

**与前两代的本质区别**：

| 维度 | 第一代 | 第二代 | 第三代 |
|------|-------|-------|-------|
| 自主性 | 无（按键确认）| 低（生成后人执行）| 高（自主循环执行）|
| 上下文 | 当前文件 | 人工提供片段 | 自主扫描整个项目 |
| 验证 | 无 | 人工运行 | 自动运行测试 |
| 迭代 | 无 | 人工多轮 | 自动迭代直到通过 |
| 任务粒度 | 行/函数 | 函数/类 | 功能/issue/PR |

**SWE-bench 能力跃迁解读**：

```
为什么第三代有 16 倍的能力跃迁？

原因一：工具使用
  - 能读整个仓库（grep/find），不限于当前文件
  - 能实际运行代码（pytest/npm test），发现真实错误

原因二：迭代修复
  - 测试失败 → 分析报错 → 修改代码 → 再次运行
  - 人类工程师也是这样工作的，AI 第三代才做到

原因三：跨文件理解
  - bug 往往跨越多个文件，需要整体理解
  - Agent 可以追踪调用链、理解模块依赖

原因四：更强的基础模型
  - Claude 3.5 的代码理解和生成能力显著强于前代
```

---

### Part 2：Claude Code 架构深度解析（18 min）

#### 2.1 整体架构与执行循环（5 min）

**Claude Code = 一个专为软件工程设计的 Agent**

```
┌──────────────────────────────────────────────────────┐
│                  Claude Code 架构                      │
│                                                      │
│  用户输入（终端）                                       │
│       ↓                                              │
│  ┌─────────────────────────────────────────────┐    │
│  │              Agent 核心循环                   │    │
│  │                                             │    │
│  │  Claude 3.5 Sonnet（大脑）                   │    │
│  │       ↓ 规划 ↓                              │    │
│  │  ┌────────────────────────────────────┐     │    │
│  │  │         工具集（手脚）               │     │    │
│  │  │  文件工具  Shell工具  搜索工具  Git工具│     │    │
│  │  │  Read/Write  Bash  Grep/Glob  diff │     │    │
│  │  └────────────────────────────────────┘     │    │
│  │       ↓ 执行 → 观察 → 再规划                │    │
│  └─────────────────────────────────────────────┘    │
│                                                      │
│  ┌──────────────────┐  ┌─────────────────────────┐  │
│  │   MCP 扩展层      │  │      CLAUDE.md（记忆）    │  │
│  │ 外部工具接入       │  │  项目规范 + 架构约定       │  │
│  └──────────────────┘  └─────────────────────────┘  │
└──────────────────────────────────────────────────────┘
```

**agentic loop（执行循环）的五个阶段**：

```
① 感知（Perception）
   读取用户指令 + 加载 CLAUDE.md + 扫描项目结构

② 规划（Planning）
   LLM 理解任务，生成执行计划
   "我需要：先找相关文件 → 理解现有实现 → 修改代码 → 跑测试 → 验证"

③ 行动（Action）
   调用工具执行计划中的每一步
   Read(file) / Bash("pytest") / Edit(file, old, new)

④ 观察（Observation）
   接收工具返回结果
   测试通过 → 继续下一步；测试失败 → 分析错误信息

⑤ 更新规划（Re-planning）
   根据观察结果更新计划，继续循环
   直到任务完成或遇到无法处理的情况（请求用户介入）
```

**权限模型（Permission Model）**：
Claude Code 的安全边界由三层构成：
1. **自动允许**（无需确认）：读文件、搜索代码、查看 git 状态
2. **需要确认**（默认）：写文件、执行命令、删除内容、git commit/push
3. **永远禁止**（不管用户怎么说）：访问系统配置之外的路径、泄露 API Key

#### 2.2 工具集详解（5 min）

**工具类别一：文件系统工具**

```python
# Read：读取文件内容（带行号范围）
Read(file_path="src/auth.py", offset=100, limit=50)
# → 返回第100-150行内容

# Write：写入/覆盖文件
Write(file_path="src/auth.py", content="...")
# 注意：Write 前必须先 Read，否则工具拒绝执行（防止覆盖未读内容）

# Edit：精确字符串替换（最常用）
Edit(file_path="src/auth.py",
     old_string="def login(user, pwd):",
     new_string="def login(user: str, pwd: str) -> dict:")
# 要求 old_string 在文件中唯一（避免误替换）

# MultiEdit：一次多处修改（减少 round-trip）
# Glob：按模式匹配文件路径
Glob(pattern="src/**/*.py")  # → 返回所有 Python 文件列表
```

**工具类别二：Shell 执行工具**

```bash
# Bash：执行 shell 命令，返回 stdout/stderr
Bash("pytest tests/test_auth.py -v --tb=short")
Bash("npm run test 2>&1 | tail -50")
Bash("git diff HEAD --stat")

# 安全约束：
# - 不能执行 rm -rf /（危险命令检测）
# - 不能访问 ~/.ssh/ 等敏感目录（路径白名单）
# - 每次执行都在新 shell（无状态，cd 不持久化）
# → 需要连续命令用 && 链接：Bash("cd src && python main.py")
```

**工具类别三：搜索工具**

```bash
# Grep：内容搜索（ripgrep，支持正则）
Grep(pattern="def login", path="src/", type="py")
Grep(pattern="TODO|FIXME", glob="**/*.py", output_mode="count")

# 代码 RAG 的底层实现：
# Claude Code 不只是字面搜索，还会根据语义决定搜索策略
# "找到处理用户认证的地方" →
#   Grep("authenticate") + Grep("login") + Glob("**/auth*")
#   → 综合多次搜索定位相关代码
```

**工具类别四：Git 工具**

```bash
# 通过 Bash 调用 git（Git 操作全部通过 shell）
Bash("git status")
Bash("git diff src/auth.py")
Bash("git add src/auth.py tests/test_auth.py")
Bash("git commit -m 'fix: resolve N+1 query in user auth flow'")

# 注意：git push 默认需要用户确认（影响远程仓库）
```

#### 2.3 代码 RAG：AI 如何理解大型代码库（4 min）

**核心挑战**：
- 真实项目代码库可能有数十万行，远超上下文窗口
- Claude Code 如何在不读完所有文件的情况下"理解"项目？

**分层理解策略**：

```
第一层：结构感知（项目入口，几秒内完成）
  Glob("**") → 获取文件树
  Read("README.md") → 理解项目概述
  Read("package.json") or Read("pyproject.toml") → 依赖和入口

第二层：按需深入（根据任务聚焦）
  任务="修复登录 bug" →
  Grep("login", "auth") → 找到相关文件
  Read("src/auth/login.py") → 深度阅读相关文件

第三层：追踪依赖（理解调用链）
  发现 login() 调用了 validate_token() →
  Grep("def validate_token") → 找到定义
  Read(定义文件) → 理解实现
```

**CLAUDE.md 如何加速理解**：
> CLAUDE.md 相当于给 AI 写了一份"新员工入职手册"——告诉它项目架构、重要约定、禁止事项，让 AI 不需要从零探索就能快速定位关键代码。

#### 2.4 CLAUDE.md：项目记忆系统（4 min）

**CLAUDE.md 是什么**：
- 放在项目根目录（或 `~/.claude/CLAUDE.md` 全局）
- 每次 Claude Code 启动时自动读取，注入上下文
- 相当于"每次对话的固定 System Prompt"，但针对项目而非角色

**CLAUDE.md 的五个核心模块**：

```markdown
# CLAUDE.md 模板

## 项目概述
[一句话描述项目是什么，服务什么业务]
技术栈：Python 3.11 + FastAPI + PostgreSQL + Redis

## 架构约定
- API 层：/src/api/（薄层，只做路由和参数校验）
- 服务层：/src/services/（业务逻辑，不直接操作数据库）
- 数据层：/src/models/（SQLAlchemy ORM 模型）
- 测试：每个服务文件对应 tests/ 下同名测试文件

## 开发规范
- 所有函数必须有类型注解
- 使用 Black 格式化（行长 88）
- 提交信息遵循 Conventional Commits
- 数据库迁移用 Alembic，不直接修改表结构

## 常用命令
- 运行测试：`pytest tests/ -v --cov=src`
- 启动服务：`uvicorn src.main:app --reload`
- 数据库迁移：`alembic upgrade head`

## ⚠️ 禁止操作
- 不得修改 /migrations/ 目录下的已有迁移文件
- 不得删除 /src/core/ 下的任何文件（核心基础设施）
- 不得直接运行 `alembic downgrade`（需人工确认）
- 生产环境配置在 .env.prod，不得读取或修改
```

**CLAUDE.md 的三级作用域**：

| 作用域 | 位置 | 覆盖范围 | 用途 |
|--------|------|---------|------|
| 全局 | `~/.claude/CLAUDE.md` | 所有项目 | 个人偏好、通用规范 |
| 项目 | `[repo-root]/CLAUDE.md` | 当前项目 | 架构约定、技术栈 |
| 目录 | `[subdir]/CLAUDE.md` | 子目录 | 模块级规范 |

**优先级**：目录级 > 项目级 > 全局级（更具体的覆盖更通用的）

---

### Part 3：代码实战——三段工程实践（15 min）

#### 3.1 高质量任务描述：让 AI 真正理解你的意图（5 min）

**低质量 vs 高质量任务描述对比**：

```bash
# ❌ 低质量：模糊、无上下文
claude "修复 bug"

# ❌ 低质量：只描述现象，不给上下文
claude "用户登录报错了"

# ✅ 高质量：有背景、有约束、有验收标准
claude "
背景：用户报告在登录时偶现 500 错误，错误信息：
'AttributeError: NoneType object has no attribute user_id'
出现频率：每天约 20 次，通常在高峰期

位置：应该在 src/api/auth.py 的 login() 函数，
     调用 src/services/user_service.py 的 get_user_by_email()

要求：
1. 定位并修复根本原因（不是 try-catch 掩盖）
2. 添加单元测试覆盖这个场景
3. 不要修改 API 接口的返回格式

验收：pytest tests/test_auth.py 全部通过
"
```

**高质量任务描述的六要素**：

| 要素 | 说明 | 示例 |
|------|------|------|
| **背景** | 为什么要做这件事 | "用户反映登录报错" |
| **现象** | 具体的错误信息或行为 | "AttributeError: ..." |
| **范围** | 影响哪些文件/模块 | "在 src/api/auth.py" |
| **约束** | 不能做什么 | "不要修改接口格式" |
| **验收** | 怎么判断完成了 | "pytest 全部通过" |
| **上下文** | 相关依赖或前置知识 | "使用 SQLAlchemy ORM" |

#### 3.2 实战：用 Claude Code 做代码重构（5 min）

```bash
# 场景：重构一个性能差的数据库查询函数
# 当前代码存在 N+1 查询问题

# 第一步：探索性任务（先让 AI 分析，再决定是否执行）
claude "分析 src/api/orders.py 中的 get_orders_with_items() 函数，
       找出所有 N+1 查询问题，说明原因，但先不要修改代码"

# Claude 会输出类似：
# [Read src/api/orders.py]
# [Grep "get_orders_with_items"]
# 分析结果：
#   发现 3 处 N+1 查询：
#   1. 第 47 行：循环中调用 order.items（触发 N 次 SELECT）
#   2. 第 58 行：order.user 懒加载（N 次 SELECT）
#   3. 第 72 行：循环中调用 item.product（N 次 SELECT）
#   建议：使用 joinedload/selectinload 预加载

# 第二步：确认分析正确后，执行修改
claude "
按照刚才的分析，修复这 3 个 N+1 查询：
- 用 SQLAlchemy 的 selectinload 预加载 items
- 用 joinedload 预加载 user（因为总是需要）
- 用 selectinload 预加载 items.product

修改完成后运行 pytest tests/test_orders.py -v 验证
"

# Claude 的执行过程（verbose 输出）：
# [Read src/api/orders.py]     → 读取当前实现
# [Read src/models/order.py]   → 理解 ORM 关系定义
# [Edit src/api/orders.py ...]  → 修改查询
# [Bash "pytest tests/test_orders.py -v"]  → 运行测试
# Tests passed: 12/12 ✓
# 汇报：已修复 3 处 N+1 查询，查询数从 O(N) 降至 O(1)
```

#### 3.3 CLAUDE.md 最佳实践示例（5 min）

```markdown
# CLAUDE.md — AI 金融数据分析平台

## 一句话描述
为机构投资者提供实时市场数据分析和风险评估的 SaaS 平台。

## 技术栈
- 后端：Python 3.11, FastAPI, Celery, Redis
- 数据库：PostgreSQL 15（主），ClickHouse（时序数据）
- 消息队列：Kafka（行情数据流）
- 前端：React 18 + TypeScript + TradingView 图表库

## 项目结构
src/
├── api/          # HTTP 路由层（只处理请求/响应格式）
├── services/     # 业务逻辑层（所有业务规则在这里）
├── models/       # PostgreSQL ORM（SQLAlchemy 2.0）
├── tasks/        # 异步任务（Celery）
├── market_data/  # 行情数据处理（ClickHouse 查询）
└── core/         # 基础设施（不要随意修改）

## 开发规范
1. 所有 Service 方法必须有 async/await（全异步架构）
2. 金融计算必须使用 Decimal，绝对不能用 float
3. 所有外部 API 调用必须有超时设置（默认 5s）
4. 数据库查询在 services 层，models 层只定义 schema
5. 错误处理：业务错误用自定义 BusinessException，系统错误让它向上传播

## 测试规范
- 单元测试：pytest，mock 所有外部依赖
- 集成测试：tests/integration/（需要真实数据库，CI 环境运行）
- 运行单测：`pytest tests/unit/ -v --cov=src --cov-fail-under=80`
- 覆盖率要求：新代码 > 80%

## 常用命令速查
```bash
make dev          # 启动开发环境（Docker Compose）
make test         # 运行单元测试
make test-all     # 运行所有测试（含集成测试）
make migration    # 生成 Alembic 迁移
make lint         # Black + isort + mypy
```

## ⚠️ 绝对禁止
- **不得使用 float 做金融计算**（会导致精度丢失，这是生产事故的历史原因）
- **不得修改 src/core/risk_engine.py**（已通过监管合规审计，修改需走变更流程）
- **不得直接操作 ClickHouse**（必须通过 market_data/ 的封装层）
- **不得在 models 层写业务逻辑**（架构红线）
- **不得 git push --force**（共享分支，会破坏他人工作）

## 重要背景知识
- 所有时间戳统一用 UTC，不存本地时区
- 行情数据延迟规则：实时用户 < 15ms，延迟用户 15 分钟
- 合规要求：所有数据访问必须记录审计日志（通过 @audit_log 装饰器）
```

**为什么这个 CLAUDE.md 有效**：
- 📌 告诉 AI"为什么"有些约束（float 是历史事故根源），不只是"不能做"
- ⚠️ 明确标注绝对禁止，防止 AI 在自主执行时越界
- 🔧 提供具体命令，AI 可以直接运行而不需要推断
- 🏗️ 解释架构意图，帮助 AI 在不确定时做出符合设计的决策

---

### Part 4：产品视角——选型与工作流集成（15 min）

#### 4.1 四款 AI 编程工具横向对比（6 min）

| 维度 | GitHub Copilot | Cursor | Claude Code | Devin |
|------|---------------|--------|-------------|-------|
| **定位** | IDE 内嵌补全 | AI 原生 IDE | Agent CLI | AI 软件工程师 |
| **交互方式** | Tab 补全 | 内联编辑 + Chat | 终端命令 | Web 界面 + PR |
| **自主性** | 最低（单行建议）| 中低（生成+Apply）| 高（自主循环）| 最高（全自主）|
| **上下文获取** | 当前文件 | 打开的文件 | 主动探索整个仓库 | 同 Claude Code |
| **代码执行** | ✗ | ✗（部分支持）| ✓（Bash 工具）| ✓ |
| **测试运行** | ✗ | ✗ | ✓ | ✓ |
| **MCP 集成** | ✗ | 部分 | ✓（完整支持）| 有限 |
| **CLAUDE.md** | ✗ | ✗ | ✓（核心特性）| 有类似设计 |
| **价格（2026）** | $10-19/月 | $20/月 | 按 Token 计费 | $500/月 |
| **最适合** | 所有开发者日常 | 前端/快速开发 | 复杂工程任务 | 外包式任务委托 |

**选型建议**：
- **日常编码提效**：Copilot（低侵入，随时在线）
- **复杂功能开发**：Cursor（IDE 集成，适合全局改动）
- **技术债务重构**：Claude Code（自主探索大仓库，自动测试）
- **独立功能模块**：Claude Code / Devin（给目标，AI 全程完成）
- **团队全栈**：Copilot（普适）+ Claude Code（复杂任务）的组合

#### 4.2 将 Claude Code 集成到团队工作流（5 min）

**模式一：Code Review 辅助**

```bash
# 在 PR 创建时触发 Claude Code 分析
# .github/workflows/ai-review.yml

name: AI Code Review
on: [pull_request]

jobs:
  claude-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: AI Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          # 获取 PR 的变更内容
          git diff origin/main...HEAD > /tmp/pr_diff.txt

          # 让 Claude 审查（使用 API，非 Claude Code CLI）
          python scripts/ai_review.py /tmp/pr_diff.txt

      - name: Post Review Comment
        uses: actions/github-script@v6
        # 将 AI Review 结果作为 PR Comment 发布
```

```python
# scripts/ai_review.py
import anthropic, sys

client = anthropic.Anthropic()

with open(sys.argv[1]) as f:
    diff = f.read()

REVIEW_PROMPT = """你是一位严格但友善的代码审查者。请审查以下代码变更：

```diff
{diff}
```

请检查：
1. 潜在的 bug 或边界情况
2. 性能问题（如 N+1 查询、不必要的循环）
3. 安全漏洞（注入、越权等）
4. 是否符合项目规范（见 CLAUDE.md）

输出格式：
- 🔴 严重问题（必须修复）：...
- 🟡 建议改进（可选）：...
- ✅ 做得好的地方：...
"""

response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=2000,
    messages=[{"role": "user", "content": REVIEW_PROMPT.format(diff=diff[:8000])}]
)

print(response.content[0].text)
```

**模式二：自动化测试生成**

```bash
# 为新写的代码自动生成测试
claude "
查看最近的 git 变更（git diff HEAD~1），
为所有新增的 public 函数生成完整的单元测试。

要求：
- 测试文件放在对应的 tests/ 目录
- 覆盖正常路径和边界情况
- 使用 pytest fixtures，不要重复代码
- 运行测试确保全部通过
"
```

**模式三：文档自动更新**

```bash
# 代码改了，文档同步更新
claude "
比较 git diff HEAD~1 和 docs/api.md 的内容，
找出代码变更中导致 API 文档过时的部分，
更新 docs/api.md 中对应的接口描述和示例
"
```

#### 4.3 AI 编程助手的局限性与使用边界（4 min）

**AI 编程助手目前做不好的事**：

| 局限 | 原因 | 应对策略 |
|------|------|---------|
| **理解业务意图** | AI 不了解公司背景和历史决策 | CLAUDE.md 注入业务背景 |
| **跨系统集成测试** | 无法在测试环境运行完整系统 | 人工负责集成测试的设计和验收 |
| **安全敏感操作** | AI 可能遗漏安全边界 | 所有安全相关代码人工审查 |
| **架构决策** | 没有业务全局视野 | AI 实现，人类设计架构 |
| **超长文件（>2000行）** | 上下文压缩导致理解偏差 | 提前拆分文件，保持函数小巧 |

**AI 编程助手的最佳使用场景**：

```
✅ 高效使用：
  - 已有清晰规范（有 CLAUDE.md，有测试）的项目
  - 重复性工作（生成 CRUD、写测试、格式转换）
  - 技术债务重构（有测试保护的情况下）
  - 代码解释和文档生成

⚠️ 谨慎使用：
  - 全新项目从零开始（AI 会做很多假设）
  - 安全关键模块（认证、支付、权限）
  - 复杂的并发/分布式逻辑

❌ 不推荐使用：
  - 没有测试的遗留系统（AI 修改无法验证）
  - 需要深度业务知识的核心算法
  - 合规审计过的代码（修改流程有限制）
```

**🔥 面试考点**：AI 编程助手的价值不在于"替代程序员"，而在于**消除重复性工作，让工程师专注于架构决策、业务理解和质量把控**。SWE-bench 49% 意味着"近一半的 bug 可以由 AI 自主修复"，但另一半（和所有的架构设计）仍然需要人类工程师。

---

### Part 5：总结与作业（5 min）

#### 5.1 本课核心知识地图

```
三代 AI 编程助手
  第一代：补全（Copilot）
  第二代：对话（ChatGPT/Cursor）
  第三代：Agent（Claude Code）
        │
SWE-bench 能力基准
  2023: 3% → 2025: 49%
        │
Claude Code 架构
  agentic loop + 工具集（文件/Shell/搜索/Git）
        │
        ├── CLAUDE.md：项目记忆（五模块）
        └── MCP 扩展：接入外部系统
        │
工程实践
  高质量任务描述 + CI/CD 集成 + 局限性边界
```

#### 5.2 三类人群的学习重点

| 人群 | 重点掌握 | 可略读 |
|------|---------|-------|
| 转行小白 | Part 0-1（三代演进）、Part 2.4（CLAUDE.md）、Part 4.3（局限性）| Part 3 代码实现 |
| 算法工程师 | Part 2.1-2.3（架构+工具集）、Part 3.1（任务描述）、Part 3.3（CLAUDE.md）| Part 4.1 产品对比 |
| AI 产品经理 | Part 1.3（SWE-bench 解读）、Part 4.1（选型对比）、Part 4.3（局限性）| Part 2.2 工具细节 |

#### 5.3 面试真题与参考答案

**🔥 高频真题 1**：第三代 AI 编程助手和前两代的核心区别是什么？
> 三个维度：① 自主性——第三代可以自主循环执行（读文件→写修改→运行测试→迭代），不需要人工介入每一步；② 上下文——第三代主动探索整个代码库而非依赖人工提供片段；③ 验证——第三代会自己运行测试验证结果，发现失败则自动修复。SWE-bench 数据：能力从 3% 提升到 49%，量化体现这三个差距。

**🔥 高频真题 2**：SWE-bench 测试什么，为什么有意义？
> SWE-bench 使用真实 GitHub Issue（2294 个），测试 AI 在只给问题描述的情况下能否独立修复 Bug——需要理解代码库、定位问题、编写修复、通过测试。相比"能否写出正确代码"，SWE-bench 测的是完整软件工程能力（理解、定位、修复、验证），更贴近真实工作场景。

**⚠️ 中频真题 3**：CLAUDE.md 有什么作用，如何设计？
> CLAUDE.md 是项目的 AI 指令文件，在每次启动时自动注入上下文，解决"AI 不了解项目背景"的问题。五个核心模块：项目概述（一句话描述）、架构约定（文件组织规则）、开发规范（代码风格/测试要求）、常用命令（AI 可直接使用）、禁止操作（安全红线）。重点：要说明"为什么"有某些约束，不只是列规则。

**💡 进阶真题 4**：如何评估 AI 编程助手在团队中的 ROI？
> 三个维度：① 速度提升（代码编写速度，可测量：同类任务用时对比）；② 质量变化（bug 率、测试覆盖率变化）；③ 工程师体验（是否愿意用，满意度调查）。同时要计算成本（API 费用 + 工程师学习曲线 + 审查 AI 代码的时间）。行业经验：Copilot 类工具约提升 20-40% 效率；Agent 类工具对特定任务（重构/测试生成）提升可达 5-10 倍。

#### 5.4 实战作业

**Level 1（基础）**：配置你的 CLAUDE.md
1. 选择你正在参与的一个项目（个人项目也可以）
2. 参考 Part 3.3 的模板，为这个项目编写 CLAUDE.md
3. 包含：项目概述、技术栈、架构约定、常用命令、至少 3 条禁止操作

**Level 2（进阶）**：实战 Claude Code 任务
1. 安装 Claude Code（`npm install -g @anthropic-ai/claude-code`）
2. 在你的项目中执行一个真实任务：
   - 建议：为一个现有函数补充单元测试
   - 或：找出并修复一个简单 bug
3. 记录 Claude Code 的执行步骤，观察它如何探索代码库

**Level 3（挑战）**：构建 AI Code Review 流水线
1. 参考 Part 4.2，在你的 GitHub 仓库中配置 AI Code Review Action
2. 创建一个 PR，观察 AI Review 结果
3. 评估 AI Review 的准确性：有哪些有价值的建议？有哪些误报？

---

## 引用说明

| 编号 | 引用来源 | 引用位置 | 说明 |
|------|---------|---------|------|
| [1] | Jimenez et al., "SWE-bench: Can Language Models Resolve Real-world GitHub Issues?", ICLR 2024 | Part 0.2 / Part 1.3 | SWE-bench 基准论文 |
| [2] | Cognition AI, "Introducing Devin", Blog 2024.03 | Part 1.3 | Devin 发布博客 |
| [3] | Yang et al., "SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering", 2024 | Part 1.3 | SWE-agent 论文 |
| [4] | GitHub, "GitHub Copilot Research: Quantifying GitHub Copilot's impact on developer productivity and happiness", 2022 | Part 1.1 | Copilot 效率研究 |
| [5] | Anthropic, "Claude Code: Best practices for agentic coding", Docs 2025 | Part 2.4 | CLAUDE.md 官方最佳实践 |
| [6] | Anthropic, "Claude 3.5 Sonnet SWE-bench Results", 2024.10 | Part 0.2 | Claude 3.5 SWE-bench 49% 结果 |

---

## 必须包含的元素

- [x] YAML 元信息（版本/日期/复审时间）
- [x] Wow Moment（SWE-bench 3%→49% 的 16 倍跃迁）
- [x] 术语速查表（10 个核心术语）
- [x] 三代 AI 编程助手演进（含架构图和能力对比表）
- [x] SWE-bench 深度解读（时间线 + 能力跃迁原因分析）
- [x] Claude Code 完整架构图（agentic loop + 工具集）
- [x] 工具集四类详解（文件/Shell/搜索/Git + 代码示例）
- [x] 代码 RAG 分层理解策略
- [x] CLAUDE.md 五模块模板 + 完整真实示例（金融平台）
- [x] 三段代码（高质量任务描述对比 / 重构实战 / CLAUDE.md 示例）
- [x] 四款产品横向对比表（Copilot/Cursor/Claude Code/Devin）
- [x] CI/CD 集成代码（AI Code Review Action）
- [x] 局限性与使用边界表（✅⚠️❌ 三档）
- [x] 面试真题（4 道，含 🔥⚠️💡 标注）
- [x] 三层作业（基础/进阶/挑战）
- [x] 引用说明（6 条权威来源）

---

## 与其他课程的关系

```
课程09 Agent 七种武器          课程10 MCP/Skill/CLI
（agentic loop / 规划范式）    （MCP 协议 / Skill 设计）
        │                             │
        └──────────────┬──────────────┘
                       ▼
              课程11（本课）
          终端里的 AI 工程师
          Claude Code / Codex
                       │
                       ▼
              课程12 OpenClaw 架构
          企业级 AI 管家：将 AI 编程助手、
          RAG、Agent、MCP 集成为完整系统
          Claude Code 是其核心编程执行引擎
```

**前置依赖**：
- **课程 09**（必要）：Claude Code 的 agentic loop 是 Agent 七种武器的完整体现，不理解 Agent 架构难以理解 Claude Code 的执行机制
- **课程 10**（必要）：Claude Code 的工具扩展依赖 MCP，团队复用依赖 Skill，CLI 范式演进是 Claude Code 的直接背景

**后续影响**：
- **课程 12（OpenClaw）**：OpenClaw 将 Claude Code 作为编程执行引擎，结合 RAG（知识库）、多 Agent（分工协作）、MCP（工具生态）构建企业级 AI 管家，是本课所有内容的系统集成
