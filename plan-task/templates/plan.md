# <Category> - Plan: <Title>

Concrete steps implementing the decisions from `research.md`. Each step is **one commit** and ends green under the project's verification gates (<project's verification gates — e.g. format check, linter, docs build, test suite; fill from the project's agent instructions (AGENTS.md / CLAUDE.md) or build config>).

Decisions baked in (as resolved by the user's `Note:` in `research.md`):

- D1: <decision as settled>
- D2: <decision as settled>

Guiding principle (from `research.md`): <restate if there is one — delete otherwise>

Executor per step: who implements it — `lead`, or a worker tier sized to the step (`worker/light` | `worker/standard` | `worker/strong`; see the skill's *Implementation: lead and workers*). Decided here so it is reviewed with the steps.

---

## Step 1 — <title> (<decision refs, e.g. D1, D8>)

Goal: <what this step achieves, in one line.>

Executor: <`lead` | `worker/light` | `worker/standard` | `worker/strong`> — <why, in one clause: e.g. "single-file, fully specified above" / "multi-file but bounded by D3" / "riskiest step per research; needs the planning context">

Changes:

- <`file:line` — concrete change>
- <`file:line` — concrete change>

Verify: <project's verification gates>.

---

## Step 2 — <title> (<decision refs>)

Goal: <…>

Executor: <…> — <why>

Changes:

- <…>

Verify: <project's verification gates>.

---

## Out-of-this-plan items

- <deferred item> — <why, and the condition that would bring it back>

## Sequencing rationale

<Why the steps are in this order: which build the baseline, which depend on it, which are independent. Name the independent steps explicitly — those are the only ones the lead may run in parallel. Close with the invariant: each step ends green; one commit per step, made by the lead.>
