# 3.2 Decision Memo
# Section A: Problem Framing
## A1. Business Action Enabled

This model enables the Preventative Care team to proactively prioritise outreach to the highest-risk pets, with capacity limited to approximately 300 at any given time. Rather than waiting for high-cost claims to occur, the team can intervene early by conducting triage calls, providing care plan guidance, offering veterinary advice, and scheduling follow-ups. The objective is to reduce either the likelihood or severity of a high-cost claim within the next 90 days. Without the model, outreach would be reactive — triggered only after claims arise — or broadly applied without clear prioritisation. With the model in place, pets are ranked each scoring cycle, allowing limited resources to be directed to those with the greatest short-term risk, ensuring interventions are both targeted and operationally efficient.

## A2. Definition of “High Cost”

The current definition of a high-cost claim is any claim greater than $2,000 occurring within 90 days of the scoring snapshot. This provides a clear financial interpretation and aligns the model to materially impactful claims from a business perspective. However, to ensure the definition remains economically relevant over time, I would recommend anchoring the threshold to a severity percentile — for example, targeting the top 5–10% of claims — or formally reviewing the $2,000 cutoff annually in line with claim inflation. This approach preserves a clear financial boundary while maintaining stability and ensuring we consistently identify claims that are genuinely severe, even as treatment costs rise over time.

# Section B: Model Choice
## B1. Model Selected for Deployment

I chose XGBoost. Beyond delivering stronger PR-AUC than logistic regression on the 1st iteration, it was better suited to this problem because the assignment required accurate ranking in the extreme tail for a capacity-constrained Top-300 selection. The data exhibited clear non-linear threshold effects and interaction patterns — such as age acceleration and severity breakpoints — which logistic regression would require manual feature engineering to capture. XGBoost learns these non-linearities and interactions automatically, handles missing values and class imbalance effectively. For this operational ranking use case, it provided both strong performance and great stability.

## B2. Model Deliberately Not Tried

I did not pursue a deep neural network for this assignment because the dataset is structured tabular data with mixed categorical and numeric variables, where tree-based methods typically perform very strongly. Given the limited project timeline of three to four days, implementing a neural network would have required additional effort around architecture selection, hyperparameter tuning, feature embedding for categorical variables, and probability calibration. In contrast, XGBoost is widely recognised as a strong baseline for tabular ranking problems and often matches or outperforms neural networks without the need for extensive architecture search or heavy feature engineering. Given the time constraints and operational focus of the assignment, XGBoost represented the most efficient high-performance and production-ready choice.

# Section C: Evaluation & Threshold
## C1. Recommended Operating Threshold

Operating threshold: 0.712713. Pets with predicted probability ≥ 0.712713 are flagged. This corresponds to selecting the top 300 highest-risk pets in the validation population. Because selection is ranking-based, the threshold will dynamically adjust in future scoring cycles.

## C2. Pets Flagged Per Week

Approximately 300 pets per scoring cycle. If scoring is weekly → ~300 per week. If scoring is monthly → ~300 per month. The operational assumption is weekly scoring.

## C3. Plain-Language Interpretation for the Preventative Care Team

At the current operating threshold, where we select the top 300 highest-risk pets, Precision@300 is approximately 24–30%, meaning roughly one in four flagged pets is expected to lodge a high-cost claim within 90 days if no intervention occurs. This corresponds to a Lift@300 of around 3.7×, indicating the team is approximately three to four times more likely to contact a genuinely high-risk pet compared to random selection. If the business wished to capture more at-risk pets, the threshold could be lowered from 0.7127 to, for example, 0.65. This would increase the flagged population to roughly 500 pets and capture around 100 true high-cost cases instead of 75, but precision would fall to about 20%, meaning an additional 200 lower-risk pets would also be contacted. The trade-off is clear: lowering the threshold increases recall but reduces precision and raises operational workload. Ideally, the operating point should not be determined solely by a fixed capacity constraint, but instead by maximising expected net value — balancing the anticipated savings from prevented high-cost claims against the cost of outreach interventions.

# Section D: Deployment Recommendation
## D1. Deploy Next Month?
At this stage, I would not deploy the model next month without further refinement. While it demonstrates strong ranking performance — with stable Precision@300 and approximately 3–4× lift over random selection — there are important structural improvements needed before live deployment. Most critically, the dataset does not appear to be built on strictly defined policy-level scoring timestamps with time-based validation. Without constructing true historical snapshots and validating performance on forward-looking periods, there is a risk that the model may overstate performance due to subtle temporal leakage or instability under real-world drift.

Before deployment, I would prioritise implementing time-based train/validation splits, rebuilding features strictly from information available at each scoring date, and validating performance across rolling periods. This would provide greater confidence that the model can maintain ranking stability under claim inflation, product changes, and portfolio mix shifts. The current model is a strong prototype and demonstrates clear potential value, but given the operational impact and reputational risk of live deployment, I would recommend strengthening temporal robustness first to ensure sustainable performance in production.

## D2. Biggest Risk

One of the key risks to ongoing performance is temporal drift, where changes in the business or external environment reduce the model’s accuracy over time. Because the model is trained on historical data, any structural shift in claim behaviour may weaken its ranking power. This could occur due to claim inflation, changes in treatment costs, product or pricing adjustments, shifts in portfolio mix, or changes in underwriting practices. If the characteristics of high-cost claims evolve, the patterns learned from past data may no longer fully reflect current risk dynamics. For this reason, ongoing monitoring and periodic retraining are essential to ensure the model continues to rank high-risk pets accurately.

## D3. Weekly Monitoring Framework
The primary performance metric would be Precision@300, measured with a 90-day outcome lag to reflect the true intervention window. This means we evaluate, for each weekly scoring cohort, what proportion of the top 300 flagged pets actually lodged a high-cost claim within the following 90 days. As leading indicators, we would monitor Population Stability Index (PSI) on both the predicted score distribution and the top 10 most important model features. PSI measures how much the current distribution differs from the training baseline. For example, if the proportion of pets falling into higher predicted risk bands shifts materially compared to the original training population, the PSI for the score distribution will increase. Similarly, if key drivers such as age, severity score, or claim frequency begin to show materially different distributions, PSI will quantify that shift. A PSI greater than 0.25 is generally considered a significant distribution change and would trigger an alert.

If an alert is triggered — either because the predicted score distribution PSI exceeds 0.25, or because three or more key driver features each exceed 0.25 — a structured review process would begin. First, we would verify data pipeline integrity to rule out data errors or transformation issues. Next, we would examine which feature distributions have shifted and determine whether the change reflects genuine portfolio evolution (e.g. claim inflation or product changes) rather than data defects. If the shift is real, the model would be retrained using the latest available data and revalidated, with particular focus on Precision@300 stability. If necessary, the operating threshold could be temporarily adjusted while retraining is underway to maintain operational performance.


# Underwriting and pricing

## Pricing Review – Cohort Alignment Analysis

A cohort-level pricing alignment review indicates material misalignment between observed high-cost risk and premium relativities, particularly in senior pets. The 10+ age band exhibits a high-cost claim rate of 19.5%, representing 2.48× portfolio risk, while premiums are only 1.39× portfolio average, resulting in a pricing alignment ratio of 1.78. This implies risk is increasing approximately 78% faster than premium in this segment, indicating likely underpricing. The 7–10 age band shows a milder but similar pattern (alignment ratio 1.08).

Conversely, younger cohorts appear to cross-subsidise older pets. The 0–1 age band carries 0.52× portfolio risk but pays 0.89× portfolio premium, producing an alignment ratio of 0.59, suggesting relative overpricing.

Breed-level analysis reveals additional pressure points. Working and Hound groups show alignment ratios of 1.29 and 1.33 respectively, indicating premiums may not fully reflect elevated risk. Mixed and Domestic Longhair segments appear moderately overpriced (alignment ratios 0.80 and 0.76). Weight bands are broadly aligned, with only minor deviation in large breeds (alignment 1.12).

Overall, structural pricing pressure is concentrated in senior pets and selected higher-risk breed groups, suggesting targeted review of age and breed relativities to reduce cross-subsidisation across cohorts.

## Structural Risk Drivers – Portfolio Segmentation Analysis

Both descriptive analysis and model-based segmentation confirm that risk concentration is systematic rather than random. Age is the dominant structural driver: severe claim rates increase from 4.1% in pets aged 0–1 to 19.5% in pets aged 10+, representing nearly a fivefold increase. This age gradient persists even after adjusting for breed, premium, and prior claims behaviour, reinforcing that senior risk acceleration is a structural portfolio feature.

Breed segmentation remains materially relevant. Pedigree and Toy groups show severe claim rates around 13%, compared to 5–6% in Domestic Longhair and Mixed segments. These gaps are stable and economically meaningful.

Claims behaviour further segments risk. Policies with no vet visits in the past six months show a 7.1% target rate, increasing steadily to 8.9% among pets with four or more visits. In addition, the adjusted claim severity score shows a clear structural breakpoint at approximately 0.6. Above this threshold, severe claim likelihood accelerates materially, identifying a distinct high-severity behavioural cohort not fully explained by age or breed alone.

Premium levels broadly track risk up to approximately $2,000 per year. Beyond this level, incremental risk contribution flattens once underlying factors are considered, suggesting very high premium tiers may reflect coverage selection rather than proportionally higher severe claim risk.

In summary, portfolio risk is concentrated in senior age bands, specific higher-risk breed groups, policies exhibiting increasing claims frequency, and those exceeding the severity score threshold. These cohort-level drivers provide a clear basis for pricing calibration review and improved actuarial alignment across segments.
