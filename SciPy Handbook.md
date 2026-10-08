# SciPy — Ultimate Exam API Handbook

> Purpose: Q1–Q24 Data Cleaning / Pandas / ML exam
>
> Actual role in this assignment:
> - Numerical distances
> - Binary-vector distances/similarities
> - Mahalanobis distance
>
> Main module used:
>
>     scipy.spatial
>
>     scipy.spatial.distance

---

# 1. What is SciPy?

SciPy = Scientific Python.

It provides higher-level scientific/numerical algorithms built on top of NumPy.

Mental model:

    NumPy
      ↓
    arrays + basic numerical operations

    SciPy
      ↓
    scientific algorithms / distances / optimization / statistics / etc.

For this assignment, SciPy is mainly used for:

    distance calculations


# 2. Import

The main import used:

    from scipy.spatial import distance

Then:

    distance.euclidean(...)
    distance.cityblock(...)
    distance.jaccard(...)
    distance.cosine(...)


# 3. Direct Mahalanobis Import

The reference also uses:

    from scipy.spatial.distance import mahalanobis

Then:

    mahalanobis(x, mean, inverse_covariance)


# 4. scipy.spatial.distance

This module contains many distance metrics.

The ones relevant to the assignment are:

    euclidean
    cityblock
    jaccard
    cosine
    mahalanobis


# 5. Euclidean Distance

API:

    distance.euclidean(x, y)

Mathematical definition:

    d(x,y)
      = sqrt(
          Σ (xᵢ - yᵢ)²
        )

Example:

    x = np.array([1, 2, 3])
    y = np.array([4, 6, 3])

    distance.euclidean(x, y)

Conceptually:

    sqrt(
        (1-4)² +
        (2-6)² +
        (3-3)²
    )

    = 5


# 6. When to Think "Euclidean"

Question wording:

    Euclidean distance
    straight-line distance
    L2 distance

Think:

    distance.euclidean(x, y)


# 7. Cityblock Distance

API:

    distance.cityblock(x, y)

Also known as:

    Manhattan distance
    L1 distance

Formula:

    Σ |xᵢ - yᵢ|

Example:

    x = [1, 2, 3]
    y = [4, 6, 3]

    cityblock
      =
    |1-4| + |2-6| + |3-3|

      = 3 + 4 + 0

      = 7


# 8. When to Think "Cityblock"

Question wording:

    Manhattan distance
    Cityblock distance
    L1 distance

Think:

    distance.cityblock(x, y)


# 9. Euclidean vs Cityblock

    Euclidean
        → sqrt(sum of squared differences)

    Cityblock
        → sum of absolute differences

Mental shortcut:

    L2 → Euclidean
    L1 → Manhattan / Cityblock


# 10. Jaccard Distance

API:

    distance.jaccard(x, y)

This returns:

    Jaccard DISTANCE

not similarity.

For binary vectors:

    Jaccard similarity
        = intersection / union

Jaccard distance:

    1 - Jaccard similarity


# 11. Converting Jaccard Distance to Similarity

If SciPy gives:

    d = distance.jaccard(x, y)

then:

    similarity = 1 - d


# 12. Example

Suppose:

    x = [1, 1, 0, 0]
    y = [1, 0, 1, 0]

Intersection:

    1

Union:

    3

Similarity:

    1/3

Distance:

    1 - 1/3
    = 2/3

Therefore:

    distance.jaccard(x, y)
        → 2/3

and:

    1 - distance.jaccard(x, y)
        → 1/3


# 13. Cosine Distance

API:

    distance.cosine(x, y)

SciPy returns:

    cosine DISTANCE

not cosine similarity.

Cosine similarity:

    x · y
    -------
    ||x|| ||y||

Cosine distance:

    1 - cosine_similarity


# 14. Converting Cosine Distance to Similarity

    similarity = 1 - distance.cosine(x, y)


# 15. Important Cosine Edge Case

If a vector is all zeros:

    [0, 0, 0, ...]

its norm is zero.

Therefore ordinary cosine similarity is undefined mathematically.

The assignment explicitly handles zero-vector cases before calling SciPy.

Pattern:

    x_zero = np.all(x == 0)
    y_zero = np.all(y == 0)

    if x_zero and y_zero:
        return 1.0, 1.0

    if x_zero or y_zero:
        return 0.0, 0.0


# 16. Binary Similarity Pattern

For binary vectors:

    Jaccard_similarity =
        1 - distance.jaccard(x, y)

    Cosine_similarity =
        1 - distance.cosine(x, y)


# 17. The Critical SciPy Trap

The assignment explicitly expects:

    SIMILARITY

while SciPy's functions return:

    DISTANCE

Therefore:

    SciPy distance
          ↓
        1 - d
          ↓
    similarity


# 18. Mahalanobis Distance

API:

    mahalanobis(x, mean, inverse_covariance)

Import:

    from scipy.spatial.distance import mahalanobis


# 19. Mahalanobis Formula

For point x:

    d(x)
      =
    sqrt(
        (x - μ)ᵀ
        Σ⁻¹
        (x - μ)
    )

where:

    μ
        → mean vector

    Σ
        → covariance matrix

    Σ⁻¹
        → inverse covariance matrix


# 20. Why Mahalanobis?

Euclidean distance treats dimensions independently and equally.

Mahalanobis accounts for:

    scale
    variance
    covariance
    correlation between features


# 21. Preparing Mahalanobis Inputs

Typical NumPy/Pandas workflow:

    X_complete = ...

    mean = np.mean(
        X_complete,
        axis=0
    )

    cov = np.cov(
        X_complete,
        rowvar=False
    )

    inv_cov = np.linalg.inv(cov)


# 22. Computing One Mahalanobis Distance

    d = mahalanobis(
        x,
        mean,
        inv_cov
    )


# 23. Computing Distances for Every Row

Typical:

    scores = []

    for x in X:
        scores.append(
            mahalanobis(
                x,
                mean,
                inv_cov
            )
        )

Then:

    scores = np.array(scores)


# 24. `rowvar=False`

When using:

    np.cov(X, rowvar=False)

interpretation:

    rows    → observations
    columns → variables/features

For ML-style data:

    X.shape = (
        n_samples,
        n_features
    )

you generally want:

    rowvar=False


# 25. SciPy Distance Functions — Summary

    distance.euclidean(x, y)
        → Euclidean distance

    distance.cityblock(x, y)
        → Manhattan / L1 distance

    distance.jaccard(x, y)
        → Jaccard distance

    distance.cosine(x, y)
        → Cosine distance

    mahalanobis(
        x,
        mean,
        inverse_covariance
    )
        → Mahalanobis distance


# 26. Distance vs Similarity

This distinction is extremely important.

DISTANCE:

    smaller = more similar

SIMILARITY:

    larger = more similar

For the assignment:

    Jaccard similarity
        = 1 - Jaccard distance

    Cosine similarity
        = 1 - Cosine distance


# 27. Distance API Cheat Sheet

## Numeric vectors

    distance.euclidean(x, y)

    distance.cityblock(x, y)

## Binary vectors

    distance.jaccard(x, y)

    distance.cosine(x, y)

## Multivariate numerical data

    mahalanobis(
        x,
        mean,
        inverse_covariance
    )


# 28. EXAM RECALL

If you see:

    Euclidean
        → distance.euclidean()

    Manhattan
        → distance.cityblock()

    Jaccard
        → distance.jaccard()

    Cosine
        → distance.cosine()

    Mahalanobis
        → mahalanobis()


# 29. EXAM TRAPS

## Trap 1 — Distance vs similarity

SciPy:

    jaccard()
        → distance

    cosine()
        → distance

If question asks for similarity:

    1 - distance


## Trap 2 — Zero vectors

Cosine similarity has a zero-vector issue.

Check:

    np.all(x == 0)

before blindly computing cosine similarity.


## Trap 3 — Mahalanobis needs covariance information

You cannot just call:

    mahalanobis(x, y)

You need:

    x
    mean vector
    inverse covariance matrix


## Trap 4 — Covariance orientation

For:

    X.shape = (
        observations,
        features
    )

use:

    np.cov(
        X,
        rowvar=False
    )


# 30. FINAL SCIpy MENTAL MODEL

Think:

    "I need a distance"
             ↓
       scipy.spatial.distance
             ↓
       ┌─────┼───────────┐
       ↓     ↓           ↓
    Euclidean Cityblock  Jaccard/Cosine
       │       │           │
       │       │           └── convert distance
       │       │               to similarity
       │       │
       └───────┴── numerical distances


    "I need Mahalanobis"
             ↓
    scipy.spatial.distance
             ↓
       mahalanobis()
             ↓
    mean + covariance⁻¹

# 31. THE ONLY SCIpy APIs YOU REALLY NEED TOMORROW

from scipy.spatial import distance

distance.euclidean(x, y)

distance.cityblock(x, y)

distance.jaccard(x, y)

distance.cosine(x, y)

from scipy.spatial.distance import mahalanobis

mahalanobis(
    x,
    mean,
    inverse_covariance
)


And remember:


Jaccard similarity
    = 1 - Jaccard distance

Cosine similarity
    = 1 - Cosine distance



That's the whole **relevant SciPy universe** for this exam. The actual supplied solution uses exactly these distance APIs for Q3, Q4, and Q11. :contentReference[oaicite:0]{index=0} :contentReference[oaicite:1]{index=1}

Next is the big one: **scikit-learn**. That handbook will be considerably larger because we need to cover imputation, scaling/transformation, encoding, covariance/outlier detection, and the train/test preprocessing workflow.
