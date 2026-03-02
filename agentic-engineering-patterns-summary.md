# Agentic Engineering Patterns - 完整指南

**来源**: https://simonwillison.net/guides/agentic-engineering-patterns/  
**作者**: Simon Willison  
**更新时间**: 2026年3月2日

---

## 📖 概述

这是一份关于如何更好地使用编码代理(如 Claude Code 和 OpenAI Codex)的完整模式指南。本指南涵盖了从基本原则到具体实践的各个方面,帮助开发者充分发挥 AI 编码助手的潜力。

---

## 1️⃣ 原则 (Principles)

### 1.1 编写代码的成本如今已大幅降低 (Writing code is cheap now)

**核心观点**:
- 代码编写成本的急剧下降颠覆了传统的软件工程直觉
- 过去几百行代码需要一整天,现在 AI 可以在几分钟内完成
- 这改变了宏观层面(项目规划、功能评估)和微观层面(重构、文档、测试)的决策

**好代码的标准**:
- ✅ 代码能正常工作,没有 bug
- ✅ 我们知道代码能工作(经过验证)
- ✅ 解决了正确的问题
- ✅ 优雅地处理错误情况
- ✅ 简单且最小化
- ✅ 有测试保护
- ✅ 有适当的文档
- ✅ 设计便于未来修改
- ✅ 满足相关的"ilities"(可访问性、可测试性、可靠性、安全性等)

**关键洞察**:
> 编写代码几乎免费了,但交付**好代码**仍然需要付出显著的努力。

**实践建议**:
- 质疑自己的直觉 - 当你觉得"不值得花时间"时,试着发个提示词
- 使用异步代理会话 - 最坏的情况只是浪费了一些 tokens
- 建立新的个人和组织习惯来应对代理工程的机遇

---

### 1.2 积累你已掌握的技能 (Hoard things you know how to do)

**核心理念**:
收集和保存你知道如何做的事情的工作示例,这些示例会成为编码代理的强大输入。

**为什么要收集**:
- 了解什么是可能的,什么是不可能的
- 对如何实现有大致的想法
- 能够发现别人可能没想到的技术应用机会

**收集方式**:
- 📝 博客和 TIL(Today I Learned)博客
- 💻 GitHub 仓库(小型概念验证项目)
- 🛠️ HTML 工具集合(单页面工具)
- 🔬 研究仓库(复杂示例和报告)

**重组你的收藏**:
最强大的提示模式之一是告诉代理通过组合两个或多个现有工作示例来构建新东西。

**实际案例**:
Simon 结合了两个已有的 JavaScript 库示例:
- Tesseract.js (OCR 库)
- PDF.js (PDF 渲染库)

通过一个提示词,让 Claude 创建了一个浏览器端的 PDF OCR 工具。

**编码代理让这更强大**:
```
"Clone my simonw/research repo to /tmp and use the examples there"
"Fetch the raw HTML from tools.simonwillison.net/ocr using curl"
"Search my ~/projects folder for examples of WebSocket implementations"
```

**关键思想**:
> 我们只需要弄清楚一个有用的技巧**一次**。如果有工作代码示例记录下来,代理就可以在未来解决任何类似形状的项目。

---

## 2️⃣ 测试与质量保证 (Testing and QA)

### 2.1 红/绿测试驱动开发 (Red/green TDD)

**什么是 TDD**:
测试驱动开发 - 确保你编写的每段代码都有自动化测试来证明代码有效。

**最严格的形式**:
1. 先编写自动化测试
2. 确认测试失败(红色)
3. 迭代实现直到测试通过(绿色)

**为什么适合编码代理**:
- ✅ 防止代理编写不工作的代码
- ✅ 防止构建不必要且从未使用的代码
- ✅ 确保有健壮的自动化测试套件
- ✅ 保护未来的功能不被破坏

**为什么要确认测试失败**:
如果跳过这一步,你可能会构建一个已经通过的测试,从而无法验证你的新实现。

**简洁的提示词**:
```
"Use red/green TDD"
```

这四个词包含了大量的软件工程纪律,而这些已经内置在模型中。

**示例提示**:
```
"Add a new --format json option to the CLI. Use red/green TDD."
```

---

### 2.2 首先运行测试 (First run the tests)

**核心观点**:
在使用编码代理处理现有项目时,自动化测试不再是可选的。

**旧借口不再成立**:
- ❌ "测试耗时且昂贵"
- ✅ 代理可以在几分钟内搞定测试

**测试的重要性**:
- 确保 AI 生成的代码真正有效
- 帮助代理快速了解现有代码库
- 推动代理为新更改编写测试

**四字提示词**:
```
"First run the tests"
```

或者对于 Python 项目:
```
"uv run pytest"
```

**这个提示的多重目的**:
1. 告诉代理存在测试套件,强制它弄清楚如何运行测试
2. 测试数量可以作为项目规模和复杂度的代理指标
3. 让代理进入测试思维模式

**效果**:
- 代理几乎肯定会在未来运行测试以确保没有破坏任何东西
- 如果想了解更多,代理会搜索测试本身
- 自然地扩展测试套件

---

## 3️⃣ 理解代码 (Understanding code)

### 3.1 交互式解释说明 (Interactive explanations)

**认知债务**:
当我们不了解代理编写的代码如何工作时,我们就承担了**认知债务**。

**为什么重要**:
- 简单的代码可以通过试用和浏览来理解
- 但核心应用成为黑盒时,我们无法自信地推理它
- 这会让规划新功能变得困难,最终像技术债务一样减慢进度

**如何偿还认知债务**:
通过构建**交互式解释**来提高对代码工作原理的理解。

**实际案例: 理解词云算法**

Simon 想了解词云是如何工作的:

1. **第一步**: 让 Claude Code 构建了一个 Rust CLI 词云工具
2. **第二步**: 请求线性演练来理解 Rust 代码结构
3. **第三步**: 请求动态解释来直观理解算法

**关键提示**:
```
"Build an animated HTML explanation of how the word cloud placement 
algorithm works. Show words being placed one at a time, with visual 
indicators of where the algorithm is trying to place them and how it 
handles collisions."
```

**结果**:
- 创建了一个动画演示,展示每个单词如何尝试放置
- 显示算法如何检查重叠
- 展示螺旋式向外移动寻找合适位置的过程

**关键洞察**:
> 好的编码代理可以按需生成动画和交互界面来帮助解释概念 - 无论是它自己的代码还是其他人编写的代码。

---

### 3.2 线性演练 (Linear walkthroughs)

**使用场景**:
- 需要了解现有代码库
- 忘记了自己代码的细节
- "氛围编码"(vibe coded)整个项目,需要理解它实际如何工作

**什么是线性演练**:
让编码代理为你构建代码库的结构化演练文档。

**实际案例: Present 应用**

Simon 用 Claude Code 和 Opus 4.6 "氛围编码"了一个 SwiftUI 幻灯片演示应用:

1. 完全通过提示词创建,没有关注代码
2. 发布到 GitHub 后意识到不知道它如何工作
3. 使用新的 Claude Code 会话请求演练

**使用的工具: Showboat**

Showboat 是一个帮助编码代理编写演示其工作的文档的工具:
- `showboat note` - 添加 Markdown 到文档
- `showboat exec` - 执行 shell 命令并将命令和输出添加到文档

**关键提示**:
```
"Use showboat to create a detailed walkthrough of this codebase. 
Use sed or grep or cat or whatever you need to include snippets 
of code you are talking about."
```

**为什么使用 sed/grep/cat**:
确保 Claude Code 不会手动复制代码片段,避免幻觉或错误的风险。

**结果**:
- Claude Code 创建了详细的演练文档
- 讲解了所有 6 个 .swift 文件
- 提供了清晰可操作的代码工作原理解释
- Simon 学到了 SwiftUI 应用结构和 Swift 语言的细节

**关键洞察**:
> 即使是 ~40 分钟的"氛围编码"玩具项目也可以成为探索新生态系统和学习新技巧的机会。

**对学习的影响**:
如果你担心 LLM 可能会降低你学习新技能的速度,强烈建议采用这样的模式。

---

## 4️⃣ 附录 (Appendix)

### 4.1 我使用的提示词 (Prompts I use)

这部分会持续更新 Simon 自己使用的提示词。

#### Artifacts 提示词

用于 Claude 的 Artifacts 功能(在聊天界面中直接构建 HTML 应用):

**问题**: 模型喜欢使用 React,但 React 需要额外的构建步骤

**解决方案**: 在 Claude 项目中使用以下自定义指令:

```
When creating artifacts:
- Use vanilla JavaScript, not React
- Keep everything in a single HTML file
- Embed CSS and JavaScript inline
- Make it copy-paste ready for static hosting
```

#### 校对提示词

**原则**:
- 不让 LLM 为博客写文本
- 表达观点或使用"我"的内容必须由自己编写
- 但使用 LLM 校对发布的文本

**校对提示词**:
```
You are a proofreader. Review the following text for:
- Spelling and grammar errors
- Clarity and readability
- Consistency in tone and style
- Factual accuracy (flag anything that seems questionable)

Do not rewrite the text. Only suggest specific corrections.
Maintain the author's voice and personality.
```

---

## 🎯 核心要点总结

### 原则层面
1. **代码编写成本暴跌** - 但好代码仍需努力
2. **收集工作示例** - 成为代理的强大输入

### 测试层面
3. **红/绿 TDD** - 先写测试,确认失败,再实现
4. **首先运行测试** - 每个会话开始时的四字咒语

### 理解层面
5. **交互式解释** - 用动画和可视化偿还认知债务
6. **线性演练** - 让代理创建结构化的代码库文档

### 实用工具
7. **提示词集合** - Artifacts、校对等实用提示词

---

## 💡 实践建议

### 开始新项目时
```bash
# 1. 首先运行测试
"First run the tests"

# 2. 使用 TDD 添加新功能
"Add feature X. Use red/green TDD."

# 3. 收集工作示例
"Clone my examples repo and use the WebSocket implementation"
```

### 理解现有代码时
```bash
# 1. 请求线性演练
"Use showboat to create a detailed walkthrough of this codebase"

# 2. 创建交互式解释
"Build an animated HTML explanation of how algorithm X works"

# 3. 探索和学习
"Explain the key design patterns used in this codebase"
```

### 构建新工具时
```bash
# 1. 组合现有示例
"Combine the OCR example with the PDF rendering example"

# 2. 使用 Artifacts
"Create a single-file HTML tool that does X"

# 3. 异步研究
"Research how to implement X and write a report with working code"
```

---

## 🔗 相关资源

- **原文链接**: https://simonwillison.net/guides/agentic-engineering-patterns/
- **Simon Willison 博客**: https://simonwillison.net/
- **TIL 博客**: https://til.simonwillison.net/
- **工具集合**: https://tools.simonwillison.net/
- **研究仓库**: https://github.com/simonw/research
- **Showboat 工具**: https://github.com/simonw/showboat

---

## 📝 关键引用

> "我们只需要弄清楚一个有用的技巧一次。如果有工作代码示例记录下来,代理就可以在未来解决任何类似形状的项目。"

> "编写代码几乎免费了,但交付好代码仍然需要付出显著的努力。"

> "好的编码代理可以按需生成动画和交互界面来帮助解释概念。"

> "即使是 ~40 分钟的'氛围编码'玩具项目也可以成为探索新生态系统和学习新技巧的机会。"

---

## 🏷️ 标签

`#ai` `#coding-agents` `#ai-assisted-programming` `#tdd` `#testing` `#agentic-engineering` `#prompt-engineering` `#software-development` `#best-practices`

---

*本文档由 OpenClaw AI 助手整理自 Simon Willison 的 Agentic Engineering Patterns 指南*  
*最后更新: 2026-03-02*
