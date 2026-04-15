# 🔍 HR Employee Attrition Analysis
### *Why Are Employees Leaving — and What Is It Costing the Business?*

## 📌 Project Overview

Most attrition analyses stop at surface-level correlations: *"low salary leads to attrition"* or *"overtime increases turnover."* This project goes further.

By investigating **interaction effects** — how two or more variables combine to create risk — this analysis identifies the specific employee segments where attrition is not just elevated, but critical. The findings are translated into six concrete, costed business recommendations with estimated annual savings.

---

## 💼 The Business Problem

> *"What is employee attrition actually costing this organization — and where is that cost concentrated?"*

| Metric | Value |
|---|---|
| Dataset size | 1,470 employees × 35 features |
| Employees who left | 237 (16.1% attrition rate) |
| Industry benchmark | ~10–12% |
| **Estimated annual cost** | **~$18.5 million** (100% salary replacement basis) |
| Potential savings (25% reduction) | **~$4.6 million/year** |

The 16.1% attrition rate is above the industry benchmark — but the aggregate number hides the real problem. As this analysis shows, certain segments of the workforce are experiencing attrition at **3–5× the company average**.

## 📖 Notebook Structure — 6 Chapters

The notebook follows a deliberate structure: every chapter builds on the one before it, and every finding traces back to the $18.5M cost figure established in Chapter 1.

---

### Chapter 1 — Business Problem Definition

**Goal:** Translate headcount loss into financial impact before writing a single line of analysis.

The first chapter builds a cost model using three replacement cost scenarios (50%, 100%, 200% of annual salary) and breaks the total cost down by department.

Key output:
- R&D and Sales each contribute approximately **$6.5M** in estimated annual attrition cost
- Average institutional knowledge lost per exit: **5.1 years**
- If attrition reduced by just 25%: **$4.6M saved annually**

> This framing ensures every subsequent finding has a financial context — not just a statistical one.

---

### Chapter 2 — Data Audit & Cleaning

**Goal:** Document every decision made on all 35 columns — with a business justification for each one.

Every column is categorized into one of four actions:

| Action | Columns | Reason |
|---|---|---|
| **DROP** | EmployeeCount, Over18, StandardHours, EmployeeNumber | Zero variance or identifier — no analytical value |
| **DROP** | DailyRate, HourlyRate, MonthlyRate | Correlation with attrition < 0.05 — verified before dropping |
| **ENCODE** | Attrition, OverTime, BusinessTravel | Categorical strings → numeric for analysis |
| **ENGINEER** | 5 new features | Domain-driven segmentation (see below) |

**Engineered features — built from business logic, not just statistical convenience:**

| Feature | Logic | Why |
|---|---|---|
| `Age_Group` | 18–25 / 26–35 / 36–45 / 46+ | Different career stages have different attrition drivers |
| `Income_Tier` | Salary quartiles (Low / Mid-Low / Mid-High / High) | Avoids assuming salary effect is linear |
| `Tenure_Band` | 0–2 / 3–5 / 6–10 / 11–20 / 20+ years | Captures known early-tenure and mid-career attrition peaks |
| `Stagnation_Flag` | No promotion AND no role change ≥ 3 years | Compound career dissatisfaction signal |
| `Commute_Category` | Near (1–5) / Mid (6–15) / Far (16–29) km | Makes distance interpretable as commute burden |

> Outliers were documented but not removed — these are valid employee records, and removing them would bias analysis against senior employees.

---

### Chapter 3 — Exploratory Data Analysis

**Goal:** Systematically audit every major variable's relationship with attrition. Every finding is benchmarked against the company average of **16.1%**.

**Key single-variable findings:**

| Variable | Finding |
|---|---|
| Age Group | 18–25 year olds leave at **35.8%** — 2.2× the company average |
| Marital Status | Single employees leave at **25.5%** — 2.5× divorced employees |
| Job Role | Sales Representatives: **39.8%** — highest attrition of any role |
| Tenure | First 2 years: **29.8%** — the most vulnerable employment window |
| Income Tier | Low tier: **29.3%** — nearly 3× the High tier (10.3%) |
| Overtime | Overtime employees: **30.5%** — **2.9× non-overtime employees** |
| Business Travel | Frequent travelers: **24.9%** — 3.1× non-travelers |
| Job Involvement | Low involvement (Rating 1): **33.7%** — disengagement is a leading indicator |
| Stock Options | No stock options: **24.4%** vs **9.4%** for Level 1 holders |

> This chapter closes with an important caveat: single-variable analysis tells us *what* correlates. Chapter 4 tells us *where* and *for whom*.

---

### Chapter 4 — Interaction Effect Analysis ⭐

**This is the core of the project.**

Standard attrition analyses report correlations. This chapter investigates how pairs of variables *combine* to create risk — and the findings are dramatically different from what single-variable analysis suggests.

---

**Interaction 1 — Salary × Department**

> *"Does low salary affect all departments equally?"*

| Segment | Attrition Rate |
|---|---|
| Sales + Low Income | **43.8%** |
| R&D + Low Income | **25.2%** |
| Sales + High Income | **17.5%** |
| R&D + High Income | **6.2%** |

**Finding:** Low pay is a crisis in Sales, but a targeted compensation adjustment — not a company-wide pay raise — is what the data recommends. Notably, even high-income Sales employees leave at 17.5%, suggesting that compensation alone is not the full answer for Sales.

---

**Interaction 2 — Overtime × Tenure**

> *"Is overtime equally damaging at all career stages?"*

| Tenure Band | No Overtime | With Overtime | Multiplier |
|---|---|---|---|
| 0–2 years | 20.6% | **51.0%** | **2.5×** |
| 3–5 years | 7.8% | 28.3% | 3.6× |
| 11–20 years | 5.8% | 9.3% | 1.6× |

**Finding:** Overtime is NOT a uniform risk. It is a new-employee crisis. The same overtime policy that is tolerable for a 10-year employee is catastrophic for someone in their first 2 years. Policies should be applied asymmetrically.

---

**Interaction 3 — Work-Life Balance × Age Group**

> *"Do different age groups respond differently to poor work-life balance?"*

| Age Group | WLB Rating 1 (Bad) | WLB Rating 4 (Best) |
|---|---|---|
| 18–25 | 40.0% | 38.9% |
| 36–45 | **40.9%** | **5.9%** |
| 46+ | 11.1% | 16.7% |

**Finding:** Work-life balance is life-stage dependent, not universal. Mid-career employees (36–45) are acutely sensitive — poor WLB drives 40.9% attrition, but good WLB brings this to 5.9%. Young employees leave regardless of WLB score, suggesting other drivers dominate for that group.

---

**Interaction 4 — Marital Status × Overtime**

> *"Does overtime affect single employees differently?"*

| Segment | Attrition Rate |
|---|---|
| Single, No Overtime | 16.2% |
| Married, Overtime | 23.1% |
| Divorced, Overtime | 19.2% |
| **Single, Overtime** | **49.6%** |

**Finding:** Being single does not inherently cause attrition (16.2% without overtime). It is the *combination* of single status and overtime that creates the highest-risk identifiable segment in this dataset — nearly 1 in 2 employees leaving.

---

### Chapter 5 — Statistical Risk Scoring

**Goal:** Build a transparent, explainable flight risk score for every employee — without machine learning.

Rather than a black-box model, this chapter builds a **weighted factor score (0–100)** based on the 11 most predictive variables identified in Chapters 3 and 4. Every weight is documented and justified.

| Factor | Weight | Justification |
|---|---|---|
| Overtime | 18 | 2.9× attrition multiplier — strongest binary predictor |
| Low Income | 15 | 29.3% attrition in low tier — gradient effect confirmed |
| Short Tenure | 14 | 29.8% in first 2 years — strongest tenure effect |
| Low Job Involvement | 12 | 33.7% attrition at Rating 1 — leading indicator |
| Low Job Satisfaction | 10 | Clear inverse relationship confirmed |
| Single/Married | 9 | Amplified by overtime interaction |
| Business Travel | 8 | Ordered 0→4→8 by travel intensity |
| Poor Work-Life Balance | 7 | Critical for 36–45 age group |
| No Stock Options | 6 | 60% attrition reduction at Level 1 |
| Career Stagnation | 5 | Compound dissatisfaction signal |
| Young Age (18–25) | 4 | Independent signal beyond tenure |

**Why this is better than a Random Forest for HR:**
- Every score is **explainable** — HR can see exactly why an employee scores high
- No black box — the score can be audited, challenged, and refined by the business
- Identifies **Critical and High risk employees before they resign** — giving HR a 3–6 month intervention window

---

### Chapter 6 — Business Recommendations & ROI

**Goal:** Translate every finding into a concrete, costed recommendation.

| Recommendation | Target Segment | Est. Annual Savings* |
|---|---|---|
| Sales compensation audit | ~200 Sales employees in low-mid income tiers | Largest savings driver |
| Overtime protection for new hires | ~100 employees with ≤2 years tenure on overtime | High priority |
| WLB programs for mid-career employees | ~60 employees aged 36–45 with WLB ≤ 2 | High ROI per person |
| Retention program for single employees on overtime | ~120 single employees on overtime | Immediate intervention |
| Expand stock options to high-risk employees | High/Critical risk employees with Level 0 stock | Structural fix |
| Career development for stagnant employees | ~338 employees with Stagnation_Flag = 1 | Long-term retention |

*\*Estimated at 25% attrition reduction in each target segment, using 100% salary replacement cost basis.*

---

## 🛠️ Tools & Libraries

| Tool | Purpose |
|---|---|
| Python | Core language |
| Pandas | Data manipulation and feature engineering |
| NumPy | Numerical operations |
| Matplotlib | Custom visualizations |
| Seaborn | Statistical plots and heatmaps |
| Jupyter Notebook | Development and presentation environment |

---

## 📊 Dataset

**Source:** [https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset) via Kaggle

| Property | Value |
|---|---|
| Rows | 1,470 employees |
| Original columns | 35 features |
| Missing values | 0 |
| Columns dropped | 7 (zero variance or identifier columns) |
| Features engineered | 8 new columns |
| Target variable | Attrition (Yes / No) |

---

## 🚀 How to Run

**1. Clone the repository**
```bash
git clone https://github.com/yourusername/hr-attrition-analysis.git
cd hr-attrition-analysis
```

**2. Install dependencies**
```bash
pip install pandas numpy matplotlib seaborn jupyter
```

**3. Launch the notebook**
```bash
jupyter notebook HR_Attrition_Analysis.ipynb
```

**4. Run all cells**

Run cells sequentially from top to bottom. All chapters share a single kernel session — do not restart between chapters. Charts will be saved automatically to the working directory.

---

## 💡 Key Design Decisions

**Business-first framing.**
The analysis begins with a cost model ($18.5M), not a confusion matrix. Every subsequent finding is anchored to that number — so results are presented in the language of business, not statistics.

**Interaction effects over correlations.**
The most actionable findings in this project came from Chapter 4 — not from the single-variable correlations in Chapter 3. Single employees on overtime leaving at 49.6% is not visible in any single-variable analysis.

**Outliers retained.**
Statistical outliers (high-income employees, 40-year tenure records) were documented but not removed. These are valid employee records. Removing them would bias the analysis against senior employees.

---

## 📈 Results Summary

| Finding | Insight |
|---|---|
| Highest-risk single segment | Single employees on overtime → **49.6% attrition** |
| New hire crisis | First-2-year employees on overtime → **51.0% attrition** |
| Sales compensation gap | Sales + Low Income → **43.8% attrition** |
| Work-life balance sweet spot | Mid-career (36–45) + Good WLB → **5.9% attrition** |
| Equity as retention tool | Stock Level 0 → 24.4% vs Level 1 → **9.4% attrition** |
| Estimated annual cost | **~$18.5M** at 100% salary replacement |
| Potential annual savings | **~$4.6M** with 25% attrition reduction |

---

## 👤 Author
[Apoorv Vimal]
LinkedIn: [https://www.linkedin.com/in/apoorv-vimal-analytics/]
Email: [vimalapoorv08@gmail.com]
