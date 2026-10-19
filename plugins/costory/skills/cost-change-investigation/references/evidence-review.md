# Worked cost-change evidence review

All amounts, labels, observations, and dates in this example are synthetic. They illustrate how to review evidence; never reuse them as findings about a workspace. Run the workflow in `SKILL.md` against the user's actual scope and comparison periods.

## Scenario and contribution evidence

Assume the client already sees the scenario's total change. The investigation finds these contributors:

| Contributor | Observed increase | Nesting evidence |
|---|---:|---|
| Service A | $1,000 | Parent of Storage A |
| Storage A | $700 | Child of Service A |
| Service B | $200 | No ancestor or descendant relationship to Service A |

Service A and Storage A overlap. Their combined contribution is not $1,700. Choose the level that best explains the finding, and describe Storage A as part of Service A if both are relevant. Service A and Service B may be summed because they are independent in this example. For a longer nesting chain, follow every ancestor before adding contributions.

## Timing and context review

1. Keep the scenario scope and contributor values exactly as returned. Translate the SQL scope to CEL without broadening it. If a predicate has no equivalent, omit that contributor's timing query and record the omission in the methodology.
2. Query the scenario total and each selected contributor as separate series across both comparison periods. Do not add `groupBy` to change-point series.
3. Compare the scenario-total series with the known comparison totals before interpreting change points. If the response is all zero while contribution analysis shows spend, timing was not retrieved. An `isError: false` response does not make that zero series evidence of flat spending.
4. Assume corrected, scope-matched queries show Storage A spending and its usage both rising during the same interval. This supports a storage-usage explanation for that contributor. It does not explain all of Service A's increase.
5. Assume a deployment occurred during that interval but concerns a different service. Exclude it as an explanation. A shared date alone does not establish relevance or causality.

## Final response for the supported finding

After the corrected timing and usage queries support the finding, return only the two fields required by the skill. Keep the client summary focused on the concrete contributor; put reliability and omitted evidence in the methodology.

```json
{
  "summary": "Storage A spending increased by $700 as storage usage rose during the same interval, accounting for most of Service A's increase.",
  "methodology": "Compared the scenario periods and investigated Service A, its nested Storage A contributor, and independent Service B. Used Storage A's observed usage and corrected, scope-matched timing results to explain the supported portion. Did not add Storage A to its parent. Excluded the unrelated deployment and the initial all-zero timing response; the remaining increase was not attributed to an operational cause."
}
```

If the corrected queries still return no useful explanation, follow the skill's direct-cost-query fallback. Use an empty summary only as the documented last resort and explain the missing evidence in the methodology. Do not fill the gap with the synthetic explanation above.
