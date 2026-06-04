# plan-task

A portable skill for AI coding agents that runs non-trivial engineering work through a staged **research → plan → implementation** flow. Work is planned in the open under `docs/`, one folder per task, with a review gate between each stage. Nothing in it is language-, toolchain-, or agent-specific -- the verification gates are derived from the target project at runtime.

It ships as a `SKILL.md` skill — the same `SKILL.md` + frontmatter convention used by agents such as Claude Code and opencode -- so any agent that loads skills can use it, and any agent that doesn't can simply be pointed at the file as guidance.

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

## Workflow

A task lives in `docs/<category>/<N>-<slug>/`, where `<category>` is one of `bug`, `feature`, `refactor`, `test`, `migration`, or `idea`. Most categories use three files written and reviewed in order — `description.md` → `research.md` → `plan.md`; migrations add `why.md` up front and `results.md` at the end; an `idea` is a single `description.md` holding pen. Each stage stops for review: you reply with inline `Note:` lines in the Markdown, which become the binding decisions. Every `plan.md` step is one commit that ends green under the project's own checks.

## Install

**As a skill** — drop the `plan-task/` folder into the directory your agent loads skills from. For example, Claude Code reads `~/.claude/skills/<name>/`:

```sh
ln -s "$(pwd)/plan-task" ~/.claude/skills/plan-task   # symlink keeps it live
# or: cp -r plan-task ~/.claude/skills/plan-task
```

Other agents (opencode and the like) that support the `SKILL.md` convention have their own skills directory — see your tool's docs for the path, then symlink or copy `plan-task/` there. For a single project rather than globally, use that project's local skills directory instead.

**As plain guidance** — for any agent without a skills loader, reference or inline `plan-task/SKILL.md` from your project's agent-instructions file (e.g. `AGENTS.md`). The file is self-contained Markdown.

## Use

Invoke it however your agent invokes skills (in Claude Code: `/plan-task`), or just start describing non-trivial work and let the skill's description trigger it. It scaffolds the `docs/` folder and writes the first stage, then waits for your review before continuing.

It defers to a project's own documented workflow (e.g. an `AGENTS.md` / `CLAUDE.md` that already prescribes one), so it won't conflict where local conventions already exist.
