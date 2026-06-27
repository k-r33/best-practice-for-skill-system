[English](./README.md) | [中文](./README.zh.md)

---

# System of Skills - How to organize Claude Code skills into a coherent system

Anthropic's docs teach you to write **one** good skill. They do not teach you what happens when you have ten of them.

Most people discover this the hard way: they add skills one by one, each one looks fine, and at some point the agent gets *worse*, not better. Two skills fight over the same request. A third silently no-ops. You edit one file and something unrelated breaks. You can't tell why, because the bug is not in any single skill. It's in the **system**.

This project is the missing layer: how to **organize agent skills** as a coherent whole, detect **skill trigger conflict** before it ships, and decide **when to build a new skill** versus merge, demote, or delete.

> Writing one skill is like writing one function. Nobody taught you to architect the whole codebase.

A skill collection is a codebase. This is its engineering discipline.

---

## If any of this sounds familiar

- "I added more skills and my agent got **worse**, not better." (This is real. It is called *skill shadowing*, and academic eval work has measured it.)
- "Two of my skills both fire on the same request and the agent picks the wrong one." (**skill trigger conflict**)
- "Duplicate skill content is loading on every turn and **wasting my context window**." (a common complaint on agent-tooling forums)
- "I edited one skill and something unrelated broke elsewhere." (cascading breakage)
- "One of my skills grew to handle everything, and now its trigger doesn't mean anything." (the god-skill problem)
- "I don't know **when to build a new skill** vs. extend an existing one."

Every piece looks right in isolation. The assembled system is broken, and you can't tell why.

---

## What this is (and what it isn't)

**It is** a systems-engineering methodology for a *collection* of agent skills. Six interlocking design principles plus an empirical evaluation loop that asks: do all the pieces together cover the user×scenario space, trigger unambiguously, survive changes, and run end-to-end?

**It is NOT** another guide on how to author a single skill. Description fields, progressive disclosure, degrees of freedom, the SKILL.md format. Anthropic's official best-practices already cover that, and cover it well. Go read them first if you're writing your first skill.

The differentiator, stated plainly: **Anthropic teaches the craft of one skill. This teaches the architecture of the whole collection.** When skills conflict, when to NOT build a new skill, how to verify the assembled system rather than each piece. That layer did not exist as a written methodology before. It does here.

### A skill collection is a codebase

| Codebase concept | What it becomes in a skill system |
| --- | --- |
| **module** - a unit of code with one job | **skill** - a unit of agent behavior with one trigger |
| **interface** - how callers address the module | **trigger** - the observable yes/no condition that loads the skill |
| **contract** - what the module promises to return | **output shape** - what the skill leaves for the next skill or turn |
| **kernel / core lib** - shared primitives every module relies on | **core prompt** - the stable principles every skill traces back to |
| **regression test** - does this piece still work after a change | **eval loop** - does the whole collection still trigger cleanly and run end-to-end |

Treat your skills like code. They have grown past the point where anything else works.

---

## The six principles + eval loop

1. **Persistence Gradient** - place knowledge by *frequency of use*, not importance. Every-turn goes in the core prompt. Role-specific becomes a skill. Sub-task drops to a `reference/` file.
2. **System Coherence** - every skill traces back to a principle in the core prompt. Can't trace it? Delete it. It's floating.
3. **Regime Gating** - the decision rule for *when to build a new skill*. Build one only when the (user × scenario) combination is fundamentally different from existing skills. Most "new skills" are actually a mode of an existing one.
4. **Orthogonal Coverage** - every skill declares its boundary: `Not for X → [[sibling]]`. Triggering should be unambiguous for each system state.
5. **Flow** - triggers are observable yes/no conditions, not descriptive labels. Intent first inside SKILL.md, then methodology.
6. **Granularity Discipline** - too coarse, split by regime. Too thin to stand alone, merge into the parent or demote to a `reference/` file.

**+ Evaluation Loop** - single-skill eval asks "does this piece work?" System-level eval asks "do all pieces together cover the space, trigger unambiguously, survive changes, and run end-to-end?" Baseline without the skill first. No reproducible failure, no justification to build it.

Full definitions, the regime-gating matrix, and a worked multi-agent example live in the guides.

---

## Install

Use this methodology from inside Claude Code:

```bash
cp -r skill/ ~/.claude/skills/system-of-skills/
```

Once installed, Claude applies these principles when you ask things like "should I build a new skill?", "why is my agent behaving unpredictably?", or "how should I organize these skills?"

---

## Who this is for

- You have shipped a few skills and they are starting to **fight each other** on the same request.
- You are building a **multi-agent system** and need more than "write good skills" advice.
- You want to change one skill **without breaking something elsewhere** in the system.

---

## Read the guides

- **[System of Skills - Full Methodology (English)](./guide.en.html)** - the complete guide, with a worked coding-agent example (a planner orchestrating write-tests / review-diff / commit / debug subagents).
- **[完整指南（中文）](./skill-authoring-guide.html)** - Chinese edition.
- **[Anthropic official best-practices](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/best-practices)** - the complement, not the competitor. Read both. They cover different layers.

> Tip: enable GitHub Pages (Settings → Pages → main branch) to open the HTML guides directly in your browser instead of reading raw source.

---

## Why I built this

I hit the skill-shadowing problem on a real multi-agent codebase: two skills firing on the same turn, the planner building on a misread intent, and no single skill to blame because every piece passed its own review. The fix was not "write better skills." The fix was a design discipline for the collection. I wrote it down so I would stop re-deriving it, and so others could stress-test it.

---

## Star and share

If a collection of agent skills is part of your workflow, this layer is coming for you eventually. Star it now so you find it when it does. Issues and counterexamples welcome; the principles are meant to be sharpened, not worshipped.

*License: this repo's methodology text is MIT-friendly for practical use. The ideas are meant to spread; cite back if they help.*
