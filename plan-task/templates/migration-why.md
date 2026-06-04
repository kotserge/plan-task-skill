# Migration - Why: <Title>

## Observation

<What was noticed, and the design assumption it contradicts. Cite what surfaced it — the commit range, the bench run, the recurring complaint, or the correctness gap.>

## The numbers

<The empirical motivation: bench data, profiling output, or the quantified pattern of complaints. A before/observed table makes the regression legible. If the motivation is a correctness gap rather than perf, describe the failing case concretely here instead.>

| <param> | <baseline> | <observed> | <delta> |
|---|---|---|---|
| <…> | <…> | <…> | <…> |

## Root cause

<The mechanism behind the numbers — why the observed behavior happens, not just that it does.>

## Why this matters

<The consequence if left unaddressed, and which prior design notes/decisions the data invalidates.>

## What this document doesn't decide

<This is the problem statement. Point forward: candidate solution and chosen direction in `research.md`, implementation steps in `plan.md`, post-implementation measurements in `results.md`.>
