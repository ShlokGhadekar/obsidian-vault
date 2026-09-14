**Retentia predicts:**
- whether a customer is likely to churn
- probability of churn
- major factors contributing to the prediction
- which customers should be prioritized for retention

**Tools used:
1. [[pandas]] for eda

**Dataset used :** Online Retail II (UCI)  ~1M transaction rows, 2009–2011, real e-commerce invoices per customer
![[Pasted image 20260914221514.png]]

**Target**: customer churn = binary label we derive (e.g., no purchase in the final 2-month holdout window, given the data's observation period).  
**Static/tabular features**: country, average order value, total customer tenure so far, product category diversity, etc. (engineered from transactions, aggregated as-of a cutoff date).  
**Sequential features**: monthly aggregates per customer (order count, total spend, average basket size) over the months leading up to the cutoff — this feeds the LSTM/GRU branch.  
**Limitation to state upfront**: churn label is heuristic, not ground truth, and there's some class imbalance and noisy/cancelled-order rows we'll need to clean.

## Interview Must-Knows

1. Why shouldn't you commit datasets or trained models to git? (repo bloat, no diffing value, often licensing issues — use `.gitignore` + a README note on how to regenerate)
2. Why pin/track dependencies at all? (reproducibility — someone else, or future-you, can recreate your exact environment)
3. Why a venv instead of installing globally? (isolation — avoids version conflicts across projects)
4. Why do exact duplicate rows sometimes need to stay in transactional data (e.g., two identical items bought in the same order) instead of being blindly dropped?
5. Why is "missing Customer ID" not something you can impute — and why does that force you to drop those rows rather than fill them?
6. Why look at cancellations/returns before deciding whether to include them in "purchase" features (hint: including them could bias frequency/recency features)?
7. Why is it wrong to just do `df[df['Quantity'] > 0]` globally and call it clean, without separately tracking cancellations? (you'd silently lose a real behavioral signal — return rate — that could be predictive)
8. What's the difference between "missing at random" and "missing not at random" — and which do you think Customer ID is? (hint: guest checkouts aren't random — this is a modeling limitation worth stating explicitly in your README)