# <Target> — <technical / logical> review

**Target:** `<repo or subsystem>` @ `<commit>` (`<branch>`)
**Baseline (optional — delete if reviewing standalone code):** <the upstream repo + fork point, the PR base, or the paper/spec the code claims to implement — whatever "correct" is measured against>
**Scope:** read-only review of <the defect class this pass hunts — e.g. logical/soundness defects that could produce incorrect results>. No source file was modified.

---

## Table of contents (optional — skip below ~6 findings)

<One linked line per finding, severity visible, so the spread is readable at a glance:>

- [F1 (<Severity>) — <claim>](#…)

---

## 1. What is under review

<Survey before judging: what the code actually does or actually changed, separated from what it claims. This establishes the vocabulary the findings use and shows the whole surface was read. For a fork or PR wave: the real delta vs the baseline, grouped by area, with tables for file/config inventories. Close with the untouched surface — it bounds the blast radius of everything below.>

### 1.1 <area>

<…>

### 1.<n> Not changed

<…>

---

## 2. Findings

Severity levels used below — define them in consequences for *this* project, not generic labels:

| Level | Meaning |
|---|---|
| Critical | <e.g. invalidates results that have already been produced> |
| Major | <e.g. the experiment/feature does not do what it claims to do> |
| Moderate | <e.g. correctness or reproducibility defect with a bounded blast radius> |
| Minor | <e.g. hygiene; costs time later rather than correctness now> |

"Confirmed" = verified by reading the code/data path end to end and/or reproducing the semantics locally. "Likely" = strong, but conditional on <what could not be checked — e.g. how the script was invoked>.

---

### F1 (<Severity>) — <the defect as a claim, not a topic — "every metric uses 2 points", never "issues in metrics">

#### Impact / summary

<Three to six sentences a reader could act on without the rest: what is wrong, which outputs it corrupts, and the remediation cost (e.g. "evaluation-only — costs a re-run, not a re-training"). End with:> Status: **Confirmed | Likely** <— if Likely, say what would confirm it>.

#### Source

<`file:line` for every claim, plus the minimal code quote that shows the defect. If the pattern is copy-pasted, list every occurrence — the fix must hit them all.>

#### Explanation and impact

<The mechanism: *why* the code misbehaves, demonstrated — a minimal repro, a worked example, or a table of cases. Whether the failure is silent or loud. Then the blast radius: every artifact, result, and consumer that inherits the error. If a correct reference exists (upstream, the paper, a sibling function), cite where it does it right — that removes "matter of interpretation" as a defense.>

#### Proposed fix

<A concrete code sketch — enough to implement without re-deriving the analysis.>

**Reasoning.** <Why this fix over the alternatives, and what invariant it establishes so the defect cannot recur (cast at the boundary, fail loudly, one source of truth).>

**Verification step:** <how to prove the fix worked — ideally one that also retroactively validates or invalidates runs already performed.>

**Re-run cost:** <which already-produced artifacts must be regenerated, and how expensive that is.>

---

### F2 (<Severity>) — <claim>

<Same anatomy for every finding. Number them F1, F2, …; letter sub-defects within one finding (F3(b)) — these IDs are how the order of work, follow-up passes, and commit messages refer back.>

---

### F<n> (Minor) — Smaller items

<Grab-bag so hygiene stays on the record without inflating the findings list: lettered items, each a short paragraph with `file:line`.>

#### (a) <item>

#### (b) <item>

---

## 3. Suggested order of work

<Priority is what-blocks-what, not severity alone; group by gate and say when an item is cheap:>

**Before <the next result wave / release / merge>**

1. **F1** — <the action, and why it is first — often: cheap, and it validates or invalidates everything downstream.>

**Before <the secondary results are interpreted / written up>**

2. **F<n>** — <…>

**Hygiene — cheap, do it once**

3. **F<n>** — <…>

---

## 4. Other notes

<What was checked and found *sound* — as load-bearing as the defects, since it tells the author which worries to drop. Design properties that are not bugs but belong in the write-up. Legitimate deviations from the baseline that should be documented as decisions. Weaknesses inherited from upstream. Close with positive observations, named specifically — credit is information too.>

---

## 5. Files read

<The evidence base: every file read, grouped by directory. Close with what was **not** reviewed and why — it bounds what this review can vouch for.>
