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
