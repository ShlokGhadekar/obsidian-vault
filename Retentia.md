**Retentia predicts:**
- whether a customer is likely to churn
- probability of churn
- major factors contributing to the prediction
- which customers should be prioritized for retention

**Tools used:
1. [[pandas]] for eda
2. [[sklearn]] for training
3. 

**Dataset used :** Online Retail II (UCI)  ~1M transaction rows, 2009–2011, real e-commerce invoices per customer
![[Pasted image 20260914221514.png]]

**Target**: customer churn = binary label we derive (e.g., no purchase in the final 2-month holdout window, given the data's observation period).  
**Static/tabular features**: country, average order value, total customer tenure so far, product category diversity, etc. (engineered from transactions, aggregated as-of a cutoff date).  
**Sequential features**: monthly aggregates per customer (order count, total spend, average basket size) over the months leading up to the cutoff — this feeds the LSTM/GRU branch.  
**Limitation to state upfront**: churn label is heuristic, not ground truth, and there's some class imbalance and noisy/cancelled-order rows we'll need to clean.

**Features:**
- **recency_days** — how long since their last purchase (before cutoff). Classic churn signal.
- **frequency** — how many distinct orders they've placed. Loyal customers order more often.
- **total_spend / avg_order_value** — monetary value, standard RFM.
- **tenure_days** — how long they've been a customer. New customers churn differently than long-time ones.
- **distinct_products** — variety of products bought; broader engagement may correlate with lower churn.

for a retail dataset like this (not a subscription), a "customer" who buys once and never returns isn't necessarily a retention failure — they may have never intended to be repeat customers. This is a real limitation of using purchase-based churn as a proxy, and it's worth being upfront about it rather than treating the number as ground truth.(61% churn rate)
## Interview Must-Knows

1. Why shouldn't you commit datasets or trained models to git? (repo bloat, no diffing value, often licensing issues — use `.gitignore` + a README note on how to regenerate)
2. Why pin/track dependencies at all? (reproducibility — someone else, or future-you, can recreate your exact environment)
3. Why a venv instead of installing globally? (isolation — avoids version conflicts across projects)
4. Why do exact duplicate rows sometimes need to stay in transactional data (e.g., two identical items bought in the same order) instead of being blindly dropped?
5. Why is "missing Customer ID" not something you can impute — and why does that force you to drop those rows rather than fill them?
6. Why look at cancellations/returns before deciding whether to include them in "purchase" features (hint: including them could bias frequency/recency features)?
7. Why is it wrong to just do `df[df['Quantity'] > 0]` globally and call it clean, without separately tracking cancellations? (you'd silently lose a real behavioral signal — return rate — that could be predictive)
8. What's the difference between "missing at random" and "missing not at random" — and which do you think Customer ID is? (hint: guest checkouts aren't random — this is a modeling limitation worth stating explicitly in your README)
9. Why compare mean vs. median specifically to detect skew? (in a symmetric distribution they're roughly equal; a big gap means the mean is being pulled by extreme values — a direct sign of skew)
10. Why do we keep the _original_ columns around instead of overwriting them? (useful for EDA/interpretability later — e.g., SHAP explanations are more intuitive in raw units like "3 orders" than in log units)
11. Standardization (z-score scaling) still needs to happen later — why can't we standardize now, before we've split train/val/test? (this is the data leakage question again: fitting a scaler means computing a mean/std, and that must only ever be computed on the training set)