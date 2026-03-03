# CCG Workflow 命令使用指南和最佳实践

基于 DeepWiki 获取的 fengshao1227/ccg-workflow 仓库文档

生成时间: 2026-03-03

---

## 目录

1. [核心概念](#核心概念)
2. [安装与配置](#安装与配置)
3. [开发工作流命令](#开发工作流命令)
4. [分析与优化命令](#分析与优化命令)
5. [Git 集成命令](#git-集成命令)
6. [OPSX 规范驱动开发](#opsx-规范驱动开发)
7. [最佳实践](#最佳实践)
8. [故障排查](#故障排查)

---

## 核心概念

### 什么是 CCG Workflow?

CCG (Claude Code Generator) Workflow 是一个多模型协作的开发工作流系统,通过编排 Claude、Codex 和 Gemini 三个 AI 模型,实现高效的代码开发、审查和优化。

### 架构特点

- **多模型协作**: Claude 负责编排,Codex 擅长后端逻辑,Gemini 擅长前端 UI
- **零写入权限**: 外部模型(Codex/Gemini)只提供建议,Claude 负责实际文件操作
- **会话管理**: 支持跨阶段的上下文保持
- **MCP 工具集成**: 通过 MCP (Model Context Protocol) 集成代码搜索和提示增强

---

## 安装与配置

### 快速安装

```bash
# 一键安装
npx ccg-workflow

# 或全局安装
npm install -g ccg-workflow@latest
```

### 初始化

```bash
# 初始化工作流
npx ccg-workflow init

# 强制重新安装
npx ccg-workflow init --force
```

### 配置 MCP 工具

```bash
# 配置 MCP 服务
npx ccg-workflow config mcp

# 诊断 MCP 配置
npx ccg-workflow diagnose-mcp

# 修复 Windows MCP 配置
npx ccg-workflow fix-mcp
```

### 更新

```bash
# 更新到最新版本
npx ccg-workflow update

# 或使用菜单
npx ccg-workflow menu
```

---

## 开发工作流命令

### `/ccg:workflow` - 完整 6 阶段开发

**用途**: 从需求到交付的完整开发流程

**阶段**:
1. **Phase 0 (可选)**: 提示词增强
2. **Phase 1**: 上下文收集 (MCP 搜索)
3. **Phase 2**: 多模型分析 (Codex + Gemini 并行)
4. **Phase 3**: 原型生成
5. **Phase 4**: 实现和重构
6. **Phase 5**: 多模型审查

**使用示例**:
```
/ccg:workflow "实现用户登录功能,支持 JWT 认证"
```

**最佳实践**:
- 需求描述要清晰具体
- Phase 2 结束后会要求确认,仔细审查分析结果
- 适合中大型功能开发

---

### `/ccg:plan` 和 `/ccg:execute` - 分离式规划执行

**用途**: 将规划和执行分离,允许人工审查计划

**Plan 阶段**:
```
/ccg:plan "添加用户权限管理模块"
```

输出: `.claude/plan/<feature>.md` (包含 SESSION_ID)

**Execute 阶段**:
```
/ccg:execute
```

读取计划文件并执行实现

**最佳实践**:
- 复杂功能使用 plan/execute 分离
- 审查计划后再执行,避免方向错误
- SESSION_ID 自动传递,保持上下文

---

### `/ccg:feat` - 智能功能开发

**用途**: 快速开发单个功能

**使用示例**:
```
/ccg:feat "添加导出 CSV 功能"
```

**特点**:
- 自动路由到合适的模型
- 包含测试生成
- 适合小型功能快速迭代

---

### `/ccg:frontend` - 前端开发工作流

**用途**: UI/UX 相关任务

**使用示例**:
```
/ccg:frontend "优化用户个人资料页面的响应式布局"
```

**模型路由**:
- 主要模型: Gemini (UI 设计专家)
- 辅助模型: Claude (实现)

**5 阶段流程**:
1. 提示词增强
2. 上下文检索
3. 多模型分析 (Gemini + Claude)
4. 原型生成
5. 多模型审查

---

### `/ccg:backend` - 后端开发工作流

**用途**: 服务端逻辑、API、数据库

**使用示例**:
```
/ccg:backend "实现订单处理 API,包含事务管理"
```

**模型路由**:
- 主要模型: Codex (后端逻辑专家)
- 辅助模型: Claude (实现)

**5 阶段流程**:
1. 提示词增强
2. 上下文检索
3. 多模型分析 (Codex + Gemini)
4. 原型生成
5. 多模型审查

---

## 分析与优化命令

### `/ccg:debug` - 多模型调试

**用途**: 诊断和修复 bug

**使用示例**:
```
/ccg:debug "用户登录后 session 丢失"
```

**工作流程**:
1. 上下文收集 (错误日志、堆栈跟踪)
2. **并行诊断**: Codex (后端) + Gemini (前端)
3. 假设整合 (Top-2 最可能原因)
4. **用户确认** (硬停止点)
5. 修复实现

**最佳实践**:
- 提供详细的错误信息和复现步骤
- 审查诊断假设后再确认修复
- 信任规则: 后端问题以 Codex 为准,前端问题以 Gemini 为准

---

### `/ccg:optimize` - 性能优化

**用途**: 代码性能分析和优化

**使用示例**:
```
/ccg:optimize "优化商品列表页面的加载速度"
```

**6 阶段流程**:
1. 提示词增强
2. 性能基线收集
3. **并行分析**: Codex (后端性能) + Gemini (前端性能)
4. 成本收益排序 (影响程度 × 实施难度⁻¹)
5. 用户确认优化
6. 测试验证和指标对比

**优化维度**:
- **后端**: 数据库查询、算法复杂度、缓存策略
- **前端**: Core Web Vitals、包大小、渲染性能

---

### `/ccg:analyze` - 技术分析

**用途**: 深度技术分析

**使用示例**:
```
/ccg:analyze "分析当前架构的可扩展性"
```

**分析类型**:
- 架构分析
- 技术债务评估
- 安全审计
- 性能瓶颈识别

---

### `/ccg:review` - 多模型代码审查

**用途**: 全面的代码审查

**使用示例**:
```
/ccg:review "审查用户认证模块"
```

**审查维度**:
- **Codex**: 安全性、正确性、API 合规
- **Gemini**: 可访问性、设计一致性、响应式验证

---

### `/ccg:test` - 测试生成

**用途**: 自动生成测试用例

**使用示例**:
```
/ccg:test "为订单处理模块生成单元测试"
```

**测试类型**:
- 单元测试
- 集成测试
- E2E 测试

---

## Git 集成命令

### `/ccg:commit` - 智能提交消息

**用途**: 生成符合规范的 commit message

**使用示例**:
```
/ccg:commit
```

**特点**:
- 自动分析 git diff
- 生成 Conventional Commits 格式
- 支持多语言

**格式**:
```
feat(auth): 添加 JWT 认证支持

- 实现 token 生成和验证
- 添加刷新 token 机制
- 更新用户登录流程
```

---

### `/ccg:rollback` - 交互式回滚

**用途**: 安全地回滚代码更改

**使用示例**:
```
/ccg:rollback
```

**功能**:
- 显示最近的 commits
- 交互式选择回滚点
- 自动处理冲突

---

### `/ccg:clean-branches` - 分支清理

**用途**: 清理已合并的分支

**使用示例**:
```
/ccg:clean-branches
```

---

### `/ccg:worktree` - Worktree 管理

**用途**: 管理 Git worktree

**使用示例**:
```
/ccg:worktree
```

---

## OPSX 规范驱动开发

### 概述

OPSX (OpenSpec eXperimental) 是一种约束驱动、零决策的开发工作流,通过明确的规范消除实现阶段的歧义。

### 命令流程

```
/ccg:spec-init → /ccg:spec-research → /ccg:spec-plan → /ccg:spec-impl → /ccg:spec-review
```

### `/ccg:spec-init` - 环境设置

**用途**: 初始化 OpenSpec 环境

**使用示例**:
```
/ccg:spec-init
```

**操作**:
- 安装 `@fission-ai/openspec`
- 创建 `openspec/` 目录
- 验证环境

---

### `/ccg:spec-research` - 约束发现

**用途**: 需求 → 约束集

**使用示例**:
```
/ccg:spec-research "实现用户权限系统"
```

**输出**: `openspec/changes/proposal.md`

**关键原则**:
- 输出**约束**而非信息堆砌
- 例如: "JWT TTL=15min" (约束) vs "JWT 是一种认证方式" (背景知识)
- 并行探索: Codex (后端) + Gemini (前端)

---

### `/ccg:spec-plan` - 零决策规划

**用途**: 约束 → 零决策计划

**使用示例**:
```
/ccg:spec-plan
```

**输出**:
- `openspec/changes/specs/` - 详细规范
- `openspec/changes/design/` - 设计文档
- `tasks.md` - 任务清单

**目标**: 消除所有歧义,实现阶段无需做决策

---

### `/ccg:spec-impl` - 机械执行

**用途**: 按计划机械执行

**使用示例**:
```
/ccg:spec-impl
```

**约束**:
- **禁止新决策**: 所有决策应在规划阶段完成
- 严格按照 `tasks.md` 执行
- 使用 `openspec validate` 和 `openspec diff` 验证

---

### `/ccg:spec-review` - 独立验证

**用途**: 独立审查所有产物

**使用示例**:
```
/ccg:spec-review
```

**审查内容**:
- 规范完整性
- 实现正确性
- 测试覆盖率
- 双模型交叉审查

---

## 最佳实践

### 1. 选择合适的命令

| 场景 | 推荐命令 | 原因 |
|------|---------|------|
| 新功能开发 | `/ccg:workflow` 或 `/ccg:feat` | 完整流程,包含测试 |
| 复杂功能 | `/ccg:plan` + `/ccg:execute` | 允许审查计划 |
| Bug 修复 | `/ccg:debug` | 多模型诊断 |
| 性能问题 | `/ccg:optimize` | 专门的性能分析 |
| 代码审查 | `/ccg:review` | 多维度审查 |
| UI 开发 | `/ccg:frontend` | Gemini 专长 |
| API 开发 | `/ccg:backend` | Codex 专长 |
| 规范驱动 | OPSX 系列 | 消除歧义 |

### 2. 多模型协作原则

**信任规则**:
- **后端逻辑**: Codex 为权威
- **前端设计**: Gemini 为权威
- **全栈问题**: 综合两者建议

**并行执行**:
- 分析和审查阶段使用并行执行
- 必须等待所有模型返回后再综合
- 超时设置: 600000ms (10 分钟)

### 3. 会话管理

**新会话 vs 会话复用**:
- **并行执行**: 使用新会话 (无 `resume`)
- **顺序执行**: 复用会话 (使用 `resume SESSION_ID`)

**示例**:
```bash
# 新会话
codeagent-wrapper --backend codex - "$(pwd)" <<'EOF'
ROLE_FILE: ~/.claude/.ccg/prompts/codex/analyzer.md
<TASK>分析代码...</TASK>
EOF

# 复用会话
codeagent-wrapper --backend codex resume SESSION_123 - "$(pwd)" <<'EOF'
<TASK>继续实现...</TASK>
EOF
```

### 4. 超时配置

**推荐配置** (`~/.claude/settings.json`):
```json
{
  "env": {
    "BASH_DEFAULT_TIMEOUT_MS": "600000",
    "BASH_MAX_TIMEOUT_MS": "3600000",
    "CODEX_TIMEOUT": "7200",
    "CODEAGENT_POST_MESSAGE_DELAY": "5"
  }
}
```

### 5. MCP 工具使用

**代码搜索**:
```
使用 MCP 工具 search_context 搜索相关代码
参数: query="用户认证相关代码"
```

**提示词增强**:
```
使用 MCP 工具 enhance_prompt 增强需求描述
```

### 6. 工作流程建议

**标准开发流程**:
```
1. /ccg:plan "功能需求"
2. 审查计划
3. /ccg:execute
4. /ccg:test "生成测试"
5. /ccg:review "代码审查"
6. /ccg:commit
```

**快速迭代流程**:
```
1. /ccg:feat "小功能"
2. /ccg:commit
```

**性能优化流程**:
```
1. /ccg:analyze "性能分析"
2. /ccg:optimize "性能优化"
3. /ccg:test "性能测试"
4. /ccg:commit
```

---

## 故障排查

### 常见问题

#### 1. 命令未找到

**症状**: `codeagent-wrapper: command not found`

**解决方案**:
```bash
# 检查 PATH
echo $PATH | grep claude

# 添加到 PATH (Unix)
echo 'export PATH="$HOME/.claude/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Windows PowerShell
$env:PATH += ";$HOME\.claude\bin"
```

#### 2. MCP 工具不可用

**症状**: MCP 工具调用失败

**解决方案**:
```bash
# 诊断
npx ccg-workflow diagnose-mcp

# 修复 (Windows)
npx ccg-workflow fix-mcp

# 重新配置
npx ccg-workflow config mcp
```

#### 3. 超时错误

**症状**: 命令执行超时

**解决方案**:
- 增加超时配置 (见上文超时配置)
- 检查网络连接
- 简化任务复杂度

#### 4. 模型路由错误

**症状**: 错误的模型被调用

**解决方案**:
```bash
# 检查配置
cat ~/.claude/.ccg/config.toml

# 重新安装工作流
npx ccg-workflow init --force
```

#### 5. Windows 特定问题

**进程挂起**:
```bash
# 更新到最新版本
npx ccg-workflow@latest update
```

**路径问题**:
- 使用正斜杠 `/` 而非反斜杠 `\`
- 每个用户独立运行 `npx ccg-workflow init`

---

## 配置文件参考

### `~/.claude/.ccg/config.toml`

```toml
[general]
version = "1.7.x"
language = "zh-CN"

[routing]
mode = "smart"  # smart | parallel | sequential

[routing.frontend]
models = ["gemini"]
primary = "gemini"
strategy = "parallel"

[routing.backend]
models = ["codex"]
primary = "codex"
strategy = "parallel"

[mcp]
provider = "ace-tool"  # ace-tool | ace-tool-rs | contextweaver
setup_url = "https://linux.do/t/topic/284963"
```

### `~/.claude.json` (MCP 配置)

```json
{
  "mcpServers": {
    "ace-tool": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "ace-tool@latest", "--base-url", "YOUR_URL", "--token", "YOUR_TOKEN"]
    }
  }
}
```

---

## CLI 命令参考

```bash
# 初始化
npx ccg-workflow init

# 更新
npx ccg-workflow update

# 菜单
npx ccg-workflow menu

# MCP 配置
npx ccg-workflow config mcp

# MCP 诊断
npx ccg-workflow diagnose-mcp

# MCP 修复 (Windows)
npx ccg-workflow fix-mcp

# 卸载
npx ccg-workflow  # 选择 "Uninstall"
```

---

## 资源链接

- **GitHub**: https://github.com/fengshao1227/ccg-workflow
- **文档**: 内置于安装包
- **社区**: linux.do 论坛

---

**生成时间**: 2026-03-03  
**来源**: DeepWiki - fengshao1227/ccg-workflow  
**版本**: 基于 v1.7.x 系列
