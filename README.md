# 3.2 Decision Memo
# Section A: Problem Framing
## A1. Business Action Enabled

This model enables the Preventative Care team to proactively contact the highest-risk pets (capacity-limited to ~300 at a time) and offer early interventions such as:

Triage call

Care plan guidance

Vet advice

Follow-up scheduling

Goal: Reduce the likelihood or severity of a high-cost claim within the next 90 days.

Without the model:

Outreach would be reactive (after claims occur)

Or broad and untargeted

With the model:

Outreach is prioritised to the top-ranked pets each scoring cycle

Resources are allocated where risk is highest

## A2. Definition of “High Cost”

Current Definition

Any claim greater than $2,000 occurring within 90 days of the scoring snapshot.

This threshold:

Provides a clear financial interpretation

Aligns with material claim impact

Recommendation: Anchor to a Severity Percentile

To ensure economic relevance over time:

$2,000 should correspond approximately to a fixed upper percentile (e.g. top 5–10% of claims), OR

The dollar threshold should be reviewed annually based on claim inflation

This preserves:

A clear financial boundary

Stability against inflation

Consistent identification of materially severe claims

# Section B: Model Choice
## B1. Model Selected for Deployment

Model: XGBoost (Feature Engineering 1)
Tuned on: PR-AUC
Operational selection: Top-300 ranking

Why this model?

It demonstrated:

Stable Precision@300 across 5-fold cross-validation

Lower variability than the Top-K tuned alternative

Essentially identical mean Precision@300

This makes it the more reliable model for consistent weekly operations.

## B2. Model Deliberately Not Tried

Not tried: Deep Neural Network

Reason:

Dataset is structured tabular data

Mixed categorical and numeric variables

Limited project timeline (3–4 days)

XGBoost is a strong baseline for tabular ranking problems and typically matches or outperforms neural networks without requiring:

Extensive architecture search

Advanced calibration

Heavy feature embedding engineering

Given constraints, it was the most efficient high-performance choice.

# Section C: Evaluation & Threshold
## C1. Recommended Operating Threshold

Operating threshold: 0.712713

Pets with predicted probability ≥ 0.712713 are flagged.

This corresponds to selecting the top 300 highest-risk pets in the validation population.

Because selection is ranking-based:

The threshold will dynamically adjust in future scoring cycles.

## C2. Pets Flagged Per Week

Approximately 300 pets per scoring cycle.

If scoring is weekly → ~300 per week
If scoring is monthly → ~300 per month

The operational assumption is weekly scoring.

## C3. Plain-Language Interpretation for the Preventative Care Team

At this operating point:

Precision@300 ≈ 24–30%

Around 1 in 4 flagged pets will lodge a high-cost claim within 90 days (if no intervention occurs)

Lift@300 ≈ 3.7×

In simple terms:

The team is ~3–4 times more likely to contact a truly high-risk pet than if they selected randomly.

C4. If the Business Wants to Catch More Pets

Current threshold (0.7127):

Flags 300 pets

~75 true high-cost claims captured

Precision ≈ 25%

If threshold lowered (e.g. 0.65):

Flags ~500 pets

Precision drops to ~20%

~100 true positives captured

~200 additional low-risk pets contacted

Trade-Off

Lower threshold:

↑ Recall (more true high-cost pets captured)

↓ Precision (more false positives)

↑ Operational workload

The optimal threshold should ideally maximise:

Expected Net Value = (Expected Claims Prevented × Savings) – Outreach Cost

Rather than relying purely on a fixed capacity constraint.

# Section D: Deployment Recommendation
## D1. Deploy Next Month?

Recommendation: YES

Reasons:

Stable ranking performance at Top-300

Clear operational workflow

Measurable programme impact

3–4× improvement over random selection

Deployment enables structured experimentation and outcome tracking.

## D2. Biggest Risk

Temporal drift / policy environment change

Examples:

Claim inflation

Product or pricing changes

Portfolio mix shift

Changes in underwriting behaviour

Because the model is trained on historical data, shifts in claim behaviour may reduce ranking accuracy over time.

## D3. Weekly Monitoring Framework
Primary Metric

Precision@300 (measured with 90-day outcome lag)

Leading Indicators

Population Stability Index (PSI) on:

Predicted score distribution

Top 10 key features

Alert Threshold

Trigger alert if:

PSI > 0.25 on predicted score distribution
OR

PSI > 0.25 on ≥3 key driver features

Action Plan

If alert fires:

Check data pipeline integrity

Inspect feature distribution shifts

Retrain using latest snapshots

Re-validate top-300 precision

Temporarily adjust threshold if required

# Summary

This model:

Converts reactive claims management into proactive risk intervention

Improves targeting efficiency by ~3–4× over random selection

Provides a clear capacity-constrained operational workflow

Includes monitoring guardrails to manage drift risk

It is suitable for controlled deployment with structured outcome tracking.

# Underwriting and pricing

## Pricing Review – Cohort Alignment Analysis

A cohort-level pricing alignment review indicates material misalignment between observed high-cost risk and premium relativities, particularly in senior pets. The 10+ age band exhibits a high-cost claim rate of 19.5%, representing 2.48× portfolio risk, while premiums are only 1.39× portfolio average, resulting in a pricing alignment ratio of 1.78. This suggests risk is increasing approximately 78% faster than premium, indicating potential underpricing in this segment. The 7–10 age band shows a milder but similar pattern (alignment ratio 1.08). Conversely, younger cohorts appear to cross-subsidise older pets: the 0–1 age band has a risk level of 0.52× portfolio but pays 0.89× portfolio premium, producing an alignment ratio of 0.59, suggesting relative overpricing. Breed-level analysis reveals further tension: Working and Hound groups show alignment ratios of 1.29 and 1.33 respectively, indicating premiums may not fully reflect elevated risk, while Mixed and Domestic Longhair segments appear moderately overpriced (alignment ratios 0.80 and 0.76). Weight bands are broadly aligned, with only minor deviation in large breeds (alignment 1.12). Overall, the primary structural pricing pressure lies in senior pets and certain high-risk breed groups, suggesting targeted review of age and breed relativities may be warranted to improve actuarial alignment while managing potential cross-subsidisation across cohorts.

## SHAP value analysis and initial EDA

The exploratory analysis and model results consistently show that risk is concentrated in identifiable portfolio segments. Age is the dominant driver. In the raw data, target rates increase from 4.1% in pets aged 0–1, to 5.2% (1–3), 7.3% (3–7), 11.8% (7–10), and 19.5% in pets aged 10+. The model confirms this is not just correlation — age remains the strongest independent contributor to severe claim risk even after accounting for breed, premium, and prior claims. Breed segmentation shows similar alignment: pedigree and toy breeds exhibit target rates around 13%, compared to 5–6% in domestic longhair and mixed groups. The model also identifies breed risk as structurally important after adjusting for other factors.

Claims behaviour further differentiates risk. In the EDA, policies with 0 vet visits in the past 6 months show a 7.1% target rate, increasing steadily to 8.9% among pets with 4+ visits. The model confirms that even small increases in recent claim frequency independently elevate short-term severe claim likelihood. Additionally, the adjusted claim severity score shows a clear structural threshold around 0.6: below this level risk rises gradually, but above it predicted severe claim probability increases sharply. This identifies a distinct high-severity behavioural cohort not fully explained by age or breed alone.

Premium generally increases alongside risk up to approximately $2,000 per year, indicating pricing broadly reflects underlying risk. However, beyond this level the incremental risk contribution flattens once age and claims behaviour are considered, suggesting that very high premium tiers may reflect coverage selection rather than proportionally higher severe claim risk.

Overall, both the descriptive data and the model-adjusted analysis point to consistent portfolio-level risk concentration in older pets, certain breed groups, policies with increasing recent claims activity, and those exceeding the severity score threshold. These insights support age and breed segmentation, validate behavioural rating components, and highlight clear cohorts for pricing calibration review.
