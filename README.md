# A/B Test Design & Results Analysis — Recommendation Algorithm

Full-cycle A/B test analysis for a new content recommendation algorithm
in an entertainment app — from test design and sample size calculation,
through live monitoring, to statistical evaluation of the final results.

**Notebook:** [`AB_Testing_Recommendation_Algorithm.ipynb`](./AB_Testing_Recommendation_Algorithm.ipynb)

---

## 1. Business Context

An entertainment app with an "infinite feed" feature (similar to
short-video apps) monetizes through a paid ad-free subscription and ads
for non-subscribers. The recommendation systems team built a new
algorithm, expected to surface more engaging content per user.

**Goal:** design, monitor, and evaluate the A/B test needed to validate
whether the new algorithm actually improves user engagement — and
deliver a clear go/no-go recommendation to the development team.

**Scope:**
- Explore historical session data to establish a baseline
- Choose a target metric and calculate the required sample size and test duration
- Monitor the live test for correctness (group balance, independence, distribution checks)
- Statistically evaluate the final results and recommend next steps

---

## 2. Tech Stack

- **Python** (pandas, numpy)
- **Matplotlib** — visualizations (line charts, bar charts)
- **SciPy** — Mann-Whitney U test
- **statsmodels** — sample size calculation (`NormalIndPower`), z-test for proportions
- **Jupyter Notebook**

---

## 3. Dataset

Three datasets sharing the same schema, covering different time windows:

| File | Period | Purpose |
|---|---|---|
| `sessions_project_history.csv` | 2025-08-15 – 2025-09-23 | Historical baseline (pre-test) |
| `sessions_project_test_part.csv` | 2025-10-14 (day 1) | Early test monitoring |
| `sessions_project_test.csv` | 2025-10-14 – 2025-11-02 | Full test results |

**Columns:** `user_id`, `session_id`, `session_date`, `session_start_ts`,
`install_date`, `session_number`, `registration_flag`, `page_counter`,
`region`, `device`, `test_group` (test datasets only).

---

## 4. Methodology
 
1. **Historical EDA** — examined session-level behavior for the most
   active user, tracked daily registration trends, analyzed the
   distribution of pages viewed per session, and engineered a
   `good_session` flag (1 if 4+ pages viewed) as a proxy for content
   satisfaction
2. **Test design**
   - **Target metric:** average pages viewed per session — the most
     direct engagement signal, since better recommendations should
     drive more page views
   - **Sample size:** calculated via `NormalIndPower.solve_power()`
     (α = 0.05, power = 0.8, MDE = 3%)
   - **Test duration:** derived from required sample size and average
     daily traffic from the historical data
3. **Live monitoring** — validated the test while running:
   - Group size balance (percentage difference between A and B)
   - Group independence (checked for users appearing in both groups)
   - Distribution balance across device type and region
4. **Results analysis**
   - Validity check: confirmed daily session counts didn't differ
     significantly between groups (Mann-Whitney U test)
   - Compared the share of "good sessions" between groups A and B
   - Tested statistical significance of the observed uplift (z-test for proportions)

---
