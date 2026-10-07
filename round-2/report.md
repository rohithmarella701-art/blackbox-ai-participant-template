# round-1 – Investigate

**Team:** BB-016
**Queries used:** 86 / 150

## What we concluded

The system is an automated content moderation scoring engine that evaluates user account metadata, activity metrics, and post attributes to calculate an approval probability score[cite: 16, 17]. High approval scores (near `0.9900`) are driven primarily by longer post lengths and operating on `Surface D`[cite: 16, 17]. Conversely, the model imposes systematic score penalties for active account tenure (`months_active`), high reputation values above baseline (`reputation`), and account strikes (`recent_strikes`)[cite: 16, 17]. Features such as `linked_accounts` are completely ignored by the decision pipeline[cite: 16, 17].

## How we got there

1. **Surface and Reputation Calibration (Queries 71–75):** We varied surface types and reputation values while keeping other parameters fixed[cite: 16, 17]. Query 73 revealed that `Surface D` yielded the maximum approval score of `0.9900`, outperforming `Surface A` and `Surface B`[cite: 16, 17]. Furthermore, increasing `reputation` from `300` to `350` (Query 74) resulted in a score drop from `0.9900` to `0.9858`, showing an inverse relationship[cite: 16, 17].
2. **Strikes vs. Account Age Sensitivity (Queries 76–78):** Query 76 tested the effect of a recent strike (`recent_strikes = 1`), causing a score reduction to `0.9871`[cite: 16, 17]. Query 77 decreased `account_age_days` from `75` to `61.605`, resulting in a smaller drop to `0.9877`[cite: 16, 17]. This proved that recent infractions impose a sharper penalty than minor variations in account age[cite: 16, 17].
3. **Feature Independence Testing on `linked_accounts` (Queries 79, 80, 83):** We held all baseline parameters constant while adjusting `linked_accounts` across `15.6`, `7.8`, and `20.0`[cite: 16, 17]. Every run returned an identical score of `0.9900`, proving this feature is not weighted in the scoring model[cite: 16, 17].
4. **Tenure Penalty Analysis (Queries 81–83):** By increasing `months_active` from `0.0` to `6.4` (Query 81) and `32.2` (Query 82), the approval score dropped linearly to `0.9878` and `0.9640` respectively[cite: 16, 17]. This confirmed a steady penalty rate of approximately `-0.0008` per active month[cite: 16, 17].
5. **Content Length Impact (Queries 84–86):** Isolating `post_length` (Query 85) showed that dropping post length from `100` to `17.5` drastically lowered the score to `0.9275`[cite: 17]. Comparing this to strike penalties in Query 84 confirmed `post_length` as one of the strongest positive scoring drivers[cite: 16, 17].

## What we ruled out

- **Non-zero weight for `linked_accounts`:** We hypothesized that connected social or bank accounts would increase user trustworthiness[cite: 16, 17]. This was rejected because changing `linked_accounts` from `7.8` to `20.0` resulted in zero change to the score (`0.9900`)[cite: 16, 17].
- **Positive correlation for `months_active`:** We initially assumed account tenure would improve reputation and approval scores[cite: 16, 17]. This was rejected as increasing active months consistently lowered the output score[cite: 16, 17].
- **Positive linear effect of high `reputation` values:** We tested whether higher reputation scores directly boosted approval[cite: 16, 17]. This was ruled out after observing a score drop when reputation increased from `300` to `350`[cite: 16, 17].

## What we are still unsure about

- **Non-linear threshold limits for `post_length`:** While short post lengths clearly penalize the score, we have not established whether there is a point of diminishing returns or a maximum cutoff length after which additional text yields no score gain[cite: 16, 17].
- **Interaction effects between `recent_strikes` and `Surface` types:** It remains unclear whether operating on non-optimal surfaces (`Surface A` or `B`) magnifies the penalty caused by recent account strikes[cite: 16, 17].
- **Exact mathematical bounds for `account_age_days`:** While minor reductions caused small score dips, we have not tested extreme boundary values (e.g., brand-new accounts under 1 day old)[cite: 16, 17].
