# 课程 8：Claude Code 架构原理和应用（模型训练 + Harness 方案）

> **学习时长**：60 分钟
> **先修知识**：课程 1-7
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 8.1 Claude Code 是什么？

**定义**：Claude Code 是 Anthropic 推出的 AI 编程助手，集成在 Claude 生态中。

```
Claude Code = Claude 模型 + 代码理解工具 + 终端执行能力 + 项目管理

特点：
- 深度理解代码库（全仓库上下文）
- 直接读写文件、执行命令
- 支持 Git 操作
- 安全沙箱环境
```

### 8.2 Claude 模型训练流程

```
阶段1：预训练
   数据：文本、代码、多模态数据
   目标：语言建模 + 代码建模
   产出：Claude Base

阶段2：指令微调（SFT）
   数据：指令-回答对 + 代码指令
   目标：理解并遵循指令
   产出：Claude Instruct

阶段3： Constitutional AI（RLAIF）
   数据：模型自生成的输出 + AI 反馈
   目标：自我改进，减少对人类的依赖
   产出：Claude（对齐版）
```

**Constitutional AI 的核心创新**：
- 不用人类标注偏好数据
- 模型自己根据"宪法"（一组原则）批评和改进输出
- 自我监督的对齐方式

### 8.3 Claude Code 的架构

```
┌─────────────────────────────────────────┐
│            Claude Code CLI               │
├─────────────────────────────────────────┤
│  用户交互层                               │
│  - 自然语言对话                            │
│  - 代码编辑指令                            │
├─────────────────────────────────────────┤
│  工具层（Tools）                          │
│  - 文件读写                                │
│  - 终端命令执行                            │
│  - Git 操作                               │
│  - 代码搜索（grep, ripgrep）              │
│  - 浏览器访问                              │
├─────────────────────────────────────────┤
│  上下文管理                               │
│  - 项目文件索引                            │
│  - 增量上下文构建                          │
│  - 窗口管理                                │
├─────────────────────────────────────────┤
│  Claude 模型（API）                       │
│  - Claude 3.5 Sonnet / Opus              │
│  - 代码专用训练                            │
└─────────────────────────────────────────┘
```

### 8.4 Harness 架构

**Harness（工具调用框架）** 的核心是：

```
Tool Use Pattern:
1. LLM 输出包含 tool_use 块
2. 执行环境调用工具
3. 将工具结果以 tool_result 块返回给 LLM
4. LLM 根据结果继续推理

工具定义格式（JSON Schema）:
{
  "name": "read_file",
  "description": "读取文件内容",
  "parameters": {
    "path": {"type": "string", "description": "文件路径"}
  }
}
```

### 8.5 Claude Code vs 其他编程助手

| 特性 | Claude Code | Copilot | Codex CLI |
|------|------------|---------|-----------|
| 上下文窗口 | 200K tokens | 文件级 | 项目级 |
| 工具调用 | 内置文件系统/终端 | 建议补全 | 工具使用 |
| 代码理解 | 全仓库级 | 单文件/附近文件 | 多文件 |
| 交互方式 | 对话式 Agent | 编辑器内补全 | 对话式 |
| 自主执行 | 可以 | 不行 | 可以 |

---

## 二、代码实践（25 分钟）

### 8.1 Claude Tool Use API 示例

```python
import anthropic

client = anthropic.Anthropic()

# 定义工具
tools = [
    {
        "name": "get_weather",
        "description": "获取某个城市的天气",
        "input_schema": {
            "type": "object",
            "properties": {
                "city": {"type": "string", "description": "城市名称"}
            },
            "required": ["city"]
        }
    }
]

# 多轮对话
messages = [{"role": "user", "content": "北京今天天气怎么样？"}]

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=messages,
    tools=tools,
)

# 解析响应
for content in response.content:
    if content.type == "text":
        print(f"Claude 说: {content.text}")
    elif content.type == "tool_use":
        print(f"工具调用: {content.name}({content.input})")
        # 执行工具...
        result = "北京晴天，25°C"
        # 将结果返回给 Claude
        messages.append({"role": "assistant", "content": response.content})
        messages.append({
            "role": "user",
            "content": [{
                "type": "tool_result",
                "tool_use_id": content.id,
                "content": result
            }]
        })
```

### 8.2 构建简单的代码助手

```python
import subprocess
from pathlib import Path

class SimpleCodeAgent:
    """简单的代码助手，模拟 Claude Code 的核心能力"""
    
    def __init__(self):
        self.tools = {
            "read_file": self.read_file,
            "write_file": self.write_file,
            "run_command": self.run_command,
            "search_code": self.search_code,
        }
    
    def read_file(self, path: str) -> str:
        """读取文件"""
        try:
            return Path(path).read_text()
        except Exception as e:
            return f"Error: {e}"
    
    def write_file(self, path: str, content: str) -> str:
        """写入文件"""
        try:
            Path(path).parent.mkdir(parents=True, exist_ok=True)
            Path(path).write_text(content)
            return f"✅ 已写入 {path} ({len(content)} 字符)"
        except Exception as e:
            return f"Error: {e}"
    
    def run_command(self, cmd: str) -> str:
        """执行命令"""
        try:
            result = subprocess.run(cmd, shell=True, capture_output=True, text=True, timeout=30)
            output = result.stdout
            if result.stderr:
                output += f"\n[stderr] {result.stderr}"
            return output[:5000]  # 限制输出长度
        except Exception as e:
            return f"Error: {e}"
    
    def search_code(self, pattern: str, path: str = ".") -> str:
        """搜索代码"""
        try:
            result = subprocess.run(
                f"grep -rn '{pattern}' {path}",
                shell=True, capture_output=True, text=True, timeout=10
            )
            return result.stdout[:5000] or "未找到匹配"
        except Exception as e:
            return f"Error: {e}"
    
    def execute(self, tool: str, **kwargs) -> str:
        """执行工具调用"""
        if tool in self.tools:
            return self.tools[tool](**kwargs)
        return f"未知工具: {tool}"


# 使用示例
agent = SimpleCodeAgent()
print(agent.read_file("README.md")[:200])
print(agent.search_code("def main", path="/workspace"))
```

---

## 三、作业与思考题（10 分钟）

1. **理解题**：Claude Code 的"全仓库上下文"是什么意思？和 GitHub Copilot 的上下文有什么不同？

2. **分析题**：Constitutional AI 相比 RLHF 有什么优势？为什么 Anthropic 选择这条路？

3. **设计题**：如果要为一个 50 万行代码的 Python 项目构建代码助手，上下文窗口有限，你会怎么设计上下文管理策略？

---

## 四、扩展阅读

- 📄 **Constitutional AI: Harmlessness from AI Feedback** (Bai et al., 2022)
- 🌐 **Claude Code 官方文档**：https://docs.anthropic.com
- 🌐 **Anthropic Tool Use 教程**：https://docs.anthropic.com/claude/docs/tool-use
