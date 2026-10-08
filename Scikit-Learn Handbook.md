# Scikit-Learn Ultimate API Handbook
## Data Cleaning Assignment — Q1–Q24

---

# 0. Core Mental Model

Most scikit-learn objects used in this assignment are **estimators/transformers**.

The fundamental pattern is:

```python
obj = SomeTransformer(...)
obj.fit(X_train)
X_train_new = obj.transform(X_train)
X_test_new = obj.transform(X_test)
````

or, for training data:

```
X_train_new = obj.fit_transform(X_train)
```

## The three operations

### fit(X)

Learn parameters from X.

Examples:

```
imputer.fit(X)
scaler.fit(X)
encoder.fit(X)
```

The object stores what it learned internally.

---

### transform(X)

Apply already-learned parameters to X.

```
X_new = obj.transform(X)
```

It does **not** relearn anything.

---

### fit_transform(X)

Equivalent conceptually to:

```
obj.fit(X)
X_new = obj.transform(X)
```

Mostly used on training data.

---

# 1. THE MOST IMPORTANT RULE

## Fit on training data, transform everything else

Correct:

```
imputer.fit(X_train)

X_train = imputer.transform(X_train)
X_test = imputer.transform(X_test)
```

or:

```
X_train = imputer.fit_transform(X_train)
X_test = imputer.transform(X_test)
```

Wrong:

```
X_train = imputer.fit_transform(X_train)
X_test = imputer.fit_transform(X_test)
```

Why?

Because fitting learns information from the data.

If you fit on test data, information from the test set leaks into your preprocessing.

This is exactly the principle behind Q23's:

> leak-free model-ready feature matrix

---

# 2. sklearn.impute

Main objects used:

```
from sklearn.impute import SimpleImputer
from sklearn.impute import KNNImputer

from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer
```

---

# 3. SimpleImputer

## Purpose

Replace missing values using a simple statistical rule.

```
SimpleImputer(...)
```

---

## Basic usage

```
imp = SimpleImputer(strategy="mean")

X_new = imp.fit_transform(X)
```

---

# 4. SimpleImputer strategies

The important strategies are:

```
strategy="mean"
strategy="median"
strategy="most_frequent"
strategy="constant"
```

### Mean

```
SimpleImputer(strategy="mean")
```

For each column:

```
missing → column mean
```

---

### Median

```
SimpleImputer(strategy="median")
```

```
missing → column median
```

This is particularly useful when the data contains outliers.

Used heavily in your assignment.

---

### Most frequent

```
SimpleImputer(strategy="most_frequent")
```

```
missing → most common value
```

Useful for categorical data.

---

### Constant

```
SimpleImputer(
    strategy="constant",
    fill_value=0
)
```

Every missing value becomes `0`.

For strings:

```
SimpleImputer(
    strategy="constant",
    fill_value="UNKNOWN"
)
```

---

# 5. SimpleImputer.fit()

```
imp.fit(X)
```

Learns the replacement statistic for each column.

For median:

```
imp = SimpleImputer(strategy="median")
imp.fit(X)
```

The learned values are available through:

```
imp.statistics_
```

Example:

```
print(imp.statistics_)
```

Conceptually:

```
column 0 → median
column 1 → median
column 2 → median
...
```

---

# 6. SimpleImputer.transform()

```
X_new = imp.transform(X)
```

Uses the statistics learned during `fit()`.

It does NOT recompute them.

---

# 7. SimpleImputer.fit_transform()

Shortcut:

```
X_new = imp.fit_transform(X)
```

Equivalent conceptually to:

```
imp.fit(X)
X_new = imp.transform(X)
```

---

# 8. SimpleImputer.statistics_

Very important for your assignment.

```
imp.statistics_
```

Returns the learned statistic for each feature.

For:

```
imp = SimpleImputer(strategy="median")
imp.fit(X)
```

then:

```
imp.statistics_
```

contains the training-column medians.

This is used in Q23:

```
medians = np.asarray(
    imp.statistics_,
    dtype=float
)
```

---

# 9. keep_empty_features

An entirely-missing column can be a special case.

```
SimpleImputer(
    strategy="median",
    keep_empty_features=True
)
```

This tells the imputer to preserve completely empty features rather than automatically dropping them in situations where the default behavior would remove them.

Your reference solution explicitly uses this compatibility pattern:

```
try:
    imp = SimpleImputer(
        strategy="median",
        keep_empty_features=True
    )
except TypeError:
    imp = SimpleImputer(
        strategy="median"
    )
```

For your exam, remember the concept:

> `keep_empty_features=True` → preserve all-empty columns.

---

# 10. SimpleImputer — Assignment Usage

## Q8

Mean:

```
imp = SimpleImputer(
    strategy="mean",
    keep_empty_features=True
)

X_new = imp.fit_transform(X)
```

Median:

```
imp = SimpleImputer(
    strategy="median",
    keep_empty_features=True
)

X_new = imp.fit_transform(X)
```

---

## Q20

Used to compare median imputation against other methods.

```
imp = SimpleImputer(strategy="median")
X_imputed = imp.fit_transform(X)
```

---

## Q22

Used before outlier detection:

```
imp = SimpleImputer(strategy="median")
X_imputed = imp.fit_transform(X)
```

Why?

Distance/covariance/outlier algorithms generally need numeric values rather than NaNs.

---

## Q23

Used for leak-free preprocessing:

```
imp = SimpleImputer(strategy="median")

num_train = imp.fit_transform(num_train_raw)
num_test = imp.transform(num_test_raw)
```

This is extremely important.

---

# 11. KNNImputer

Import:

```
from sklearn.impute import KNNImputer
```

Purpose:

> Fill a missing value using information from nearby rows.

---

## Basic usage

```
imp = KNNImputer(n_neighbors=5)

X_new = imp.fit_transform(X)
```

---

# 12. n_neighbors

```
KNNImputer(n_neighbors=k)
```

Controls how many neighboring samples contribute to the imputation.

Example:

```
KNNImputer(n_neighbors=3)
```

uses the 3 nearest suitable neighbors.

---

# 13. KNNImputer API

Same estimator pattern:

```
imp = KNNImputer(n_neighbors=5)

imp.fit(X)

X_new = imp.transform(X)
```

or:

```
X_new = imp.fit_transform(X)
```

---

# 14. KNNImputer intuition

Suppose:

```
row A = [10, 20, NaN]
row B = [11, 21, 30]
row C = [10, 19, 32]
row D = [90, 80, 100]
```

For row A's missing third feature, rows B and C are much closer than D.

So KNNImputer uses nearby rows to estimate the missing value.

The important conceptual difference:

```
SimpleImputer
    ↓
column-level statistic

KNNImputer
    ↓
neighboring rows
```

---

# 15. IterativeImputer

Import:

```
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer
```

Purpose:

> Estimate missing values using relationships between features.

---

## Basic usage

```
imp = IterativeImputer()

X_new = imp.fit_transform(X)
```

---

# 16. IterativeImputer intuition

Suppose:

```
height
weight
age
```

are correlated.

Instead of independently filling each column with a median, IterativeImputer tries to use the other features to predict missing values.

Conceptually:

```
feature A missing
       ↓
use other features
       ↓
estimate A
       ↓
repeat/refine
```

---

# 17. IterativeImputer parameters

Common:

```
IterativeImputer(
    max_iter=10,
    random_state=42
)
```

### max_iter

Controls how many rounds of iterative estimation are performed.

```
max_iter=10
```

---

### random_state

Controls randomness/reproducibility where applicable.

```
random_state=42
```

Same seed → reproducible behavior.

---

# 18. IterativeImputer API

```
imp = IterativeImputer(
    max_iter=10,
    random_state=42
)

X_new = imp.fit_transform(X)
```

---

# 19. Q20 — Three Imputation Methods

Your assignment compares:

```
1. KNN
2. Iterative
3. Median
```

Typical structure:

```
knn = KNNImputer(n_neighbors=k)

iterative = IterativeImputer(
    random_state=0
)

median = SimpleImputer(
    strategy="median"
)
```

Then:

```
X_knn = knn.fit_transform(X)
X_iter = iterative.fit_transform(X)
X_median = median.fit_transform(X)
```

The evaluation is performed only on artificially masked cells.

---

# 20. sklearn.preprocessing

Main objects used in your assignment:

```
from sklearn.preprocessing import (
    MinMaxScaler,
    StandardScaler,
    RobustScaler,
    PowerTransformer,
    OneHotEncoder
)
```

---

# 21. MinMaxScaler

## Purpose

Scale each feature into a specified range.

Default:

```
[0, 1]
```

---

## Basic usage

```
scaler = MinMaxScaler()

X_new = scaler.fit_transform(X)
```

---

# 22. MinMaxScaler formula

For default `[0,1]`:

```
x_scaled = (x - min) / (max - min)
```

So:

```
minimum → 0
maximum → 1
```

---

## Custom range

```
MinMaxScaler(
    feature_range=(-1, 1)
)
```

---

# 23. MinMaxScaler learned attributes

After:

```
scaler.fit(X)
```

you can inspect:

```
scaler.data_min_
scaler.data_max_
scaler.scale_
scaler.min_
```

Most important conceptually:

```
data_min_ → minimum of each feature
data_max_ → maximum of each feature
```

---

# 24. StandardScaler

## Purpose

Standardize features using z-score scaling.

```
from sklearn.preprocessing import StandardScaler
```

---

## Basic usage

```
scaler = StandardScaler()

X_new = scaler.fit_transform(X)
```

---

# 25. StandardScaler formula

For each feature:

```
z = (x - μ) / σ
```

where:

```
μ = mean
σ = standard deviation
```

Therefore approximately:

```
mean → 0
std  → 1
```

---

# 26. StandardScaler learned attributes

After:

```
scaler.fit(X)
```

important attributes include:

```
scaler.mean_
scaler.var_
scaler.scale_
```

In Q23:

```
train_scaler_center = np.asarray(
    scaler.mean_,
    dtype=float
)
```

So:

```
scaler.mean_
    ↓
training feature means
```

---

# 27. VERY IMPORTANT: fit_transform returns transformed data

If:

```
X_scaled = scaler.fit_transform(X)
```

then:

```
X_scaled
```

is the new transformed ndarray.

It does NOT return the scaler itself.

The scaler object contains learned parameters:

```
scaler.mean_
scaler.scale_
...
```

while:

```
X_scaled
```

contains transformed values.

---

# 28. RobustScaler

Import:

```
from sklearn.preprocessing import RobustScaler
```

Purpose:

> Scaling based on median and IQR, making it less sensitive to outliers.

Conceptually:

```
center using median
scale using IQR
```

---

## Basic usage

```
scaler = RobustScaler()

X_new = scaler.fit_transform(X)
```

Useful when extreme values/outliers can distort mean/std-based scaling.

---

# 29. Scaling comparison

```
MinMaxScaler
    min/max
    → fixed range

StandardScaler
    mean/std
    → z-score

RobustScaler
    median/IQR
    → robust to outliers
```

---

# 30. PowerTransformer

Import:

```
from sklearn.preprocessing import PowerTransformer
```

Purpose:

> Transform skewed numerical distributions toward a more Gaussian-like shape.

---

## Basic usage

```
pt = PowerTransformer()

X_new = pt.fit_transform(X)
```

---

# 31. Yeo-Johnson

Your assignment uses:

```
PowerTransformer(
    method="yeo-johnson",
    standardize=False
)
```

Yeo-Johnson can handle zero and negative values.

---

# 32. Box-Cox

Another method:

```
PowerTransformer(
    method="box-cox"
)
```

Important:

> Box-Cox requires strictly positive data.

---

# 33. standardize parameter

```
PowerTransformer(
    method="yeo-johnson",
    standardize=True
)
```

performs the power transformation and standardization.

With:

```
standardize=False
```

the power transformation happens without the final standardization step.

Your assignment/reference specifically uses:

```
standardize=False
```

---

# 34. OneHotEncoder

Import:

```
from sklearn.preprocessing import OneHotEncoder
```

Purpose:

> Convert categorical values into binary indicator columns.

Suppose:

```
city
----
Delhi
Mumbai
Delhi
```

One-hot encoding conceptually creates:

```
city_Delhi    city_Mumbai
     1             0
     0             1
     1             0
```

---

# 35. Basic OneHotEncoder

Modern sklearn:

```
encoder = OneHotEncoder(
    handle_unknown="ignore",
    sparse_output=False
)
```

Then:

```
X_new = encoder.fit_transform(X)
```

---

# 36. sparse_output=False

By default, modern sklearn may produce a sparse matrix.

For this assignment we want a normal dense ndarray:

```
OneHotEncoder(
    sparse_output=False
)
```

Then:

```
type(X_new)
```

is effectively a dense NumPy array.

---

# 37. Older sklearn compatibility

Older versions use:

```
sparse=False
```

instead of:

```
sparse_output=False
```

Hence the reference solution uses:

```
try:
    encoder = OneHotEncoder(
        handle_unknown="ignore",
        sparse_output=False
    )
except TypeError:
    encoder = OneHotEncoder(
        handle_unknown="ignore",
        sparse=False
    )
```

Exam idea:

```
new sklearn → sparse_output=False
old sklearn → sparse=False
```

---

# 38. handle_unknown="ignore"

VERY IMPORTANT for Q23.

Suppose training contains:

```
Delhi
Mumbai
```

but test contains:

```
Delhi
Chennai
```

`Chennai` was never seen during training.

With:

```
handle_unknown="ignore"
```

the encoder doesn't crash.

Instead, the unseen category gets zeros across the learned one-hot columns.

---

# 39. Why handle_unknown matters

Correct:

```
encoder = OneHotEncoder(
    handle_unknown="ignore",
    sparse_output=False
)
```

Then:

```
encoder.fit(train)
train_encoded = encoder.transform(train)
test_encoded = encoder.transform(test)
```

This is another part of making Q23 robust.

---

# 40. OneHotEncoder.fit()

```
encoder.fit(X_train)
```

Learns the categories present in each feature.

You can inspect:

```
encoder.categories_
```

---

# 41. OneHotEncoder.transform()

```
X_new = encoder.transform(X)
```

Uses the categories learned during `fit()`.

---

# 42. OneHotEncoder.fit_transform()

```
X_new = encoder.fit_transform(X_train)
```

Learns categories and transforms training data.

---

# 43. OneHotEncoder.get_feature_names_out()

Very important for Q23.

```
feature_names = encoder.get_feature_names_out(
    categorical_columns
)
```

Example:

```
["city_Delhi", "city_Mumbai", "city_Pune"]
```

These are the names of the generated one-hot features.

---

# 44. OneHotEncoder.categories_

```
encoder.categories_
```

Contains the categories learned for each categorical feature.

Useful for understanding what the encoder learned.

---

# 45. Q23 categorical pipeline

The assignment essentially does:

```
categorical training data
        ↓
fill missing values using TRAINING mode
        ↓
convert to string
        ↓
OneHotEncoder
        ↓
fit on train
        ↓
transform train/test
```

Code:

```
encoder = OneHotEncoder(
    handle_unknown="ignore",
    sparse_output=False
)

cat_train_enc = encoder.fit_transform(cat_train)
cat_test_enc = encoder.transform(cat_test)
```

---

# 46. sklearn.covariance

Relevant object:

```
from sklearn.covariance import MinCovDet
```

---

# 47. MinCovDet

Full name:

> Minimum Covariance Determinant

Purpose:

> Estimate a robust center and covariance matrix while reducing the influence of outliers.

Used in Q11/Q22-style multivariate outlier detection.

---

## Basic usage

```
mcd = MinCovDet(
    random_state=0
)

mcd.fit(X)
```

---

# 48. MinCovDet.mahalanobis()

After fitting:

```
scores = mcd.mahalanobis(X)
```

This gives squared Mahalanobis distances according to the robust covariance estimate.

Conceptually:

```
X
 ↓
MinCovDet
 ↓
robust center + covariance
 ↓
Mahalanobis distance
```

---

# 49. MinCovDet important attributes

After fitting:

```
mcd.location_
mcd.covariance_
mcd.precision_
```

Conceptually:

```
location_
    ↓
robust center

covariance_
    ↓
robust covariance matrix

precision_
    ↓
inverse covariance matrix
```

---

# 50. Why MinCovDet?

Ordinary covariance can itself be distorted by extreme outliers.

MinCovDet attempts to estimate covariance robustly.

Therefore:

```
ordinary covariance
    ← can be influenced by outliers

MinCovDet covariance
    ← designed to be robust
```

---

# 51. sklearn.neighbors

Relevant object:

```
from sklearn.neighbors import LocalOutlierFactor
```

---

# 52. LocalOutlierFactor

LOF = Local Outlier Factor.

Purpose:

> Detect observations that have unusually low local density compared with their neighbors.

This is a **local** outlier method.

---

# 53. Basic LOF usage

```
lof = LocalOutlierFactor(
    n_neighbors=20,
    contamination="auto"
)

labels = lof.fit_predict(X)
```

Output labels are approximately:

```
 1 → inlier
-1 → outlier
```

---

# 54. n_neighbors

```
LocalOutlierFactor(
    n_neighbors=20
)
```

Controls the local neighborhood used for density comparison.

Small:

```
small local neighborhood
```

Large:

```
broader neighborhood
```

---

# 55. contamination

Controls the expected proportion of outliers when determining labels.

Examples:

```
contamination="auto"
```

or:

```
contamination=0.05
```

Meaning approximately 5% contamination.

---

# 56. LOF negative_outlier_factor_

After:

```
lof.fit(X)
```

you can inspect:

```
lof.negative_outlier_factor_
```

Important:

> The attribute is NEGATIVE.

The reference converts it:

```
lof_score = -lof.negative_outlier_factor_
```

So the resulting score is easier to interpret as a positive LOF-like score.

---

# 57. LOF interpretation

Rough intuition:

```
≈ 1
    normal local density

> 1
    increasingly outlier-like
```

The exact interpretation depends on the implementation/context, but the important assignment-level distinction is:

```
negative_outlier_factor_
```

is negative, so the reference negates it when returning a positive score.

---

# 58. LOF fit vs fit_predict

You can do:

```
lof.fit(X)
```

then inspect:

```
lof.negative_outlier_factor_
```

Or:

```
labels = lof.fit_predict(X)
```

to directly get:

```
1  → inlier
-1 → outlier
```

---

# 59. Q22 Outlier Toolbox

Your Q22 combines multiple approaches.

### Univariate robust Z

Conceptually:

```
median
MAD
Modified Z
```

No sklearn object is required for this calculation.

---

### IQR

Also calculated manually.

```
Q1
Q3
IQR = Q3 - Q1
```

Then:

```
lower = Q1 - 1.5 × IQR
upper = Q3 + 1.5 × IQR
```

---

### Multivariate robust distance

```
mcd = MinCovDet(...)
mcd.fit(X)

scores = mcd.mahalanobis(X)
```

---

### Local density

```
lof = LocalOutlierFactor(...)

labels = lof.fit_predict(X)
```

---

# 60. The sklearn preprocessing objects — quick table

|Object|Main idea|
|---|---|
|`SimpleImputer`|Simple missing-value replacement|
|`KNNImputer`|Missing values from nearby rows|
|`IterativeImputer`|Missing values predicted from other features|
|`MinMaxScaler`|Scale using min/max|
|`StandardScaler`|Mean/std z-score|
|`RobustScaler`|Median/IQR scaling|
|`PowerTransformer`|Reduce skew using power transform|
|`OneHotEncoder`|Categorical → binary columns|
|`MinCovDet`|Robust covariance estimation|
|`LocalOutlierFactor`|Local-density outlier detection|

---

# 61. Most Important Constructor Patterns

## SimpleImputer

```
SimpleImputer(
    strategy="median"
)
```

---

## KNNImputer

```
KNNImputer(
    n_neighbors=5
)
```

---

## IterativeImputer

```
IterativeImputer(
    max_iter=10,
    random_state=42
)
```

---

## MinMaxScaler

```
MinMaxScaler()
```

or:

```
MinMaxScaler(
    feature_range=(-1, 1)
)
```

---

## StandardScaler

```
StandardScaler()
```

---

## RobustScaler

```
RobustScaler()
```

---

## PowerTransformer

```
PowerTransformer(
    method="yeo-johnson",
    standardize=False
)
```

---

## OneHotEncoder

```
OneHotEncoder(
    handle_unknown="ignore",
    sparse_output=False
)
```

---

## MinCovDet

```
MinCovDet(
    random_state=0
)
```

---

## LocalOutlierFactor

```
LocalOutlierFactor(
    n_neighbors=20,
    contamination="auto"
)
```

---

# 62. The Universal sklearn Pattern

Memorize this structure rather than individual commands:

```
transformer = SomeTransformer(...)

transformer.fit(X_train)

X_train_new = transformer.transform(X_train)
X_test_new = transformer.transform(X_test)
```

or:

```
X_train_new = transformer.fit_transform(X_train)
X_test_new = transformer.transform(X_test)
```

---

# 63. fit_transform vs transform

This is an EXAM TRAP.

### Training:

```
X_train_new = scaler.fit_transform(X_train)
```

### Test:

```
X_test_new = scaler.transform(X_test)
```

NOT:

```
X_test_new = scaler.fit_transform(X_test)
```

---

# 64. What does fit actually mean?

Different objects learn different things.

### SimpleImputer

```
fit
↓
learn median/mean/etc.
```

### StandardScaler

```
fit
↓
learn mean + std
```

### MinMaxScaler

```
fit
↓
learn min + max
```

### OneHotEncoder

```
fit
↓
learn categories
```

### MinCovDet

```
fit
↓
learn robust center + covariance
```

### LOF

```
fit
↓
learn local-neighborhood structure
```

---

# 65. Q23 — Complete sklearn mental model

Q23 is basically:

```
                 TRAIN
                   │
        ┌──────────┴──────────┐
        │                     │
     numeric              categorical
        │                     │
     median                mode
    imputation            imputation
        │                     │
        ├── row mean          │
        ├── interaction       │
        │                     │
    StandardScaler       OneHotEncoder
        │                     │
        └──────────┬──────────┘
                   │
                 X_train
```

For test:

```
TEST
 │
 ├── use TRAINED imputer
 ├── construct same features
 ├── use TRAINED scaler
 └── use TRAINED encoder
```

Nothing is learned from test.

---

# 66. Q23 exact important APIs

Numeric:

```
SimpleImputer(strategy="median")

imp.fit_transform(train)
imp.transform(test)
```

Scaling:

```
StandardScaler()

scaler.fit_transform(train)
scaler.transform(test)
```

Categorical:

```
OneHotEncoder(
    handle_unknown="ignore",
    sparse_output=False
)

encoder.fit_transform(train)
encoder.transform(test)
```

Feature names:

```
encoder.get_feature_names_out(
    categorical_columns
)
```

Training statistics:

```
imp.statistics_
scaler.mean_
```

---

# 67. sklearn attributes vs methods

Another exam trap.

### Methods

Usually perform an action:

```
fit()
transform()
fit_transform()
fit_predict()
get_feature_names_out()
```

### Learned attributes

Usually end in `_`:

```
statistics_
mean_
var_
scale_
data_min_
data_max_
categories_
negative_outlier_factor_
location_
covariance_
precision_
```

The trailing `_` is a strong clue:

> "This was learned during fitting."

---

# 68. Shape awareness

Most sklearn transformers expect:

```
X.shape = (n_samples, n_features)
```

NOT:

```
(n_features,)
```

For one feature, you generally want:

```
X.reshape(-1, 1)
```

rather than:

```
X.reshape(-1)
```

---

# 69. sklearn vs NumPy

Think:

```
NumPy
    ↓
mathematical operations / arrays

Pandas
    ↓
tables / labels / missing data / grouping

SciPy
    ↓
scientific algorithms and distances

scikit-learn
    ↓
preprocessing / imputation / ML algorithms
```

---

# 70. Q1–Q24 sklearn map

|Question|sklearn APIs|
|---|---|
|Q1|—|
|Q2|—|
|Q3|—|
|Q4|—|
|Q5|—|
|Q6|—|
|Q7|—|
|Q8|`SimpleImputer`|
|Q9|`KNNImputer`|
|Q10|—|
|Q11|`SimpleImputer`, `MinCovDet`, `LocalOutlierFactor`|
|Q12|`SimpleImputer`, `MinMaxScaler`, `StandardScaler`, `RobustScaler`, `PowerTransformer`|
|Q13|—|
|Q14|—|
|Q15|—|
|Q16|—|
|Q17|—|
|Q18|—|
|Q19|—|
|Q20|`SimpleImputer`, `KNNImputer`, `IterativeImputer`|
|Q21|—|
|Q22|`SimpleImputer`, `MinCovDet`, `LocalOutlierFactor`|
|Q23|`SimpleImputer`, `StandardScaler`, `OneHotEncoder`|
|Q24|—|

---

# 71. FINAL EXAM CHEAT SHEET

## Missing values

```
SimpleImputer(strategy="mean")
SimpleImputer(strategy="median")
SimpleImputer(strategy="most_frequent")
SimpleImputer(strategy="constant", fill_value=...)
```

---

## KNN imputation

```
KNNImputer(n_neighbors=k)
```

---

## Iterative imputation

```
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer

IterativeImputer(
    max_iter=10,
    random_state=42
)
```

---

## Scaling

```
MinMaxScaler()
StandardScaler()
RobustScaler()
PowerTransformer(
    method="yeo-johnson",
    standardize=False
)
```

---

## One-hot

```
OneHotEncoder(
    handle_unknown="ignore",
    sparse_output=False
)
```

---

## Robust covariance

```
MinCovDet(random_state=0)
```

---

## LOF

```
LocalOutlierFactor(
    n_neighbors=20,
    contamination="auto"
)
```

---

## Universal pipeline

```
obj = Transformer(...)

X_train_new = obj.fit_transform(X_train)
X_test_new = obj.transform(X_test)
```

---

# 72. THE 10 THINGS TO REMEMBER TOMORROW

1. `fit()` = LEARN parameters.
2. `transform()` = APPLY learned parameters.
3. `fit_transform()` = fit + transform.
4. Never `fit()` on test data.
5. `SimpleImputer(strategy="median")` = median imputation.
6. `KNNImputer(n_neighbors=k)` = neighbor-based imputation.
7. `IterativeImputer` = model relationships between features to estimate missing values.
8. `StandardScaler` = `(x - mean) / std`.
9. `OneHotEncoder(handle_unknown="ignore")` = categorical encoding that won't crash on unseen test categories.
10. sklearn learned attributes usually end in `_`:
    
    ```
    statistics_
    mean_
    scale_
    categories_
    ```
    

```

That completes the **four major library handbooks**: NumPy → Pandas → SciPy → scikit-learn.

Next is the small-stuff combined handbook: **RapidFuzz + `re` + `unicodedata` + Python built-ins/core patterns**.
```