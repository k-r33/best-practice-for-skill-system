---
name: system-of-skills
description: Use when designing or auditing a system of multiple skills for an agent — organizing existing skills, deciding whether a proposed skill belongs in an existing system or warrants a new regime (system-level placement, not single-skill authoring), detecting trigger conflicts or coverage gaps, or evaluating whether the assembled system triggers unambiguously and covers the user×scenario space. Not for writing a single skill (use writing-skills for that).
---

# System of Skills — 系统工程方法论

## Why this exists · 为什么存在

Single-skill authoring is a solved problem. This skill handles the layer above: **how a collection of skills and multiple agents form a coherent system.**

Good work means: each skill maps to a unique (user × scenario) pair; triggering is unambiguous for any system state; changing one skill doesn't break another; the whole system can be empirically verified. **Good work does NOT mean:** grouping skills by topic similarity; building a skill because the set "feels incomplete"; or a trigger that needs a paragraph of prose to disambiguate.

## What failure looks like · 失败长什么样

Make the cost concrete before you start — the bill arrives in production, not in a design doc:

- **Trigger conflict** — You ship two skills that both fire on the same user turn. At 02:00 the agent picks the wrong one, emits the wrong handoff artifact, and the planner builds a plan on a misread intent. By morning the system has taken hundreds of actions on a false premise. The post-mortem names whoever let the triggers overlap.
- **Coverage gap** — Friday 17:00, under release pressure, the agent hits a real state no skill owns. It reaches for "probably fine / leave it for the next pass," improvises an answer, and the deploy ships. Monday the regression suite catches it — but the Friday deploy already reached users. Whoever left that matrix cell without an owner signed off on it.
- **Floating skill** — A skill that can't be traced to a Soul principle has nothing tying it down. You delete a sibling's reference; this skill's trigger still points at the deleted file. Three weeks later an audit shows it has been silently no-op'ing on every call since — whoever demoted the reference without checking inbound pointers owns that downtime.

You sign off on the gaps and overlaps you don't surface. Carry that.

**Soul anchor:** `system-as-kernel` — skills are modules, triggers are interfaces, outputs are contracts, the core prompt is the kernel. **Piece-level correctness ≠ system-level correctness.** This skill exists to serve that Soul principle: it is the design discipline that makes the assembled system coherent, not a guide to any single piece. (Traces to Soul principle `system-as-kernel`.)

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

Most common mistake: building a skill because the content feels related. Right question: "Is this (user × scenario) combination fundamentally different from existing skills?" **Operational test:** two skills are the same regime iff they share trigger conditions AND share a standard of good work — otherwise different regimes.

**4 · Orthogonal Coverage · 正交覆盖**

Every skill declares its boundary: `Not for X → [[sibling]]`. Triggering should be unambiguous for each system state — this is a design ideal for disambiguation, not a runtime constraint that forbids combining skills.

**5 · Flow · 心流**

Triggers are observable yes/no conditions, not descriptive labels. Inside SKILL.md: intent first ("why this role exists / what good work looks like"), then methodology. Intent sets direction; methodology follows naturally.

*Intent is direction; pressure is fuel.* A skill owns only **method**; the agent's **posture & responsibility** is a separate concern — it must be injected wherever any audit/design skill fires (at Soul level for an always-on agent, or loaded as `reference/responsibility.md` when this skill activates), never assumed to live inside a skill body. The full posture system prompt (concrete cost of failure / you are the post-mortem defendant / banned self-soothing phrases / never lower severity to hide a finding) lives there — not duplicated here.

**6 · Granularity Discipline · 粒度纪律**

Standard: substantive, concise, and clear — not line count. Too coarse → split by regime. Too thin to stand alone → merge into parent or demote to reference file.

## Wisdom & experience · 经验模式（参考·非约束）

The principles above are design invariants (law). The items below are **observations and heuristics — reference, not constraint.** They hold often enough to act on, but are not definitional; treat them as defeasible judgment, label them separately from the invariants, and re-check time-sensitive ones.

- *"Building a skill because the content feels related"* is the most common mistake (Principle 3) — a high-frequency observation, not part of the regime definition.
- *"Weaker models surface ambiguous triggers that stronger models paper over"* (Eval step 6) — empirical, model-dependent; reverses as models change.
- *"No official eval runner exists"* → **time-sensitive, partially superseded**: skill-creator v2 (2026-03) added in-skill Eval. Re-verify before relying on it.
- Most behavioral evals use natural-language rubrics graded by model — a current best practice, not a law of nature.

## Evaluation Loop · 评估回路（系统级）

Single-skill evaluation asks "does this piece work?" System-level evaluation asks "do all pieces together cover the space, trigger unambiguously, survive changes, and run end-to-end?"

1. **Baseline without skill** — Run target scenarios with the skill removed. No reproducible failure = no justification to build it.
2. **Trigger disambiguation regression** — For a batch of concrete system states, assert triggering is unambiguous. Observation recipe: have the agent emit the single skill it would activate before acting, and record that as the firing observation (reconciles should-fire vs actually-fired, no harness hook needed). Inject pressure scenarios (time / sunk cost / authority) — this also tests whether responsibility holds, or whether the agent reaches for a banned phrase ("probably fine") to skip a skill that should fire. Fix conflicts before adding new skills.
3. **Coverage gap detection** — Enumerate the user × scenario matrix. Reuse step 2's probing: collect each skill's declared trigger, probe pair-overlap boundaries, give each skill a must-fire and must-not-fire input. 0 skills firing = gap; multiple = conflict. Stop when every cell has an owner or is explicitly "intentionally uncovered." Fill gaps or record them as intentionally uncovered.
4. **Cross-agent contract tests** — Verify artifact schema at handoff points (path, fields, format). Version artifacts with `contract_version`; breaking changes bump version + are gated by the suite. Declare failure handling (malformed artifact / dead-end routing) as the planner skill's responsibility. Contracts are the rare part that can be hard-asserted; most behavioral evals use natural-language rubrics graded by model.
5. **System-wide regression** — After any change, re-run the full eval suite, not just the changed skill's cases. Agent-authored seed/ changes are skill changes too — they must re-pass this suite before taking effect; trigger/Not-for changes additionally need a recorded diff, constitution-adjacent ones a human gate.
6. **End-to-end + multi-model** — Run on Haiku / Sonnet / Opus. Weaker models surface ambiguous triggers and unclear intent that stronger models paper over.

*Note on eval runners (mid-2026): Anthropic's best-practices still states there's no built-in runner, but **skill-creator v2 (2026-03)** added Create/Eval/Improve/Benchmark modes whose Eval does `should_trigger=true/false` regression + blind A/B — single-skill only. The system-level regression above (trigger disambiguation, coverage, contracts, e2e) remains this methodology's extrapolation; build your own. Evals mostly use natural-language rubrics graded by model — not strict unit-test assertions.*

## Pre-publish Checklist · 发布前七问（静态自洽）

1. Does each skill map to a unique (user × scenario) pair?
2. Can every skill be traced to a specific principle in the Soul?
3. Is triggering unambiguous for any system state?
4. Is SKILL.md free of heavy implementation details? (Short inline examples are fine.)
5. Does every reference file have a skill pointing to it?
6. Are experiential wisdom (reference) and invariants (law) labeled separately?
7. Does each skill clearly say one thing at the right granularity?

## Reference · 参考

- Full methodology + examples → project README / `skill-authoring-guide.html` (中文) / `guide.en.html` (English)
- Deep dive on posture & responsibility → `reference/responsibility.md` (in this skill)
- Single-skill authoring → `writing-skills` skill
- Anthropic official single-skill guide → https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/best-practices
