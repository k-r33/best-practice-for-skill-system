# 下一步操作清单（拿 star 用）

> 已推送：`0390c5a` → `github.com/k-r33/best-practice-for-skill-system` (main)
> 本文件不进版本库（已在 `.gitignore` 排除 `reviewer-responsibility.md`，如需排除本文件可自行加）。实际未排除——见末尾说明。

---

## 1. 开启 GitHub Pages（让落地页上线）

落地页 `index.html` 里的 OG/canonical URL 已填好 `https://k-r33.github.io/best-practice-for-skill-system/`，开了 Pages 就能直接访问。

**操作**：
1. 仓库页 → **Settings** → 左侧 **Pages**
2. **Source** 选 `Deploy from a branch`
3. **Branch** 选 `main` / `(root)` → **Save**
4. 等 1-2 分钟，访问 `https://k-r33.github.io/best-practice-for-skill-system/` 确认首屏渲染正常

> 上线后，README 里的 HTML 指南链接（`./guide.en.html`、`./skill-authoring-guide.html`）也会变成可直接浏览器打开的渲染页，不再是 raw 源码。

---

## 2. 填 repo Description + Topics（GitHub 搜索命中关键）

仓库主页右上角 **About** 齿轮图标。

**Description**（直接复制）：
```
How to organize agent skills as a coherent system — stop trigger conflicts and know when to build a new skill. The systems-engineering layer Anthropic's single-skill docs don't cover.
```

**Topics**（逐个添加，这些是 GitHub 搜索/Explore 的命中文本）：
```
claude-code   agent-skills   claude-skills   multi-agent   skill-system   ai-agents   llm-agents   prompt-engineering   claude
```

**Website**（About 里也可填）：`https://k-r33.github.io/best-practice-for-skill-system/`

---

## 3. 投放到社区入口（流量来源）

按优先级（已联网验证这些是真实的活跃 skill 社区）：

### 3a. 提 PR 到 awesome-claude-skills（最对口，长期流量）
- 仓库：https://github.com/ComposioHQ/awesome-claude-skills
- fork → 在合适的分类下加一行 → PR
- 建议分类：methodology / best-practices / system-design（看它现有分类选最接近的）

### 3b. Reddit r/claude（即时讨论）
标题 + 正文见下方「Social Pitch · Reddit 版」。

### 3c. Hacker News（爆量但挑剔）
标题见「Social Pitch · HN 版」。HN 受众吃"诚实 + 有洞见"，忌营销腔——README 的差异化定位（"Anthropic 教一个 skill，这个教整套系统"）就是钩子。

### 3d. Twitter/X
见「Social Pitch · Twitter 版」。

---

## 4. Social Pitch（直接复制用）

### Reddit 版（r/claude）

**标题**：
```
You have ten agent skills. They are starting to fight each other. Here's the systems-engineering layer for skill collections.
```

**正文**：
```
The official docs teach you how to write ONE skill well — progressive disclosure, description fields, the craft. Nobody teaches what happens when you have ten of them and they start fighting.

Sound familiar?
- two skills both fire on the same request, the agent picks the wrong one (trigger conflict)
- adding more skills made the agent WORSE, not better (skill shadowing — this is a measured effect)
- duplicate skills loading every turn, wasting your context window
- you edit one skill and something unrelated breaks

I wrote down the missing layer: a systems-engineering methodology for skill COLLECTIONS. Six principles (persistence gradient, system coherence, regime gating, orthogonal coverage, flow, granularity) + an empirical eval loop. It also ships as an installable agent skill (works in Claude Code and any agent that reads ~/.claude/skills/).

The one question it answers that no single-skill guide can: WHEN should you build a new skill vs merge into an existing one?

Install:  cp -r skill/ ~/.claude/skills/system-of-skills/

Full guide + repo: https://github.com/k-r33/best-practice-for-skill-system

Happy to hear where the principles are wrong — they're meant to be sharpened, not worshipped.
```

### HN 版

**标题**（HN 偏直白，忌标题党）：
```
Show HN: A systems-engineering methodology for organizing agent skill collections
```
正文用上面 Reddit 版，但去掉 emoji 和营销句，语气更平实。HN 评论者会挑刺——用 README 里"for single-skill authoring see Anthropic docs, this covers the system layer only"的边界声明来挡"这个早就有了"的质疑。

### Twitter 版

```
Anthropic's docs teach you to write ONE agent skill well.

Nobody teaches what happens when you have ten — and they start fighting over the same request, shadowing each other, breaking when you edit one.

Wrote down the missing systems-engineering layer ↓

github.com/k-r33/best-practice-for-skill-system
```
配图建议：落地页的「系统状态图」截图（两个 skill 冲突 + 一个 no-owner 虚线节点），或 README 的「codebase → skill-system 映射表」截图。

---

## 5. 发布后自查（可选，确认 SEO 生效）

Pages 上线 1-2 天后：
- 在 Google 搜索 `site:k-r33.github.io/best-practice-for-skill-system` 确认被收录
- 用 [Open Graph debugger](https://www.opengraph.xyz/) 贴落地页 URL，确认分享卡片预览正常（标题/描述/无图——目前无 og:image，见下方「可选增强」）
- 搜 `organize agent skills` / `skill trigger conflict` 看是否开始浮现（新站收录 + 排名需要时间，别急）

---

## 6. 可选增强（之后想做的话）

- **og:image**：目前 OG 卡片无图，分享时只有文字。生成一张 1200×630 的图（可用落地页首屏截图，或映射表）放到 repo，在 `index.html` 的 `<head>` 加 `<meta property="og:image" content="...">`。这是分享转化率的最大单点提升。
- **LICENSE 文件**：JSON-LD 里声明了 MIT，但 repo 根目录还没有 `LICENSE` 文件。加一个标准 MIT LICENSE 文本（GitHub 仓库页 Add file → Choose license template → MIT）。
- **中文 README 链接**：README 顶部已有 `[English](./README.md) | [中文](./README.zh.md)` 切换，确认 `README.zh.md` 内容也同步了本次的痛点/映射表（目前是旧版，如需我可同步）。

---

## 关于本文件

`NEXT-STEPS.md` 目前**会**被 git 跟踪（未在 .gitignore 里）。如果你不想把它公开到 repo，操作完步骤后删掉，或加到 `.gitignore`：
```
echo "NEXT-STEPS.md" >> .gitignore
```
