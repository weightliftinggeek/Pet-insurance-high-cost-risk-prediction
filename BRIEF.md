# ML Engineer Case Study — Pet Health Risk Prediction

**Pet insurance company**

---

## Part 1: Business Context

The company wants to proactively identify pets at risk of a high-cost insurance claim in the next 90 days. There are two business consumers for this model:

- **Preventative Care team:** They will send nudge communications (email/SMS) to pet parents of flagged pets. They have capacity to contact a maximum of **300 pet parents per week**. False positives waste budget and annoy customers. False negatives mean missed intervention opportunities.
- **Underwriting & Pricing:** They want to understand risk drivers at a cohort level to inform pricing reviews. They do not act on individual predictions; they need aggregate feature importance and risk segmentation.

> **Key constraint:** The Preventative Care team has told you they will not act on more than 300 flags per week. Your model and threshold must respect this operational ceiling. Explain how this constraint influenced your threshold selection.

---

## Part 2: What are provided

### 2.1 The Dataset

A CSV file (`pet_health_risk_dataset.csv`) with approximately 25,000 rows and a number of columns. The dataset is synthetic but modelled on realistic distributions. It includes:

- Pet demographics (species, breed group, age band, weight band, desexed status)
- Policy details (cover type, tenure in months, excess amount, premium band)
- Claims history over the prior 12 months (count, total value, max single claim)
- Vet visit indicators (visit count last 6 months, gap since last visit)
- Behavioural proxies (claim frequency trend, seasonal indicators)
- **Target column:** `high_cost_claim_next_90d` (binary: 1 = claim > $2,000 in next 90 days)


### 2.2 Data Dictionary 

A data dictionary (`data_dictionary.md`).

---

## Part 3: Deliverables

### 3.1 Technical Artefact

A Jupyter notebook or a small repository with a README.

#### EDA (Focused)

- Perform EDA on the dataset provided. 

#### Feature Engineering

- Document every feature created, dropped, or transformed, and why.

#### Model Training & Evaluation

- Train a model to solve the business problem 
- Validate the model with the appropriate metric, justify the decision as it relates to the problem  
- Justify why the model was chosen with that threshold

---

### 3.2 Decision Memo (Max 2 Pages)

#### Section A: Problem Framing

- **A1.** State the exact business action this model enables. Who does what differently because this model exists?
- **A2.** What definition of "high cost" was used, and would I change it if I could? Why?

#### Section B: Model Choice

- **B1.** Name the model you would deploy. State one specific reason it is better than an alternative. 
- **B2.** Name one model you deliberately did not try and explain why it was not worth the time given the constraints.

#### Section C: Evaluation & Threshold

- **C1.** State the recommended operating threshold
- **C2.** At that threshold, how many pets per week would be flagged (approximately)? 
- **C3.** Given the evaluation metric, what does this mean in plain language for the Preventative Care team?
- **C4.** If the business said "we want to catch more at-risk pets even if it means more false positives", which direction should we move the threshold and what would the impact be? 

#### Section D: Deployment Recommendation

- **D1.** Would you recommend deploying this model to the Preventative Care team next month?
- **D2.** What is the single biggest risk if this model is deployed as-is?
- **D3.** Describe one specific monitoring check that should be run weekly post-deployment. What metric, what threshold for alerting, and what action should be taken if the alert fires?




