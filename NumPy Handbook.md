# NumPy — Ultimate Exam API Handbook

> Purpose: Q1–Q24 Data Cleaning / Pandas / ML exam
>
> Philosophy:
> - Know what operation you need.
> - Know the NumPy API that performs it.
> - Know the shape/dtype behavior.
> - Use Jupyter autocomplete/documentation for exact signatures when needed.

---

# 1. What is NumPy?

NumPy = Numerical Python.

The central object is the:

    np.ndarray

It is a multidimensional, homogeneous array.

Example:

    x = np.array([1, 2, 3])

    X = np.array([
        [1, 2],
        [3, 4],
        [5, 6]
    ])

Think:

    Python list
        ↓
    np.ndarray
        ↓
    vectorized numerical operations
        ↓
    fast numerical computation

NumPy is heavily used underneath Pandas and scikit-learn.

---

# 2. Import

    import numpy as np

Almost all code uses the conventional alias:

    np

---

# 3. ndarray — The Core Concept

An ndarray can have any number of dimensions.

## 1D

    x = np.array([10, 20, 30])

Shape:

    (3,)

Think:

    [10, 20, 30]

---

## 2D

    X = np.array([
        [1, 2],
        [3, 4],
        [5, 6]
    ])

Shape:

    (3, 2)

Meaning:

    3 rows
    2 columns

---

## 3D+

Possible, but not important for this assignment.

---

# 4. Shape

    X.shape

Example:

    X.shape
    # (3, 2)

Very important.

If:

    X.shape == (n, p)

then:

    n = number of rows
    p = number of columns

---

# 5. Number of Dimensions

    X.ndim

Example:

    X = np.array([1, 2, 3])
    X.ndim
    # 1

    X = np.array([[1, 2], [3, 4]])
    X.ndim
    # 2

---

# 6. Number of Elements

    X.size

Example:

    X = np.array([[1, 2], [3, 4]])
    X.size
    # 4

---

# 7. dtype

    X.dtype

Example:

    X = np.array([1, 2, 3])
    X.dtype

Can be:

    int64
    float64
    bool
    object
    etc.

---

# 8. np.array()

Create an ndarray.

    np.array([1, 2, 3])

2D:

    np.array([
        [1, 2],
        [3, 4]
    ])

Specify dtype:

    np.array([1, 2, 3], dtype=float)

Useful when you need to explicitly create an array.

---

# 9. np.asarray()

Convert something into an ndarray.

    np.asarray(x)

With dtype:

    np.asarray(x, dtype=float)

Very common pattern:

    X = np.asarray(X, dtype=float)

Meaning:

    "Make sure X is a NumPy array of floats."

Difference from np.array is usually not important for the exam.

---

# 10. np.arange()

Create regularly spaced integer/numeric values.

    np.arange(5)

Result:

    [0, 1, 2, 3, 4]

    np.arange(2, 7)

Result:

    [2, 3, 4, 5, 6]

Used in DSU:

    np.arange(n)

creates:

    [0, 1, 2, ..., n-1]

---

# 11. np.empty()

Create an array without initializing its values.

    np.empty((0, 2), dtype=int)

This was useful for returning an empty pair matrix.

Shape:

    (0, 2)

Important:

np.empty() does NOT mean "fill with zeros".

It allocates the array but its contents are unspecified.

---

# 12. np.zeros()

Create an array filled with zero.

    np.zeros(5)

    np.zeros((3, 4))

Specify dtype:

    np.zeros(5, dtype=bool)

Example:

    np.zeros(len(tx), dtype=bool)

creates a Boolean flag array initialized to False.

---

# 13. np.ones()

Create an array filled with 1.

    np.ones(5)

    np.ones((3, 4))

Not heavily used in the assignment, but useful to recognize.

---

# 14. np.nan

Special floating-point value representing missing/not-a-number data.

    np.nan

Example:

    x = np.array([1, 2, np.nan, 4])

Important:

    np.nan != np.nan

So DO NOT use:

    x == np.nan

Use:

    np.isnan(x)

or Pandas:

    pd.isna(x)

---

# 15. NaN-aware Functions

Many NumPy statistical functions have two versions:

Normal:

    np.mean()
    np.median()
    np.std()
    np.percentile()

NaN-aware:

    np.nanmean()
    np.nanmedian()
    np.nanstd()
    np.nanpercentile()

The `nan` version ignores NaN values.

---

# 16. np.isnan()

Element-wise test for NaN.

    np.isnan(x)

Example:

    x = np.array([1, np.nan, 3])

    np.isnan(x)

Result:

    [False, True, False]

---

# 17. np.isfinite()

Check whether values are finite.

    np.isfinite(x)

Returns False for:

    np.nan
    np.inf
    -np.inf

Example:

    observed = np.isfinite(x)

Useful when calculating statistics only for valid numerical values.

---

# 18. np.isinf()

Checks infinity.

    np.isinf(x)

Not central to the assignment, but useful to recognize as part of NumPy's numerical validity checks.

---

# 19. Boolean Masks

One of the MOST IMPORTANT NumPy concepts.

Suppose:

    x = np.array([10, 20, 30, 40])

Create a mask:

    mask = x > 20

Result:

    [False, False, True, True]

Then:

    x[mask]

gives:

    [30, 40]

Mental model:

    condition
       ↓
    Boolean array
       ↓
    select elements

---

# 20. Boolean Indexing

Example:

    x[x > 20]

Selects all values satisfying the condition.

Multiple conditions:

    (x > 10) & (x < 40)

Important:

Use:

    &
    |
    ~

NOT:

    and
    or
    not

For NumPy arrays.

---

# 21. Boolean NOT — ~

If:

    mask = np.array([True, False, True])

then:

    ~mask

gives:

    [False, True, False]

This appeared conceptually in the missingness tasks.

---

# 22. np.all()

Check whether all values satisfy a condition.

    np.all(x == 0)

Example:

    x = np.array([0, 0, 0])

    np.all(x == 0)
    # True

Used for checking whether a binary vector is entirely zero.

---

# 23. np.any()

Check whether at least one value is True.

    np.any(mask)

Example:

    mask = np.array([False, False, True])

    np.any(mask)
    # True

Very useful for:

    if mask.any():

and:

    array.any(axis=...)

---

# 24. Array Methods vs NumPy Functions

Many operations exist in both styles.

Example:

    np.mean(x)

and:

    x.mean()

Similarly:

    np.any(x)

    x.any()

    np.sum(x)

    x.sum()

Know both styles.

---

# 25. np.sum()

Sum values.

    np.sum(x)

Example:

    np.sum([1, 2, 3])
    # 6

For Boolean arrays:

    np.sum(mask)

counts True values because:

    True  == 1
    False == 0

Very useful.

---

# 26. axis

One of the MOST IMPORTANT NumPy concepts.

For:

    X.shape == (rows, columns)

## axis=0

Operate down rows.

Result is one value per column.

Example:

    X.mean(axis=0)

means:

    mean of each column

---

## axis=1

Operate across columns.

Result is one value per row.

Example:

    X.mean(axis=1)

means:

    mean of each row

Mental picture:

    X:

        c1  c2  c3
    r1   1   2   3
    r2   4   5   6

    axis=0
        ↓   ↓   ↓
        2.5 3.5 4.5

    axis=1
        →   2
        →   5

REMEMBER:

    axis=0 → column-wise result
    axis=1 → row-wise result

---

# 27. keepdims=True

Normally:

    X.mean(axis=1)

for a matrix of shape:

    (n, p)

returns:

    (n,)

But:

    X.mean(axis=1, keepdims=True)

returns:

    (n, 1)

This is extremely useful when you want to concatenate the result back into a matrix.

Example:

    row_mean = np.mean(X, axis=1, keepdims=True)

Then:

    np.hstack([X, row_mean])

works naturally.

---

# 28. np.mean()

Arithmetic mean.

    np.mean(x)

Column-wise:

    np.mean(X, axis=0)

Row-wise:

    np.mean(X, axis=1)

NaN-aware version:

    np.nanmean(x)

---

# 29. np.median()

Median.

    np.median(x)

Column-wise:

    np.median(X, axis=0)

NaN-aware:

    np.nanmedian(x)

---

# 30. np.nanmean()

Mean ignoring NaNs.

    np.nanmean(x)

Important for raw data containing missing values.

---

# 31. np.nanmedian()

Median ignoring NaNs.

    np.nanmedian(x)

Used heavily in robust statistics.

Example:

    median = np.nanmedian(x)

---

# 32. np.std()

Standard deviation.

    np.std(x)

With axis:

    np.std(X, axis=0)

---

# 33. np.nanstd()

Standard deviation ignoring NaNs.

    np.nanstd(x)

Used for:

- detecting constant columns
- robust preprocessing
- outlier preparation

Example:

    keep = np.nanstd(Xi, axis=0) > 0

Meaning:

    keep columns whose standard deviation is non-zero.

---

# 34. np.abs()

Element-wise absolute value.

    np.abs(x)

Example:

    x = np.array([-3, 2, -5])

    np.abs(x)

gives:

    [3, 2, 5]

Very important for:

- absolute deviation
- modified Z-score
- error calculations

---

# 35. np.sqrt()

Element-wise square root.

    np.sqrt(x)

Used for:

- RMSE
- Mahalanobis distance

RMSE:

    np.sqrt(np.mean((predicted - actual) ** 2))

---

# 36. Exponentiation

Use:

    x ** 2

for squaring.

Example:

    squared_error = (predicted - actual) ** 2

---

# 37. np.maximum()

Element-wise maximum.

    np.maximum(a, b)

Example:

    np.maximum(x, 0)

forces values below zero to zero.

Used in the Mahalanobis calculation to protect against tiny negative numerical errors before sqrt:

    np.sqrt(np.maximum(distance_squared, 0.0))

---

# 38. np.quantile()

Compute quantiles.

    np.quantile(x, 0.25)

25th percentile:

    Q1 = np.quantile(x, 0.25)

75th percentile:

    Q3 = np.quantile(x, 0.75)

Column-wise:

    np.quantile(X, 0.25, axis=0)

---

# 39. np.nanquantile()

Same idea as np.quantile(), but ignores NaNs.

    np.nanquantile(x, 0.25)

Example:

    Q1 = np.nanquantile(x, 0.25)
    Q3 = np.nanquantile(x, 0.75)

---

# 40. np.percentile()

Compute percentile.

    np.percentile(x, 25)

Equivalent conceptually to:

    np.quantile(x, 0.25)

Important distinction:

    percentile → 0 to 100

    quantile → 0 to 1

---

# 41. np.nanpercentile()

NaN-aware percentile.

    np.nanpercentile(x, 25)

Used in the univariate outlier helper.

Example:

    Q1 = np.nanpercentile(x, 25)
    Q3 = np.nanpercentile(x, 75)

---

# 42. IQR using NumPy

Definition:

    IQR = Q3 - Q1

Typical:

    Q1 = np.nanpercentile(x, 25)
    Q3 = np.nanpercentile(x, 75)

    IQR = Q3 - Q1

Fences:

    lower = Q1 - 1.5 * IQR
    upper = Q3 + 1.5 * IQR

Flag:

    x < lower
    |
    x > upper

---

# 43. np.unique()

Return unique values.

    np.unique(x)

Example:

    np.unique([1, 1, 2, 3, 3])

gives:

    [1, 2, 3]

Used when combining/cleaning index sets.

---

# 44. np.sort()

Sort an array.

    np.sort(x)

Example:

    np.sort([3, 1, 2])

gives:

    [1, 2, 3]

---

# 45. Python sorted() vs np.sort()

Python:

    sorted(x)

NumPy:

    np.sort(x)

Important distinction:

    sorted(...)
        → Python sorting
        → returns a new object

    np.sort(...)
        → NumPy sorting
        → returns sorted ndarray

Both may appear in code.

---

# 46. np.concatenate()

Join arrays along an existing axis.

    np.concatenate([a, b])

Example:

    a = np.array([1, 2])
    b = np.array([3, 4])

    np.concatenate([a, b])

gives:

    [1, 2, 3, 4]

Used when combining index arrays.

---

# 47. np.hstack()

Horizontal stacking.

    np.hstack([A, B])

If:

    A.shape == (n, p)
    B.shape == (n, 1)

then:

    np.hstack([A, B]).shape
    == (n, p+1)

Very important in Q23.

Example:

    num_train_aug = np.hstack([
        num_train,
        train_row_mean,
        train_interaction
    ])

---

# 48. np.vstack()

Vertical stacking.

    np.vstack([A, B])

If both have same number of columns:

    A.shape == (n, p)
    B.shape == (m, p)

then:

    np.vstack([A, B]).shape
    == (n+m, p)

Not central to the assignment, but know the distinction:

    hstack → add columns
    vstack → add rows

---

# 49. np.empty() + Shape

Important exam pattern:

    np.empty((0, 2), dtype=int)

means:

    zero rows
    two columns
    integer dtype

This is how we return an empty list of `(i, j)` pairs while preserving the required shape.

---

# 50. np.arange() in DSU

In the DSU:

    self.parent = np.arange(n)

For:

    n = 5

we get:

    [0, 1, 2, 3, 4]

This initializes every element as its own parent.

---

# 51. np.logical_or()

Element-wise OR.

    np.logical_or(a, b)

Equivalent conceptually to:

    a | b

for Boolean arrays.

Example:

    a = [True, False, True]
    b = [False, False, True]

    np.logical_or(a, b)

gives:

    [True, False, True]

Used in Q24 to count rows flagged by at least one outlier method.

---

# 52. np.atleast_2d()

Ensure an array has at least 2 dimensions.

    np.atleast_2d(x)

Examples:

    np.atleast_2d([1, 2, 3])

becomes shape:

    (1, 3)

Useful when a covariance matrix might otherwise become 0D/1D in edge cases.

---

# 53. np.cov()

Compute covariance matrix.

    np.cov(X, rowvar=False)

Important parameter:

    rowvar=False

means:

    rows = observations
    columns = variables/features

For:

    X.shape == (n_samples, n_features)

use:

    np.cov(X, rowvar=False)

to obtain feature covariance.

---

# 54. np.linalg.pinv()

Moore-Penrose pseudoinverse.

    np.linalg.pinv(A)

Used as a numerically safer fallback than:

    np.linalg.inv(A)

when the covariance matrix may be singular.

Mental distinction:

    inv()
        ordinary inverse

    pinv()
        pseudoinverse
        works with singular / rank-deficient matrices

---

# 55. np.linalg.inv()

Matrix inverse.

    np.linalg.inv(A)

Appeared in the basic Mahalanobis implementation.

If:

    covariance = Σ

then:

    inverse = Σ⁻¹

Important:

`np.linalg.inv()` can fail if the matrix is singular.

For robust/fallback code:

    np.linalg.pinv()

is safer.

---

# 56. np.einsum()

Einstein summation.

    np.einsum(...)

This is an advanced NumPy operation, but it appeared in the Mahalanobis fallback.

Example:

    np.einsum(
        "ij,jk,ik->i",
        difference,
        inverse,
        difference
    )

Conceptually computes, for every row:

    dᵢᵀ Σ⁻¹ dᵢ

where:

    dᵢ = xᵢ - μ

You DO NOT need to memorize the `"ij,jk,ik->i"` string.

Know:

    np.einsum()
        → generalized tensor/matrix multiplication
        → compact way to express indexed sums

For tomorrow, Jupyter documentation is enough for exact syntax.

---

# 57. np.number

NumPy's numeric type hierarchy.

Used with Pandas:

    df.select_dtypes(include=np.number)

Meaning:

    select columns whose dtype is numeric.

---

# 58. dtype=int

Explicit integer dtype.

    np.array([1, 2, 3], dtype=int)

Also:

    np.empty((0, 2), dtype=int)

---

# 59. dtype=float

Explicit floating-point dtype.

    np.asarray(X, dtype=float)

Very common when doing numerical ML/statistics.

Why?

Because:

    np.nan

requires floating-point representation in normal NumPy arrays.

---

# 60. dtype=bool

Explicit Boolean dtype.

    np.zeros(n, dtype=bool)

Useful for flag arrays:

    False → not flagged
    True  → flagged

---

# 61. Type Conversion

Common:

    np.asarray(x, dtype=float)

    np.asarray(x, dtype=bool)

    np.asarray(x, dtype=int)

Useful when an API expects a particular type.

---

# 62. 2D Indexing

For:

    X.shape == (rows, columns)

general syntax:

    X[rows, columns]

---

## All rows, column 0

    X[:, 0]

Meaning:

    every row
    column 0

Result shape:

    (n,)

---

## All rows, column 0 as a 2D column

    X[:, [0]]

Result shape:

    (n, 1)

THIS DISTINCTION IS IMPORTANT.

Compare:

    X[:, 0]
        → 1D

    X[:, [0]]
        → 2D column

---

# 63. All rows, multiple columns

    X[:, [0, 2]]

Select columns 0 and 2.

Shape:

    (n, 2)

---

# 64. Column range

    X[:, 1:3]

means:

    all rows
    columns 1 and 2

because Python slicing excludes the endpoint.

---

# 65. First row

    X[0, :]

or simply:

    X[0]

Both select the first row.

Usually:

    X[0]

returns a 1D array.

---

# 66. Last row

    X[-1]

---

# 67. Boolean 2D Masking

Suppose:

    X.shape == mask.shape

and:

    mask[i, j] == True

means:

    cell (i, j) is selected.

Then:

    X[mask]

returns all selected elements as a 1D array.

This is CRITICAL for Q20.

Example:

    X[mask]

selects the original values at masked positions.

---

# 68. Assignment Using a Mask

One of the most useful NumPy patterns:

    Xmasked[mask] = np.nan

Meaning:

    wherever mask == True,
    replace that element with NaN.

This is the core operation in Q20.

---

# 69. Masked RMSE Pattern

Given:

    X       = original complete data
    Xi      = imputed data
    mask    = cells artificially hidden

correct evaluation:

    Xi[mask] - X[mask]

then:

    np.mean((Xi[mask] - X[mask]) ** 2)

then:

    np.sqrt(...)

Full:

    rmse = np.sqrt(
        np.mean(
            (Xi[mask] - X[mask]) ** 2
        )
    )

IMPORTANT:

Do NOT compare all cells.

Compare only:

    mask == True

---

# 70. Column-wise Masking

For a matrix:

    mask[:, j]

means:

    all rows
    column j

So:

    m = mask[:, j]

is the missing/evaluation mask for column j.

Then:

    Xi[m, j]

selects the imputed values only at masked cells in column j.

---

# 71. Fancy Indexing

Examples:

    X[:, [0, 1]]

    X[[0, 3, 5], :]

    X[mask]

Fancy indexing means using arrays/lists/masks to select specific elements.

---

# 72. Broadcasting

NumPy can perform operations between arrays of compatible shapes.

Example:

    X.shape = (n, p)

and:

    mean.shape = (p,)

Then:

    X - mean

subtracts the corresponding feature mean from every row.

This is broadcasting.

---

## Broadcasting example from outliers

Suppose:

    Xi.shape = (n, p)

and:

    median.shape = (p,)

Then:

    Xi - median

subtracts each column's median from that column.

---

# 73. Broadcasting + keepdims

Suppose:

    X.shape = (n, p)

Then:

    row_mean = X.mean(axis=1, keepdims=True)

has shape:

    (n, 1)

Now:

    X - row_mean

works naturally.

Without keepdims:

    X.mean(axis=1)

has shape:

    (n,)

which may behave differently under broadcasting.

---

# 74. Element-wise Operations

NumPy operators usually operate element by element.

    X + Y
    X - Y
    X * Y
    X / Y
    X ** 2

IMPORTANT:

    X * Y

is element-wise multiplication.

It is NOT matrix multiplication.

---

# 75. Matrix Multiplication

For matrix multiplication:

    A @ B

Example:

    A @ B

This distinction matters:

    A * B
        → element-wise

    A @ B
        → matrix multiplication

---

# 76. Comparisons are Element-wise

    X > 10
    X < 5
    X == 0
    X != 0

produce Boolean arrays.

Example:

    X > 10

returns a Boolean matrix with the same shape as X.

---

# 77. Combining Conditions

Use:

    &
    |
    ~

Example:

    (X > lower) | (X < upper)

More realistically:

    (X < lower) | (X > upper)

Do NOT write:

    X < lower or X > upper

because NumPy arrays contain multiple Boolean values.

---

# 78. Modified Z-score

Formula:

    modified_z =
        0.6745 * (x - median) / MAD

NumPy implementation:

    median = np.nanmedian(x)

    MAD = np.nanmedian(
        np.abs(x - median)
    )

    modified_z = (
        0.6745
        * (x - median)
        / MAD
    )

Flag:

    np.abs(modified_z) > 3.5

---

# 79. MAD

Median Absolute Deviation:

    MAD = median(|x - median(x)|)

NumPy:

    median = np.nanmedian(x)

    MAD = np.nanmedian(
        np.abs(x - median)
    )

---

# 80. IQR

    Q1 = np.nanpercentile(x, 25)
    Q3 = np.nanpercentile(x, 75)

    IQR = Q3 - Q1

    lower = Q1 - 1.5 * IQR
    upper = Q3 + 1.5 * IQR

Flag:

    (x < lower) | (x > upper)

---

# 81. RMSE

Root Mean Squared Error:

    RMSE = sqrt(mean((prediction - truth)^2))

NumPy:

    np.sqrt(
        np.mean(
            (prediction - truth) ** 2
        )
    )

---

# 82. Mahalanobis Distance

Mathematical definition:

    d(x) =
        sqrt(
            (x - μ)^T
            Σ⁻¹
            (x - μ)
        )

NumPy building blocks:

    mean = np.mean(X, axis=0)

    covariance = np.cov(
        X,
        rowvar=False
    )

    inverse = np.linalg.inv(covariance)

or robust fallback:

    inverse = np.linalg.pinv(covariance)

Then the quadratic form can be expressed using:

    np.einsum()

---

# 83. Counting Rows / Elements

Python:

    len(X)

NumPy:

    X.shape[0]

Both represent the number of rows for a 2D array.

For number of columns:

    X.shape[1]

Total number of elements:

    X.size

---

# 84. np.array vs list

Python list:

    x = [1, 2, 3]

NumPy array:

    x = np.array([1, 2, 3])

NumPy gives:

    vectorized arithmetic
    multidimensional indexing
    broadcasting
    numerical functions
    efficient numerical storage

Example:

Python lists:

    [1, 2, 3] * 2

gives:

    [1, 2, 3, 1, 2, 3]

NumPy:

    np.array([1, 2, 3]) * 2

gives:

    [2, 4, 6]

---

# 85. Vectorization

Instead of:

    result = []

    for x in X:
        result.append(x * 2)

NumPy allows:

    result = X * 2

This is vectorization.

Many operations in the assignment rely on this.

---

# 86. NumPy vs Pandas

Use NumPy when you are primarily working with:

    arrays
    numerical computation
    masks
    matrix operations
    shapes
    statistics

Use Pandas when you are primarily working with:

    DataFrames
    Series
    named columns
    tabular data
    missing values in tables
    groupby
    sorting/filtering

Typical pipeline:

    Pandas DataFrame
          ↓
    .to_numpy()
          ↓
    NumPy ndarray
          ↓
    numerical computation / sklearn

---

# 87. DataFrame → NumPy

Common:

    df.to_numpy()

With explicit type:

    df.to_numpy(float)

or:

    df.to_numpy(dtype=float)

Another common pattern:

    df.apply(
        pd.to_numeric,
        errors="coerce"
    ).to_numpy(float)

Meaning:

    DataFrame
      ↓
    force numeric
      ↓
    invalid → NaN
      ↓
    NumPy float array

---

# 88. NumPy → Pandas

Example:

    pd.DataFrame(X)

This is useful when numerical computation produces an ndarray but the required output is a DataFrame.

---

# 89. Common NumPy Patterns From the Assignment

## Pattern 1 — Numerical conversion

    X = np.asarray(X, dtype=float)

---

## Pattern 2 — Empty pair matrix

    np.empty((0, 2), dtype=int)

---

## Pattern 3 — DSU initialization

    np.arange(n)

---

## Pattern 4 — Boolean flags

    np.zeros(n, dtype=bool)

---

## Pattern 5 — Missing values

    np.nan

---

## Pattern 6 — Ignore NaNs

    np.nanmean(x)
    np.nanmedian(x)
    np.nanstd(x)
    np.nanpercentile(x, 25)
    np.nanquantile(x, 0.25)

---

## Pattern 7 — Boolean masking

    x[mask]

---

## Pattern 8 — Mask assignment

    X[mask] = np.nan

---

## Pattern 9 — Column-wise statistic

    np.mean(X, axis=0)

---

## Pattern 10 — Row-wise statistic

    np.mean(X, axis=1)

---

## Pattern 11 — Keep 2D shape

    np.mean(X, axis=1, keepdims=True)

---

## Pattern 12 — Add columns

    np.hstack([X, new_column])

---

## Pattern 13 — Combine arrays

    np.concatenate([...])

---

## Pattern 14 — OR two Boolean arrays

    np.logical_or(a, b)

or:

    a | b

---

## Pattern 15 — Check whether anything is True

    mask.any()

---

## Pattern 16 — Check whether everything is True

    mask.all()

---

## Pattern 17 — Count True values

    mask.sum()

---

## Pattern 18 — RMSE

    np.sqrt(
        np.mean(
            (pred - actual) ** 2
        )
    )

---

## Pattern 19 — Modified Z

    median = np.nanmedian(x)

    MAD = np.nanmedian(
        np.abs(x - median)
    )

    modified_z = (
        0.6745 * (x - median) / MAD
    )

---

## Pattern 20 — IQR

    Q1 = np.nanpercentile(x, 25)
    Q3 = np.nanpercentile(x, 75)

    IQR = Q3 - Q1

---

# 90. NumPy API Quick Reference

## Array creation

    np.array()
    np.asarray()
    np.arange()
    np.empty()
    np.zeros()
    np.ones()

## Constants

    np.nan

## Inspection / structure

    x.shape
    x.ndim
    x.size
    x.dtype

## Missing / validity

    np.isnan()
    np.isfinite()
    np.isinf()

## Logical

    np.all()
    np.any()
    np.logical_or()

## Statistics

    np.mean()
    np.median()
    np.std()

    np.nanmean()
    np.nanmedian()
    np.nanstd()

## Absolute / arithmetic

    np.abs()
    np.sqrt()
    np.maximum()

    +
    -
    *
    /
    **
    @

## Quantiles

    np.quantile()
    np.nanquantile()

    np.percentile()
    np.nanpercentile()

## Array manipulation

    np.concatenate()
    np.hstack()
    np.vstack()
    np.unique()
    np.sort()
    np.atleast_2d()

## Linear algebra

    np.linalg.inv()
    np.linalg.pinv()
    np.cov()
    np.einsum()

## Types

    np.ndarray
    np.number

## Indexing

    X[i]
    X[i, j]
    X[:, j]
    X[:, [j]]
    X[:, a:b]
    X[mask]
    X[mask, j]

## Important parameters

    axis=
    dtype=
    keepdims=
    rowvar=

---

# 91. EXAM RECALL — MUST KNOW

If you remember nothing else, remember these:

    np.array()
    np.asarray()

    np.nan

    np.isnan()
    np.isfinite()

    np.mean()
    np.median()
    np.std()

    np.nanmean()
    np.nanmedian()
    np.nanstd()

    np.percentile()
    np.nanpercentile()

    np.quantile()
    np.nanquantile()

    np.abs()
    np.sqrt()
    np.maximum()

    np.zeros()
    np.empty()
    np.arange()

    np.concatenate()
    np.hstack()
    np.unique()
    np.sort()

    np.any()
    np.all()
    np.logical_or()

    np.cov()
    np.linalg.inv()
    np.linalg.pinv()
    np.einsum()

    X.shape
    X.ndim
    X.size
    X.dtype

    X[:, j]
    X[:, [j]]
    X[mask]
    X[mask, j]

    axis=0
    axis=1
    keepdims=True

    dtype=float
    dtype=int
    dtype=bool

---

# 92. EXAM MENTAL MODEL

When you see a NumPy problem, ask:

1. What is my array's shape?

2. Do I want a row-wise or column-wise operation?

    axis=1 → row-wise
    axis=0 → column-wise

3. Do I have NaNs?

    yes → consider np.nan...

4. Do I need to select specific elements?

    boolean mask / indexing

5. Am I adding rows or columns?

    rows    → vstack
    columns → hstack

6. Do I need a 1D or 2D column?

    X[:, j]   → (n,)
    X[:, [j]] → (n,1)

7. Am I doing element-wise or matrix operations?

    \* → element-wise
    @ → matrix multiplication

8. Am I constructing a Boolean flag?

    dtype=bool

9. Do I need to preserve shape?

    keepdims=True

10. Do I need numerical stability?

    pinv()
    maximum(..., 0)
    nan-aware functions

---

# 93. THE 10 NUMPY THINGS MOST LIKELY TO SAVE YOU TOMORROW

1. `np.asarray(X, dtype=float)`

2. `np.nanmedian(x)`

3. `np.nanpercentile(x, 25)`

4. `np.abs(x)`

5. `np.sqrt(...)`

6. `X[:, j]` vs `X[:, [j]]`

7. `axis=0` vs `axis=1`

8. `X[mask]`

9. `X[mask] = np.nan`

10. `np.hstack([...])`

If these are automatic, a huge portion of the numerical work in the assignment becomes straightforward.