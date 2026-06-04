---
name: plan-task
description: Plan non-trivial engineering work in the open using a staged research → plan → implementation flow backed by per-task docs/ folders, reviewed by the user between stages. Use when starting a feature, bug fix, refactor, or migration big enough to warrant a written plan before coding, or when capturing a shelved idea. Provides per-category Markdown templates (description/research/plan, plus why/results for migrations) and the review-gate conventions that tie them together.
---

# plan-task

A lightweight, paper-trail-first way to take a non-trivial change from "here's the problem" to "here's the merged code." Work is planned **in the open** under `docs/`, one folder per task, with the user reviewing each stage before the next one starts. Nothing about it is language-specific — the only project-specific input is what "ends green" means (see *Verification gates* below).

## When to use it

Reach for this when a change is big enough that writing it down first pays off: a new feature, a non-obvious bug, a structural refactor, a performance migration, or an idea worth parking. Skip it for one-line fixes, typo corrections, and mechanical edits — those don't need a folder.

If the project's own `CLAUDE.md` or `README` already describes a planning workflow, that document wins. This skill is the portable default; defer to local convention where they differ (especially folder names and the verification gates).

## Directory convention

```
docs/<category>/<N>-<slug>/
```

- `<category>` ∈ `bug` | `feature` | `refactor` | `test` | `migration` | `idea`
- `<N>` numbers the task **within its category** (e.g. `feature/9-…`, `migration/2-…`)
- `<slug>` is kebab-case and short

Before creating a folder, list the existing ones in that category (`ls docs/<category>/`) and pick the next free number.

## The files, per category

| Category | Files (written and reviewed in this order) |
|---|---|
| `bug`, `feature`, `refactor`, `test` | `description.md` → `research.md` → `plan.md` |
| `migration` | `why.md` → `research.md` → `plan.md` → `results.md` |
| `idea` | `description.md` only |

Templates for each live next to this file in `templates/`:

- `templates/description.md` — the task / problem statement
- `templates/research.md` — analysis + open decisions, each with a recommendation
- `templates/plan.md` — ordered implementation steps, one commit each
- `templates/migration-why.md` — the motivating observation (bench data, complaint, or a discovered gap)
- `templates/migration-results.md` — post-implementation measurements that close the loop on `why.md`
- `templates/idea-description.md` — a shelved direction: use case, open questions, why it's parked

Copy the matching template into the new folder, then fill it in. Keep the section headings; delete the `<…>` placeholders and any `(optional)` section you don't need.

## The staged loop (the important part)

Each stage is a **review gate**. Write the stage, then stop and let the user respond before moving on — do not write `research.md` and `plan.md` in the same turn.

1. **Describe.** Write `description.md` (or `why.md` for a migration). State the problem and what "done" looks like. Name the real consumer/caller if there is one — concrete beats abstract. Stop for review.
2. **Research.** Write `research.md`: the after-state, what's in/out of scope, and an **Open decisions** section. Each decision lays out options and ends with a **Recommendation**. Stop for review — the user replies *inline* (see below).
3. **Plan.** Once decisions are settled, write `plan.md`: concrete, ordered steps derived from the resolved research. Each step is **one commit** and ends green under the project's verification gates. Stop for review.
4. **Implement.** Follow the approved `plan.md`, one commit per step. (Migrations then add `results.md` — measurements that answer the question `why.md` posed.)

## Conventions that make it work

- **Inline `Note:` review.** The user reviews by adding `Note: …` lines directly under the relevant decision or option in the Markdown, not in chat. When you read a reviewed file back, treat each `Note:` as the binding decision. Leave the original recommendation and alternatives in place — the note records *why* the call was made.
- **One commit per step.** Every `plan.md` step is a self-contained commit that leaves the tree green. Don't bundle steps.
- **Append, don't rewrite.** When a decision changes *after* planning, append a new decision (`D<next>`) to `research.md` and a new step to `plan.md` rather than editing the originals in place. The folder is a record of how the work actually unfolded, not a tidied-up final state.
- **Decisions are numbered and referenced.** Label decisions `D1`, `D2`, … in `research.md`, and reference them from `plan.md` steps (`## Step 3 — … (D8)`) so the plan traces back to its rationale.
- **`idea/` is a holding pen.** A single `description.md` captures the use case, the open design questions, and *why it's shelved*. Promote to `feature/N-<slug>/` (with full research + plan) only when a concrete consumer appears.

## Verification gates (project-adaptive)

`plan.md` steps and migration `results.md` both assert the change "ends green." **What green means is per-project** — do not assume any particular toolchain. At the start of a task, determine the gates from the project itself, in this order of preference:

1. A `CLAUDE.md` / `AGENTS.md` / `CONTRIBUTING.md` that lists the build/lint/test commands.
2. The build manifest and CI config (e.g. `Makefile`, `package.json` scripts, `.github/workflows/`, `Cargo.toml`, `pyproject.toml`).
3. Ask the user if it's genuinely unclear.

Then write those concrete commands into the plan's per-step *Verify* line and the results' *Verification gates* section. The templates carry a `<project's verification gates>` placeholder for exactly this — fill it with the real commands, don't leave it generic.
