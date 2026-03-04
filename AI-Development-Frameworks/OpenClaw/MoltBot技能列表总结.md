# MoltBot/OpenClaw 精选技能列表

基于 DeepWiki 获取的 VoltAgent/awesome-moltbot-skills 仓库文档

生成时间: 2026-03-04

---

## 目录

1. [概述](#概述)
2. [技能分类统计](#技能分类统计)
3. [开发与编码技能](#开发与编码技能)
4. [Git 与 GitHub 技能](#git-与-github-技能)
5. [DevOps 与云服务](#devops-与云服务)
6. [Web 与前端开发](#web-与前端开发)
7. [安全与保护](#安全与保护)
8. [搜索与研究](#搜索与研究)
9. [内容与媒体](#内容与媒体)
10. [通信与社交](#通信与社交)
11. [记忆与上下文管理](#记忆与上下文管理)
12. [Apple 应用与服务](#apple-应用与服务)
13. [平台特定工具](#平台特定工具)

---

## 概述

**Awesome MoltBot Skills** 是 OpenClaw 技能生态系统的精选列表,包含 **3,002 个经过审核的技能**,从 5,705 个提交中筛选而来。

### 核心特点

- **安全第一**: 与 VirusTotal 合作进行安全扫描
- **社区驱动**: 只收录经过实际使用验证的技能
- **严格筛选**: 48% 的提交被过滤(垃圾账号、恶意代码、重复等)
- **持续更新**: 由安全研究人员和社区维护

### 三层架构

```
github.com/openclaw/skills  →  官方技能仓库(源代码)
         ↓
clawhub.ai                   →  公共注册表(安全扫描)
         ↓
awesome-moltbot-skills       →  精选列表(质量筛选)
```

---

## 技能分类统计

| 分类 | 技能数量 | 占比 | 主要功能 |
|------|---------|------|---------|
| **开发与编码** | 613 | 20% | 编码代理、IDE 集成、技能创建 |
| **搜索与研究** | 253 | 8% | Web 搜索、学术研究、内容发现 |
| **DevOps 与云** | 212 | 7% | CI/CD、容器编排、云平台管理 |
| **Web 与前端** | 202 | 7% | 框架开发、UI/UX 设计、API 开发 |
| **Clawdbot 工具** | 120 | 4% | 记忆管理、更新维护、文档 |
| **Notes & PKM** | 100 | 3% | 笔记管理、知识库、个人知识管理 |
| **媒体与流媒体** | 80 | 3% | 音频/视频播放、流媒体服务 |
| **语音与转录** | 65 | 2% | TTS、语音识别、字幕提取 |
| **PDF 与文档** | 67 | 2% | PDF 处理、文档转换、文本提取 |
| **Git & GitHub** | 66 | 2% | 仓库自动化、CI/CD 集成 |
| **安全与密码** | 64 | 2% | 安全扫描、密码管理、加密 |
| **图像与视频生成** | 60 | 2% | AI 图像生成、视频合成、3D 建模 |
| **Apple 应用** | 35 | 1% | macOS/iOS 原生应用集成 |
| **其他** | 865 | 29% | 通信、社交、生产力等 |

**总计**: 3,002 个精选技能

---

## 开发与编码技能

### 编码代理调度器

**核心技能**:

| 技能名称 | 主要功能 | 执行方式 |
|---------|---------|---------|
| `coding-agent` | 调度到 Codex CLI、Claude Code、OpenCode | CLI 进程生成 |
| `multi-coding-agent` | 并行运行多个编码代理 | 多个 tmux 会话 |
| `claude-team` | 通过 iTerm2 编排 Claude Code workers | AppleScript 自动化 |
| `cursor-agent` | Cursor CLI 代理集成 | Cursor CLI 命令 |
| `codex-monitor` | 监控 Codex 会话日志 | SQLite 日志解析 |

### IDE 和开发环境

**会话管理**:
- `codex-monitor` - 列出/监控会话
- `codex-quota` - 速率限制检查
- `codex-account-switcher` - 多账号管理

**工作空间控制**:
- `coder-workspaces` - Coder 平台
- `perry-workspaces` - Docker 隔离
- `sandboxer` - Web 仪表板控制

### 技能创建和元开发

| 技能 | 功能 | 输出格式 |
|------|------|---------|
| `create-agent-skills` | 技能创建指南 | SKILL.md 格式 |
| `skill-creator` | 基于模板的技能生成 | 目录结构 |
| `agentskills-io` | 验证和发布工作流 | ClawHub 格式 |
| `skill-scaffold` | CLI 脚手架工具 | 样板代码 |

---

## Git 与 GitHub 技能

### GitHub CLI 集成

**核心技能**:
- `github` - GitHub CLI (gh) 包装器
- `github-pr` - PR 操作
- `git-workflows` - 高级 git 操作

### 备份和同步模式

**备份技能**:
- `backup` - 完整备份操作
- `gitclaw` - 持续后台同步
- `git-crypt-backup` - 加密敏感数据
- `git-sync` - 自动提交工作区更改

### 协作开发工作流

**工作流技能**:
- `conventional-commits` - 格式化提交消息
- `commit-analyzer` - 监控自主代理提交模式
- `auto-pr-merger` - 自动 PR 合并
- `deploy-agent` - 部署代理

---

## DevOps 与云服务

### 多云平台覆盖

**AWS 生态系统** (23 个技能):
- `aws-infra` - 通用 AWS 管理
- `aws-ecs-monitor` - ECS 健康检查
- `aws-security-scanner` - 安全审计
- `aws-solution-architect` - 架构设计

**Azure 生态系统** (18 个技能):
- `azure-cli` - CLI 操作
- `azure-infra` - 基础设施
- `azure-ai-agents-py` - AI Foundry
- `azure-storage-blob-py` - Blob 存储

**Kubernetes** (15 个技能):
- `kubectl-skill` - 集群管理
- `k8-autoscaling` - HPA/VPA/KEDA
- `k8-multicluster` - 多集群
- `k8s-backup` - Velero 备份

**容器平台** (18 个技能):
- `docker-ctl` - Podman 控制
- `portainer` - 容器 GUI API
- `komodo` - 基础设施管理

### CI/CD 集成

**管道监控**:
- `gitflow` - 管道监控器
- `pr-reviewer` - 自动审查
- `commit-analyzer` - 模式分析

---

## Web 与前端开发

### 框架特定开发

**React 生态系统** (45 个技能):
- `nextjs-expert` - Next.js 14/15 App Router
- `vercel-react-best-practices` - 性能模式
- `remotion-best-practices` - 视频生成

**UI/UX 设计** (38 个技能):
- `frontend-design` - 生产界面
- `shadcn-ui` - 组件库
- `ui-audit` - 自动审计
- `figma` - 设计分析

**后端框架** (25 个技能):
- `phoenix-api-gen` - Phoenix JSON API
- `elixir-dev` - Phoenix 伴侣
- `trpc-best-practices` - tRPC 指导

### API 开发和数据库

**API 层**:
- `api-dev` - REST/GraphQL 脚手架
- `trpc-best-practices` - 端到端类型安全
- `phoenix-api-gen` - 从 OpenAPI 生成

**数据库层**:
- `database-operations` - 模式设计、查询
- `sql-toolkit` - 查询优化
- `ecto-migrator` - Elixir 迁移

---

## 安全与保护

### 多层安全架构

**Layer 1: 预安装安全**
- VirusTotal 合作伙伴关系
- 安全研究人员手动审计
- ClawHub 过滤管道

**Layer 2: 安装时安全**
- `skill-vetting` - 安装前审查 ClawHub 技能的安全性和实用性
- `secure-install` - 通过 ClawDex API 扫描技能
- `clawscan` - 专为 ClawHub 技能设计的安全扫描器
- `skill-scanner` - 恶意软件检测

**Layer 3: 运行时保护**
- `openguardrails` - 检测和阻止隐藏在长上下文中的提示注入攻击
- `clawdefender` - AI 代理的安全扫描器和输入清理器
- `prompt-guard` - 针对复杂提示操纵尝试的高级防御
- `input-guard` - 在处理前扫描不受信任的外部文本

**Layer 4: 监控和审计**
- `security-audit` - Clawdbot 部署的全面安全审计
- `security-monitor` - Clawdbot 的实时安全监控
- `clawskillshield` - OpenClaw 技能的本地优先安全扫描器

### 过滤统计

从 5,705 个提交中过滤出 2,748 个 (48%):
- 垃圾账号: 1,180
- 加密/金融: 672
- 重复: 492
- 恶意: 396
- 非英语: 8

---

## 搜索与研究

### 搜索服务分类

| 搜索类型 | 示例技能 | 用例 |
|---------|---------|------|
| 通用 Web 搜索 | brave-search, exa, tavily, serper | 实时 Web 信息检索 |
| 学术研究 | arxiv-watcher, deep-research | 科学论文发现和分析 |
| 社交媒体搜索 | twitter-search-skill, x-search | 趋势分析、社交监听 |
| YouTube 发现 | youtube-search, youtube-data | 视频内容发现 |
| 新闻聚合 | news-aggregator, technews | 当前事件监控 |
| 代码与文档 | github search, npm-search | 开发者资源发现 |

### 主要 Web 搜索

- `brave-search` - 通过 Brave Search API 进行 Web 搜索和内容提取
- `exa` - 通过 Exa AI API 进行神经 Web 搜索和代码上下文
- `tavily` - 通过 Tavily API 进行 AI 优化的 Web 搜索
- `serper` - 通过 Serper API 进行 Google 搜索,包含完整页面内容提取
- `google-search` - 使用 Google 自定义搜索引擎搜索 Web

### 学术与研究

- `deep-research` - 专门从事复杂、多步骤研究任务的深度研究代理
- `academic-deep-research` - 透明、严谨的研究,包含完整引用
- `arxiv-watcher` - 搜索和总结 ArXiv 论文
- `research-cog` - DeepResearch Bench 排名第一 (2026 年 2 月)

---

## 内容与媒体

### 媒体播放和控制

**音乐和音频播放**:
- `spongo` / `spotify` - 终端 Spotify 播放和搜索
- `alexa-cli` - 通过 CLI 控制 Amazon Alexa 设备
- `roon-controller` - 通过 Roon API 控制 Roon 音乐播放器
- `apple-music` - 搜索 Apple Music,添加歌曲到库,管理播放列表

**视频和媒体播放器**:
- `appletv` - 通过 pyatv 库控制 Apple TV
- `tautulli` - 通过 Tautulli API 监控 Plex 活动和统计
- `stremio-cast` - 搜索 Stremio Web 内容并投射到设备

### 图像与视频生成

**多模型图像 API**:
- `fal-ai` - 通过 fal.ai API 生成图像、视频和音频 (FLUX, SDXL, Whisper 等)
- `nvidia-image-gen` - 使用 NVIDIA FLUX 模型生成和编辑图像
- `beauty-generation-api` - 免费 AI 图像生成服务
- `krea-api` - 通过 Krea.ai API 生成图像
- `venice-ai` - 生成、编辑、放大图像;从图像创建视频

**文本到视频模型**:
- `sora-video-gen` - 使用 OpenAI 的 Sora API 生成视频
- `veo` - 使用 Google Veo 生成视频 (Veo 3.1 / Veo 3.0)
- `ai-video-gen` - 从文本描述端到端 AI 视频生成

**头像和说话头生成**:
- `heygen-avatar-lite` - 使用 HeyGen API 创建 AI 数字人视频
- `kameo` - 使用 Kameo AI 从静态图像生成富有表现力的说话头视频
- `liveavatar` - 使用实时视频头像与 OpenClaw 代理面对面交谈

### 语音与转录

**文本转语音 (TTS)**:
- `clawvox` - OpenClaw 的 ElevenLabs 语音工作室
- `aliyun-tts` - 阿里云文本转语音合成服务
- `mac-tts` - 使用 macOS 内置 `say` 命令的文本转语音
- `voice-reply` - 使用 Piper 语音通过 sherpa-onnx 进行本地文本转语音

**语音转文本 (STT)**:
- `asr` - 快速、准确且廉价的自动语音转文本
- `mlx-stt` - 使用 MLX (Apple Silicon) 在本地使用 GLM-ASR-Nano-2512 进行语音转文本

**YouTube 转录提取**:
- `captions` - 从 YouTube 视频提取隐藏字幕和字幕
- `subtitles` - 从 YouTube 视频获取字幕用于翻译、语言学习
- `transcript` - 从任何 YouTube 视频获取转录用于总结、分析
- `youtube-summarizer` - 自动获取 YouTube 视频转录,生成摘要

---

## 通信与社交

### 代理通信堆栈

**身份层**:
- `agent-identity-kit` - AI 代理的便携式身份系统
- `identity-manager` - 严格管理用户身份映射

**发现层**:
- `clawprint` - 代理发现、信任建立和交换协议
- `registry-broker` - 使用 HashNet 发现协议搜索和聊天 72,000+ AI 代理
- `clawdgigs` - 在 ClawdGigs 上注册和管理 AI 代理配置文件

**协议层**:
- MCP 服务器 - 模型上下文协议
- `agentchat` - 实时协议
- `moltcomm` - 去中心化文本

**优化层**:
- `moltspeak` - 令牌减少
- `moltlang` - 符号语言

**加密层**:
- `clawdzap` - 基于 Nostr 的 P2P 加密
- `whisper` - 端到端加密的死信箱消息
- `clawdlink` - 加密的机器人到机器人
- `clawdtalk` - 语音和短信

**信任层**:
- `trust-protocol` - 验证和维护信任
- `clawdentials-escrow` - 托管/声誉/支付
- `uid-life` - A2A 市场

### 优化通信格式

**MoltSpeak**:
- 具有令牌减少功能的代理互联网通信协议
- 令牌减少策略: 符号压缩、上下文省略、语义压缩
- 可实现 70% 的令牌减少

**MoltLang**:
- 专为高效 AI 到 AI 通信设计的紧凑符号语言
- 超紧凑的代理通信
- 常见操作的最小令牌使用

### 加密通信

**Clawdzap**:
- 使用 Nostr 协议的代理 P2P 加密消息
- NIP-04 端到端加密
- 无中央服务器的 P2P 架构

**Whisper**:
- 通过 Moltbook 死信箱的端到端加密代理到代理私人消息
- 死信箱架构: 代理之间无直接连接
- 消息存储直到检索

---

## 记忆与上下文管理

### 记忆系统架构

**工作记忆层**:
- `context-manager` - 会话上下文优化
- `context-compressor` - 自动压缩
- `context-recovery` - 会话恢复

**语义记忆层**:
- `lightrag` - LightRAG API 集成
- `local-rag-search` - mcp-local-rag 服务器
- `context7` - Context7 API

**情景记忆层**:
- `cognitive-memory` - 多存储类人记忆
- `agentmemory` - 加密云同步
- `git-notes-memory` - Git 支持的持久性

**知识组织层**:
- `para-second-brain` - PARA 方法组织
- `knowledge-graph` - 复合知识图谱
- `chromadb-memory` - LanceDB + Ollama

### 多存储认知记忆系统

**cognitive-memory**:
- 实现具有类人记忆特征的智能多存储记忆系统
- 工作记忆容量有限
- 短期到长期记忆巩固
- 语义和情景记忆分离
- 记忆衰减和强化机制

### 持久存储后端

**git-notes-memory**:
- 基于 Git notes 的跨会话持久记忆
- 使用 Git notes 进行版本控制的记忆存储
- 支持多个代理实例之间的协作
- 提供完整的历史和回滚功能

**agentmemory**:
- AI 代理的端到端加密云记忆
- 零知识加密(客户端加密数据)
- 跨设备同步
- 安全云备份

**chromadb-memory**:
- 通过 ChromaDB 使用本地 Ollama 嵌入的长期记忆
- 使用 LanceDB 进行向量存储
- 本地嵌入生成(无 API 调用)
- 保护隐私(数据不离开系统)

---

## Apple 应用与服务

### 平台覆盖

**联系人和消息**:
- `apple-contacts` - 从 macOS Contacts.app 查找联系人
- `apple-mail-search` - macOS 上通过 SQLite 快速搜索 Apple Mail

**音乐和娱乐**:
- `apple-music` - 搜索 Apple Music,管理播放列表,控制播放
- `appletv` - 通过 pyatv 控制 Apple TV

**快捷方式和自动化**:
- `shortcuts-generator` - 通过创建 plist 文件生成 macOS/iOS 快捷方式
- `alter-actions` - 通过 x-callback-urls 触发 Alter macOS 应用操作

**照片和位置**:
- `apple-photos` - macOS 的 Apple Photos.app 集成
- `icloud-findmy` - 查询家庭设备的 Find My 位置和电池状态
- `findmy-location` - 通过 Apple Find My 跟踪共享联系人位置

**语音和语音**:
- `mac-tts` - 使用 macOS 内置 `say` 命令的 TTS
- `mlx-stt` - 通过 MLX 在 Apple Silicon 上进行语音转文本
- `voice-wake-say` - 通过 macOS `say` 大声说出响应

**健康和健身**:
- `healthkit-sync` - iOS HealthKit 数据同步 CLI 命令和模式

**系统实用程序**:
- `homebrew` - macOS 的 Homebrew 包管理器
- `mole-mac-cleanup` - Mac 清理和优化工具

---

## 平台特定工具

### Clawdbot 工具 (120 个技能)

**记忆和持久性**:
- `memory` - OpenClaw 代理的完整记忆系统
- `memory-complete` - 扩展记忆系统变体
- `memory-hygiene` - 审计、清理、优化向量记忆
- `clawdbot-sync` - 跨实例同步记忆、首选项、技能
- `triple-memory` - LanceDB + Git-Notes + 基于文件的记忆

**更新和维护**:
- `openclaw-update` - 备份、更新和恢复工作流
- `clawdbot-update-plus` - 完整备份、更新和恢复
- `openclaw-hardener` - 加固 `~/.openclaw` 和工作区

**文档和帮助**:
- `clawdbot-documentation-expert` - OpenClaw 文档专家
- `clawddocs` - 具有决策树导航的文档

**安全和审计**:
- `clawdbot-security-check` - 全面的只读安全检查
- `sona-security-audit` - ClawHub 技能的失败关闭安全审计
- `skills-audit` - 审计本地安装的技能的安全/策略问题

**上下文和会话**:
- `context-manager` - 会话的 AI 驱动上下文管理
- `cost-report` - 按日期跟踪 OpenClaw 使用成本
- `cron-mastery` - 掌握 OpenClaw 的计时和 cron 系统

**代理构建**:
- `agent-builder` - 端到端构建 OpenClaw 代理
- `skill-deps` - 跟踪和管理技能之间的依赖关系

---

## 贡献指南

### 提交要求

在提交 PR 之前,验证技能满足所有要求:

| 要求 | 描述 | 验证 |
|------|------|------|
| **发布到官方仓库** | 技能存在于 `github.com/openclaw/skills/tree/main/skills/author/skill-name/` | 检查 URL 是否可访问 |
| **有 SKILL.md** | 技能目录中存在文档文件 | 验证 `SKILL.md` 存在 |
| **描述 ≤10 词** | 简洁、信息丰富的描述 | 字数检查 |
| **真实社区使用** | 经过用户验证的采用,不是全新的 | 使用证据(星标、分叉、提及) |
| **非加密/金融** | 无加密货币、区块链、DeFi、交易技能 | 检查领域 |
| **ClawHub 安全清洁** | 在 `clawhub.ai` 上未标记为可疑 | 访问 ClawHub 上的技能页面 |
| **无重复** | 技能服务于现有条目未涵盖的独特目的 | 搜索 `README.md` 查找类似技能 |

### 自动拒绝类别

- **垃圾**: 批量账号、机器人账号、测试/垃圾技能 (1,180 个被拒绝)
- **加密/区块链/金融/交易**: 政策排除 (672 个被拒绝)
- **重复**: 类似名称或功能 (492 个被拒绝)
- **恶意**: 由安全审计识别 (396 个被拒绝)
- **非英语**: 描述不是英语 (8 个被拒绝)

---

## 资源链接

- **GitHub 官方仓库**: https://github.com/openclaw/skills
- **ClawHub 注册表**: https://clawhub.ai
- **精选列表**: https://github.com/VoltAgent/awesome-moltbot-skills
- **OpenClaw 文档**: https://docs.openclaw.ai
- **社区**: https://discord.com/invite/clawd

---

**生成时间**: 2026-03-04  
**来源**: DeepWiki - VoltAgent/awesome-moltbot-skills  
**技能总数**: 3,002 个精选技能(从 5,705 个提交中筛选)
