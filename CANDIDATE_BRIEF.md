# ML Engineer Case Study — Pet Health Risk Prediction

**PetSure | ML Solutions Team**

---

## Part 1: Business Context

PetSure wants to proactively identify pets at risk of a high-cost insurance claim in the next 90 days. There are two business consumers for this model:

- **Preventative Care team:** They will send nudge communications (email/SMS) to pet parents of flagged pets. They have capacity to contact a maximum of **300 pet parents per week**. False positives waste budget and annoy customers. False negatives mean missed intervention opportunities.
- **Underwriting & Pricing:** They want to understand risk drivers at a cohort level to inform pricing reviews. They do not act on individual predictions; they need aggregate feature importance and risk segmentation.

> **Key constraint:** The Preventative Care team has told you they will not act on more than 300 flags per week. Your model and threshold must respect this operational ceiling. Explain how this constraint influenced your threshold selection.

---

## Part 2: What You Receive

### 2.1 The Dataset

You will receive a CSV file (`pet_health_risk_dataset.csv`) with approximately 25,000 rows and a number of columns. The dataset is synthetic but modelled on realistic distributions. It includes:

- Pet demographics (species, breed group, age band, weight band, desexed status)
- Policy details (cover type, tenure in months, excess amount, premium band)
- Claims history over the prior 12 months (count, total value, max single claim)
- Vet visit indicators (visit count last 6 months, gap since last visit)
- Behavioural proxies (claim frequency trend, seasonal indicators)
- **Target column:** `high_cost_claim_next_90d` (binary: 1 = claim > $2,000 in next 90 days)


### 2.2 Data Dictionary 

You will receive a data dictionary (`data_dictionary.md`).

---

## Part 3: Deliverables

### 3.1 Technical Artefact

Submit either a Jupyter notebook or a small repository with a README. You can invite me to view your repo `@bryce-ram`

#### EDA (Focused)

- Perform EDA on the dataset provided. 
- We are not looking for exhaustive plotting of every feature. Show us the **5–7 most important things you found** in the data.

#### Feature Engineering

- Document every feature you created, dropped, or transformed, and why.

#### Model Training & Evaluation

- Train a model to solve the business problem 
- Validate your model with the appropriate metric, justify your decision as it relates to the problem  
- Justify why you chose that threshold

---

### 3.2 Decision Memo (Max 2 Pages)

This memo must contain **concrete answers, not hedged discussions**. Where a question asks for a number or a yes/no, provide one.


#### Section A: Problem Framing

- **A1.** State the exact business action this model enables. Who does what differently because this model exists?
- **A2.** What definition of "high cost" did you use, and would you change it if you could? Why?

#### Section B: Model Choice

- **B1.** Name the model you would deploy. State one specific reason it is better than an alternative. 
- **B2.** Name one model you deliberately did not try and explain why it was not worth the time given the constraints.

#### Section C: Evaluation & Threshold

- **C1.** State your recommended operating threshold
- **C2.** At that threshold, how many pets per week would be flagged (approximately)? 
- **C3.** Given your evaluation metric, what does this mean in plain language for the Preventative Care team?
- **C4.** If the business said "we want to catch more at-risk pets even if it means more false positives", which direction would you move the threshold and what would the impact be? 

#### Section D: Deployment Recommendation

- **D1.** Would you recommend deploying this model to the Preventative Care team next month? Answer **yes or no**, then justify.
- **D2.** What is the single biggest risk if this model is deployed as-is?
- **D3.** Describe one specific monitoring check you would run weekly post-deployment. What metric, what threshold for alerting, and what action would you take if the alert fires?


---

## Part 4: Constraints

- **Time:** 3–4 days from receipt of the dataset.
- **Tools:** You may use any tools, including AI assistants. However, you must be able to explain every decision in the live walkthrough.
- **Scope:** We do not expect a production-ready system. We expect clear thinking, honest assessment of limitations, and specific recommendations.


