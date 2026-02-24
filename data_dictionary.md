# Data Dictionary — Pet Health Risk Dataset

---

## Identifiers

| Column | Type | Description | Values / Range |
|--------|------|-------------|----------------|
| `pet_id` | string | Unique pet identifier | `PET000001` – `PET025000` |

---

## Pet Demographics

| Column | Type | Description | Values / Range |
|--------|------|-------------|----------------|
| `species` | categorical | Pet species | `Dog`, `Cat` |
| `breed_group` | categorical | Breed group classification (dogs: kennel club groupings; cats: coat type / lineage) | `working`, `toy`, `hound`, `terrier`, `sporting`, `herding`, `non-sporting`, `mixed`, `domestic_shorthair`, `domestic_longhair`, `pedigree` |
| `age_years` | float | Age in years at time of scoring | 0.3 – 16.0 |
| `age_band` | categorical | Age band bucket | `0-1`, `1-3`, `3-7`, `7-10`, `10+` |
| `weight_band` | categorical | Weight category (dogs only; cats are always `small`) | `small` (<10 kg), `medium` (10–25 kg), `large` (>25 kg) |
| `desexed` | categorical | Desexed status | `Y`, `N` |
| `breed_risk_category` | categorical | Actuarial breed risk tier based on breed predisposition to costly conditions | `low`, `medium`, `high` |

---

## Policy Details

| Column | Type | Description | Values / Range |
|--------|------|-------------|----------------|
| `cover_type` | categorical | Type of policy coverage | `comprehensive`, `accident_only`, `illness_only` |
| `tenure_months` | integer | Months since current policy inception | 1 – 96 |
| `excess_amount` | integer | Policy excess amount in AUD | 0, 100, 200, 500 |
| `premium_band` | categorical | Premium tier (derived from cover type, breed risk, and age) | `low`, `medium`, `high`, `very_high` |
| `annual_premium` | float | Annual premium amount in AUD | ~150 – ~3,500 |
| `excess_waived` | integer | Whether the excess was waived on the most recent claim (0/1) | 0, 1 |
| `policy_renewal_count` | integer | Number of times the policy has been renewed | 0 – 8 |
| `policy_active` | integer | Whether the policy is currently active |  |
| `customer_tenure_months` | integer | Total months as a PetSure customer (may exceed policy tenure for customers with prior policies) | 1 – ~119 |
| `multi_pet_policy` | integer | Whether this pet is covered under a multi-pet policy (0/1) | 0, 1 |
| `direct_debit_active` | integer | Whether premium is paid by direct debit (0/1) | 0, 1 |
| `state` | categorical | Australian state or territory of the policyholder | `NSW`, `VIC`, `QLD`, `SA`, `WA`, `TAS`, `ACT`, `NT` |
| `partner_clinic_enrolled` | integer | Whether the pet is registered with a PetSure partner vet clinic (0/1) | 0, 1 |
| `wellness_plan_enrolled` | integer | Whether the pet is enrolled in a wellness/preventative care plan (0/1) | 0, 1 |

---

## Claims History (Prior 12 Months)

| Column | Type | Description | Values / Range |
|--------|------|-------------|----------------|
| `prior_claim_any` | integer | Whether any claim has ever been lodged under this policy (0/1) | 0, 1 |
| `claim_count_12m` | integer | Number of claims lodged in the prior 12 months | 0 – 5 |
| `total_claims_12m_value` | float | Total value of all claims lodged in the prior 12 months (AUD) | 0 – ~15,000 |
| `max_single_claim_12m` | float | Value of the single largest claim in the prior 12 months (AUD). 0 if no claims. | 0 – ~12,000 |
| `months_since_last_claim` | float | Months since the most recent claim was lodged. | 0 – 11, or null |
| `claim_freq_trend` | categorical | Direction of claim frequency relative to prior period | `increasing`, `stable`, `decreasing`, `no_claims` |

---

## Vet Visit History

| Column | Type | Description | Values / Range |
|--------|------|-------------|----------------|
| `vet_visit_count_6m` | integer | Number of vet visits in the past 6 months | 0 – 6 |
| `days_since_last_vet_visit` | integer | Days since the most recent vet visit. High values indicate the pet has not visited a vet recently. | 1 – ~730 |

---

## Seasonal Indicators

These columns indicate the calendar quarter in which the policy book is being scored. Exactly one will be 1 per row.

| Column | Type | Description |
|--------|------|-------------|
| `seasonal_risk_q1` | integer | Scored in Q1 (January – March) |
| `seasonal_risk_q2` | integer | Scored in Q2 (April – June) |
| `seasonal_risk_q3` | integer | Scored in Q3 (July – September) |
| `seasonal_risk_q4` | integer | Scored in Q4 (October – December) |

---

## Target Variable

| Column | Type | Description | Values |
|--------|------|-------------|--------|
| `high_cost_claim_next_90d` | integer | **Target.** Whether the pet lodged a claim exceeding $2,000 in the 90 days following the scoring date. | 0 = no high-cost claim, 1 = high-cost claim |

---

*Last updated: February 2026*
