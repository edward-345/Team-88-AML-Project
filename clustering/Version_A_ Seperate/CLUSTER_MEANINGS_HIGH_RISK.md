# Cluster Meanings (Version A: `Kmeans_Seperate.csv` + Plots)
Date: 2026-03-30

This report summarizes what each cluster represents (for both KYC **individuals** and **businesses**) using:
- `clustering/Version_A_ Seperate/Cluster Profile.pdf` (cluster-by-cluster “High” and “Moderate” feature lists and “Main Province”)
- `clustering/Version_A_ Seperate/Clustering_Individual_Plots/Heatmap_Numerical.png` and `Heatmap_categorical.png`
- `clustering/Version_A_ Seperate/Clustering_Business_Plots/Heatmap_Numerical.png`

## How to read this
The PDF provides, per cluster:
- **High**: engineered signals that are unusually high in that segment (the main “profile”)
- **Moderate**: additional signals that are somewhat elevated
- **Main Province**: where members of the cluster tend to concentrate geographically

Important note about cluster IDs:
- In this “Version A Seperate” setup, **`individual_0` and `business_0` are not present** in `clustering/Version_A_ Seperate/Kmeans_Seperate.csv` (the size table in the PDF also lists only `*_1`..`*_6`).
- So this report focuses on clusters `*_1`..`*_6` as the defined segments used by the pipeline input.

## High-risk definition used for this report
I treat a cluster as **high risk** if its **“High”** list indicates AML-relevant behavior in multiple dimensions, especially:
- **Cross-border / geographic dispersion**: `multi_country_transactions` and/or high unique locations
- **Concentration / burstiness**: `pct_transactions_top10_busiest_days`
- **Suspicious channels**: higher activity in `transaction_count_EFT`, `transaction_count_EMT`, `transaction_count_Cheque`, and/or `cheque_user`
- **Cash intensity** (for individuals): `abm_cash_count` and/or `transaction_count_ABM`
- **Materiality**: high `credit_transaction_amount`, `debit_transaction_amount`, `average_*_transaction_amount`, `total_transaction_count`, and/or `sales_cents`

Clusters that show only a narrower subset of these signals are treated as **medium-high** (still notable, but less strongly “AML-risk-shaped” than the high-risk set).

---
## Individual Clusters (`individual_1`..`individual_6`)

### `individual_1` (n=1844; Main Province: Manitoba)
**Meaning:** Cross-border style activity with elevated EFT and credit amounts.

High:
- `multi_country_transactions`
- `transaction_count_EFT`
- `credit_transaction_amount`

Moderate (selected from PDF):
- `unique_transaction_provinces`
- `credit_transaction_count`
- `pct_transactions_top10_busiest_days`
- `transaction_count_EMT`

Risk note:
- Strong cross-border + EFT/credit signal is concerning, but this cluster’s “High” list does not emphasize the cash intensity (`abm_cash_count`) and concentration (`pct_transactions_top10_busiest_days` appears more as Moderate than High).

### `individual_2` (n=15886; Main Province: Ontario)
**Meaning:** Timing/amount variability cluster (elevated debit/credit amounts and time-gap statistics), without the strongest cross-border/cash pattern.

High:
- `average_debit_transaction_amount`
- `Averagetime_between_transaction_day`
- `std_time_between_transactions`
- `median_time_between_transactions`
- `average_credit_transaction_amount`

Moderate:
- `birth_business_age`

Risk note:
- Elevated amount and time-gap structure can still be suspicious, but the profile is less explicitly tied (in the PDF “High” list) to cross-border concentration, cash intensity, or channel mix.

### `individual_3` (n=15659; Main Province: Other)
**Meaning:** A multi-signal “high-risk-shaped” segment: cross-border + cash intensity + concentrated transaction behavior + suspicious channel mix.

High:
- `account_age`
- `birth_business_age`
- `transaction_count_ABM`
- `transaction_count_EFT`
- `average_credit_transaction_amount`
- `multi_country_transactions`
- `card_top_mcc_weight`
- `abm_cash_count`
- `pct_transactions_top10_busiest_days`
- `cheque_user`
- `credit_transaction_amount`
- `credit_transaction_count`

Moderate (selected from PDF):
- `unique_transaction_provinces`
- `card_mcc_diversity_count`

Risk note:
- This cluster contains **cross-border** (`multi_country_transactions`), **cash intensity** (`abm_cash_count`), **concentration** (`pct_transactions_top10_busiest_days`), and **suspicious channel activity** (EFT + ABM + cheque usage) simultaneously. Highest-risk profile among individuals.

### `individual_4` (n=3892; Main Province: Alberta)
**Meaning:** Cross-border credit concentration with high EMT/EFT channel activity.

High:
- `multi_country_transactions`
- `pct_transactions_top10_busiest_days`
- `transaction_count_EMT`
- `credit_transaction_amount`
- `transaction_count_EFT`
- `credit_transaction_count`

Moderate (selected from PDF):
- `unique_transaction_provinces`
- `transaction_count_ABM`
- `abm_cash_count`
- `card_top_mcc_weight`
- `debit_transaction_amount`

Risk note:
- Contains **cross-border** + **concentration** + **EFT/EMT credit-heavy activity**. Very strong high-risk candidate.

### `individual_5` (n=14509; Main Province: Ontario)
**Meaning:** High overall transactional volume with multi-channel card activity and cross-border geographic dispersion.

High:
- `total_transaction_count`
- `transaction_count_Card`
- `transaction_count_EFT`
- `transaction_count_EMT`
- `debit_transaction_count`
- `debit_transaction_amount`
- `credit_transaction_count`
- `credit_transaction_amount`
- `unique_transaction_cities`
- `unique_transaction_provinces`
- `multi_country_transactions`
- `card_ecommerce_count`
- `card_non_ecommerce_count`
- `card_mcc_diversity_count`

Moderate (selected from PDF):
- `transaction_count_ABM`
- `abm_cash_count`

Risk note:
- Cross-border + high volume + diverse card usage pattern. Less centered on ABM cash/custodial-style signals, but still high-risk-shaped due to materiality + cross-border dispersion.

### `individual_6` (n=1309; Main Province: Quebec)
**Meaning:** Cross-border segment with concentrated activity and cheque usage, concentrated in credit/average credit amount signals.

High:
- `multi_country_transactions`
- `pct_transactions_top10_busiest_days`
- `cheque_user`
- `credit_transaction_amount`
- `average_credit_transaction_amount`

Moderate (selected from PDF):
- `unique_transaction_provinces`
- `credit_transaction_count`
- `transaction_count_ABM`
- `transaction_count_EMT`
- `abm_cash_count`
- `card_top_mcc_weight`
- `card_mcc_diversity_count`
- `debit_transaction_amount`

Risk note:
- Cross-border + concentration + cheque usage is a classic “unusual mix” pattern in this AML framing. High-risk candidate.

---
## Business Clusters (`business_1`..`business_6`)

### `business_1` (n=4004; Main Province: Ontario)
**Meaning:** Business age + time-gap variability + burstiness (top-10 busiest days), with moderate debit amount elevated.

High:
- `birth_business_age`
- `Averagetime_between_transaction_day`
- `std_time_between_transactions`
- `median_time_between_transactions`
- `pct_transactions_top10_busiest_days`
- `account_age`

Moderate:
- `average_debit_transaction_amount`

Risk note:
- Concentration/burstiness present, but the “High” list does not strongly include multi-country / cross-border. Medium-high rather than top-tier.

### `business_2` (n=618; Main Province: Alberta)
**Meaning:** EMT/time irregularity emphasis.

High:
- `std_time_between_transactions`
- `transaction_count_EMT`

Moderate:
- `Averagetime_between_transaction_day`
- `median_time_between_transactions`

Risk note:
- Irregular time gaps + EMT activity. Not explicitly cross-border in “High”, so less strongly high-risk than the `business_4`/`business_6` segments.

### `business_3` (n=196; Main Province: Ontario)
**Meaning:** High-activity “amount + channel” cluster driven by cheque/EFT and debit/credit magnitude signals, plus concentration.

High:
- `account_age`
- `total_transaction_count`
- `transaction_count_Cheque`
- `transaction_count_EFT`
- `debit_transaction_count`
- `debit_transaction_amount`
- `credit_transaction_count`
- `credit_transaction_amount`
- `average_debit_transaction_amount`
- `average_credit_transaction_amount`
- `pct_transactions_top10_busiest_days`
- `transaction_count_EMT` (listed in PDF high list)

Moderate (selected from PDF):
- (not emphasized in the page segment; PDF notes a main province and “High” dominates)

Risk note:
- Even without the explicit multi-country emphasis, the combination of **cheque/EFT** + **material debit/credit amounts** + **concentration** makes this a strong high-risk candidate.

### `business_4` (n=2687; Main Province: Ontario)
**Meaning:** Cross-border and EMT-driven activity with sales/cash-register style scale and card top-MCC weighting.

High:
- `sales_cents`
- `transaction_count_EMT`
- `multi_country_transactions`
- `card_top_mcc_weight`
- `account_age`

Moderate (selected from PDF):
- `birth_business_age`
- `std_time_between_transactions`
- `Averagetime_between_transaction_day`
- `median_time_between_transactions`
- `unique_transaction_provinces`

Risk note:
- This is one of the strongest “cross-border + suspicious channel” profiles for businesses.

### `business_5` (n=108; Main Province: Manitoba)
**Meaning:** Account-age and time-gap variability emphasis (less cross-border/channel-mix emphasis in “High”).

High:
- `account_age`
- `std_time_between_transactions`

Moderate (selected from PDF):
- `Averagetime_between_transaction_day`
- `median_time_between_transactions`
- `birth_business_age`

Risk note:
- Not the strongest combination of cross-border/concentration/suspicious channel mix vs the top high-risk set.

### `business_6` (n=698; Main Province: Quebec)
**Meaning:** Cross-border + multi-channel card/EMT activity with high dispersion and ecommerce/non-ecommerce engagement.

High:
- `transaction_count_Card`
- `transaction_count_EMT`
- `unique_transaction_cities`
- `unique_transaction_provinces`
- `multi_country_transactions`
- `card_ecommerce_count`
- `card_non_ecommerce_count`
- `card_mcc_diversity_count`
- `birth_business_age`
- `account_age`

Moderate (selected from PDF):
- (PDF indicates “High” dominates for the core discriminators)

Risk note:
- Cross-border + dispersed geography + multi-channel electronic activity + card diversity. High-risk candidate.

---
## High-risk clusters (summary)

### High risk (recommended set)
Individuals:
- `individual_3`
- `individual_4`
- `individual_6`

Businesses:
- `business_3`
- `business_4`
- `business_6`

### Medium-high (not top-tier, but still notable)
Individuals:
- `individual_1`
- `individual_5`

Businesses:
- `business_1`
- `business_2`

