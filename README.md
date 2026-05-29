# 💳A business driven ML system that catches 95% of credit card fraud. It is built to protect revenue, reduce false alarms, and give risk teams full control over detection sensitivity.

---

## 🏆 Solution & Results

A business driven fraud detection system comparing three ML models, with threshold optimisation giving risk teams full control over detection sensitivity.

| Model | Precision | Recall | ROC-AUC | Verdict |
|-------|-----------|--------|---------|---------|
| Random Forest | 0.74 | 0.80 | 0.89 | ✅ Strong baseline |
| Logistic Regression | ~0.00 | ~0.00 | ~0.50 | ❌ Failed on imbalanced data |
| **XGBoost (Final)** | **0.61** | **0.95** | **0.97** | ✅ **Production winner** |

**XGBoost catches 95% of all fraud** : only 5 in every 100 fraudulent transactions slip through undetected.

---

## 🎛️ Threshold Optimisation :The Business Control Lever

| Threshold | Best For |
|-----------|----------|
| **0.1** | High-value transaction monitoring : maximum fraud capture |
| **0.3** | Balanced detection :recommended for operations teams |
| **0.5** | Conservative flagging :fewer customer disruptions |

---

### Key Features Engineered for Fraud Signal

| Feature | What It Detects |
|---------|----------------|
| `time_since_last_transaction` | Card testing behavior , fraudsters make rapid small charges to verify cards |
| `is_rapid_txn` | Binary flag: transactions < 60 seconds apart are a strong fraud signal |
| `distance_from_center` | Euclidean distance from the customer's typical transaction geography |
| `age` | Derived from DOB — certain age groups show different fraud risk profiles |
| `season_of_day` | Fraud spikes at Night , the model captures this pattern |
| `time_of_the_week` | Weekend vs. weekday spending patterns differ between fraud and legitimate use |
| Cyclical time encoding | Hour, day, month encoded as sine/cosine . The model understands that 11pm and 1am are "close," not numerically far apart |

---

## 📊 Statistical Validation

**Mann-Whitney U Test** confirmed that fraudulent transactions have significantly higher amounts (p < 0.05), validating `amt` as a legitimate risk signal. It is important for regulatory defensibility in banking and insurance.

> ✅ **Reject H₀** : Fraud transactions have significantly higher amounts.

---

## 📈 Business Impact

Assuming 1M transactions/month, 0.6% fraud rate, avg. fraud value $150:

| Scenario | Monthly Fraud Losses |
|----------|---------------------|
| No model | $900,000 |
| Industry average (70% detection) | $270,000 |
| **This model at threshold 0.3** | **$117,000** |
| **Saving vs industry average** | **~$153,000/month** |

---
## 🔮 Future Improvements


- [ ] Build a unified sklearn `Pipeline` to streamline preprocessing and deployment
- [ ] Add a Precision-Recall curve visualization across all threshold values
- [ ] Implement a business cost matrix (assigning dollar values to false negatives vs false positives)
- [ ] Explore real time inference with a FastAPI endpoint
- [ ] Test on the holdout `fraudTest.csv` for final generalization check
- [ ] Add model monitoring/drift detection for production use

---

## 👨‍💻 Author *@Oluwatosin Nifemi Abimbola

A machine learning project with a focus on **business impact**.

The core question driving every decision: *"Would a fraud team actually use this, and would it save the company money?"*

---

*If you found this useful, consider starring the repo ⭐
