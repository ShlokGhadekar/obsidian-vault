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
