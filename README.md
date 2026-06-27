[English](./README.md) | [中文](./README.zh.md)

---

# System of Skills

**Designing agent skills is a systems-engineering problem — not just a craft problem.**

Every guide teaches you to write a good *single* skill: progressive disclosure, description fields, degrees of freedom. Anthropic's official docs cover that well. But that's teaching you to write a good function.

A skill collection forms your agent's **capability system**. Once you have ten well-written skills together, a different class of problem emerges:

- **Trigger conflicts** — Two skills both want to handle the same request. The agent doesn't know which authority to follow.
- **Coverage gaps** — Certain task types have no skill to handle them. The agent improvises.
- **Cascading breakage** — You edit one skill; something unrelated breaks elsewhere.
- **God skills** — One skill grows to handle everything. Its trigger no longer means anything.

Every piece looks right in isolation. The assembled system is broken — and you can't tell why.

This is not a single-skill quality problem. It's an absence of **system design**: how to organize skills, coordinate agents, and — most critically — know when you should *not* build a new skill at all.

Writing one skill ≈ writing one function. Nobody taught you to architect the whole codebase.

---

## 📖 Read the Guide

→ **[System of Skills — Full Methodology Guide (English)](./guide.en.html)**  
→ [完整指南（中文）](./skill-authoring-guide.html)

Six interlocking design principles + an empirical evaluation loop, illustrated with a real coding-agent example (planner orchestrating write-tests / review-diff / commit / debug subagents).

> 💡 Enable GitHub Pages (Settings → Pages → main branch) to open the guides directly in your browser.

---

## 🔧 Install the Skill

Use this methodology from within Claude Code:

```bash
cp -r skill/ ~/.claude/skills/system-of-skills/
```

Once installed, Claude applies these principles when you ask "should I build a new skill?", "why is my agent behaving unpredictably?", or "how should I organize these skills?"

---

## Who This Is For

- You've added skills one by one — and they're starting to fight each other
- You're building a multi-agent system and need more than "write good skills"
- You want to change one skill without breaking something else

---

*For how to write a single skill well → [Anthropic's official best-practices](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/best-practices). This project covers the system layer only.*
