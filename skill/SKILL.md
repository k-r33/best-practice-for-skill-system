---
name: system-of-skills
description: Use when designing or auditing a system of multiple skills for an agent — deciding when to build a new skill, how to organize existing skills, detecting trigger conflicts or coverage gaps, or evaluating the coherence of a multi-skill or multi-agent system. Not for single-skill authoring (use writing-skills for that).
---

# System of Skills — 系统工程方法论

## Why this exists · 为什么存在

Single-skill authoring is a solved problem. This skill handles the layer above: **how a collection of skills and multiple agents form a coherent system.**

Good work means: each skill maps to a unique (user × scenario) pair; triggering is unambiguous for any system state; changing one skill doesn't break another; the whole system can be empirically verified.

## Not for · 不处理

- How to write a single skill (description, progressive disclosure, degrees of freedom) → use `writing-skills` or Anthropic's official best-practices
- SKILL.md format spec → see official docs

## Six System Design Principles · 六条系统设计原则

**1 · Persistence Gradient · 持久性梯度**

Place knowledge by *frequency of use*, not importance:
- Needed every turn → Soul (core prompt)
- Needed for specific role/scenario → Skill  
- Needed for certain sub-tasks within a skill → `reference/` or `scripts/`

**2 · System Coherence · 系统一致性**

Every skill must trace back to a principle in the core prompt. Can't trace it? Either add the missing Soul anchor, or delete the skill — it's floating.

**3 · Regime Gating · Regime 门控**

| | Scenario changed | Same scenario |
|---|---|---|
| **User changed** | 2 independent skills | 2 independent skills |
| **Same user** | Single skill, multi-mode (or sub-skill) | Don't build a new skill |

Most common mistake: building a skill because the content feels related. Right question: "Is this (user × scenario) combination fundamentally different from existing skills?"

**4 · Orthogonal Coverage · 正交覆盖**

Every skill declares its boundary: `Not for X → [[sibling]]`. Triggering should be unambiguous for each system state — this is a design ideal for disambiguation, not a runtime constraint that forbids combining skills.

**5 · Flow · 心流**

Triggers are observable yes/no conditions, not descriptive labels. Inside SKILL.md: intent first ("why this role exists / what good work looks like"), then methodology. Intent sets direction; methodology follows naturally.

**6 · Granularity Discipline · 粒度纪律**

Standard: substantive, concise, and clear — not line count. Too coarse → split by regime. Too thin to stand alone → merge into parent or demote to reference file.

## Evaluation Loop · 评估回路（系统级）

Single-skill evaluation asks "does this piece work?" System-level evaluation asks "do all pieces together cover the space, trigger unambiguously, survive changes, and run end-to-end?"

1. **Baseline without skill** — Run target scenarios with the skill removed. No reproducible failure = no justification to build it.
2. **Trigger disambiguation regression** — For a batch of concrete system states, assert triggering is unambiguous. Fix conflicts before adding new skills.
3. **Coverage gap detection** — Enumerate the user × scenario matrix. Any cell with no skill owner is a gap: fill it or explicitly record it as intentionally uncovered.
4. **Cross-agent contract tests** — Verify artifact schema at handoff points (path, fields, format). Contracts are the rare part that can be hard-asserted; most behavioral evals use natural-language rubrics graded by model.
5. **System-wide regression** — After any change, re-run the full eval suite, not just the changed skill's cases.
6. **End-to-end + multi-model** — Run on Haiku / Sonnet / Opus. Weaker models surface ambiguous triggers and unclear intent that stronger models paper over.

*Note: no official eval runner exists; build your own. Evals mostly use natural-language rubrics graded by model — not strict unit-test assertions.*

## Pre-publish Checklist · 发布前七问（静态自洽）

1. Does each skill map to a unique (user × scenario) pair?
2. Can every skill be traced to a specific principle in the Soul?
3. Is triggering unambiguous for any system state?
4. Is SKILL.md free of heavy implementation details? (Short inline examples are fine.)
5. Does every reference file have a skill pointing to it?
6. Are experiential wisdom (reference) and invariants (law) labeled separately?
7. Does each skill clearly say one thing at the right granularity?

## Reference · 参考

- Full methodology + examples → project README / guide.html  
- Single-skill authoring → `writing-skills` skill  
- Anthropic official single-skill guide → https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/best-practices
