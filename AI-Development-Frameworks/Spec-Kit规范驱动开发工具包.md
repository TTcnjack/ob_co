# Spec-Kit - 规范驱动开发工具包

**GitHub**: https://github.com/github/spec-kit  
**版本**: v0.1.0  
**类型**: Python CLI 工具 + 工作流模板  
**平台**: 多 AI 编码助手（Claude, Gemini, Copilot 等 17+ 个）

生成时间: 2026-03-04

---

## 核心概念

Spec-Kit 是一个规范驱动开发（Spec-Driven Development, SDD）工具包，通过结构化的工作流和 AI 编码助手集成，帮助开发者从需求到实现的完整开发过程。它提供了一套标准化的模板、脚本和命令，支持 17+ 个主流 AI 编码助手。

### 设计理念

- **规范优先**: 先写规范，后写代码，确保需求清晰
- **AI 辅助**: 与 AI 编码助手深度集成，自动化重复工作
- **跨平台**: 支持 Bash 和 PowerShell，适配 Linux/macOS/Windows
- **多 AI 支持**: 一套工具包，支持 Claude、Gemini、Copilot 等 17+ 个 AI 助手
- **宪法式治理**: 通过 constitution.md 定义不可变的架构原则

---

## 核心功能

### 1. 规范驱动工作流

**五阶段开发流程**:
```
Constitution → Specify → Plan → Tasks → Implement
```

**各阶段说明**:
- **Constitution**: 定义项目的架构原则和约束（9 条宪法）
- **Specify**: 从自然语言生成详细的功能规范
- **Plan**: 创建实现计划，包含技术栈和阶段划分
- **Tasks**: 生成具体的任务列表
- **Implement**: 执行实现并验证

### 2. CLI 工具

**主要命令**:
```bash
specify init --ai claude --script bash    # 初始化项目
specify check                             # 检查环境和依赖
specify version                           # 显示版本信息
specify extension add jira                # 添加扩展
```

**特点**:
- 自动下载和配置 AI 助手命令
- 环境验证和依赖检查
- 扩展系统支持
- 跨平台脚本生成

### 3. 多 AI 助手支持

**支持的 AI 助手**（17+）:
- **CLI 类型**: Claude Code, Gemini CLI, Qwen Code, OpenCode, Auggie, Roo, CodeBuddy, Amp, SHAI, Bob, Qoder
- **IDE 类型**: GitHub Copilot, Cursor, Windsurf, Codex, Kilo Code, Amazon Q

**集成方式**:
- 每个 AI 助手有专属的命令目录（如 `.claude/commands/`, `.github/agents/`）
- 统一的命令模板，自动转换为各 AI 的格式（Markdown 或 TOML）
- 上下文文件（如 `CLAUDE.md`, `GEMINI.md`）提供项目信息

### 4. 模板系统

**核心模板**:
- `spec-template.md`: 功能规范模板
- `plan-template.md`: 实现计划模板
- `tasks-template.md`: 任务列表模板
- `constitution.md`: 架构原则模板

**命令模板**:
- 位于 `templates/commands/` 目录
- 使用 YAML frontmatter 定义元数据
- 支持占位符替换（`$ARGUMENTS`, `{{args}}`）
- 自动生成 AI 助手特定格式

### 5. 自动化脚本

**跨平台脚本**（Bash + PowerShell）:
- `create-new-feature.sh/.ps1`: 创建新功能分支和规范目录
- `update-agent-context.sh/.ps1`: 从 plan.md 提取技术栈并更新 AI 上下文
- `check-prerequisites.sh/.ps1`: 验证环境依赖

**功能**:
- 自动分支命名和编号
- Git 集成（可选）
- JSON 输出供 AI 解析
- 智能错误处理

### 6. 扩展系统

**功能**:
- 扩展注册表（`extensions/catalog.json`）
- 生命周期钩子（pre-specify, post-plan 等）
- 四层配置级联（默认 → 项目 → 本地 → 环境变量）
- 命令注册和模板扩展

**官方扩展**:
- **Jira**: 与 Jira 集成，同步 Issues 和任务

### 7. 自动化发布系统

**发布流程**:
```
代码变更 → 版本检测 → 生成 32 个包 → 发布到 GitHub Releases
```

**包变体**:
- 17 个 AI 助手 × 2 种脚本类型（Bash/PowerShell）= 34 个包
- 每个包包含完整的模板、脚本和 AI 命令
- 自动版本管理和 CHANGELOG 生成

---

## 安装和配置

### 安装 CLI 工具

```bash
# 使用 pip 安装
pip install specify-cli

# 或使用 uv（推荐）
uv pip install specify-cli

# 验证安装
specify --help
```

### 初始化项目

```bash
# 基本初始化（交互式选择 AI 助手）
specify init

# 指定 AI 助手和脚本类型
specify init --ai claude --script bash
specify init --ai gemini --script ps
specify init --ai copilot --script bash

# 不初始化 Git 仓库
specify init --ai claude --no-git
```

### 项目结构

```
my-project/
├── .specify/                   # Spec-Kit 配置和资源
│   ├── memory/
│   │   └── constitution.md     # 架构原则
│   ├── scripts/                # 自动化脚本
│   │   ├── create-new-feature.sh
│   │   └── update-agent-context.sh
│   ├── templates/              # 工作流模板
│   │   ├── spec-template.md
│   │   ├── plan-template.md
│   │   └── tasks-template.md
│   └── extensions/             # 扩展目录
├── .claude/commands/           # Claude 命令（或其他 AI 目录）
│   ├── speckit.constitution.md
│   ├── speckit.specify.md
│   ├── speckit.plan.md
│   ├── speckit.tasks.md
│   └── speckit.implement.md
├── CLAUDE.md                   # AI 上下文文件
└── specs/                      # 功能规范目录
    └── 001-feature-name/
        ├── spec.md
        ├── plan.md
        └── tasks.md
```

---

## 命令参考

### CLI 命令

```bash
# 初始化
specify init [OPTIONS]
  --ai AGENT              指定 AI 助手（claude, gemini, copilot 等）
  --script TYPE           脚本类型（bash 或 ps）
  --no-git                不初始化 Git 仓库

# 环境检查
specify check               # 检查 AI CLI 工具是否安装

# 版本信息
specify version             # 显示 Spec-Kit 版本

# 扩展管理
specify extension add NAME      # 添加扩展
specify extension remove NAME   # 移除扩展
specify extension list          # 列出已安装扩展
specify extension search QUERY  # 搜索扩展
```

### AI 助手命令

**在 AI 助手中使用**（以 Claude 为例）:
```
/speckit.constitution    # 创建或查看架构原则
/speckit.specify         # 生成功能规范
/speckit.clarify         # 澄清规范中的模糊点
/speckit.plan            # 创建实现计划
/speckit.analyze         # 跨文档验证
/speckit.tasks           # 生成任务列表
/speckit.implement       # 执行实现
/speckit.checklist       # 质量检查清单
```

### 脚本命令

```bash
# 创建新功能
.specify/scripts/create-new-feature.sh "Add user authentication"
.specify/scripts/create-new-feature.sh --short-name auth "Add user authentication"
.specify/scripts/create-new-feature.sh --number 5 "Custom feature"

# 更新 AI 上下文
.specify/scripts/update-agent-context.sh
.specify/scripts/update-agent-context.sh --agent claude
```

---

## 配置

### AGENT_CONFIG（AI 助手配置）

**位置**: `src/specify_cli/__init__.py`

**配置项**:
```python
{
    "name": "Claude Code",           # 显示名称
    "cli_tool": "claude",            # CLI 命令
    "agent_type": "cli",             # 类型: cli 或 ide
    "commands_dir": ".claude/commands",  # 命令目录
    "config_file": "CLAUDE.md",      # 上下文文件
    "format": "markdown"             # 格式: markdown 或 toml
}
```

### 扩展配置

**四层配置级联**:
1. **扩展默认值**: `extension.yml` 中的 `config` 部分
2. **项目配置**: `.specify/extensions/{ext-id}/{ext-id}-config.yml`
3. **本地配置**: `.specify/extensions/{ext-id}/local-config.yml`（gitignored）
4. **环境变量**: `SPECKIT_{EXT_ID}_{KEY}`

**示例**（Jira 扩展）:
```yaml
# .specify/extensions/jira/jira-config.yml
jira_url: "https://mycompany.atlassian.net"
project_key: "PROJ"
```

### 脚本配置

**环境变量**:
```bash
SPECIFY_FEATURE="001-feature-name"  # 当前功能分支
```

---

## 使用示例

### 基本工作流

```bash
# 1. 初始化项目
specify init --ai claude --script bash

# 2. 在 AI 助手中创建架构原则
/speckit.constitution

# 3. 生成功能规范
/speckit.specify "Add user authentication with JWT"

# 4. 澄清模糊点（如果需要）
/speckit.clarify

# 5. 创建实现计划
/speckit.plan

# 6. 生成任务列表
/speckit.tasks

# 7. 执行实现
/speckit.implement

# 8. 质量检查
/speckit.checklist
```

### 使用扩展

```bash
# 安装 Jira 扩展
specify extension add jira

# 配置 Jira
cat > .specify/extensions/jira/jira-config.yml << EOF
jira_url: "https://mycompany.atlassian.net"
project_key: "PROJ"
api_token: "your-token"
EOF

# 在 AI 助手中使用 Jira 命令
/jira.sync-issues
/jira.create-issue
```

### 多功能开发

```bash
# 创建第一个功能
.specify/scripts/create-new-feature.sh "User authentication"
# 生成: specs/001-user-authentication/

# 创建第二个功能
.specify/scripts/create-new-feature.sh "Payment integration"
# 生成: specs/002-payment-integration/

# 使用自定义短名称
.specify/scripts/create-new-feature.sh --short-name auth "User authentication"
# 生成: specs/001-auth/
```

---

## 最佳实践

### 1. 编写清晰的规范

- **使用模板**: 遵循 `spec-template.md` 的结构
- **明确需求**: 避免模糊的描述，使用 `[NEEDS CLARIFICATION]` 标记
- **包含示例**: 提供输入/输出示例
- **定义边界**: 明确功能范围内外的内容

### 2. 遵循宪法原则

**9 条架构原则**:
1. **Library-First Principle**: 优先使用库而非框架
2. **CLI Interface Mandate**: 提供 CLI 接口
3. **Test-First Imperative**: 测试驱动开发
4. **Documentation Mandate**: 文档与代码同步
5. **Dependency Minimalism**: 最小化依赖
6. **Configuration Simplicity**: 简化配置
7. **Simplicity Gate**: 限制项目复杂度（≤3 个子项目）
8. **Anti-Abstraction Gate**: 避免过度抽象
9. **Integration-First Testing**: 优先集成测试

### 3. 利用 AI 助手

- **上下文管理**: 定期运行 `update-agent-context.sh` 更新 AI 上下文
- **命令链**: 按顺序执行工作流命令（constitution → specify → plan → tasks → implement）
- **迭代改进**: 使用 `/speckit.clarify` 和 `/speckit.analyze` 持续改进规范

### 4. 版本控制

- **提交规范**: 每个阶段完成后提交（spec.md, plan.md, tasks.md）
- **分支策略**: 使用功能分支（由 `create-new-feature.sh` 自动创建）
- **忽略文件**: 添加 AI 目录到 `.gitignore`（避免泄露凭证）

---

## 故障排查

### 常见问题

**specify init 失败**
- 检查网络连接（需要从 GitHub Releases 下载包）
- 验证 Python 版本（需要 3.11+）
- 确认 AI 助手已安装（运行 `specify check`）

**AI 命令不显示**
- 检查命令目录是否存在（如 `.claude/commands/`）
- 验证命令文件格式（Markdown 或 TOML）
- 重启 AI 助手或重新加载工作区

**脚本执行失败**
- **Bash**: 确保脚本有执行权限（`chmod +x .specify/scripts/*.sh`）
- **PowerShell**: 检查执行策略（`Set-ExecutionPolicy RemoteSigned`）
- **Git 错误**: 确认 Git 已安装且仓库已初始化

**扩展安装失败**
- 检查扩展名称是否正确（`specify extension search <name>`）
- 验证网络连接（需要访问扩展目录）
- 查看错误日志（通常在 `.specify/extensions/.cache/`）

**版本冲突**
- 更新 CLI: `pip install --upgrade specify-cli`
- 清除缓存: `rm -rf .specify/extensions/.cache/`
- 重新初始化: `specify init --ai <agent> --script <type>`

---

## 系统要求

### 必需
- **Python**: 3.11+
- **Git**: 任意版本（可选，使用 `--no-git` 跳过）
- **AI 编码助手**: 至少一个（Claude, Gemini, Copilot 等）

### 推荐
- **uv**: 快速的 Python 包管理器
- **tmux**: 终端复用器（用于监控）
- **jq**: JSON 处理工具

### 平台支持
- **Linux**: 完全支持
- **macOS**: 完全支持
- **Windows**: 支持（需要 PowerShell 5.1+ 或 PowerShell Core）

---

## 架构特点

### 1. 模板处理流程

```
命令模板 (YAML + Markdown)
  ↓
提取 frontmatter
  ↓
占位符替换 ($ARGUMENTS → $ARGUMENTS 或 {{args}})
  ↓
路径重写 (memory/ → .specify/memory/)
  ↓
格式转换 (Markdown 或 TOML)
  ↓
输出到 AI 命令目录
```

### 2. 发布流程

```
代码变更 → 触发 GitHub Actions
  ↓
版本检测 (get-next-version.sh)
  ↓
检查重复 (check-release-exists.sh)
  ↓
生成包 (create-release-packages.sh)
  ├─ 17 个 AI × Bash = 17 个包
  └─ 17 个 AI × PowerShell = 17 个包
  ↓
生成 CHANGELOG (generate-release-notes.sh)
  ↓
创建 GitHub Release (create-github-release.sh)
  ↓
更新版本 (update-version.sh)
```

### 3. 扩展系统架构

```
扩展目录
  ├─ extension.yml (清单)
  ├─ commands/ (命令模板)
  ├─ hooks/ (生命周期钩子)
  └─ config/ (配置文件)
    ↓
注册表 (.specify/extensions/.registry)
  ↓
配置级联 (4 层)
  ↓
命令注册 (CommandRegistrar)
  ↓
钩子执行 (HookExecutor)
```

---

## 相关资源

- **GitHub**: https://github.com/github/spec-kit
- **文档**: 仓库中的 `spec-driven.md`
- **贡献指南**: `CONTRIBUTING.md`
- **变更日志**: `CHANGELOG.md`
- **扩展目录**: `extensions/catalog.json`

---

## 开发和贡献

### 本地开发设置

```bash
# 克隆仓库
git clone https://github.com/github/spec-kit.git
cd spec-kit

# 安装依赖
uv sync

# 运行 CLI（开发模式）
uv run specify --help

# 运行测试
uv run pytest

# 生成本地包（用于测试）
./.github/workflows/scripts/create-release-packages.sh v1.0.0
```

### 测试模板变更

```bash
# 1. 修改模板或脚本
vim templates/commands/speckit.specify.md

# 2. 生成本地包
./.github/workflows/scripts/create-release-packages.sh v1.0.0

# 3. 复制到测试项目
cp -r .genreleases/sdd-claude-package-sh/. /path/to/test-project/

# 4. 在 AI 助手中测试
cd /path/to/test-project
# 打开 AI 助手并测试命令
```

### 贡献要求

- **AI 辅助披露**: 必须在 PR 中披露 AI 使用情况
- **测试覆盖**: 新功能需要测试
- **文档更新**: 用户可见的变更需要更新文档
- **代码规范**: 遵循现有代码风格
- **提前讨论**: 大型变更需要先与维护者讨论

---

**版本**: v0.1.0  
**许可证**: MIT  
**维护者**: GitHub  
**最后更新**: 2026-03-04
