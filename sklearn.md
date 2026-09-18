# Scikit-learn (sklearn) — Quick Notes

## 1. ML Workflow

```text
Data → Clean → X,y → Train/Test Split → Preprocess → Train → Predict → Evaluate → Tune → Save
```

```python
X = df.drop("target", axis=1)
y = df["target"]

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

model.fit(X_train, y_train)
y_pred = model.predict(X_test)
```

---

## 2. Train/Test Split

```python
from sklearn.model_selection import train_test_split
```

- Train → learn
- Test → final evaluation
- `random_state=42` → reproducible
- Classification → often use `stratify=y`

---

## 3. Preprocessing

### Scaling

```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)
```

**Important:** `fit` only on training data.

Scaling important for:
- Logistic Regression
- KNN
- SVM
- PCA
- K-Means

Usually unnecessary for:
- Decision Trees
- Random Forest

### Encoding

```python
from sklearn.preprocessing import OneHotEncoder
```

Categorical → numerical.

```python
OneHotEncoder(handle_unknown="ignore")
```

### Missing Values

```python
from sklearn.impute import SimpleImputer

SimpleImputer(strategy="mean")
```

Strategies: `mean`, `median`, `most_frequent`, `constant`

---

## 4. Pipeline ⭐

Combines preprocessing + model.

```python
from sklearn.pipeline import Pipeline

pipe = Pipeline([
    ("scaler", StandardScaler()),
    ("model", LogisticRegression())
])

pipe.fit(X_train, y_train)
```

**Main benefit:** prevents data leakage + cleaner workflow.

---

## 5. Common Models

### Classification

```python
LogisticRegression()
KNeighborsClassifier()
DecisionTreeClassifier()
RandomForestClassifier()
SVC()
GaussianNB()
```

### Regression

```python
LinearRegression()
Ridge()
Lasso()
DecisionTreeRegressor()
RandomForestRegressor()
```

### Unsupervised

```python
KMeans()
PCA()
```

---

## 6. Models — Know the Idea

| Model | Main Idea |
|---|---|
| Linear Regression | Fits linear relationship |
| Logistic Regression | Classification using probabilities |
| KNN | Predict using nearest points |
| Decision Tree | If/else splits |
| Random Forest | Many decision trees |
| SVM | Finds separating boundary |
| K-Means | Groups similar points |
| PCA | Reduces dimensions |

---

## 7. Classification Metrics

```python
accuracy_score(y_test, y_pred)
precision_score(y_test, y_pred)
recall_score(y_test, y_pred)
f1_score(y_test, y_pred)
confusion_matrix(y_test, y_pred)
```

```text
Precision = TP / (TP + FP)
Recall    = TP / (TP + FN)
F1        = harmonic mean of Precision + Recall
```

**Accuracy:** overall correctness.

**Precision:** "When I predict positive, am I right?"

**Recall:** "Did I find most actual positives?"

### Confusion Matrix

```text
          Predicted
          0     1
Actual 0  TN    FP
       1  FN    TP
```

### ROC-AUC

Measures how well the model separates classes.

```python
roc_auc_score(y_test, y_prob)
```

---

## 8. Regression Metrics

```python
mean_absolute_error(y_test, y_pred)
mean_squared_error(y_test, y_pred)
r2_score(y_test, y_pred)
```

- **MAE** → average absolute error
- **MSE** → penalizes large errors more
- **RMSE** → √MSE, same units as target
- **R²** → variance explained by model

---

## 9. Cross Validation

```python
from sklearn.model_selection import cross_val_score

scores = cross_val_score(model, X, y, cv=5)
scores.mean()
```

**5-fold CV:** train/test process repeated 5 times using different validation folds.

---

## 10. Hyperparameter Tuning

### GridSearchCV

```python
from sklearn.model_selection import GridSearchCV

grid = GridSearchCV(model, params, cv=5)
grid.fit(X_train, y_train)

grid.best_params_
```

### RandomizedSearchCV

Tests random combinations → useful when many parameters exist.

---

## 11. Overfitting / Underfitting

```text
Underfitting → model too simple
Overfitting  → model memorizes training data
```

Overfitting fixes:
- More data
- Regularization
- Simpler model
- Cross-validation
- Feature selection

---

## 12. Data Leakage ⭐⭐⭐

**Leakage = test/future information influences training.**

Wrong:

```python
scaler.fit_transform(X)   # before split
```

Correct:

```python
scaler.fit_transform(X_train)
scaler.transform(X_test)
```

Pipeline is the safest approach.

---

## 13. Class Imbalance

Example:

```text
99% Not Fraud
1%  Fraud
```

Accuracy can be misleading.

Use:
- Precision
- Recall
- F1
- ROC-AUC
- Confusion Matrix

Can use:

```python
LogisticRegression(class_weight="balanced")
```

---

## 14. Probability

```python
model.predict_proba(X_test)
```

Returns class probabilities.

Useful when adjusting classification thresholds.

---

## 15. Feature Importance

Tree models:

```python
model.feature_importances_
```

Lasso can perform feature selection by shrinking some coefficients to zero.

---

## 16. Save Model

```python
import joblib

joblib.dump(model, "model.pkl")

model = joblib.load("model.pkl")
```

Prefer saving the **entire pipeline**.

---

# Must-Know APIs

```python
train_test_split()

StandardScaler()
OneHotEncoder()
SimpleImputer()
Pipeline()

LogisticRegression()
LinearRegression()
DecisionTreeClassifier()
RandomForestClassifier()
KNeighborsClassifier()
SVC()
KMeans()
PCA()

accuracy_score()
precision_score()
recall_score()
f1_score()
confusion_matrix()
roc_auc_score()

mean_absolute_error()
mean_squared_error()
r2_score()

cross_val_score()
GridSearchCV()
RandomizedSearchCV()
```

# Interview Checklist

- [ ] ML workflow
- [ ] Train vs test
- [ ] `fit()` vs `transform()` vs `fit_transform()`
- [ ] Scaling
- [ ] Encoding
- [ ] Pipeline
- [ ] Data leakage
- [ ] Linear vs Logistic Regression
- [ ] Decision Tree vs Random Forest
- [ ] Precision vs Recall
- [ ] Confusion Matrix
- [ ] F1 / ROC-AUC
- [ ] MAE / MSE / RMSE / R²
- [ ] Cross-validation
- [ ] Hyperparameters
- [ ] Overfitting / underfitting
- [ ] Class imbalance
- [ ] K-Means / PCA