# Cluster risk boost justification (Version A “Seperate” clusters)
Date: 2026-03-30

This note documents the reasoning behind the `rule_based.cluster_risk_boost` and `rule_based.cluster_secondary` lists in `config.yaml`.

## Why cluster risk exists in the pipeline
In `aml_detection_pipeline/scripts/rule_based_scorer.py`, cluster membership can add a **fixed score uplift** (`cluster_boost_amount` or `cluster_secondary_boost_amount`) to a customer’s `rule_based_score`.

Because this is a **hard uplift**, the “risky cluster” designation should be **rare** and reserved for clusters whose *overall segment profile* strongly matches AML red-flag patterns (not merely “distinct” clusters).

## Evidence sources used
Version A clustering deliverables:
- `clustering/Version_A_ Seperate/Cluster Profile.pdf` (per-cluster “High” / “Moderate” discriminating features + main province)
- `clustering/Version_A_ Seperate/Clustering_Individual_Plots/Heatmap_Numerical.png` and `Heatmap_categorical.png`
- `clustering/Version_A_ Seperate/Clustering_Business_Plots/Heatmap_Numerical.png`

## Risk criteria used for *cluster-level* boosts
A cluster is considered **high risk** (eligible for `cluster_risk_boost`) only when its **High** profile includes multiple AML-shaped drivers, especially:
- **Cross-border / geographic dispersion**: `multi_country_transactions`, high unique locations
- **Concentration / burstiness**: `pct_transactions_top10_busiest_days`
- **Higher-risk channels**: elevated `transaction_count_EFT`, `transaction_count_EMT`, `transaction_count_Cheque` / `cheque_user`, and/or cash/ABM intensity for individuals
- **Materiality**: large debit/credit amount signals or scale proxy (e.g., `sales_cents` for businesses)

Clusters that have only one narrow driver (e.g., mostly timing irregularity) are better treated as “distinct segments” rather than automatically “risky.”

## Important observation: “5 out of 6 business clusters risky” is a smell
The cluster profiles often list some “High” features for each cluster (that’s normal for cluster summaries).
It would be misleading to label most business clusters as “risky” purely from clustering, because the pipeline uses these lists as a **direct scoring boost**.

So we intentionally constrain the boost list to clusters with **cross-border + channel + concentration/materiality** style profiles.

---
## Cluster-by-cluster rationale (Version A clusters)

### Individuals

#### `individual_3` — **high risk**
From `Cluster Profile.pdf` **High** features include:
- Cross-border: `multi_country_transactions`
- Cash/ABM intensity: `transaction_count_ABM`, `abm_cash_count`
- Higher-risk channels: `transaction_count_EFT` + cheque usage (`cheque_user`)
- Concentration: `pct_transactions_top10_busiest_days`
- Materiality: `credit_transaction_amount`, `credit_transaction_count`, `average_credit_transaction_amount`

This is a multi-signal, AML-shaped segment (cross-border + cash + transfer + concentrated activity).

#### `individual_4` — **high risk**
**High** includes:
- Cross-border: `multi_country_transactions`
- Concentration: `pct_transactions_top10_busiest_days`
- Higher-risk channels: `transaction_count_EMT`, `transaction_count_EFT`
- Materiality: `credit_transaction_amount`, `credit_transaction_count`

This reads as “international + bursty + transfer-heavy,” which fits the intended uplift.

#### `individual_6` — **high risk**
**High** includes:
- Cross-border: `multi_country_transactions`
- Concentration: `pct_transactions_top10_busiest_days`
- Cheque usage: `cheque_user`
- Materiality: `credit_transaction_amount`, `average_credit_transaction_amount`

Cheque + cross-border + concentrated credit activity is a strong “unusual mix” signal.

#### `individual_1` — **secondary / notable**
**High** includes cross-border + EFT + credit amount (`multi_country_transactions`, `transaction_count_EFT`, `credit_transaction_amount`), but **cash intensity** and **concentration** are not as central (often Moderate).

This can be used as a smaller boost if desired, but is weaker than `individual_3/4/6`.

#### `individual_5` — **secondary / notable**
**High** is dominated by overall transaction volume, dispersion, and card diversity, including `multi_country_transactions` and high channel usage.
However, the “cash + concentration” style indicators are not the dominant High drivers (ABM/cash tends to be Moderate).

This can be a smaller boost if you want to emphasize cross-border + high activity.

#### `individual_2` — **not boosted**
The High profile is largely timing/amount variability (time-gap statistics, average amounts) without the strongest cross-border/cash/channel mix bundle.

---
### Businesses

#### `business_4` — **high risk**
**High** includes:
- Cross-border: `multi_country_transactions`
- Higher-risk channel: `transaction_count_EMT`
- Materiality proxy: `sales_cents`
- Merchant concentration proxy: `card_top_mcc_weight`

Clear cross-border + channel + scale combination.

#### `business_6` — **high risk**
**High** includes:
- Cross-border: `multi_country_transactions`
- Geographic dispersion: `unique_transaction_cities`, `unique_transaction_provinces`
- Broad electronic/card activity: `transaction_count_Card`, `transaction_count_EMT`
- Card diversity and ecommerce/non-ecommerce activity

Cross-border + dispersed + multi-channel profile is strongly AML-shaped.

#### `business_3` — **high risk**
**High** includes:
- Higher-risk channels: `transaction_count_Cheque`, `transaction_count_EFT`, `transaction_count_EMT`
- Materiality: debit/credit counts and amounts + average amounts
- Concentration: `pct_transactions_top10_busiest_days`

Even if cross-border is not the primary High driver, this is “large flows + multiple channels + concentration.”

#### `business_1` / `business_2` / `business_5` — **not boosted (or at most secondary)**
These clusters are driven mainly by timing irregularity / burstiness and age variables, without the strongest explicit cross-border + channel + materiality bundle in their High lists.

Including them as “risky” would inflate the interpretation that “most businesses are risky,” which is not appropriate for a hard score uplift.

---
## Recommended mapping to `config.yaml`
If you want a conservative, audit-friendly boost list:
- **High risk (`cluster_risk_boost`)**: `["individual_3","individual_4","individual_6","business_3","business_4","business_6"]`
- **Secondary (`cluster_secondary`)**: optional; if used at all, consider individuals-only, e.g. `["individual_1","individual_5"]`

