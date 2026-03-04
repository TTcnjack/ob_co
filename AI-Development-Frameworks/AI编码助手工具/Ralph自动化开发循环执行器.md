# Ralph - 自动化开发循环执行器

**GitHub**: https://github.com/frankbria/ralph-claude-code  
**版本**: v0.11.5  
**类型**: Bash 应用程序  
**平台**: Claude Code

生成时间: 2026-03-04

---

## 核心概念

Ralph 是一个自动化执行 Claude Code 开发循环的 Bash 应用程序，具有智能监控和资源管理功能。它实现了 Geoffrey Huntley 的 Ralph Wiggum 技术，让 Claude Code 持续自主改进项目直到完成。

### 设计理念

- **一次安装，到处使用**: 全局安装后可在任何项目中使用
- **智能退出检测**: 双条件检查防止过早退出
- **资源优化**: 防止无限循环和 API 滥用
- **实时监控**: tmux 集成提供实时可见性

---

## 核心功能

### 1. 自动化开发循环

**工作流程**:
```
读取指令 (PROMPT.md) → 执行 Claude Code → 跟踪进度 → 评估完成 → 重复
```

**特点**:
- 持续执行直到项目完成
- 自动更新任务列表
- 详细的执行日志
- 状态持久化

### 2. 智能退出检测

**双条件检查**:
1. `completion_indicators >= 2` (启发式检测)
2. Claude 明确的 `EXIT_SIGNAL: true`

**退出条件**:
- 所有任务标记完成
- 多个连续"完成"信号
- 过多测试循环（表示功能完整）
- Claude API 5 小时限制

### 3. 电路断路器（Circuit Breaker）

**功能**:
- 监控停滞（无文件变更）
- 检测重复错误
- 自动暂停执行
- 支持手动重置

**阈值**（可配置）:
- `CB_NO_PROGRESS_THRESHOLD=3` - 3 次循环无进展
- `CB_SAME_ERROR_THRESHOLD=5` - 5 次相同错误
- `CB_COOLDOWN_MINUTES=30` - 30 分钟冷却期

**自动恢复**:
```
OPEN → (30分钟) → HALF_OPEN → (测试) → CLOSED
```

### 4. 速率限制（Rate Limiting）

**默认限制**: 100 次调用/小时

**功能**:
- 限制每小时 API 调用次数
- 自动等待下一个时间窗口
- 倒计时显示
- 防止超出配额

### 5. 实时监控

**ralph-monitor.sh**:
- 循环计数和状态
- API 使用情况
- 最近日志条目
- 速率限制倒计时

**tmux 集成**:
```bash
ralph --monitor  # 集成监控（推荐）
```

### 6. 会话连续性

**功能**:
- 跨循环保持上下文
- 自动会话管理
- 会话过期（默认 24 小时）
- 会话历史记录

**自动重置触发**:
- 电路断路器打开
- 手动中断（Ctrl+C）
- 项目完成
- 会话过期

### 7. 实时流式输出

**--live 标志**:
- 实时查看 Claude Code 执行
- 输出写入 `.ralph/live.log`
- 提供执行可见性

---

## 安装和设置

### Phase 1: 全局安装（一次性）

```bash
git clone https://github.com/frankbria/ralph-claude-code.git
cd ralph-claude-code
./install.sh
```

添加全局命令: `ralph`, `ralph-monitor`, `ralph-setup`, `ralph-import`, `ralph-enable`

### Phase 2: 项目初始化

**选项 A: 现有项目启用（推荐）**
```bash
cd my-existing-project
ralph-enable                    # 交互式向导
ralph-enable --from beads       # 从 beads 导入任务
ralph-enable --from github      # 从 GitHub Issues 导入
ralph-enable --from prd ./docs/requirements.md
```

**选项 B: 导入现有 PRD**
```bash
ralph-import my-requirements.md my-project
cd my-project
# 审查生成的文件
ralph --monitor
```

**选项 C: 从头创建**
```bash
ralph-setup my-awesome-project
cd my-awesome-project
# 手动配置 .ralph/PROMPT.md 和 fix_plan.md
ralph --monitor
```

---

## 命令参考

### Ralph 循环选项

```bash
ralph [OPTIONS]

# 基本选项
-h, --help              显示帮助
-m, --monitor           启动 tmux 会话和实时监控（推荐）
-v, --verbose           显示详细进度更新
-l, --live              启用实时流式输出
-s, --status            显示当前状态并退出

# 配置选项
-c, --calls NUM         设置每小时最大调用次数（默认: 100）
-p, --prompt FILE       设置提示文件（默认: PROMPT.md）
-t, --timeout MIN       设置执行超时（1-120 分钟，默认: 15）
--output-format FORMAT  设置输出格式: json（默认）或 text
--allowed-tools TOOLS   设置允许的工具

# 会话管理
--no-continue           禁用会话连续性
--reset-session         手动重置会话

# 电路断路器
--reset-circuit         重置电路断路器
--circuit-status        显示电路断路器状态
--auto-reset-circuit    启动时自动重置
```

### 项目命令

```bash
ralph-setup project-name     # 创建新项目
ralph-enable                 # 在现有项目中启用（交互式）
ralph-enable-ci              # 非交互式启用
ralph-import prd.md project  # 转换 PRD 为 Ralph 项目
ralph-monitor                # 手动监控仪表板
ralph-migrate                # 迁移到 .ralph/ 结构
```

### tmux 会话管理

```bash
tmux list-sessions        # 查看活动会话
tmux attach -t <name>     # 重新连接会话
# Ctrl+B then D           # 分离会话（保持运行）
```

---

## 项目结构

```
my-project/
├── .ralph/                 # Ralph 配置和状态
│   ├── PROMPT.md           # 主要开发指令
│   ├── fix_plan.md        # 优先任务列表
│   ├── AGENT.md           # 构建和运行指令
│   ├── specs/              # 项目规范
│   │   └── stdlib/         # 标准库规范
│   ├── examples/           # 使用示例
│   ├── logs/               # 执行日志
│   └── docs/generated/     # 自动生成文档
├── .ralphrc                # Ralph 配置文件
└── src/                    # 源代码
```

### 关键文件

| 文件 | 自动生成? | 你应该... |
|------|-----------|----------|
| `.ralph/PROMPT.md` | 是（智能默认） | 审查并自定义项目目标 |
| `.ralph/fix_plan.md` | 是（可导入任务） | 添加/修改具体任务 |
| `.ralph/AGENT.md` | 是（检测构建命令） | 很少编辑（自动维护） |
| `.ralph/specs/` | 空目录 | 需要时添加详细需求 |
| `.ralphrc` | 是（项目感知） | 很少编辑（合理默认） |

---

## 配置

### .ralphrc 配置文件

```bash
# 项目信息
PROJECT_NAME="my-project"
PROJECT_TYPE="typescript"

# Claude Code CLI 命令
CLAUDE_CODE_CMD="claude"
# CLAUDE_CODE_CMD="npx @anthropic-ai/claude-code"  # 替代方案

# 循环设置
MAX_CALLS_PER_HOUR=100
CLAUDE_TIMEOUT_MINUTES=15
CLAUDE_OUTPUT_FORMAT="json"

# 工具权限
ALLOWED_TOOLS="Write,Read,Edit,Bash(git *),Bash(npm *),Bash(pytest)"

# 会话管理
SESSION_CONTINUITY=true
SESSION_EXPIRY_HOURS=24

# 电路断路器阈值
CB_NO_PROGRESS_THRESHOLD=3
CB_SAME_ERROR_THRESHOLD=5
CB_COOLDOWN_MINUTES=30
CB_AUTO_RESET=false
```

### 退出阈值

```bash
# 退出检测
MAX_CONSECUTIVE_TEST_LOOPS=3
MAX_CONSECUTIVE_DONE_SIGNALS=2
TEST_PERCENTAGE_THRESHOLD=30

# 电路断路器
CB_NO_PROGRESS_THRESHOLD=3
CB_SAME_ERROR_THRESHOLD=5
CB_OUTPUT_DECLINE_THRESHOLD=70
CB_COOLDOWN_MINUTES=30
```

---

## 使用示例

### 基本使用

```bash
# 启动 Ralph（推荐方式）
ralph --monitor

# 自定义配置
ralph --monitor --calls 50 --timeout 30 --verbose

# 实时流式输出
ralph --monitor --live

# 检查状态
ralph --status
```

### 会话管理

```bash
# 默认使用会话连续性
ralph --monitor

# 不使用会话（每次循环独立）
ralph --no-continue

# 重置会话
ralph --reset-session

# 查看会话文件
cat .ralph/.ralph_session
cat .ralph/.ralph_session_history
```

### 电路断路器

```bash
# 检查状态
ralph --circuit-status

# 手动重置
ralph --reset-circuit

# 启动时自动重置
ralph --auto-reset-circuit

# 或在 .ralphrc 中设置
CB_AUTO_RESET=true
```

---

## 最佳实践

### 1. 编写有效的提示

- **具体明确**: 清晰的需求带来更好的结果
- **优先级**: 使用 `fix_plan.md` 引导 Ralph 的焦点
- **设置边界**: 定义范围内外的内容
- **包含示例**: 展示预期的输入/输出

### 2. 项目规范

- 将详细需求放在 `.ralph/specs/`
- 使用 `fix_plan.md` 进行优先任务跟踪
- 保持 `AGENT.md` 更新构建指令
- 记录关键决策和架构

### 3. 监控进度

- 使用 `ralph-monitor` 实时状态更新
- 检查 `.ralph/logs/` 详细执行历史
- 监控 `.ralph/status.json` 程序化访问
- 注意退出条件信号

### 4. 资源管理

- 设置合理的速率限制（默认 100/小时）
- 使用电路断路器防止浪费
- 配置适当的超时时间
- 监控 API 使用情况

---

## 故障排查

### 常见问题

**Ralph 在第一次循环时静默退出**
- Claude Code CLI 可能未安装或不在 PATH 中
- 如果使用 npx，在 `.ralphrc` 中添加 `CLAUDE_CODE_CMD="npx @anthropic-ai/claude-code"`

**速率限制**
- Ralph 自动等待并显示倒计时
- 可以调整 `MAX_CALLS_PER_HOUR`

**5 小时 API 限制**
- Ralph 检测并提示用户操作（等待或退出）
- 无人值守模式：提示超时时自动等待

**循环卡住**
- 检查 `fix_plan.md` 是否有不清楚或冲突的任务
- 电路断路器会自动检测并暂停

**过早退出**
- 检查 Claude 是否设置 `EXIT_SIGNAL: false`
- Ralph 现在尊重这个标志

**权限被拒绝**
- 编辑 `.ralphrc` 更新 `ALLOWED_TOOLS`
- 常见模式: `Bash(npm *)`, `Bash(git *)`, `Bash(pytest)`
- 运行 `ralph --reset-session` 后重启

**timeout: command not found (macOS)**
- 安装 GNU coreutils: `brew install coreutils`

---

## 系统要求

- **Bash 4.0+**
- **Claude Code CLI**: `npm install -g @anthropic-ai/claude-code`
- **tmux**: 终端复用器（推荐）
- **jq**: JSON 处理
- **Git**: 版本控制
- **GNU coreutils**: `timeout` 命令
  - Linux: 预装
  - macOS: `brew install coreutils`

---

## 测试状态

- **566 个测试**，100% 通过率
- 18 个测试文件
- 单元测试 + 集成测试
- CI/CD 管道（GitHub Actions）

---

## 相关资源

- **GitHub**: https://github.com/frankbria/ralph-claude-code
- **Ralph 技术**: https://ghuntley.com/ralph/
- **Claude Code**: https://claude.ai/code
- **文档**: 仓库中的 docs/ 目录

---

**版本**: v0.11.5  
**许可证**: MIT  
**作者**: Frank Bria  
**最后更新**: 2026-03-04
