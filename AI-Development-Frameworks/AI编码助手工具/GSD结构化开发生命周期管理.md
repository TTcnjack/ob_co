# GSD - 结构化开发生命周期管理

**GitHub**: https://github.com/glittercowboy/get-shit-done  
**版本**: Latest  
**类型**: Meta-prompting 系统  
**平台**: Claude Code, OpenCode, Gemini CLI, Codex

生成时间: 2026-03-04

---

## 核心概念

GSD (Get Shit Done) 是一个轻量级但强大的元提示、上下文工程和规范驱动开发系统，专为 Claude Code、OpenCode、Gemini CLI 和 Codex 设计。

### 设计理念

- **解决上下文腐化**: 防止 Claude 填满上下文窗口时的质量下降
- **规范驱动开发**: 清晰的需求带来一致的结果
- **多 Agent 编排**: 专业化 Agent 并行工作，主会话保持清爽
- **原子化提交**: 每个任务独立提交，可追溯可回滚

### 核心问题

Vibecoding（描述想法让 AI 生成代码）通常产生不一致的垃圾代码。GSD 通过上下文工程层使 Claude Code 变得可靠：描述想法 → 系统提取所需信息 → Claude Code 开始工作。

---

## 核心功能

### 1. 阶段化开发生命周期

**工作流程**:
```
初始化项目 → 讨论阶段 → 规划阶段 → 执行阶段 → 验证工作 → 完成里程碑
```

**六步核心循环**:
1. **new-project**: 问题 → 研究 → 需求 → 路线图
2. **discuss-phase**: 捕获实现决策（布局、交互、错误处理等）
3. **plan-phase**: 研究 → 创建计划 → 验证计划
4. **execute-phase**: 波次执行，并行运行独立计划
5. **verify-work**: 手动用户验收测试
6. **complete-milestone**: 归档里程碑，标记发布

### 2. Agent 编排系统

**多 Agent 模式**: 薄编排器 + 专业化 Agent

| 阶段 | 编排器职责 | Agent 职责 |
|------|-----------|-----------|
| 研究 | 协调，呈现发现 | 4 个并行研究员调查技术栈、功能、架构、陷阱 |
| 规划 | 验证，管理迭代 | 规划器创建计划，检查器验证，循环直到通过 |
| 执行 | 分组为波次，跟踪进度 | 执行器并行实现，每个拥有全新 200k 上下文 |
| 验证 | 呈现结果，路由下一步 | 验证器检查代码库，调试器诊断失败 |

**结果**: 运行整个阶段（深度研究、多个计划创建和验证、数千行代码并行编写、自动验证）后，主上下文窗口保持在 30-40%。工作在全新子 Agent 上下文中进行。

### 3. 波次执行（Wave Execution）

**工作原理**:
```
┌─────────────────────────────────────────────────────────────────────┐
│  阶段执行                                                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  波次 1 (并行)          波次 2 (并行)          波次 3                │
│  ┌─────────┐ ┌─────────┐    ┌─────────┐ ┌─────────┐    ┌─────────┐ │
│  │ 计划 01 │ │ 计划 02 │ →  │ 计划 03 │ │ 计划 04 │ →  │ 计划 05 │ │
│  │         │ │         │    │         │ │         │    │         │ │
│  │ 用户    │ │ 产品    │    │ 订单    │ │ 购物车  │    │ 结账    │ │
│  │ 模型    │ │ 模型    │    │ API     │ │ API     │    │ UI      │ │
│  └─────────┘ └─────────┘    └─────────┘ └─────────┘    └─────────┘ │
│       │           │              ↑           ↑              ↑       │
│       └───────────┴──────────────┴───────────┘              │       │
│              依赖关系: 计划 03 需要计划 01                   │       │
│                      计划 04 需要计划 02                     │       │
│                      计划 05 需要计划 03 + 04                │       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

**波次优势**:
- 独立计划 → 同一波次 → 并行运行
- 依赖计划 → 后续波次 → 等待依赖
- 文件冲突 → 顺序计划或同一计划

### 4. 上下文工程

**上下文文件系统**:

| 文件 | 作用 |
|------|------|
| `PROJECT.md` | 项目愿景，始终加载 |
| `research/` | 生态系统知识（技术栈、功能、架构、陷阱） |
| `REQUIREMENTS.md` | 范围化的 v1/v2 需求，带阶段可追溯性 |
| `ROADMAP.md` | 目标方向，已完成内容 |
| `STATE.md` | 决策、阻塞、位置 — 跨会话记忆 |
| `PLAN.md` | 原子任务，XML 结构，验证步骤 |
| `SUMMARY.md` | 发生了什么，改变了什么，提交到历史 |
| `todos/` | 捕获的想法和任务，供后续工作 |

**大小限制**: 基于 Claude 质量下降点。保持在限制内，获得一致的卓越表现。

### 5. XML 提示格式化

**每个计划都是结构化 XML**:

```xml
<task type="auto">
  <name>创建登录端点</name>
  <files>src/app/api/auth/login/route.ts</files>
  <action>
    使用 jose 处理 JWT（不是 jsonwebtoken - CommonJS 问题）。
    根据用户表验证凭据。
    成功时返回 httpOnly cookie。
  </action>
  <verify>curl -X POST localhost:3000/api/auth/login 返回 200 + Set-Cookie</verify>
  <done>有效凭据返回 cookie，无效返回 401</done>
</task>
```

**优势**: 精确指令，无需猜测，内置验证。

### 6. 原子化 Git 提交

**每个任务完成后立即提交**:

```bash
abc123f docs(08-02): complete user registration plan
def456g feat(08-02): add email confirmation flow
hij789k feat(08-02): implement password hashing
lmn012o feat(08-02): create registration endpoint
```

**优势**:
- Git bisect 找到确切失败任务
- 每个任务独立可回滚
- 为未来会话中的 Claude 提供清晰历史
- AI 自动化工作流中更好的可观察性

### 7. 状态管理

**持久化状态跟踪**:
- 跨会话保持上下文
- 决策和阻塞记录
- 自动会话管理
- 会话历史记录

**自动重置触发**:
- 手动中断（Ctrl+C）
- 项目完成
- 会话过期

### 8. 快速模式（Quick Mode）

**用于不需要完整规划的临时任务**:

```
/gsd:quick
```

**特点**:
- 相同 Agent（规划器 + 执行器），相同质量
- 跳过可选步骤（无研究、无计划检查器、无验证器）
- 独立跟踪（位于 `.planning/quick/`，不在阶段中）

**适用场景**: 错误修复、小功能、配置更改、一次性任务。

---

## 安装和设置

### 安装

```bash
npx get-shit-done-cc@latest
```

**安装器提示选择**:
1. **运行时** — Claude Code, OpenCode, Gemini, Codex, 或全部
2. **位置** — 全局（所有项目）或本地（仅当前项目）

**验证安装**:
- Claude Code / Gemini: `/gsd:help`
- OpenCode: `/gsd-help`
- Codex: `$gsd-help`

### 非交互式安装

```bash
# Claude Code
npx get-shit-done-cc --claude --global   # 安装到 ~/.claude/
npx get-shit-done-cc --claude --local    # 安装到 ./.claude/

# OpenCode
npx get-shit-done-cc --opencode --global # 安装到 ~/.config/opencode/

# Gemini CLI
npx get-shit-done-cc --gemini --global   # 安装到 ~/.gemini/

# Codex
npx get-shit-done-cc --codex --global    # 安装到 ~/.codex/
npx get-shit-done-cc --codex --local     # 安装到 ./.codex/

# 所有运行时
npx get-shit-done-cc --all --global      # 安装到所有目录
```

### 更新

```bash
npx get-shit-done-cc@latest
```

### 推荐：跳过权限模式

GSD 设计用于无摩擦自动化。运行 Claude Code 时使用：

```bash
claude --dangerously-skip-permissions
```

**替代方案**: 在项目的 `.claude/settings.json` 中添加：

```json
{
  "permissions": {
    "allow": [
      "Bash(date:*)",
      "Bash(echo:*)",
      "Bash(cat:*)",
      "Bash(ls:*)",
      "Bash(mkdir:*)",
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Bash(git status:*)",
      "Bash(git log:*)",
      "Bash(git diff:*)",
      "Bash(git tag:*)"
    ]
  }
}
```

---

## 命令参考

### 核心工作流

```bash
/gsd:new-project [--auto]           # 完整初始化：问题 → 研究 → 需求 → 路线图
/gsd:discuss-phase [N] [--auto]     # 在规划前捕获实现决策
/gsd:plan-phase [N] [--auto]        # 研究 + 规划 + 验证阶段
/gsd:execute-phase <N>              # 并行波次执行所有计划，完成时验证
/gsd:verify-work [N]                # 手动用户验收测试
/gsd:audit-milestone                # 验证里程碑是否达到完成定义
/gsd:complete-milestone             # 归档里程碑，标记发布
/gsd:new-milestone [name]           # 开始下一版本：问题 → 研究 → 需求 → 路线图
```

### 导航

```bash
/gsd:progress                       # 我在哪里？下一步是什么？
/gsd:help                           # 显示所有命令和使用指南
/gsd:update                         # 更新 GSD，预览变更日志
/gsd:join-discord                   # 加入 GSD Discord 社区
```

### 现有代码库

```bash
/gsd:map-codebase                   # 在 new-project 前分析现有代码库
```

### 阶段管理

```bash
/gsd:add-phase                      # 向路线图追加阶段
/gsd:insert-phase [N]               # 在阶段之间插入紧急工作
/gsd:remove-phase [N]               # 删除未来阶段，重新编号
/gsd:list-phase-assumptions [N]     # 在规划前查看 Claude 的预期方法
/gsd:plan-milestone-gaps            # 创建阶段以弥合审计中的差距
```

### 会话管理

```bash
/gsd:pause-work                     # 在阶段中停止时创建交接
/gsd:resume-work                    # 从上次会话恢复
```

### 实用工具

```bash
/gsd:settings                       # 配置模型配置文件和工作流 Agent
/gsd:set-profile <profile>          # 切换模型配置文件（quality/balanced/budget）
/gsd:add-todo [desc]                # 捕获想法供以后使用
/gsd:check-todos                    # 列出待办事项
/gsd:debug [desc]                   # 系统化调试，持久化状态
/gsd:quick [--full] [--discuss]     # 执行临时任务，带 GSD 保证
/gsd:health [--repair]              # 验证 .planning/ 目录完整性
```

**标志说明**:
- `--auto`: 自动批准步骤，无需确认
- `--full`: 在 quick 模式中添加计划检查和验证
- `--discuss`: 在 quick 模式中先收集上下文
- `--skip-research`: 跳过阶段研究
- `--skip-verify`: 跳过计划验证
- `--repair`: 自动修复健康检查问题

---

## 项目结构

```
my-project/
├── .planning/              # GSD 配置和状态
│   ├── config.json         # 项目配置
│   ├── PROJECT.md          # 项目愿景
│   ├── REQUIREMENTS.md     # 范围化需求
│   ├── ROADMAP.md          # 阶段路线图
│   ├── STATE.md            # 当前状态和决策
│   ├── research/           # 领域研究
│   ├── 01-CONTEXT.md       # 阶段 1 实现决策
│   ├── 01-RESEARCH.md      # 阶段 1 研究
│   ├── 01-1-PLAN.md        # 阶段 1 计划 1
│   ├── 01-1-SUMMARY.md     # 阶段 1 计划 1 摘要
│   ├── 01-VERIFICATION.md  # 阶段 1 验证
│   ├── 01-UAT.md           # 阶段 1 用户验收测试
│   ├── quick/              # 快速模式任务
│   └── todos/              # 待办事项
├── .claude/                # Claude Code 配置
│   └── settings.json       # 权限和设置
└── src/                    # 源代码
```

### 关键文件

| 文件 | 自动生成? | 你应该... |
|------|-----------|----------|
| `PROJECT.md` | 是 | 审查并自定义项目目标 |
| `REQUIREMENTS.md` | 是 | 验证范围和需求 |
| `ROADMAP.md` | 是 | 调整阶段和优先级 |
| `{N}-CONTEXT.md` | 是 | 添加实现偏好 |
| `{N}-PLAN.md` | 是 | 很少编辑（自动维护） |
| `config.json` | 是 | 配置模型和工作流 |

---

## 配置

### 配置文件位置

GSD 将项目设置存储在 `.planning/config.json`。在 `/gsd:new-project` 期间配置或稍后使用 `/gsd:settings` 更新。

### 核心设置

```json
{
  "mode": "interactive",           // yolo | interactive
  "granularity": "standard",       // coarse | standard | fine
  "model_profile": "balanced"      // quality | balanced | budget
}
```

| 设置 | 选项 | 默认值 | 控制内容 |
|------|------|--------|----------|
| `mode` | `yolo`, `interactive` | `interactive` | 自动批准 vs 每步确认 |
| `granularity` | `coarse`, `standard`, `fine` | `standard` | 阶段粒度 — 范围切片的细度（阶段 × 计划） |
| `model_profile` | `quality`, `balanced`, `budget` | `balanced` | 模型质量 vs token 花费 |

### 模型配置文件

控制每个 Agent 使用哪个 Claude 模型。平衡质量与 token 花费。

| 配置文件 | 规划 | 执行 | 验证 |
|---------|------|------|------|
| `quality` | Opus | Opus | Sonnet |
| `balanced` (默认) | Opus | Sonnet | Sonnet |
| `budget` | Sonnet | Sonnet | Haiku |

**切换配置文件**:
```
/gsd:set-profile budget
```

### 工作流 Agent

这些在规划/执行期间生成额外的 Agent。它们提高质量但增加 token 和时间。

```json
{
  "workflow": {
    "research": true,           // 在规划每个阶段前研究领域
    "plan_check": true,         // 在执行前验证计划是否达到阶段目标
    "verifier": true,           // 在执行后确认必备项已交付
    "auto_advance": false       // 自动链式 discuss → plan → execute
  }
}
```

**每次调用覆盖**:
- `/gsd:plan-phase --skip-research`
- `/gsd:plan-phase --skip-verify`

### 执行设置

```json
{
  "parallelization": {
    "enabled": true             // 同时运行独立计划
  },
  "planning": {
    "commit_docs": true         // 在 git 中跟踪 .planning/
  }
}
```

### Git 分支策略

```json
{
  "git": {
    "branching_strategy": "none",                    // none | phase | milestone
    "phase_branch_template": "gsd/phase-{phase}-{slug}",
    "milestone_branch_template": "gsd/{milestone}-{slug}"
  }
}
```

**策略**:
- **`none`** — 提交到当前分支（默认 GSD 行为）
- **`phase`** — 每个阶段创建一个分支，阶段完成时合并
- **`milestone`** — 为整个里程碑创建一个分支，完成时合并

在里程碑完成时，GSD 提供 squash merge（推荐）或带历史的 merge。

---

## 使用示例

### 基本工作流

```bash
# 1. 初始化项目
/gsd:new-project

# 2. 讨论阶段 1（捕获实现偏好）
/gsd:discuss-phase 1

# 3. 规划阶段 1
/gsd:plan-phase 1

# 4. 执行阶段 1
/gsd:execute-phase 1

# 5. 验证工作
/gsd:verify-work 1

# 6. 重复阶段 2-N
/gsd:discuss-phase 2
/gsd:plan-phase 2
/gsd:execute-phase 2
/gsd:verify-work 2

# 7. 完成里程碑
/gsd:complete-milestone

# 8. 开始下一个里程碑
/gsd:new-milestone "v2.0"
```

### 现有代码库工作流

```bash
# 1. 映射现有代码库
/gsd:map-codebase

# 2. 初始化项目（问题将聚焦于你要添加的内容）
/gsd:new-project

# 3. 继续正常工作流
/gsd:discuss-phase 1
/gsd:plan-phase 1
/gsd:execute-phase 1
```

### 快速模式

```bash
# 基本快速任务
/gsd:quick
> 你想做什么？"在设置中添加深色模式切换"

# 带完整验证的快速任务
/gsd:quick --full

# 先讨论的快速任务
/gsd:quick --discuss
```

### 自动化模式

```bash
# 自动批准所有步骤
/gsd:new-project --auto
/gsd:discuss-phase 1 --auto
/gsd:plan-phase 1 --auto
/gsd:execute-phase 1
```

### 跳过可选步骤

```bash
# 跳过研究
/gsd:plan-phase 1 --skip-research

# 跳过计划验证
/gsd:plan-phase 1 --skip-verify

# 两者都跳过
/gsd:plan-phase 1 --skip-research --skip-verify
```

---

## 最佳实践

### 1. 编写有效的项目描述

- **具体明确**: 清晰的需求带来更好的结果
- **包含约束**: 定义技术栈、性能要求、限制
- **设置边界**: 明确范围内外的内容
- **提供示例**: 展示预期的输入/输出

### 2. 使用讨论阶段

- **不要跳过 discuss-phase**: 这是塑造实现的地方
- **深入细节**: 系统会识别灰色区域并询问
- **锁定决策**: 你的偏好直接进入研究和规划

### 3. 监控进度

- 使用 `/gsd:progress` 检查当前位置
- 检查 `.planning/STATE.md` 了解决策和阻塞
- 审查 `SUMMARY.md` 文件了解已完成的工作
- 监控 Git 历史了解原子提交

### 4. 阶段管理

- 保持阶段小而专注
- 使用 `/gsd:add-phase` 添加新工作
- 使用 `/gsd:insert-phase` 处理紧急任务
- 使用 `/gsd:remove-phase` 删除不需要的阶段

### 5. 资源管理

- 使用 `balanced` 配置文件进行日常工作
- 使用 `quality` 配置文件处理关键阶段
- 使用 `budget` 配置文件进行实验
- 禁用不需要的工作流 Agent 以节省 token

### 6. 验证工作

- 不要跳过 `/gsd:verify-work`
- 实际测试功能，不要只是查看代码
- 报告问题时要具体
- 让系统诊断和创建修复计划

---

## 故障排查

### 常见问题

**安装后找不到命令？**
- 重启运行时以重新加载命令/技能
- 验证文件存在于 `~/.claude/commands/gsd/`（全局）或 `./.claude/commands/gsd/`（本地）
- 对于 Codex，验证技能存在于 `~/.codex/skills/gsd-*/SKILL.md`

**命令未按预期工作？**
- 运行 `/gsd:help` 验证安装
- 重新运行 `npx get-shit-done-cc` 重新安装

**更新到最新版本？**
```bash
npx get-shit-done-cc@latest
```

**使用 Docker 或容器化环境？**

如果文件读取失败，使用波浪号路径（`~/.claude/...`），在安装前设置 `CLAUDE_CONFIG_DIR`：
```bash
CLAUDE_CONFIG_DIR=/home/youruser/.claude npx get-shit-done-cc --global
```

**执行卡住？**
- 检查 `.planning/STATE.md` 了解阻塞
- 使用 `/gsd:health --repair` 修复损坏的状态
- 使用 `/gsd:pause-work` 和 `/gsd:resume-work` 重新开始

**计划质量差？**
- 使用 `/gsd:discuss-phase` 提供更多上下文
- 切换到 `quality` 配置文件
- 启用 `workflow.plan_check`

**过早退出？**
- 检查 `.planning/config.json` 中的 `mode` 设置
- 使用 `--auto` 标志自动批准步骤

**权限被拒绝？**
- 编辑 `.claude/settings.json` 更新权限
- 使用 `claude --dangerously-skip-permissions`

### 卸载

```bash
# 全局安装
npx get-shit-done-cc --claude --global --uninstall
npx get-shit-done-cc --opencode --global --uninstall
npx get-shit-done-cc --codex --global --uninstall

# 本地安装（当前项目）
npx get-shit-done-cc --claude --local --uninstall
npx get-shit-done-cc --opencode --local --uninstall
npx get-shit-done-cc --codex --local --uninstall
```

---

## 系统要求

- **Claude Code CLI**: `npm install -g @anthropic-ai/claude-code`
- **OpenCode**: 开源，免费模型
- **Gemini CLI**: Google Gemini 命令行工具
- **Codex**: OpenClaw 的 Codex 平台
- **Node.js**: 用于安装器
- **Git**: 版本控制

---

## 安全性

### 保护敏感文件

GSD 的代码库映射和分析命令读取文件以理解你的项目。通过将敏感文件添加到 Claude Code 的拒绝列表来保护包含机密的文件：

```json
{
  "permissions": {
    "deny": [
      "Read(.env)",
      "Read(.env.*)",
      "Read(**/secrets/*)",
      "Read(**/*credential*)",
      "Read(**/*.pem)",
      "Read(**/*.key)"
    ]
  }
}
```

这完全阻止 Claude 读取这些文件，无论你运行什么命令。

---

## 相关资源

- **GitHub**: https://github.com/glittercowboy/get-shit-done
- **NPM**: https://www.npmjs.com/package/get-shit-done-cc
- **Discord**: https://discord.gg/gsd
- **Twitter**: https://x.com/gsd_foundation
- **用户指南**: https://github.com/glittercowboy/get-shit-done/blob/main/docs/USER-GUIDE.md

---

**版本**: Latest  
**许可证**: MIT  
**作者**: TÂCHES  
**最后更新**: 2026-03-04
