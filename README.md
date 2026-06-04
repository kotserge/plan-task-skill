# plan-task — a Claude Code skill

A portable, project-agnostic [Claude Code](https://claude.com/claude-code) skill that runs non-trivial engineering work through a staged **research → plan → implementation** flow. Work is planned in the open under `docs/`, one folder per task, with a review gate between each stage. Nothing in it is language- or toolchain-specific — the verification gates ("what does *green* mean?") are derived from the target project at runtime.

## What's here

```
plan-task/
  SKILL.md              # the methodology + review-gate conventions
  templates/
    description.md      # bug/feature/refactor/test — the problem statement
    research.md         # analysis + numbered open decisions w/ inline-Note review slots
    plan.md             # ordered steps, one commit each, project's gates
    migration-why.md    # the motivating observation (data / complaint / gap)
    migration-results.md# before/after measurements closing the loop
    idea-description.md # a shelved direction, with promotion criteria
```

## The workflow in one paragraph

A task lives in `docs/<category>/<N>-<slug>/` where `<category>` is one of `bug`, `feature`, `refactor`, `test`, `migration`, or `idea`. Most categories use three files written and reviewed in order — `description.md` → `research.md` → `plan.md`; migrations add `why.md` up front and `results.md` at the end; an `idea` is a single `description.md` holding pen. Each stage stops for review: the user replies with inline `Note:` lines in the Markdown, which become the binding decisions. Every `plan.md` step is one commit that ends green under the project's own checks.

## Install

Claude Code loads skills from `~/.claude/skills/<name>/`. Symlink it (edits in this repo stay live):

```sh
ln -s "$(pwd)/plan-task" ~/.claude/skills/plan-task
```

…or copy it:

```sh
cp -r plan-task ~/.claude/skills/plan-task
```

For a single project instead of all of them, install under that repo's `.claude/skills/` rather than `~/.claude/skills/`.

## Use

Type `/plan-task`, or just start describing non-trivial work and let the skill's description trigger it. It scaffolds the `docs/` folder and writes the first stage, then waits for your review before continuing.

It defers to a project's own documented workflow (e.g. a `CLAUDE.md` that already prescribes one), so it won't conflict where local conventions already exist.
