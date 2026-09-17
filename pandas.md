```python
import pandas as pd
sheet_2009_2010 = pd.read_excel("../data/raw/online_retail_II.xlsx", sheet_name=0)
sheet_2010_2011 = pd.read_excel("../data/raw/online_retail_II.xlsx", sheet_name=1)

df = pd.concat([sheet_2009_2010, sheet_2010_2011], ignore_index=True)
print(df.shape)
df.head()
```

```python
df.info()
df.isna().sum()
```

```python
print(df.duplicated().sum())

# Cancelled orders: Invoice starting with 'C'
df['Invoice'] = df['Invoice'].astype(str)
cancelled = df['Invoice'].str.startswith('C')
print(cancelled.sum(), "cancelled invoice rows")

# Negative or zero quantity/price often = returns, adjustments, or data errors
print((df['Quantity'] <= 0).sum())
print((df['Price'] <= 0).sum())
```
- **243,007 missing Customer ID (~23%)** — drop these. Can't build customer-level features without knowing who the customer is, and there's no valid way to impute an ID.
- **34,335 exact duplicate rows** — these are almost always double-scanned line items (same invoice, same product, same everything). We'll drop them, but only _after_ filtering to valid customers, so we don't waste time deduplicating rows we're removing anyway.
- **19,494 cancelled invoices** (`Invoice` starts with `'C'`) — these represent returns/refunds, not purchases. They're valuable _context_ (a customer who returns a lot is a signal) but should **not** count as "purchase frequency" — they'd artificially inflate or distort the RFM features we build later.
- **22,950 rows with `Quantity <= 0`** — this count is close to, but not identical to, the cancelled-invoice count. Some are cancellations, but some are non-cancellation rows with 0/negative quantity (adjustments, damaged goods, etc.) — these are junk for our purposes.
- **6,207 rows with `Price <= 0`** — usually manual entries, bank charges, "adjustment" line items, samples. Not real purchases. We'll drop these.

### cleaning
```python
# 1. Drop rows with no Customer ID
df = df.dropna(subset=['Customer ID'])

# 2. Separate cancellations before dropping — we'll use them later as a feature, not for frequency
df['is_cancelled'] = df['Invoice'].str.startswith('C')

# 3. Keep only genuine positive-value purchase rows for the "purchases" table
purchases = df[
    (~df['is_cancelled']) &
    (df['Quantity'] > 0) &
    (df['Price'] > 0)
].copy()

# 4. Drop exact duplicates within purchases
before = len(purchases)
purchases = purchases.drop_duplicates()
print(f"Dropped {before - len(purchases)} duplicate rows")

print(purchases.shape)
```
*Dropped 26124 duplicate rows (779425, 9)*

```python
print(purchases['InvoiceDate'].min())
print(purchases['InvoiceDate'].max())
```
*2009-12-01 07:45:00 
2011-12-09 12:50:00*

```python
import pandas as pd

cutoff = pd.Timestamp("2011-09-30")

history = purchases[purchases['InvoiceDate'] < cutoff]
future = purchases[purchases['InvoiceDate'] >= cutoff]

customers_with_history = set(history['Customer ID'].unique())
customers_with_future_purchase = set(future['Customer ID'].unique())

print("Customers with history (feature-eligible):", len(customers_with_history))
print("Of those, purchased again after cutoff (not churned):",
      len(customers_with_history & customers_with_future_purchase))
print("Churn rate:", 1 - len(customers_with_history & customers_with_future_purchase) / len(customers_with_history))
```
Customers with history (feature-eligible): 5430 Of those, purchased again after cutoff (not churned): 2132 Churn rate: 0.6073664825046041
*61% churn rate*


### Building a feature set:
```python
cutoff = pd.Timestamp("2011-09-30")

# Total spend per invoice line
history = history.copy()
history['LineTotal'] = history['Quantity'] * history['Price']

# Group by customer
agg = history.groupby('Customer ID').agg(
    recency_days=('InvoiceDate', lambda x: (cutoff - x.max()).days),
    frequency=('Invoice', 'nunique'),          # number of distinct orders
    total_spend=('LineTotal', 'sum'),
    avg_order_value=('LineTotal', 'sum'),      # placeholder, fix below
    tenure_days=('InvoiceDate', lambda x: (cutoff - x.min()).days),
    avg_quantity=('Quantity', 'mean'),
    distinct_products=('StockCode', 'nunique'),
).reset_index()

# avg_order_value = total spend / number of orders (fix the placeholder)
agg['avg_order_value'] = agg['total_spend'] / agg['frequency']

print(agg.shape)
agg.head()
```
![[Pasted image 20260917130143.png]]
