# Claude Code 完整功能使用指南

基于 DeepWiki 获取的 affaan-m/everything-claude-code 仓库文档

生成时间: 2026-03-03

---

## 目录

1. [核心概念](#核心概念)
2. [安装与配置](#安装与配置)
3. [Agents (代理)](#agents-代理)
4. [Skills (技能)](#skills-技能)
5. [Commands (命令)](#commands-命令)
6. [Hooks (钩子)](#hooks-钩子)
7. [Rules (规则)](#rules-规则)
8. [持续学习系统](#持续学习系统)
9. [故障排查](#故障排查)

---

## 核心概念

### 什么是 Everything Claude Code?

这是一个 Claude Code 插件,提供了完整的开发工作流自动化系统,包括:

- **Agents**: 专门化的 AI 子代理(如代码审查员、测试驱动开发指导)
- **Skills**: 可重用的工作流知识库(如 TDD 流程、Go 语言模式)
- **Commands**: 快速可执行的斜杠命令(如 `/plan`, `/tdd`, `/verify`)
- **Hooks**: 事件驱动的自动化脚本(如自动格式化、安全检查)
- **Rules**: 始终遵循的准则(如代码风格、安全规范)
- **持续学习**: 自动从会话中提取模式并生成新技能

### 架构图

```
用户输入 (/command)
    ↓
Command 加载
    ↓
委托给 Agent
    ↓
Agent 使用 Skills
    ↓
执行工具 (Read, Write, Edit, Bash)
    ↓
Hooks 触发 (PreToolUse/PostToolUse)
    ↓
Rules 验证
    ↓
结果返回
```

---

## 安装与配置

### 最低要求

- Claude Code CLI v2.1.0+
- Node.js (用于 hooks)
- Git

### 通过插件市场安装

```bash
# 1. 添加市场
/plugin marketplace add affaan-m/everything-claude-code

# 2. 安装插件
/plugin install everything-claude-code@everything-claude-code

# 3. 手动安装 Rules (插件系统不支持分发 rules)
git clone https://github.com/affaan-m/everything-claude-code.git
cp -r everything-claude-code/rules/* ~/.claude/rules/
```

### 验证安装

```bash
# 检查插件
/plugin list

# 测试命令
/plan

# 检查 rules
ls ~/.claude/rules/
```

---

## Agents (代理)

### 什么是 Agent?

Agent 是专门化的 AI 子代理,具有:
- 特定的工具权限
- 专门的知识领域
- 明确的职责范围

### 内置 Agents

| Agent | 用途 | 工具权限 |
|-------|------|---------|
| `planner` | 功能分解和规划 | Read, Grep, Glob |
| `architect` | 架构设计 | Read, Grep, Glob |
| `tdd-guide` | 测试驱动开发 | Read, Write, Edit, Bash |
| `code-reviewer` | 代码审查 | Read, Grep, Glob |
| `build-error-resolver` | 构建错误修复 | Read, Write, Edit, Bash |

### Agent 结构

```markdown
---
name: python-reviewer
description: Python 代码审查专家
tools: Read, Grep, Glob
model: sonnet
skills: [python-patterns, security-review]
---

你是 Python 代码审查专家...

## 审查清单
- PEP 8 合规性
- 类型提示
- 文档字符串
...
```

### 使用 Agent

**方式 1: 通过 Command 委托**

```bash
/code-review
# 自动委托给 code-reviewer agent
```

**方式 2: 在 CLAUDE.md 中引用**

```markdown
对于 Python 代码,委托给 python-reviewer agent 进行审查。
```

### 创建自定义 Agent

```bash
# 1. 创建文件
cat > ~/.claude/agents/rust-analyzer.md << 'EOF'
---
name: rust-analyzer
description: Rust 代码分析专家
tools: Read, Grep, Bash
model: sonnet
---

你是 Rust 代码分析专家,专注于:
- 所有权和借用检查
- 生命周期分析
- Unsafe 代码审查
...
EOF

# 2. 在命令或 CLAUDE.md 中引用
```

---

## Skills (技能)

### 什么是 Skill?

Skill 是可重用的工作流知识库,包含:
- 模式和最佳实践
- 具体代码示例
- 决策树和流程图

### 内置 Skills

| Skill | 用途 |
|-------|------|
| `tdd-workflow` | TDD 红-绿-重构循环 |
| `golang-patterns` | Go 语言惯用模式 |
| `security-review` | 安全审查清单 |
| `continuous-learning` | 模式提取方法论 |

### Skill 结构

```markdown
# TDD Workflow

## 何时使用
- 实现新功能
- 修复 bug
- 重构代码

## 工作流程

### Phase 1: RED (编写失败测试)
1. 编写测试用例
2. 运行测试,确认失败
3. 失败信息要有意义

### Phase 2: GREEN (最小实现)
1. 编写最少代码使测试通过
2. 不考虑优化
3. 运行测试,确认通过

### Phase 3: REFACTOR (重构)
1. 改进代码质量
2. 消除重复
3. 保持测试通过

## 示例
...
```

### 使用 Skill

**Agent 引用 Skill:**

```markdown
---
name: tdd-guide
skills: [tdd-workflow, golang-testing]
---

遵循 tdd-workflow skill 中的流程...
```

**Command 引用 Skill:**

```markdown
# TDD Command

加载 tdd-workflow skill 并执行...
```

### 创建自定义 Skill

```bash
# 单文件 skill
cat > ~/.claude/skills/django-patterns.md << 'EOF'
# Django 最佳实践

## 模型设计
- 使用 Fat Models, Thin Views
- 自定义 Manager 封装查询逻辑
...
EOF

# 多文件 skill
mkdir -p ~/.claude/skills/ml-pipeline/
cat > ~/.claude/skills/ml-pipeline/SKILL.md << 'EOF'
# ML Pipeline Skill
...
EOF
```

---

## Commands (命令)

### 什么是 Command?

Command 是快速可执行的斜杠命令,用于:
- 启动特定工作流
- 编排多个 Agent
- 执行自动化任务

### 内置 Commands

| Command | 功能 |
|---------|------|
| `/plan` | 功能分解和规划 |
| `/tdd` | 启动 TDD 工作流 |
| `/code-review` | 代码审查 |
| `/verify` | 完整验证循环 |
| `/checkpoint` | 保存当前状态 |

### Command 结构

```markdown
---
description: 启动测试驱动开发工作流
---

# TDD Command

你将遵循测试驱动开发方法论。

1. 加载 `tdd-workflow` skill
2. 委托给 `tdd-guide` agent
3. 遵循 RED-GREEN-REFACTOR 循环

如果使用 Go 代码,使用 `golang-testing` skill。
```

### 创建自定义 Command

```bash
cat > ~/.claude/commands/deploy.md << 'EOF'
---
description: 部署应用到生产环境
---

# Deploy Command

执行部署流程:

1. 运行 /verify 确保所有测试通过
2. 检查 git 状态,确保工作目录干净
3. 构建生产版本
4. 运行部署脚本
5. 验证部署成功
EOF
```

---

## Hooks (钩子)

### 什么是 Hook?

Hook 是事件驱动的自动化脚本,在特定时机触发:
- **PreToolUse**: 工具执行前
- **PostToolUse**: 工具执行后
- **SessionStart**: 会话开始
- **SessionEnd**: 会话结束
- **Stop**: Agent 响应后

### Hook 配置

Hooks 在 `hooks/hooks.json` 中定义:

```json
{
  "PreToolUse": [{
    "matcher": "tool == \"Bash\" && tool_input.command matches \"npm run dev\"",
    "hooks": [{
      "type": "command",
      "command": "node -e \"if(!process.env.TMUX){console.error('[Hook] Dev server must run in tmux');process.exit(1)}\""
    }]
  }],
  "PostToolUse": [{
    "matcher": "tool == \"Write\" && tool_input.file_path matches \"\\.(ts|tsx|js|jsx)$\"",
    "hooks": [{
      "type": "command",
      "command": "prettier --write \"$file_path\""
    }]
  }]
}
```

### 内置 Hooks

| Hook | 触发时机 | 功能 |
|------|---------|------|
| Dev Server Check | PreToolUse (npm run dev) | 确保在 tmux 中运行 |
| Prettier Format | PostToolUse (Write .ts/.js) | 自动格式化代码 |
| Console.log Check | Stop | 检查是否有 console.log |
| Session Save | SessionEnd | 保存会话状态 |
| Suggest Compact | PostToolUse (每 50 次工具使用) | 建议压缩上下文 |

### Matcher 语法

```javascript
// 工具类型匹配
tool == "Edit"

// 文件路径匹配 (正则)
tool_input.file_path matches "\\.ts$"

// 命令匹配
tool_input.command matches "npm run dev"

// 逻辑组合
tool == "Edit" && tool_input.file_path matches "\\.(ts|tsx)$"

// 事件类型
event == "PostToolUse"
```

### 创建自定义 Hook

在 `~/.claude/settings.json` 中添加:

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "tool == \"Write\" && tool_input.file_path matches \"\\.py$\"",
      "hooks": [{
        "type": "command",
        "command": "black \"$file_path\""
      }],
      "description": "自动格式化 Python 文件"
    }]
  }
}
```

---

## Rules (规则)

### 什么是 Rule?

Rule 是始终遵循的准则,作为系统提示加载到每个会话。

### 内置 Rules

| Rule | 用途 |
|------|------|
| `security.md` | 安全最佳实践 |
| `coding-style.md` | 代码风格规范 |
| `testing.md` | 测试要求 |
| `git-workflow.md` | Git 工作流 |
| `agents.md` | Agent 使用指南 |
| `performance.md` | 性能优化 |

### Rule 结构

```markdown
# Security Rules

## 永远不要
- 硬编码凭证
- 使用不安全的随机数生成器
- 忽略 SQL 注入风险

## 始终
- 验证用户输入
- 使用参数化查询
- 实施最小权限原则

## 密码处理
- 使用 bcrypt/argon2
- 最小长度 12 字符
- 加盐哈希
```

### 安装 Rules

```bash
# Rules 必须手动安装 (插件系统不支持)
git clone https://github.com/affaan-m/everything-claude-code.git
cp -r everything-claude-code/rules/* ~/.claude/rules/
```

### 创建自定义 Rule

```bash
cat > ~/.claude/rules/api-design.md << 'EOF'
# API Design Rules

## RESTful 原则
- 使用名词表示资源
- HTTP 方法表示操作
- 返回适当的状态码

## 版本控制
- URL 版本: /api/v1/users
- 向后兼容
...
EOF
```

---

## 持续学习系统

### 概述

持续学习系统自动从会话中提取模式并生成新的 Skills/Commands/Agents。

有两个版本:
- **v1**: 基于 Stop hook,可靠性 50-80%
- **v2**: 基于 PreToolUse/PostToolUse hooks,可靠性 100%

### v2 架构 (推荐)

```
工具使用
  ↓
PreToolUse/PostToolUse Hook
  ↓
observe.sh 记录到 observations.jsonl
  ↓
Observer Agent 分析模式
  ↓
生成 Instincts (本能)
  ↓
/evolve 命令聚类 Instincts
  ↓
生成 Skills/Commands/Agents
```

### 配置 v2

```json
// ~/.claude/settings.json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/hooks/observe.sh pre"
      }]
    }],
    "PostToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/hooks/observe.sh post"
      }]
    }]
  }
}
```

### 使用持续学习

```bash
# 1. 正常工作,系统自动记录观察
# observations 保存在 ~/.claude/homunculus/observations.jsonl

# 2. 查看学到的本能
/instinct-status

# 3. 将本能演化为 Skills/Commands
/evolve

# 4. 生成的内容在
ls ~/.claude/homunculus/evolved/skills/
ls ~/.claude/homunculus/evolved/commands/
ls ~/.claude/homunculus/evolved/agents/
```

### Instinct 结构

```markdown
---
id: prefer-table-driven-tests
trigger: Writing Go test functions
confidence: 0.7
domain: testing
source: session-observation
---

# Pattern: Table-Driven Tests in Go

## Context
When writing test functions in Go projects

## Observation
User consistently refactors multiple test cases into table-driven format

## Recommendation
Use table-driven test pattern:
```go
tests := []struct {
    name string
    input string
    want string
}{
    {"case1", "input1", "output1"},
    {"case2", "input2", "output2"},
}
for _, tt := range tests {
    t.Run(tt.name, func(t *testing.T) {
        got := Function(tt.input)
        if got != tt.want {
            t.Errorf("got %v, want %v", got, tt.want)
        }
    })
}
```

## Evidence
- Observed in 5 sessions
- User corrected inline tests to table format 3 times
```

---

## 故障排查

### 上下文窗口问题

**症状**: 上下文窗口 < 100k tokens

**原因**: 启用了太多 MCP 服务器或插件

**解决方案**:

```bash
# 1. 检查 MCP 数量
/mcp

# 2. 禁用不需要的 MCP
# 编辑 ~/.claude.json
{
  "projects": {
    "disabledMcpServers": [
      "firecrawl",
      "railway",
      "vercel"
    ]
  }
}

# 3. 目标: < 6 个 MCP, < 80 个工具
```

### Hook 执行失败

**症状**: Hook 不触发或报错

**诊断**:

```bash
# 1. 检查 hooks.json 语法
cat hooks/hooks.json | jq .

# 2. 测试 hook 脚本
node scripts/hooks/session-start.js

# 3. 检查权限
ls -la scripts/hooks/

# 4. 查看日志
grep '[Hook]' ~/.claude/logs/*.log
```

### 包管理器检测错误

**症状**: 使用了错误的包管理器

**解决方案**:

```bash
# 方式 1: 环境变量
export CLAUDE_PACKAGE_MANAGER=pnpm

# 方式 2: 项目配置
node scripts/setup-package-manager.js --project pnpm

# 方式 3: 全局配置
node scripts/setup-package-manager.js --global pnpm

# 或使用命令
/setup-pm
```

### 插件安装问题

**症状**: 插件安装后命令不可用

**检查清单**:

```bash
# 1. 验证插件已安装
/plugin list

# 2. 检查 Claude Code 版本
claude --version  # 需要 >= v2.1.0

# 3. 检查 settings.json
cat ~/.claude/settings.json | grep enabledPlugins

# 4. 手动安装 rules
cp -r everything-claude-code/rules/* ~/.claude/rules/
```

---

## 最佳实践

### 1. 项目初始化

```bash
# 1. 创建 .claude/CLAUDE.md
cat > .claude/CLAUDE.md << 'EOF'
# 项目上下文

这是一个 [项目类型] 项目。

## 技术栈
- 语言: [语言]
- 框架: [框架]
- 包管理器: [npm/pnpm/yarn]

## 开发流程
- 使用 TDD
- 代码审查必须通过
- 80% 测试覆盖率

## 委托规则
- Python 代码 → python-reviewer
- Go 代码 → go-reviewer
- 架构设计 → architect
EOF

# 2. 设置包管理器
/setup-pm

# 3. 配置 MCP (如需要)
# 编辑 ~/.claude.json
```

### 2. 工作流示例

```bash
# 新功能开发
/plan  # 分解任务
/tdd   # TDD 实现
/code-review  # 代码审查
/verify  # 验证构建和测试

# Bug 修复
/tdd   # 先写失败测试
# 修复代码
/verify  # 验证修复

# 重构
/checkpoint  # 保存当前状态
# 重构代码
/verify  # 确保没有破坏功能
```

### 3. 上下文管理

```bash
# 定期压缩上下文
/compact

# 检查上下文使用
/statusline  # 查看 ctx:XX%

# 禁用不需要的 MCP
# 目标: < 6 个 MCP, < 80 个工具
```

### 4. 持续学习

```bash
# 每周检查学到的模式
/instinct-status

# 每月演化一次
/evolve

# 审查生成的 skills
ls ~/.claude/homunculus/evolved/skills/
```

---

## 参考资源

- **GitHub**: https://github.com/affaan-m/everything-claude-code
- **作者**: [@affaanmustafa](https://x.com/affaanmustafa)
- **Claude Code 文档**: https://docs.anthropic.com/claude/docs

---

## 附录: 常用命令速查

```bash
# 插件管理
/plugin list
/plugin install everything-claude-code@everything-claude-code
/plugin uninstall everything-claude-code@everything-claude-code

# 工作流命令
/plan          # 规划
/tdd           # TDD 开发
/code-review   # 代码审查
/verify        # 验证
/checkpoint    # 保存状态

# 系统命令
/compact       # 压缩上下文
/mcp           # 查看 MCP
/setup-pm      # 配置包管理器

# 持续学习
/instinct-status  # 查看本能
/evolve           # 演化技能
/skill-create     # 从 git 历史创建技能
```

---

生成时间: 2026-03-03
来源: DeepWiki - affaan-m/everything-claude-code
