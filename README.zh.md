[English](./README.md) | [中文](./README.zh.md)

---

# Skill 系统工程方法论

**Agent skill 设计是一个系统工程问题，而不仅仅是写好一个 skill。**

现有的指南——progressive disclosure、description 怎么写、degrees of freedom 怎么留——教的都是单个 skill 的工艺。Anthropic 官方把这部分讲透了。但它教的是怎么写好一个**函数**。

Skill 的集合，构成了 agent 的**能力系统**。当你把十个写得都很好的 skill 装在一起，系统级的问题才开始出现：

- **Trigger 打架** — 同一个请求，两个 skill 同时想接管，agent 不知道听谁的
- **覆盖空白** — 某类任务整段没有 skill 接管，agent 只能裸跑硬撑
- **改一处坏他处** — 动了一个 skill，远处另一条链路莫名其妙断了
- **万能 skill** — 一个 skill 越写越胖，trigger 早就说不清什么时候该用它

每个零件单看都对，装在一起就是错——而你说不清问题出在哪。

这不是某个 skill 没写好。是缺了一套**组织它们、协调它们**的方法——以及，更要命的，知道**什么时候根本就不该再建一个新 skill**。

会写一个 skill，约等于会写一个函数。没人教你怎么架构整个代码库。

---

## 📖 读指南

→ **[完整指南（中文）](./skill-authoring-guide.html)**  
→ [Full Guide (English)](./guide.en.html)

六条相互咬合的系统设计原则 + 一套实证评估回路，用一个贯穿到底的编码 agent 实例（planner 编排，write-tests / review-diff / commit / debug 等 subagent 各司其职）带你走完——让你一眼看出自己的 skill 系统哪里在打架、哪里有黑洞、哪个根本就不该建。

> 💡 建议开启 GitHub Pages（Settings → Pages → 选 main 分支），这样上面的链接可以直接在浏览器打开渲染好的页面。

---

## 🔧 安装 Skill

把这套方法论打包成了一个 Claude Code skill，让你在设计自己的 skill 系统时能直接用上：

```bash
cp -r skill/ ~/.claude/skills/system-of-skills/
```

安装后，当你问"要不要建一个新 skill"、"为什么我的 agent 行为飘忽"、"这些 skill 怎么组织"，Claude 会自动应用这套方法论。

---

## 适合谁

- skill 一个个加进去，开始互相打架了
- 在设计多 agent 系统，光"写好每个 skill"解决不了问题
- 改一个 skill，不想再莫名其妙弄坏另一条链路

---

*单个 skill 怎么写，见 [Anthropic 官方 best-practices](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/best-practices)。本项目只讲系统层。*
