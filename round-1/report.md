# round-1 — Observe

**Team:** BB-016
**Queries used:** 65/ budget

## What we concluded

The `GK-04` system is a non-linear moderation model where trust parameters do not behave intuitively[cite: 6]:

* **Inverse Reputation Driver:** `reputation` acts as an inverse variable[cite: 5]. Lowering `reputation` from `900` to `300` produced the single largest score jump (from `~0.7010` to `0.9478`), proving that lower reported user reputation significantly increases the moderation approval score[cite: 3, 5].
* **Exponential Reach Penalty:** `reach` is a severe penalty multiplier[cite: 1, 2]. Setting `reach = 100` drops the score by `-0.4737` (from `0.6729` down to `0.1992`), requiring `reach` to remain strictly at `0` to maintain approval status[cite: 1, 2].
* **Optimal Surface Category:** `Surface D` yields the global maximum score of `0.9900` when combined with low `months_active (0)` and low `reputation (300)`[cite: 5]. `Surface B` serves as the second-best surface (`0.7010` under standard baselines)[cite: 3].
* **Linear Positive Drivers:** `post_length` ($100$) and `account_age_days` ($75$) are monotonic positive drivers[cite: 3, 4]. Decreasing `post_length` from `100` to `75` reduced the score by `-0.0847`[cite: 4].
* **Strict Penalty Factors:** `recent_strikes` ($0$) and `report_ratio` ($0$) act as hard risk flags. Non-zero values apply severe penalty drops, pushing scores down toward $0.0560$.
* **Zero-Weight Factors:** `mentions` and `linked_accounts` have zero measurable effect on the moderation score[cite: 1, 2, 5].

## How we got there

1. **Initial Risk Parameter Isolation (Queries #1–#15):** Evaluated baseline scores across all four surfaces, establishing that `recent_strikes = 5` and `report_ratio = 1` severely suppress output scores down to ~0.0560.
2. **Standard Longevity Baseline (Queries #16–#29):** Maxed longevity and post metrics (`account_age_days = 75`, `post_length = 100`, `months_active = 40`) while keeping risk factors zeroed, securing an `0.6729` (`APPROVE`) baseline on `Surface A`[cite: 1, 2].
3. **Testing Reach Sensitivity (Query #30):** Increased `reach` from `0` to `100`, which caused an immediate score crash down to `0.1992` (`DECLINE`), identifying `reach` as a primary risk flag[cite: 1, 2].
4. **Surface Sweep & Post Length Optimization (Queries #31–#35):** Tested surface dropdown options with `reach = 0`, discovering `Surface B` pushed the baseline to `0.7010`[cite: 3]. Lowered `post_length` to `75`, which caused a `-0.0847` score drop to `0.6163`, proving `100` is the optimal post length[cite: 4].
5. **Neutral Parameter Filtering (Queries #36–#44):** Varied `mentions` ($2.85 \rightarrow 6$) and `linked_accounts` ($0 \rightarrow 20$) during a plateau on the graph, confirming zero score variance across tests[cite: 1, 2, 5].
6. **Inverting Reputation (Query #45):** Reduced `reputation` from `900` to `300`, unlocking a massive score surge up to `0.9478` (`APPROVE`)[cite: 5].
7. **Global Peak Optimization (Queries #46–#65):** Combined `reputation = 300`, `reach = 0`, `post_length = 100`, `account_age_days = 75`, and `months_active = 0` on `Surface D` to achieve the final peak score of **`0.9900`**[cite: 5].

## What we ruled out

* **Universal Positive Trust Metrics:** We rejected the hypothesis that standard trust metrics always improve moderation scores. High `reputation` ($900$) actively penalizes the final output[cite: 3, 5].
* **High Reach Multiplier:** We rejected the idea that high-reach posts receive higher approval scores; setting `reach > 0` triggers an immediate risk penalty[cite: 1, 2].
* **Relevance of Mentions and Linked Accounts:** We ruled out `mentions` and `linked_accounts` as active features in `GK-04`, as modifying them across boundary values produced zero change in the score[cite: 1, 2, 5].
* **Superiority of Surface A:** We ruled out `Surface A` as the optimal platform category; both `Surface B` ($0.7010$) and `Surface D` ($0.9900$) consistently outperform it[cite: 1, 3, 5].

## What we are still unsure about

* **Bridging the $0.0100$ Gap:** The exact non-linear interaction or fractional value required to bridge the remaining distance from `0.9900` to a perfect `1.0000` score[cite: 5].
* **`months_active` & `Surface D` Coupling:** Whether `months_active = 0` provides an independent positive boost or if its effect is conditional on selecting `Surface D`[cite: 5].
* **Inflection Point for Reach Penalty:** The exact numerical threshold between `reach = 0` and `reach = 10` where the severe penalty curve begins to trigger.
