# 💳A business driven ML system that catches 95% of credit card fraud. It is built to protect revenue, reduce false alarms, and give risk teams full control over detection sensitivity.

---

## 🧩 The Business Problem

Credit card fraud costs the global financial industry **over $32 billion annually**. For any bank or payment processor, fraud sits at the intersection of two painful trade-offs:

| Risk | Business Impact |
|------|----------------|
| **Missing a fraud** (False Negative) | Direct financial loss ,the company absorbs the charge |
| **Flagging a legitimate transaction** (False Positive) | Customer frustration, blocked cards, churn, reputational damage |

A model that simply predicts "not fraud" on every transaction would be **99% accurate**  and completely useless. This project was built around that reality.

The goal was never to maximize accuracy. The goal was to **minimize the cost of being wrong**.

---

## 💡 The Solution

This project delivers a **production-aware fraud detection system** built on three machine learning models, with deliberate threshold optimization to give the business control over the precision vs. recall trade-off.

**The model doesn't just flag fraud , it helps the company decide how aggressively to flag it.**

---

## 📊 Model Performance Summary

| Model | Precision | Recall | ROC-AUC | Verdict |
|-------|-----------|--------|---------|---------|
| Random Forest | 0.74 | 0.80 | 0.89 | ✅ Strong baseline |
| Logistic Regression | ~0.00 | ~0.00 | ~0.50 | ❌ Failed on imbalanced data |
| **XGBoost (Final)** | **0.61** | **0.95** | **0.97** | ✅ **Production winner** |

### 🏆 Why XGBoost Wins in Business Terms

- **Catches 95% of all fraud** — meaning for every 100 fraudulent transactions, only 5 slip through undetected
- **97% ROC-AUC** — the model clearly separates fraudsters from legitimate customers
- **Precision of 61%** — out of every fraud alert raised, 61% are real fraud (the other 39% are false alarms — manageable with threshold tuning)

---

## 🎛️ Threshold Optimization — The Business Control Lever

One of the most important features of this system is **customizable decision thresholds**. Most models ship with a default 0.5 threshold. This project shows why that's a business decision, not a technical default.

| Threshold | Precision | Recall | Best For |
|-----------|-----------|--------|----------|
| **0.1** | Low | Very High | Maximum fraud capture, high false alarm rate — suitable for high-value transaction monitoring |
| **0.3** | Medium | **0.87** | Balanced fraud detection with manageable false alarms — **recommended for operations teams** |
| **0.5** | High | Lower | Conservative flagging, fewer customer disruptions — suitable for low-risk merchant categories |

**Business translation:** A risk manager can turn this dial. A compliance team running overnight batch fraud review would use 0.1. A real-time card-present system would use 0.5. **The model gives the business that choice.**

---

## 🔬 What Makes This Model Smarter Than a Rules Engine

Traditional fraud systems use hard-coded rules: *"Flag if transaction > $500 and location ≠ home city."* This model learns patterns rules can't capture.

### Key Features Engineered for Fraud Signal

| Feature | What It Detects |
|---------|----------------|
| `time_since_last_transaction` | Card testing behavior — fraudsters make rapid small charges to verify cards |
| `is_rapid_txn` | Binary flag: transactions < 60 seconds apart are a strong fraud signal |
| `distance_from_center` | Euclidean distance from the customer's typical transaction geography |
| `age` | Derived from DOB — certain age groups show different fraud risk profiles |
| `season_of_day` | Fraud spikes at Night — the model captures this pattern |
| `time_of_the_week` | Weekend vs. weekday spending patterns differ between fraud and legitimate use |
| Cyclical time encoding | Hour, day, month encoded as sine/cosine — the model understands that 11pm and 1am are "close," not numerically far apart |

---

## 🏗️ Technical Architecture

```
Raw Transaction Data
        │
        ▼
┌─────────────────────────────┐
│   Exploratory Data Analysis  │  ← Missing values, duplicates, distributions
│   (EDA)                      │    Uni & multivariate analysis
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│   Feature Engineering        │  ← 8 new features created
│                              │    Datetime decomposition
│                              │    Behavioral signals (rapid txn flag)
│                              │    Geographic features
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│   Encoding Pipeline          │  ← Cyclical (time), Binary (gender),
│                              │    Frequency/Count (high-cardinality),
│                              │    One-Hot (low-cardinality)
│                              │    Applied POST-split (no data leakage)
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│   Imbalance Handling         │  ← SMOTE (synthetic minority oversampling)
│                              │    scale_pos_weight = 172 (XGBoost)
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│   Model Training             │  ← Random Forest → XGBoost → Logistic Regression
│   + Hyperparameter Tuning    │    RandomizedSearchCV with cross-validation
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│   Threshold Optimization     │  ← Compared 0.1 / 0.3 / 0.5
│   + SHAP Explainability      │    Feature importance via SHAP values
└─────────────────────────────┘
        │
        ▼
    Business Decision
```

---

## 🧪 Model Experiments & What We Learned

### Why Not Logistic Regression?
Logistic Regression assumed a linear boundary between fraud and non-fraud — an assumption that breaks completely in the real world where fraud patterns are complex and non-linear. With SMOTE applied, the model returned Precision: 0.00, Recall: 0.00, ROC-AUC: 0.50 — no better than random guessing. **Conclusion: Linear models are ineffective for imbalanced, non-linear fraud classification.**

### Why Not Random Forest Alone?
Random Forest performed well (ROC-AUC: 0.89) and was the ideal baseline — it handles imbalanced data without needing feature scaling, is robust to outliers, and requires minimal preprocessing. However, XGBoost with `scale_pos_weight` and SMOTE outperformed it on the metrics that matter most for fraud: Recall and AUC.

### Why XGBoost?
XGBoost's gradient boosting approach iteratively corrects the errors of previous trees, making it exceptional at learning rare event patterns. Combined with `scale_pos_weight=172` (the ratio of non-fraud to fraud in the training set), the model was explicitly penalized for missing fraud cases — aligning the training objective with the business objective.

---

## 📂 Repository Structure

```
credit-card-fraud-detection/
│
├── notebooks/
│   ├── random-forest-fd.ipynb          # Baseline model + EDA + Feature Engineering
│   ├── xgboost-fd.ipynb                # Final production model
│   ├── logistic-regression.ipynb       # Comparative experiment
│   └── hypothesis-testing.ipynb        # Statistical validation (Mann-Whitney U)
│
├── README.md                           # You are here
└── requirements.txt                    # Dependencies
```

---

## ⚙️ Requirements & Setup

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
pip install xgboost imbalanced-learn shap
pip install feature-engine category_encoders
```

**Dataset:** [Credit Card Fraud Detection Dataset](https://www.kaggle.com/datasets/oluwanifemiabimbola/frauddetectiondataset) — available on Kaggle.

---

## 🧪 Statistical Validation — Hypothesis Testing

Good models need statistical backing. This section validates a core business assumption with rigorous testing: **do fraudulent transactions actually involve higher amounts?** If true, transaction amount is a legitimate risk signal — and the model is right to weight it.

### Hypothesis

| | Statement |
|--|-----------|
| **H₀ (Null)** | Fraudulent transactions do NOT have significantly higher amounts than legitimate ones |
| **H₁ (Alternative)** | Fraudulent transactions have significantly higher transaction amounts |
| **Significance level** | α = 0.05 |

### Why Mann-Whitney U Test?

The transaction amount data is **not normally distributed** — it is right-skewed, with a long tail of high-value transactions. This violates the assumption of a standard t-test. The **Mann-Whitney U test** is the correct non-parametric alternative: it compares the rank distributions of two groups without assuming normality, making it robust for real-world financial data.

A **log transformation (log1p)** was applied to the amount before testing to further stabilise the distribution and reduce the influence of extreme outliers.

### Result

```
✅ Reject H₀ — Fraud transactions have significantly higher amounts (p < 0.05)
```

### Business Meaning

This result is statistically significant. It confirms that **transaction amount is a genuine, evidence-backed fraud signal** — not a coincidence in the data. In practical terms:

- Fraud analysts can justifiably apply extra scrutiny to high-value transactions
- The model's use of `amt` as a feature is statistically validated
- Risk-based transaction limits (e.g., flagging transactions above a threshold for manual review) have a statistical foundation, not just an intuitive one

> This bridges the gap between model output and regulatory defensibility — banks and insurers need to justify *why* a model flags what it flags. Statistical testing provides that justification.

---

## 🚀 Business Recommendations

Based on the model's behavior and threshold analysis, here's how a financial institution should deploy this:

1. **Use threshold 0.3 for real-time transaction monitoring** — captures 87%+ of fraud with a manageable false alarm rate
2. **Use threshold 0.1 for overnight batch review of high-value transactions** — prioritize catching everything over $1,000
3. **Use SHAP feature importance to brief fraud analysts** — the model's top signals (`time_since_last_transaction`, `distance_from_center`, `is_rapid_txn`) align with known fraud typologies and can be used to train human reviewers
4. **Re-train quarterly** — fraud patterns evolve with attacker behavior; a static model degrades over time

---

## 📈 Business Impact Potential

Assuming a mid-size issuer processing 1 million transactions/month with a 0.6% fraud rate (6,000 fraud cases/month) at an average fraud value of $150:

| Scenario | Monthly Fraud Losses |
|----------|---------------------|
| No model (0% detection) | $900,000 |
| Industry average (70% detection) | $270,000 |
| **This model at threshold 0.3 (87% detection)** | **$117,000** |
| **Improvement over industry average** | **~$153,000/month saved** |

*These are illustrative estimates based on published industry fraud rates.*

---

## 🔮 Future Improvements

- [ ] Build a unified sklearn `Pipeline` to streamline preprocessing and deployment
- [ ] Add a Precision-Recall curve visualization across all threshold values
- [ ] Implement a business cost matrix (assigning dollar values to false negatives vs false positives)
- [ ] Explore real-time inference with a FastAPI endpoint
- [ ] Test on the holdout `fraudTest.csv` for final generalization check
- [ ] Add model monitoring/drift detection for production use

---

## 👨‍💻 Author

Built as a first end-to-end machine learning project with a focus on **business impact over academic metrics**.

The core question driving every decision: *"Would a fraud team actually use this, and would it save the company money?"*

---

*If you found this useful, consider starring the repo ⭐*
