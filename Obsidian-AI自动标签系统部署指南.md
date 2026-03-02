# Obsidian + AI 自动标签系统部署指南

**来源**: https://waytoagi.feishu.cn/wiki/QywpwA7rLinKvLkfWSKcUfgrnGa  
**标题**: 告别手动打标签！用 Obsidian + AI 喂养一个会自动生长的知识库  
**生成时间**: 2026-03-02

---

## 📋 内容总结

本文介绍了如何利用 Obsidian、Copilot AI 插件与精心设计的提示词模板，将繁琐的"打标签"工作升级为自动化、智能化的流程，从而激活知识图谱的全部潜力。

### 核心理念

- **文件夹分类已死，手动打标签反人性** - 把 AI 能做的事情交给 AI
- **标签是知识的桥梁** - 相比死板的树状文件夹结构，标签能自由构建多对多的网状关系
- **喂养你的个人 AI** - 好的标签系统能反过来训练你的个人 AI 的上下文

### 关键技术更新

**Copilot Release 3.0.0 多跳搜索功能**
- 基于知识图谱的"多跳"召回机制
- AI 不仅搜索笔记本身，还能顺着链接和标签"多跳几步"
- 沿着知识图谱中笔记与标签的连接路径进行延伸

---

## 🎯 两种实现方案对比

### 方案一：Auto Classifier（懒人模式）

**特点**: 静态标签库 - 依赖已有标签

**优势**:
- 一键应用，对新手友好
- 推荐效果尚可
- 不会生成额外标签"污染"系统

**缺点**:
- 只能推荐库中已有的标签
- 遇到全新主题无能为力

**使用要求**: 拥有自己的 AI API（如 OpenAI gpt-4o-mini）

---

### 方案二：Obsidian Copilot（推荐方案）⭐

**特点**: 动态标签库 - 随创新动态生长

**功能**:
- 一键调用预设好的提示词模板
- 优先使用已有标签，如不合适则新建
- 逐一解释推荐每个标签的理由
- 自动将标签添加到最合适的位置

**优势**:
- 功能强大，回答质量高
- 与 Obsidian 深度结合
- 打造沉浸式 AI 写作体验

---

## 🚀 完整部署流程（Copilot 方案）

### 前置要求

1. **已安装插件**:
   - Obsidian Copilot
   - Templater
2. **拥有 AI API**: OpenAI、Claude 等
3. **基本了解**: Obsidian 的基本操作

---

### 第一步：创建 Tags 文件

**目标**: 创建一个随 Obsidian 启动而自动更新的 Markdown 笔记，存放库中所有标签信息

#### 1.1 创建 Templater 模板

在你的模板目录中创建一个新文件，例如 `Generate Vault Tags.md`，内容如下：

```javascript
<%*
/**
 * Vault 标签统计（启动自动生成到固定位置）
 * 保存位置：Tools/Vault Tags.md
 * 忽略 Tools/ 和 tags/ 目录
 */

const targetPath = "Tools/Vault Tags.md"; // 输出文件路径
const ignoreFolders = ["_templates", ".trash", "Tools", "tags"]; // 忽略的目录

const normalize = (tag) => String(tag || "")
  .replace(/^#/, "")
  .trim()
  .replace(/\\s+/g, "-");

const tagIndex = new Map();
const files = app.vault.getMarkdownFiles();

for (const f of files) {
  // 忽略指定目录
  if (ignoreFolders.some(folder => f.path.startsWith(folder + "/"))) continue;

  const cache = app.metadataCache.getCache(f.path) || {};
  const perFileSet = new Set();

  // 1) 正文标签
  for (const t of (cache.tags || [])) {
    const name = normalize(t.tag);
    if (name) perFileSet.add(name);
  }

  // 2) frontmatter 标签
  if (cache.frontmatter && typeof cache.frontmatter === "object") {
    const raw = cache.frontmatter.tags;
    let fmTags = [];
    if (Array.isArray(raw)) {
      fmTags = raw.flatMap(x => typeof x === "string" ? x.split(",") : []);
    } else if (typeof raw === "string") {
      fmTags = raw.split(/[,\\s]+/);
    }
    for (const s of fmTags) {
      const name = normalize(s);
      if (name) perFileSet.add(name);
    }
  }

  // 汇总
  for (const tag of perFileSet) {
    if (!tag) continue;
    if (!tagIndex.has(tag)) tagIndex.set(tag, new Set());
    tagIndex.get(tag).add(f.path);
  }
}

// 排序
const entries = Array.from(tagIndex.entries())
  .sort((a, b) => (b[1].size - a[1].size) || a[0].localeCompare(b[0]));

let out = `# Vault 标签统计
生成时间：${tp.date.now("YYYY-MM-DD HH:mm")}
总标签数：${entries.length}

| 标签 | 文件数 |
|---|---|
`;

for (const [tag, set] of entries) {
  out += `| #${tag} | ${set.size} |\\n`;
}

// 创建或更新目标文件
let file = app.vault.getAbstractFileByPath(targetPath);
if (!file) {
  await app.vault.create(targetPath, out);
} else {
  await app.vault.modify(file, out);
}
%>
```

#### 1.2 配置自动执行

1. 打开 Templater 设置
2. 找到 "Startup Templates" 选项
3. 设置刚才创建的模板文件为启动时执行
4. 确保 `targetPath` 路径（如 `Tools/Vault Tags.md`）符合你的目录结构

#### 1.3 调整配置参数

根据你的实际情况修改：
- `targetPath`: 标签文件的保存位置
- `ignoreFolders`: 需要忽略的目录列表

---

### 第二步：创建提示词模板

在 Copilot 的命令库中添加名为 **【基建狂魔】Tags 推荐** 的提示词模板：

```markdown
# 🏷 Vault 标签推荐助手

## 角色
你是我的"第二大脑·标签推荐"助手，能够读取 Vault 中的标签库文件，并基于当前文档内容推荐最合适的标签。标签库文件路径固定为：[[Vault Tags]]。

## 任务

1. **读取标签库**
   - 从 [[Vault Tags]] 中获取我当前 Vault 中已有的全部标签列表（含 tag 名称与说明，如有）。

2. **分析当前文档**
   - 当前文档即正在处理的文档。
   - 提取文档的核心主题、领域、关键词。

3. **标签匹配**
   - 基于标签库与当前文档内容，推荐**3-5 个最相关的标签**。
   - 这些标签必须来自 [[Vault Tags]] 文件。
   - 若发现标签库中没有能准确覆盖文档主题的标签，可**提出新建标签建议**，并：
     - 在标签前标注 `(NEW)`。
     - 说明新建原因。
     - 推荐新建标签名称需遵循现有标签命名风格（如 kebab-case 或全小写无空格）。

4. **推荐理由**
   - 每个标签需给出 1-2 句简洁理由，说明其与当前文档的关联性。
   - 新建标签需额外说明为何标签库现有标签不足以表达该主题。

5. **插入位置**
   - 将推荐的标签，按 `#标签名` 形式（用空格分隔），添加到当前文档的**一级标题 `#` 下方的第一行**。
   - 若该位置已有标签，请在不重复的情况下追加。
   - 新建标签同样插入，但必须标注 `(NEW)` 并在理由中明确说明。

## 输出格式（严格遵循）

- **标签插入行**（最终应直接插入到文档中）
  `#tag1 #tag2 #tag3 …`
  （新建标签示例：`#new-topic(NEW)`）

- **推荐理由**（供参考，不插入文档）
  - `#tag1`：理由
  - `#tag2`：理由
  - `#new-topic(NEW)`：理由 + 新建原因

## 注意

- 不要更改文档其他部分内容。
- 标签顺序按与文档主题的相关度从高到低排序。
- 若无法匹配到任何合适标签，请明确说明，并不插入标签行。
- 新建标签必须显式告知，并提供命名建议与理由。
```

#### 配置步骤：

1. 打开 Obsidian Copilot 设置
2. 找到 "Custom Prompts" 或 "命令库" 选项
3. 添加新命令，命名为 `【基建狂魔】Tags 推荐`
4. 粘贴上述提示词内容
5. 保存设置

**重要提示**: 确保提示词中的 `[[Vault Tags]]` 路径与第一步中设置的 `targetPath` 一致！

---

### 第三步：使用一键标签推荐

#### 3.1 基本使用

1. 打开需要添加标签的笔记
2. 在 Copilot 窗口中输入 `/`
3. 选择 `【基建狂魔】Tags 推荐` 命令
4. Copilot 会自动：
   - 读取标签库
   - 分析当前文档
   - 推荐合适的标签
   - 自动插入到文档中

#### 3.2 推荐的 AI 工作流

**创作完成后，按顺序执行**:

1. **自动连接相关知识** - 使用上期文章的方法，自动建立笔记间的双向链接
2. **自动打标签** - 使用本期方法，一键添加标签
3. **后续扩展** - 根据需要添加更多自动化流程

---

## 💡 使用技巧

### 标签命名规范

- 使用 kebab-case（如 `knowledge-management`）
- 或全小写无空格（如 `obsidian`）
- 保持一致的命名风格

### 标签层级

可以使用 `/` 创建层级标签：
- `#ai/agent`
- `#ai/llm`
- `#tools/obsidian`

### 定期维护

- 每周检查一次新建的标签
- 合并相似或重复的标签
- 更新标签说明文档

---

## 🔧 故障排查

### 问题 1: Templater 脚本不执行

**解决方案**:
- 检查 Templater 插件是否启用
- 确认启动模板路径设置正确
- 重启 Obsidian

### 问题 2: Copilot 找不到标签库

**解决方案**:
- 确认 `Vault Tags.md` 文件已生成
- 检查提示词中的文件路径是否正确
- 尝试手动运行一次 Templater 模板

### 问题 3: 标签推荐不准确

**解决方案**:
- 优化提示词模板
- 增加标签库中的标签说明
- 使用更强大的 AI 模型（如 GPT-4）

---

## 📚 相关资源

### 推荐阅读

- 用 Obsidian + AI 打造沉浸式 AI 写作体验
- 知识的"基建狂魔"：AI 如何搭建你的认知高速公路

### 插件链接

- **Obsidian Copilot**: 在 Obsidian 社区插件中搜索安装
- **Templater**: 在 Obsidian 社区插件中搜索安装
- **Auto Classifier**: 备选方案，适合懒人模式

---

## 🎯 总结

通过这套系统，你可以：

✅ **解放双手** - 不再需要手动打标签  
✅ **动态生长** - 标签系统随知识库自动演进  
✅ **精准检索** - 利用多跳搜索找到深层关联  
✅ **沉浸创作** - 专注内容，让 AI 处理琐事  

**核心思想**: 在 AI 时代，把能自动化的事情交给 AI，把宝贵的精力投入到更有价值的创造中去。

---

*本文档由 OpenClaw AI 助手根据飞书知识库内容自动生成*  
*生成时间: 2026-03-02*
