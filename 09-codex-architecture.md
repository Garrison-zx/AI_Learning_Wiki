# 课程 9：OpenAI Codex 架构原理和应用（模型训练 + Harness 方案）

> **学习时长**：60 分钟
> **先修知识**：课程 1-8
> **创建时间**：2026-04-24

---

## 一、理论讲解（20 分钟）

### 9.1 Codex 的历史和定位

**Codex** 是 OpenAI 的代码专用模型，是 GPT-3 的代码微调版。

```
GPT-3 (2020) → Codex (2021) → GitHub Copilot (2021) → GPT-4 → Codex CLI (2025)
```

**Codex CLI（2025）**：OpenAI 新推出的开源 CLI 编程助手，可以直接在终端使用。

### 9.2 Codex 模型训练

**关键差异**：Codex 不只是 GPT 加代码数据，还有特殊的训练策略。

| 阶段 | 数据 | 目标 |
|------|------|------|
| 预训练 | GitHub 公开代码 + 自然语言 | 代码语言建模 |
| 微调 | 高质量代码指令对 | 代码生成和理解 |
| 对齐 | 人类反馈 + 代码审查标准 | 安全的代码输出 |

**代码数据的特殊处理**：
- 过滤低质量代码（语法错误、无注释）
- 保留代码-注释对（理解代码意图）
- 代码依赖关系建模（import 关系）

### 9.3 Codex CLI 架构

```
┌─────────────────────────────────────┐
│          Codex CLI (终端)            │
├─────────────────────────────────────┤
│  插件系统（Plugins）                  │
│  - 文件操作                            │
│  - 终端执行                            │
│  - MCP 工具集成                        │
├─────────────────────────────────────┤
│  沙箱执行（Sandbox）                   │
│  - Docker 容器                         │
│  - 资源限制                            │
│  - 网络隔离                            │
├─────────────────────────────────────┤
│  上下文管理                            │
│  - 文件树索引                          │
│  - git 忽略处理                        │
│  - 增量更新                            │
├─────────────────────────────────────┤
│  OpenAI API (GPT-4o / o3)           │
└─────────────────────────────────────┘
```

### 9.4 Codex CLI 的核心特性

**1. 权限控制**
```
--full-auto   → 自动执行所有操作
--dangerously-skip-permissions → 跳过确认
默认模式 → 每个操作都需要用户确认
```

**2. 插件系统**
```
用户可安装插件扩展能力：
- Superpowers：完整开发方法论
- 自定义工具集成
- MCP Server 连接
```

**3. 流式输出**
```
用户可以看到 AI 的每一步思考和操作
透明度高，随时可以中断
```

### 9.5 Codex vs Claude Code 对比

| 维度 | Codex CLI | Claude Code |
|------|-----------|-------------|
| 模型 | GPT-4o / o3 | Claude 3.5 Sonnet / Opus |
| 开源 | ✅ 核心开源 | ❌ 闭源 |
| 插件 | ✅ 插件市场 | 内置工具 |
| 沙箱 | Docker 隔离 | 内置安全机制 |
| 许可证 | Apache 2.0 | 商业产品 |
| 上下文 | 项目级 | 全仓库级 |

---

## 二、代码实践（25 分钟）

### 9.1 安装和使用 Codex CLI

```bash
# 安装 Codex CLI
npm install -g @openai/codex

# 基本使用
codex

# 指定目录
codex --work-dir /path/to/project

# 安装插件
# 在对话中输入 /plugins 进入插件管理
```

### 9.2 构建一个类 Codex 的代码 Agent

```python
import os
import subprocess
from pathlib import Path
from typing import Optional

class CodeAgent:
    """类 Codex 的代码 Agent"""
    
    def __init__(self, project_root: str = "."):
        self.project_root = Path(project_root)
        self.file_tree = self._build_file_tree()
    
    def _build_file_tree(self) -> str:
        """构建项目文件树"""
        tree = []
        for path in sorted(self.project_root.rglob("*")):
            if path.is_file():
                # 跳过隐藏文件和常见忽略
                rel = path.relative_to(self.project_root)
                if not any(p.startswith(".") for p in rel.parts):
                    indent = "  " * (len(rel.parts) - 1)
                    tree.append(f"{indent}{path.name}")
        return "\n".join(tree[:100])  # 限制 100 行
    
    def get_file_context(self, path: str) -> str:
        """获取文件内容 + 行号"""
        file_path = self.project_root / path
        if not file_path.exists():
            return f"File not found: {path}"
        
        content = file_path.read_text()
        lines = content.split("\n")
        
        # 带行号输出
        result = []
        for i, line in enumerate(lines, 1):
            result.append(f"{i:4d} | {line}")
        
        return "\n".join(result[:200])  # 限制 200 行
    
    def run_test(self, cmd: str) -> tuple[bool, str]:
        """运行测试并返回结果"""
        try:
            result = subprocess.run(
                cmd, shell=True, capture_output=True, text=True,
                cwd=self.project_root, timeout=60
            )
            success = result.returncode == 0
            return success, result.stdout + result.stderr
        except subprocess.TimeoutExpired:
            return False, "测试超时（>60秒）"
    
    def git_diff(self) -> str:
        """获取当前 git 变更"""
        try:
            result = subprocess.run(
                "git diff --stat",
                shell=True, capture_output=True, text=True,
                cwd=self.project_root, timeout=10
            )
            return result.stdout
        except Exception as e:
            return f"Git error: {e}"
    
    def summarize_context(self) -> dict:
        """汇总项目上下文（用于构造 Prompt）"""
        return {
            "project_root": str(self.project_root),
            "file_tree": self.file_tree,
            "total_files": sum(1 for _ in self.project_root.rglob("*.py")),
            "git_status": self.git_diff(),
        }


# 使用示例
agent = CodeAgent("/workspace/class_generation")
context = agent.summarize_context()
print(f"项目: {context['project_root']}")
print(f"Python 文件数: {context['total_files']}")
print(f"Git 变更:\n{context['git_status']}")

# 获取具体文件
print("\n=== 文件内容 ===")
print(agent.get_file_context("README.md")[:500])
```

### 9.3 代码审查 Agent

```python
class CodeReviewer:
    """自动代码审查 Agent"""
    
    REVIEW_CRITERIA = [
        "代码是否遵循 PEP 8 规范？",
        "是否有合适的类型注解？",
        "函数是否有文档字符串？",
        "错误处理是否充分？",
        "是否有冗余代码可以简化？",
        "命名是否清晰有描述性？",
    ]
    
    def review_file(self, file_path: str) -> list[dict]:
        """审查一个 Python 文件"""
        content = Path(file_path).read_text()
        issues = []
        
        # 规则1：检查 docstring
        import ast
        try:
            tree = ast.parse(content)
            for node in ast.walk(tree):
                if isinstance(node, (ast.FunctionDef, ast.ClassDef)):
                    if not ast.get_docstring(node):
                        issues.append({
                            "type": "missing_docstring",
                            "line": node.lineno,
                            "name": node.name,
                            "severity": "warning",
                            "message": f"缺少文档字符串: {node.name}",
                        })
        except SyntaxError as e:
            issues.append({
                "type": "syntax_error",
                "line": e.lineno,
                "message": f"语法错误: {e.msg}",
                "severity": "error",
            })
        
        # 规则2：检查长函数（>50行）
        lines = content.split("\n")
        for i, line in enumerate(lines, 1):
            if len(line) > 120:
                issues.append({
                    "type": "line_too_long",
                    "line": i,
                    "message": f"行长度 {len(line)} > 120",
                    "severity": "warning",
                })
        
        return issues
    
    def generate_report(self, issues: list[dict]) -> str:
        """生成审查报告"""
        if not issues:
            return "✅ 代码审查通过，未发现问题。"
        
        report = [f"🔍 代码审查报告（{len(issues)} 个问题）\n"]
        errors = [i for i in issues if i["severity"] == "error"]
        warnings = [i for i in issues if i["severity"] == "warning"]
        
        if errors:
            report.append(f"❌ 错误：{len(errors)} 个")
            for e in errors:
                report.append(f"   第 {e['line']} 行: {e['message']}")
        
        if warnings:
            report.append(f"\n⚠️ 警告：{len(warnings)} 个")
            for w in warnings[:10]:  # 最多显示 10 个
                report.append(f"   第 {w['line']} 行: {w['message']}")
        
        return "\n".join(report)


# 使用
reviewer = CodeReviewer()
issues = reviewer.review_file("/workspace/teaching-plan/07-agent-principles.md")
# print(reviewer.generate_report(issues))
```

---

## 三、作业与思考题（10 分钟）

1. **对比题**：Codex 和 Claude Code 在架构上的核心区别是什么？各自的优缺点？

2. **理解题**：为什么 Codex CLI 使用 Docker 沙箱？这解决了什么安全问题？

3. **设计题**：如果要设计一个"代码迁移 Agent"（将 Python 2 代码迁移到 Python 3），你会定义哪些工具？执行流程是怎样的？

---

## 四、扩展阅读

- 🌐 **OpenAI Codex CLI**：https://github.com/openai/codex
- 🌐 **OpenAI Plugins 文档**：https://platform.openai.com
- 📄 **Evaluating Large Language Models Trained on Code** (Chen et al., 2021) — Codex 原始论文
- 🌐 **HumanEval 基准测试**：代码生成能力评估标准
