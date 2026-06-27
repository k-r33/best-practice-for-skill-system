[English](./README.md) | [中文](./README.zh.md)

---

# System of Skills · 如何把一组 agent skills 组织成一个自洽的系统

Anthropic 的官方文档教你怎么写**一个**好的 skill。它们没教你：当你有了十个 skill 之后会发生什么。

多数人是撞了墙才发现这个问题的：一个一个地加 skill，每个单独看都没问题，但到了某个点 agent 反而变*差*了。两个 skill 抢同一个请求，第三个悄悄空转，你改了一个文件、远处另一个功能莫名其妙断了。你说不清为什么，因为 bug 不在任何一个单独的 skill 里——它在**系统**里。

这个项目就是那层缺失的东西：如何把 **agent skills** 当成一个整体来组织，在上线前就发现 **skill trigger 冲突**，并决定**何时该建一个新 skill**，何时该合并、降级或删掉。

> 写一个 skill 约等于写一个函数。没人教过你怎么架构整个代码库。

一组 skill 就是一个代码库。这里讲的是它的工程纪律。

---

## 这些情况你熟吗

- "我加了更多 skill，agent 反而**变差**了，不是变好。"（这是真实存在的现象，叫 *skill shadowing*，已有学术评估工作测量过它。）
- "我有两个 skill 在同一个请求上都会触发，agent 选了错的那个。"（**skill trigger 冲突**）
- "重复的 skill 内容每轮都在加载，**白耗我的 context window**。"（agent 工具论坛上的常见抱怨）
- "我改了一个 skill，别处一个不相关的东西坏了。"（级联破坏）
- "我的某个 skill 长成了什么都管，现在它的 trigger 已经不指代任何东西了。"（god-skill 问题）
- "我不知道**何时该建一个新 skill**，何时该扩展现有的那个。"

每个零件单看都对。装在一起的系统是坏的，而你说不清为什么。

---

## 这是什么（以及它不是什么）

**它是**一套针对 agent skill **集合**的系统工程方法论。六条相互咬合的设计原则，加一套实证评估回路，回答的问题是：所有零件装在一起，是否覆盖了完整的 user×scenario 空间、触发是否消歧、改动后是否撑得住、能否端到端跑通？

**它不是**又一份"怎么写好单个 skill"的指南。description 字段、progressive disclosure、degrees of freedom、SKILL.md 格式——这些 Anthropic 官方 best-practices 已经讲透，而且讲得好。如果你在写第一个 skill，先去读它。

把差异讲明白：**Anthropic 教的是单个 skill 的工艺；这里教的是整个集合的架构。** skill 之间冲突了怎么办、什么时候不该建新 skill、如何验证装在一起的系统而不是单个零件。这一层此前没有成文的方法论。这里有了。

### 一组 skill 就是一个代码库

| 代码库里的概念 | 在 skill 系统里它变成什么 |
| --- | --- |
| **module** - 只干一件事的代码单元 | **skill** - 只有一个 trigger 的 agent 行为单元 |
| **interface** - 调用方如何寻址这个模块 | **trigger** - 加载这个 skill 的可观测 yes/no 条件 |
| **contract** - 这个模块承诺返回什么 | **output shape** - 这个 skill 给下一个 skill 或下一轮留下什么 |
| **kernel / 核心库** - 所有模块都依赖的共享原语 | **core prompt** - 每个 skill 都能追溯回去的稳定原则 |
| **regression test** - 改动后这个零件还能用吗 | **eval loop** - 整个集合还能干净触发、端到端跑通吗 |

把你的 skill 当代码对待。它们早就过了"用别的办法也行"的阶段。

---

## 六条原则 + 评估回路

1. **持久性梯度（Persistence Gradient）** - 按*使用频率*而非重要性来安放知识。每轮都要用的进 core prompt；角色专属的做成 skill；只在某个子任务才用的下沉到 `reference/` 文件。
2. **系统一致性（System Coherence）** - 每个 skill 都能追溯到 core prompt 里的一条原则。追不回去？删掉它，它是浮空的。
3. **Regime 门控（Regime Gating）** - *何时该建新 skill* 的判定规则。只有当 (user × scenario) 的组合与已有 skill 有根本性不同时才建。多数"新 skill"其实只是某个已有 skill 的一个 mode。
4. **正交覆盖（Orthogonal Coverage）** - 每个 skill 都声明自己的边界：`Not for X → [[兄弟 skill]]`。对任意系统状态，触发都应当是消歧的。
5. **心流（Flow）** - trigger 是可观测的 yes/no 条件，不是描述性的标签。SKILL.md 里意图在前、方法论在后。
6. **粒度纪律（Granularity Discipline）** - 太粗就按 regime 拆。太薄撑不起独立存在就并回父 skill，或降级为 `reference/` 文件。

**+ 评估回路（Evaluation Loop）** - 单 skill 评估问"这个零件能用吗"。系统级评估问"所有零件装在一起，是否覆盖全、触发准、改一处不坏他处、串成 pipeline 跑得通"。先把 skill 摘掉跑一遍建立基线。没有可复现的失败，就没有建它的理由。

完整定义、Regime 决策矩阵、以及一个贯穿到底的多 agent 实例，都在指南里。

---

## 安装

从 Claude Code（或任何读取 `~/.claude/skills/` 的 agent）里用上这套方法论：

```bash
cp -r skill/ ~/.claude/skills/system-of-skills/
```

装好之后，你问"我该建个新 skill 吗"、"为什么我的 agent 行为飘忽"、"这些 skill 该怎么组织"之类问题时，Claude 会自动应用这套方法论。

---

## 适合谁

- 你已经发了几个 skill，它们开始在同一个请求上**互相打架**了。
- 你在搭一个**多 agent 系统**，光"写好每个 skill"的建议不够用。
- 你想改一个 skill，**不想弄坏别处**的某条链路。

---

## 读指南

- **[System of Skills - 完整方法论（英文）](./guide.en.html)** - 完整指南，带一个贯穿的编码 agent 实例（planner 编排 write-tests / review-diff / commit / debug 等 subagent）。
- **[完整指南（中文）](./skill-authoring-guide.html)** - 中文版。
- **[Anthropic 官方 best-practices](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/best-practices)** - 是互补，不是竞争。两份都读，它们讲的是不同的层。

> Tip：开启 GitHub Pages（Settings → Pages → main 分支），就能在浏览器里直接打开 HTML 指南，而不用看 raw 源码。

---

## 为什么做这个

我在一个真实的多 agent 代码库上撞上了 skill-shadowing 问题：两个 skill 在同一轮都触发了，planner 在一个误读的意图上建计划，却没有哪个单独的 skill 该背锅——因为每个零件单看都通过了它自己的 review。修法不是"把 skill 写得更好"，修法是给整个集合一套设计纪律。我把它写下来，是为了不再每次重新推导一遍，也为了让别人能去检验、反驳它。

---

## Star 和分享

如果一组 agent skill 是你工作流的一部分，这一层迟早会找上你。现在 star 它，等那天来了你能找到。欢迎提 issue 和反例——这些原则是用来被打磨的，不是用来被供奉的。

---

**作者：[k-r33](https://github.com/k-r33) @ [X32 Studio](https://github.com/X32Studio)** · MIT 协议。致谢与引用见 [AUTHORS.md](./AUTHORS.md)。
