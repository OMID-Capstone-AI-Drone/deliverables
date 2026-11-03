# Peer design review notes

| ID | Rubric criterion | Issue | Action | Owner | Priority | Due date | Applied in |
|---|---|---|---|---|---|---|---|
| PR-01 | Risk | No fallback if the primary temperature sensor fails during an active freeze. | Add a degraded alert state to the frost state machine that retains warnings. | Cyrus | Must-fix | Week 8 | Figure F-2, EC-F1, DR-10 |
| PR-02 | Completeness | Glue components (notifications, logging) lack behavior definitions and batching rules. | Detail the glue components, including notification batching. | Andrea | Must-fix | Week 8 | §2.6, EC-N1 to N4 |
| PR-03 | Completeness | Yield has no handling for corrupt or unreadable images. | Exclude unreadable photos in `PhotoWorker`, log `UNREADABLE_IMAGE`, and continue the run. | Dang | Must-fix | Week 8 | Figure Y-2, EC-Y9, DR-05 |
| PR-04 | Clarity | Disease behavior is unclear when model confidence is very low. | Return `MANUAL_REVIEW` when the top-class probability is below the cutoff (default 0.5). | Bridget | Must-fix | Week 8 | §2.2, EC-D1, DR-01 |

**Criterion with the most feedback:** Completeness. Several teams noted that glue components and error handling for corrupted machine-learning inputs were missing.

**Top two issues we raised while reviewing others** (briann45/senior-design):
1. **Clarity (data model):** their D2 data model lacked cardinality constraints on its primary relationships, which makes the one-to-many associations ambiguous.
2. **Completeness (state machine):** their D2 ingestion-states diagram had no error or timeout paths, so a hung ingestion could fail silently.
