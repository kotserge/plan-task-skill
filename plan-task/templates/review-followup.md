# <Target> — follow-up review (post-<batch label> changes)

**Target:** `<repo or subsystem>` @ `<new commit>` (`<branch>`)
**Previous review:** `<review.md | followup-(M-1).md>` @ `<commit reviewed then>` — findings <F1, F3, …> were forwarded to the author.
**Changes reviewed:** commits `<old>..<new>` (<the commit subjects, quoted — the author's own labels for the work>).
**Scope:** read-only review of the new changes, plus the status of the previously reported findings. No source file was modified.

---

## 0. Verdict in one paragraph

<The whole pass compressed, for the reader who reads nothing else: what got fixed, what is newly broken (bold the new finding IDs inline), and the single most important consequence — can the upcoming results/release be trusted, and what must happen before they can.>

---

## 1. What was fixed — confirmed correct

<Credit where due — but every fix verified against the diff, never assumed from the commit message:>

| Previous finding | Status |
|---|---|
| F1 (<short name>) | <what the diff shows; scope caveats ("fixed, but only in X"); whether the fix is sufficient or superseded by a new finding.> |

---

## 2. New findings

<Same anatomy as the first pass: claim in the title; *Impact / summary* ending in Status: **Confirmed | Likely**; *Source* with `file:line` and the minimal quote; *Explanation and impact*; *Proposed fix* with reasoning, verification step, and re-run cost. Fresh ID prefix per pass — `R1`, `R2`, … for the second pass, the next unused letter after that — so every finding ID stays unique across the folder.>

### R1 (<Severity>) — <claim>

#### Impact / summary

<…> Status: **Confirmed | Likely**.

#### Source

<…>

#### Explanation and impact

<…>

#### Proposed fix

<…>

---

## 3. Stated intent vs. what the code does (optional — delete if the author sent no explanation)

<Quote each claim from the author's message / PR description verbatim, then state precisely what the code actually does. Divergence here is a finding about communication and often the root of the code defect — e.g. "no prompt is adapted; ADAPT is checkpoint surgery on one weight matrix". Close with:>

**Questions to send back with this review:**

- <each missing fact that blocks a verdict — a log line to paste, the launch flags, which of two scenarios actually happened.>

---

## 4. Status of previous findings (residual risk)

<Every finding from *all* previous passes gets a row — nothing drops off the record silently:>

| Finding | Status @ `<new commit>` | Residual risk |
|---|---|---|
| F1 | <Fixed / Partially addressed / Unchanged> | <what still bites, where the fix does not reach, interactions with the new findings.> |

---

## 5. Suggested order of work

<Re-prioritized across old + new findings, gated on the project's next milestone ("the <wave> should not start until 1–3 are done"). Say which items are cheap — cheap-and-blocking goes first.>

1. **<ID>** — <…>

---

## 6. Files read

<Evidence base for *this* pass: files read, the diff range examined, references compared against.>
