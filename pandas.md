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
