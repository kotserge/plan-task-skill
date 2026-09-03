---
name: plan-task
description: Plan non-trivial engineering work in the open using a staged research → plan → implementation flow backed by per-task docs/ folders, reviewed by the user between stages. Use when starting a feature, bug fix, refactor, or migration big enough to warrant a written plan before coding, when capturing a shelved idea, or when auditing an existing body of code (a findings-based review with follow-up passes). Provides per-category Markdown templates (description/research/plan, why/results for migrations, review/follow-up for reviews) and the review-gate conventions that tie them together. Implementation runs lead-and-workers: the model that planned stays in charge, and each plan step names its executor (the lead, or a worker tier sized to the step's complexity) so delegation is reviewed with the plan.
---

# plan-task

A lightweight, paper-trail-first way to take a non-trivial change from "here's the problem" to "here's the merged code." Work is planned **in the open** under `docs/`, one folder per task, with the user reviewing each stage before the next one starts. Nothing about it is language-specific — the only project-specific input is what "ends green" means (see *Verification gates* below).

## When to use it

Reach for this when a change is big enough that writing it down first pays off: a new feature, a non-obvious bug, a structural refactor, a performance migration, or an idea worth parking. Skip it for one-line fixes, typo corrections, and mechanical edits — those don't need a folder.

If the project's own agent instructions (`AGENTS.md`, `CLAUDE.md`) or `README` already describe a planning workflow, that document wins. This skill is the portable default; defer to local convention where they differ (especially folder names and the verification gates).

## Directory convention

```
docs/<category>/<N>-<slug>/
```

- `<category>` ∈ `bug` | `feature` | `refactor` | `test` | `migration` | `idea` | `review`
- `<N>` numbers the task **within its category** (e.g. `feature/9-…`, `migration/2-…`)
- `<slug>` is kebab-case and short

Before creating a folder, list the existing ones in that category (`ls docs/<category>/`) and pick the next free number.

## The files, per category

| Category | Files (written and reviewed in this order) |
|---|---|
| `bug`, `feature`, `refactor`, `test` | `description.md` → `research.md` → `plan.md` |
| `migration` | `why.md` → `research.md` → `plan.md` → `results.md` |
| `idea` | `description.md` only |
| `review` | `review.md` → `followup-2.md` → `followup-3.md` → … (one file per pass; see *Reviews*) |

Templates for each live next to this file in `templates/`:

- `templates/description.md` — the task / problem statement
- `templates/research.md` — analysis + open decisions, each with a recommendation
- `templates/plan.md` — ordered implementation steps, one commit each
- `templates/migration-why.md` — the motivating observation (bench data, complaint, or a discovered gap)
- `templates/migration-results.md` — post-implementation measurements that close the loop on `why.md`
- `templates/idea-description.md` — a shelved direction: use case, open questions, why it's parked
- `templates/review.md` — first-pass audit: numbered, severity-ranked findings with `file:line` evidence
- `templates/review-followup.md` — later passes: verdict, fixes verified against the diff, new findings, residual-risk table

Copy the matching template into the new folder, then fill it in. Keep the section headings; delete the `<…>` placeholders and any `(optional)` section you don't need.

## The staged loop (the important part)

Each stage is a **review gate**. Write the stage, then stop and let the user respond before moving on — do not write `research.md` and `plan.md` in the same turn.

1. **Describe.** Write `description.md` (or `why.md` for a migration). State the problem and what "done" looks like. Name the real consumer/caller if there is one — concrete beats abstract. Stop for review.
2. **Research.** Write `research.md`: the after-state, what's in/out of scope, and an **Open decisions** section. Each decision lays out options and ends with a **Recommendation**. Stop for review — the user replies *inline* (see below).
3. **Plan.** Once decisions are settled, write `plan.md`: concrete, ordered steps derived from the resolved research. Each step is **one commit** and ends green under the project's verification gates. Stop for review.
4. **Implement.** Follow the approved `plan.md`, one commit per step. The model that planned now acts as **lead**: each step runs by the executor its `Executor:` line names — the lead itself, or a worker sized to the step (see *Implementation: lead and workers*). (Migrations then add `results.md` — measurements that answer the question `why.md` posed.)

## Implementation: lead and workers

Once `plan.md` is approved, the model that wrote it changes role from author to **lead**. The lead does not have to type every change itself: where the host agent can spawn subagents, each step is either executed by the lead or **delegated to a worker** sized to the step's complexity. The lead stays accountable either way — it briefs, reviews, verifies, and commits. Where the host has no subagents, the lead implements every step itself and nothing else changes.

**The decision is made at plan time, not on the fly.** Every step in `plan.md` carries an `Executor:` line naming who runs it and why, so the delegation choices are reviewed at the plan gate alongside the steps themselves:

| Executor | Use it for | Model / effort |
|---|---|---|
| `worker/light` | mechanical, single-file, fully specified by the step; the *Verify* line settles whether it worked | cheapest model, low effort |
| `worker/standard` | multi-file but bounded — no judgement calls outside the decisions the step references | mid-tier model, default effort |
| `worker/strong` | cross-cutting, or the step `research.md` flagged as riskiest; needs real reasoning within the plan's bounds | strongest model, high effort |
| `lead` | anything that needs the planning context — reconciling a surprise against the decisions, public-API shape, the step that would be hardest to brief | the lead itself |

Tiers pair a model with a default effort; when the two diverge (a small but subtle change), say so explicitly — `worker/standard, high effort`. When in doubt, go one tier up: a worker that fails a gate costs a re-run; a worker that quietly does the wrong thing costs a review cycle. Tiers are not pinned to model names: the lead maps them onto whatever models the host offers at implementation time — cheapest for light, most capable for strong.

**The brief.** A fresh worker has none of the conversation. Its prompt is assembled from the plan and must be self-contained:

- the step verbatim — *Goal*, *Changes*, *Verify* — plus the resolved decisions it references (copy the `D<n>` lines from the top of `plan.md`; don't summarise them)
- the project's verification gates as runnable commands
- what to return: the diff, the gate output, and any deviation from the plan with its reason — and **do not commit**; the lead owns the commit
- the boundary: touch only what the step lists; if the step turns out to be wrong or under-specified, stop and report rather than improvise

**On return.** The lead reviews the diff against the step — not the worker's summary of it — re-runs the gates itself, and only then commits. A worker's "done" is a claim, not evidence; the same standard as *fixes verified against the diff* in reviews. If the diff deviates from the plan, the lead decides: accept and say so in the commit, or reject and re-brief. A deviation that changes a decision goes back through the *Append, don't rewrite* convention.

**Parallelism.** Steps run sequentially in the main tree by default — each commit is the next step's baseline. Fan out only the steps the plan's *Sequencing rationale* marks as independent; give each parallel worker its own isolated copy (a worktree, where the host offers it), then land the results in plan order so the history still reads one step per commit.

Host aside (Claude Code): workers are the `Agent` tool, and its `model` argument picks the worker's model. Before the first delegation, the lead checks which models the tool accepts and maps the tiers onto them itself. A `fork` inherits the full conversation and always runs on the lead's model; use it for a step that needs the planning context but is too mechanical for the lead's attention. Effort is not a per-call knob there: state it in the brief, or pin it in a project agent definition (`.claude/agents/<name>.md` frontmatter) and name that agent as the executor instead.

## Reviews (a different loop)

A `review` task audits an existing body of code — a fork, a PR wave, a subsystem — rather than planning new work. It skips description/research/plan; each pass is one file, and the gate between passes is the *author's response*, not a plan approval.

1. **First pass — `review.md`.** Strictly read-only: no source file is modified. Survey what is actually under review before judging it, then report numbered findings (`F1`, `F2`, …; sub-defects lettered, `F3(b)`). Each finding states its claim in the title and carries the full anatomy: *Impact / summary* ending in a **Confirmed**/**Likely** status, *Source* (`file:line` plus the minimal code quote), *Explanation and impact* (mechanism, then blast radius), and *Proposed fix* (code sketch, reasoning, a verification step, the re-run cost). Close with a suggested order of work, what was checked and found sound, and the files read. Stop — the user decides which findings to forward to the author.
2. **Follow-up passes — `followup-2.md`, `followup-3.md`, ….** When the author responds with changes, review the new commit range: a one-paragraph verdict up front; fixes verified against the diff, never trusted from the commit message; new findings under a fresh ID prefix per pass (`R1`… for the second, the next unused letter after that) so IDs stay unique across the folder; and a residual-risk table covering *every* prior finding, so nothing drops off the record silently. Previous passes are never edited — the folder is the record of the exchange.

Two conventions carry the weight: **severity is defined in consequences for this project** (e.g. Critical = "invalidates results already produced"), not generic labels; and **every claim is anchored** — a finding without `file:line` evidence is an opinion.

## Conventions that make it work

- **Inline `Note:` review.** The user reviews by adding `Note: …` lines directly under the relevant decision or option in the Markdown, not in chat. When you read a reviewed file back, treat each `Note:` as the binding decision. Leave the original recommendation and alternatives in place — the note records *why* the call was made.
- **One commit per step.** Every `plan.md` step is a self-contained commit that leaves the tree green. Don't bundle steps. The lead makes the commit — for a delegated step too, after verifying the worker's diff.
- **Append, don't rewrite.** When a decision changes *after* planning, append a new decision (`D<next>`) to `research.md` and a new step to `plan.md` rather than editing the originals in place. The folder is a record of how the work actually unfolded, not a tidied-up final state.
- **Decisions are numbered and referenced.** Label decisions `D1`, `D2`, … in `research.md`, and reference them from `plan.md` steps (`## Step 3 — … (D8)`) so the plan traces back to its rationale.
- **`idea/` is a holding pen.** A single `description.md` captures the use case, the open design questions, and *why it's shelved*. Promote to `feature/N-<slug>/` (with full research + plan) only when a concrete consumer appears.

## Verification gates (project-adaptive)

`plan.md` steps and migration `results.md` both assert the change "ends green." **What green means is per-project** — do not assume any particular toolchain. At the start of a task, determine the gates from the project itself, in this order of preference:

1. The project's agent instructions or contributor docs (`AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md`) that list the build/lint/test commands.
2. The build manifest and CI config (e.g. `Makefile`, `package.json` scripts, `.github/workflows/`, `Cargo.toml`, `pyproject.toml`).
3. Ask the user if it's genuinely unclear.

Then write those concrete commands into the plan's per-step *Verify* line and the results' *Verification gates* section. The templates carry a `<project's verification gates>` placeholder for exactly this — fill it with the real commands, don't leave it generic.
