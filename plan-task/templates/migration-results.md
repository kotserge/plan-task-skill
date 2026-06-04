# Migration - Results: <Title>

<How the numbers were captured: same hardware, same benchmark/measurement, full sample size, before vs after the migration commit. Note which baselines are unchanged by construction and therefore not reported.>

## <benchmark / measurement name>

<Before/after table per measurement. Report the delta and the significance bar (e.g. is a small shift just measurement noise?).>

| <param> | Before | After | Δ |
|---|---|---|---|
| <…> | <…> | <…> | <…> |

## Pre-vs-post assessment

<Does the data answer the question `why.md` set out to answer? State it plainly — including where the result falls short or is workload-dependent. Don't oversell.>

## Verification gates

<Confirm the project's gates pass after the migration commit and before this document lands. Use the project's actual commands.>

- <gate> — <result>
- <gate> — <result>

## Cost paid

<What the change cost: a new dependency and its transitive tree, added API surface, a behavior change requiring consumer migration, etc. Be honest about the trade-off.>

## Conclusion

<Net assessment: ships positive / needs follow-up. Tie back to the original motivation and note what the next change will be measured against.>
