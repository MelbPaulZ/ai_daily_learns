# AI 每日学习摘要 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 创建 `/daily` 斜杠命令，抓取 Anthropic、OpenAI、Google、xAI、Qwen、DeepSeek 的最新动态，整理为中文学习清单，并保存到本地日志文件。

**Architecture:** 单一 Claude Code 项目本地命令文件（`.claude/commands/daily.md`），Claude 读取后执行 WebSearch、格式化输出、写入日志。日志以日期命名存放在 `logs/` 目录，`logs/README.md` 自动维护历史索引。

**Tech Stack:** Claude Code custom commands (Markdown)、WebSearch、Write tool

---

## Task 1: 创建命令目录并写入 `/daily` 命令文件

**Files:**
- Create: `.claude/commands/daily.md`

- [ ] **Step 1: 创建 `.claude/commands/` 目录**

```bash
mkdir -p .claude/commands
```

- [ ] **Step 2: 创建 `.claude/commands/daily.md`**

写入以下完整内容：

```markdown
# AI 每日学习摘要

执行以下步骤生成今日 AI 学习清单。**严格按顺序执行，不要跳过任何步骤。**

---

## Step 1：确定今日日期

获取当前日期，格式 YYYY-MM-DD，以及年份和月份（英文），用于后续搜索词。

---

## Step 2：执行 6 组 WebSearch

依次搜索以下 6 个来源，每组搜索词中包含当前年月，确保获取最新内容。搜索时使用英文关键词：

1. `Anthropic Claude {YEAR} {MONTH} new model features announcement`
2. `OpenAI GPT o-series {YEAR} {MONTH} new features release update`
3. `Google Gemini {YEAR} {MONTH} new update release announcement`
4. `xAI Grok {YEAR} {MONTH} new features update`
5. `Qwen Alibaba {YEAR} {MONTH} new model release update`
6. `DeepSeek {YEAR} {MONTH} new model update release`

每组搜索后记录关键结果，继续下一组。

---

## Step 3：分析结果，按优先级分类

根据以下标准将搜索结果分为三档：

| 优先级 | 判定标准 |
|--------|----------|
| 🔥 必须了解 | 直接影响现有使用方式的变化：重大能力跃升、API 不兼容变更、定价变化、重要新模型发布 |
| 📚 值得了解 | 新功能上线、新产品发布、重要更新，但不紧急 |
| 👀 留意即可 | 小版本更新、次要公告、性能小幅优化 |

若某来源当天无新动态，在对应位置注明"今日无重要更新"。
若搜索结果整体较少，降低过滤标准，确保 📚 栏至少有 2 条内容。

---

## Step 4：生成学习清单

按以下格式生成 Markdown 内容。**所有描述文字用中文**，模型名称、版本号、专有名词保留英文。

```
## 📅 AI 每日学习清单 — {YYYY-MM-DD}

### 🔥 今天必须了解（1-3 条）
> 对你当前使用场景影响最直接的更新

- [ ] **[公司/模型名]** 用一句话描述这件事
  - 为什么重要：一句话说明对你有什么具体影响

### 📚 值得深入了解（3-5 条）
> 重要但不紧急，可以安排时间细看

- [ ] **[公司/模型名]** 用一句话描述这件事
  - 简要说明：一句话背景

### 👀 留意即可（0-3 条）
> 知道有这件事就够了，暂不需要深入

- [ ] **[公司/模型名]** 用一句话描述这件事

---
*生成时间：{YYYY-MM-DD HH:MM}*
```

---

## Step 5：保存日志文件

**5a. 写入当日摘要**

将 Step 4 生成的完整内容写入 `logs/{YYYY-MM-DD}.md`。
若文件已存在，覆盖（以本次运行为准）。

**5b. 更新或创建 `logs/README.md`**

检查 `logs/README.md` 是否存在：

- 若不存在，创建文件，内容如下（将今日记录填入表格）：

```markdown
# AI 每日学习日志

每日执行 `/daily` 命令自动更新。

## 历史记录

| 日期 | 亮点 |
|------|------|
| [{YYYY-MM-DD}]({YYYY-MM-DD}.md) | {🔥栏第1条标题（不含公司名前缀）} |
```

- 若已存在，在表格 `| 日期 | 亮点 |` 和 `|------|------|` 两行之后、现有数据之前，插入今日新行：

```
| [{YYYY-MM-DD}]({YYYY-MM-DD}.md) | {🔥栏第1条标题（不含公司名前缀）} |
```

若 🔥 栏为空，亮点列填写 📚 栏第 1 条标题。

---

## Step 6：在终端展示

将 Step 4 生成的完整学习清单内容输出到终端，让用户直接看到。
```

- [ ] **Step 3: 验证文件内容**

确认 `.claude/commands/daily.md` 文件存在且可读：

```bash
ls -la .claude/commands/daily.md
```

Expected: 文件存在，大小 > 0

- [ ] **Step 4: 提交**

```bash
git add .claude/commands/daily.md
git commit -m "feat: add /daily command for AI news digest"
```

---

## Task 2: 冒烟测试——运行 `/daily` 验证端到端流程

**Files:**
- Auto-created: `logs/YYYY-MM-DD.md`
- Auto-created: `logs/README.md`

- [ ] **Step 1: 在 Claude Code 中运行命令**

在此项目目录下输入：

```
/daily
```

- [ ] **Step 2: 验证终端输出格式**

确认终端输出包含以下所有元素：
- 标题行：`## 📅 AI 每日学习清单 — 今天的日期`
- 三个分类：`🔥 今天必须了解`、`📚 值得深入了解`、`👀 留意即可`
- 至少 3 条 `- [ ]` 格式的条目
- 底部的生成时间戳

- [ ] **Step 3: 验证日志文件已创建**

```bash
ls logs/
```

Expected 输出（示例，日期为今天）：
```
2026-03-29.md
README.md
```

- [ ] **Step 4: 验证日志文件内容正确**

```bash
cat logs/$(date +%Y-%m-%d).md
```

Expected：内容与终端输出一致，包含三个分类标题和条目。

- [ ] **Step 5: 验证 README.md 索引已更新**

```bash
cat logs/README.md
```

Expected：包含今天的日期行和亮点摘要，格式为表格。

- [ ] **Step 6: 提交日志（可选）**

若希望将首次日志纳入版本控制：

```bash
git add logs/
git commit -m "docs: add first daily AI digest log"
```

若不希望追踪日志文件，跳过此步骤并在 `.gitignore` 中添加 `logs/`（见 Task 3）。

---

## Task 3: （可选）配置 `.gitignore`

根据是否需要版本追踪日志文件，二选一：

**若不追踪日志（推荐：日志属于本地个人记录）：**

- [ ] **Step 1: 创建或更新 `.gitignore`**

```bash
echo "logs/" >> .gitignore
git add .gitignore
git commit -m "chore: ignore logs directory"
```

**若需要追踪日志（适合做知识库）：**

- [ ] **Step 1: 保持现状，无需额外操作**

日志文件在 Task 2 Step 6 中已提交，后续每次运行后手动 `git add logs/ && git commit` 即可。
