# Pandas — Ultimate Exam API Handbook

> Purpose: Q1–Q24 Data Cleaning / Pandas / ML exam
>
> Philosophy:
> - Think in terms of Series and DataFrames.
> - Know what operation you need.
> - Know the Pandas API that performs it.
> - Know the resulting type/shape.
> - Use Jupyter autocomplete/documentation when exact syntax is forgotten.

---

# 1. What is Pandas?

Pandas is Python's main library for working with tabular data.

The two central objects are:

    Series
    DataFrame

Mental model:

    Series
       ↓
    one-dimensional labeled data

    DataFrame
       ↓
    two-dimensional labeled table

Typical workflow:

    SQL / CSV / raw data
            ↓
        DataFrame
            ↓
    clean / transform / analyze
            ↓
        NumPy array
            ↓
       sklearn / ML


# 2. Import

    import pandas as pd

The standard alias is:

    pd


# 3. Series

A Series is a one-dimensional labeled array.

Example:

    s = pd.Series([10, 20, 30])

Think:

    index   value

      0      10
      1      20
      2      30


# 4. DataFrame

A DataFrame is a two-dimensional table.

    df = pd.DataFrame({
        "name": ["Alice", "Bob"],
        "age": [20, 21]
    })


# 5. DataFrame Shape

    df.shape

Returns:

    (number_of_rows, number_of_columns)

Example:

    df.shape
    # (100, 5)


# 6. Number of Rows

    len(df)

For a DataFrame:

    len(df)

means:

    number of rows


# 7. Number of Columns

    len(df.columns)

or:

    df.shape[1]


# 8. Columns

    df.columns

Returns the column labels.

Convert to a list:

    list(df.columns)


# 9. Index

    df.index

Returns the row index.

Example:

    df.index

Could be:

    RangeIndex(start=0, stop=5, step=1)


# 10. Selecting One Column

    df["age"]

Returns:

    Series

This is VERY important.

    df["age"]

→ Series


# 11. Selecting Multiple Columns

    df[["age", "salary"]]

Returns:

    DataFrame


# 12. Critical Difference

    df["salary"]

→ Series

    df[["salary"]]

→ DataFrame

Mental rule:

    one pair of [] + column name
        → Series

    double [[]]
        → DataFrame


# 13. Selecting Rows with .loc

`.loc` is label-based indexing.

    df.loc[index]

Example:

    df.loc[5]

gets row whose label is 5.

Select a particular cell:

    df.loc[5, "age"]


# 14. .loc with Multiple Rows

    df.loc[[1, 3, 5]]

Select specific labels.

Multiple rows + columns:

    df.loc[[1, 3], ["age", "city"]]


# 15. .loc Slicing

    df.loc[1:5]

Label-based slicing.

Unlike normal Python slicing, Pandas `.loc` slicing is generally inclusive of the endpoint.

So:

    df.loc[1:5]

includes label 5.


# 16. Boolean Indexing

One of the MOST important Pandas concepts.

Suppose:

    mask = df["age"] > 18

Then:

    df[mask]

returns rows satisfying the condition.

Can also write:

    df[df["age"] > 18]


# 17. Multiple Boolean Conditions

Use:

    &
    |
    ~

Example:

    df[
        (df["age"] > 18)
        & (df["city"] == "delhi")
    ]

NOT:

    and
    or

for Series conditions.


# 18. Boolean NOT

    ~mask

Example:

    missing = df["age"].isna()

Then:

    df[~missing]

means:

    rows where age is NOT missing.


# 19. .iloc

`.iloc` is position-based indexing.

    df.iloc[0]

first row.

    df.iloc[0, 2]

first row, third column.

    df.iloc[[0, 2]]

rows at positions 0 and 2.


# 20. .loc vs .iloc

`.loc`

    label-based

`.iloc`

    integer-position-based

Example:

    df.loc[5]

means:

    row with index label 5

    df.iloc[5]

means:

    sixth row


# 21. `.at`

Fast scalar access by label.

    df.at[row_index, "column"]

Example:

    df.at[5, "age"]

This was used in the imputation audit.

Mental model:

    .at
        → one specific cell
        → label based


# 22. `.iat`

Fast scalar access by integer position.

    df.iat[row_position, column_position]

Not central to the assignment, but know the distinction:

    at  → labels
    iat → positions


# 23. Adding a Column

    df["new_column"] = values

Example:

    df["age_squared"] = df["age"] ** 2


# 24. Replacing a Column

    df["age"] = new_values

This overwrites the existing column.


# 25. Creating a DataFrame

    pd.DataFrame({
        "name": ["A", "B"],
        "age": [20, 21]
    })


# 26. Creating from Records

    pd.DataFrame([
        {"name": "A", "age": 20},
        {"name": "B", "age": 21}
    ])

Very useful when accumulating:

    rows = []

    rows.append({
        "column": value
    })

    df = pd.DataFrame(rows)


# 27. Selecting a Column

    df["column"]

Alternative:

    df.column

But prefer:

    df["column"]

because it works reliably with arbitrary column names.


# 28. `.copy()`

Create a copy of a DataFrame.

    df2 = df.copy()

This was used extensively to avoid modifying the input.

For an explicit deep copy:

    df2 = df.copy(deep=True)


# 29. Why Copy?

Suppose:

    df2 = df

Now both names refer to the same object.

Modifying df2 can affect df.

Instead:

    df2 = df.copy()

creates an independent DataFrame.

For transformation tasks:

    df = frame.copy()

is a very common safe pattern.


# 30. Missing Values

Pandas represents missing values using things such as:

    np.nan
    None
    pd.NA

For most assignment work, think:

    missing → NaN / NA


# 31. pd.isna()

Check whether values are missing.

    pd.isna(x)

Can operate on:

    scalar
    Series
    DataFrame


# 32. Series.isna()

    s.isna()

Returns a Boolean Series.

Example:

    s = pd.Series([1, np.nan, 3])

    s.isna()

returns:

    False
    True
    False


# 33. DataFrame.isna()

    df.isna()

Returns a Boolean DataFrame with the same shape.

Each cell:

    True  → missing
    False → observed


# 34. `.notna()`

Opposite of `.isna()`.

    s.notna()

    df.notna()

Returns:

    True  → observed
    False → missing


# 35. Count Missing Values

Classic pattern:

    df.isna().sum()

Why?

    df.isna()
        → Boolean DataFrame

    .sum()
        → True counts as 1

Therefore:

    df.isna().sum()

gives missing count per column.


# 36. Count Missing in One Column

    df["age"].isna().sum()


# 37. Count Non-Missing Values

    df["age"].notna().sum()


# 38. Missingness Mask

Very important:

    missing = df["age"].isna()

Then:

    df[missing]

gives rows where age is missing.

And:

    df[~missing]

gives rows where age is observed.


# 39. `.dropna()`

Remove rows/columns containing missing values.

Default:

    df.dropna()

removes rows with at least one missing value.


# 40. `.dropna(axis=1)`

Remove columns containing missing values.

    df.dropna(axis=1)


# 41. `.dropna(subset=...)`

Only consider specific columns.

    df.dropna(
        subset=["age", "salary"]
    )

Useful when determining whether a row is incomplete with respect to selected fields.


# 42. `.fillna()`

Replace missing values.

    df["age"].fillna(0)

or:

    df.fillna(0)


# 43. `.fillna()` with a Column Statistic

Example:

    median = df["age"].median()

    df["age"].fillna(median)


# 44. Important Difference

    isna()
        → detect missing

    notna()
        → detect observed

    dropna()
        → remove missing

    fillna()
        → replace missing


# 45. `.nunique()`

Number of distinct values.

    df["city"].nunique()

Default:

    dropna=True

so missing values are not counted as a distinct value.


# 46. `.nunique(dropna=True)`

Explicitly count non-missing distinct values.

    df["city"].nunique(
        dropna=True
    )


# 47. `.nunique(dropna=False)`

Count missing as a distinct category too.

    df["city"].nunique(
        dropna=False
    )


# 48. `.unique()`

Get unique values.

    df["city"].unique()


# 49. `.value_counts()`

Count frequency of each value.

    df["city"].value_counts()

Example:

    Delhi      10
    Mumbai      7
    Chennai     4


# 50. `.value_counts(dropna=False)`

Include missing values.

    df["city"].value_counts(
        dropna=False
    )


# 51. `.value_counts()` Returns a Series

Important.

    counts = df["city"].value_counts()

`counts` is a Series whose:

    index → category
    value → count


# 52. `.reset_index()`

Convert index information into columns.

Example:

    df["city"].value_counts().reset_index()

This is useful when you need a normal DataFrame.


# 53. `.astype()`

Convert dtype.

    s.astype(str)

    s.astype(float)

    s.astype(int)

Example:

    df["city"].astype(str)


# 54. `.to_numpy()`

Convert Series/DataFrame to NumPy ndarray.

    df.to_numpy()

With dtype:

    df.to_numpy(float)

Very common transition:

    Pandas
       ↓
    NumPy
       ↓
    sklearn


# 55. `pd.to_numeric()`

Convert values to numeric.

    pd.to_numeric(s)

Very important parameter:

    errors="coerce"


# 56. `errors="coerce"`

Invalid values become:

    NaN

Example:

    pd.to_numeric(
        s,
        errors="coerce"
    )

If:

    ["10", "20", "hello"]

then conceptually:

    [10, 20, NaN]


# 57. Why `to_numeric(..., errors="coerce")`?

It lets you distinguish:

    valid numeric value
        → number

    invalid numeric value
        → NaN

This is heavily used in validation and preprocessing.


# 58. `pd.to_datetime()`

Convert values to datetime.

    pd.to_datetime(s)


# 59. `pd.to_datetime(..., errors="coerce")`

Invalid dates become NaT/NaN-like missing datetime values.

    pd.to_datetime(
        s,
        errors="coerce"
    )

Very important for detecting invalid dates.


# 60. `.dt`

Datetime accessor for a Series.

After:

    dates = pd.to_datetime(s)

you can use:

    dates.dt.year
    dates.dt.month
    dates.dt.day


# 61. `.dt.year`

Extract year:

    pd.to_datetime(
        df["dob"],
        errors="coerce"
    ).dt.year


# 62. `.dt.strftime()`

Format datetime values as strings.

Example:

    parsed.dt.strftime("%Y-%m-%d")

Produces:

    "2025-08-21"

This was used to canonicalize dates.


# 63. Date Standardization Pattern

Typical:

    parsed = pd.to_datetime(
        df["dob"],
        errors="coerce"
    )

    df["dob"] = parsed.dt.strftime(
        "%Y-%m-%d"
    )

Then restore invalid/missing values if required.


# 64. `.str`

String accessor for Series.

Examples:

    s.str.lower()
    s.str.upper()
    s.str.strip()
    s.str.split()
    s.str[-4:]


# 65. `.str.lower()`

Convert strings to lowercase.

    s.str.lower()


# 66. `.str.upper()`

Convert strings to uppercase.

    s.str.upper()


# 67. `.str.strip()`

Remove leading/trailing whitespace.

    s.str.strip()


# 68. `.str.split()`

Split strings.

    s.str.split()

Example:

    "hello world"
        ↓
    ["hello", "world"]


# 69. `.str[-4:]`

Get last four characters.

    df["phone"].str[-4:]

This was used in blocking duplicate candidates.

Mental model:

    .str
      ↓
    string operations on every value


# 70. `.map()`

Apply a function element-by-element to a Series.

    s.map(function)


# 71. Why `.map()`?

Suppose you define:

    def normalize(x):
        ...

Then:

    df["name"].map(normalize)

runs:

    normalize(value)

for every element.

This was central to Q14.


# 72. `.map(lambda ...)`

You can use a lambda:

    s.map(
        lambda x: str(x).strip()
    )

Useful for small transformations.


# 73. `.map()` vs `.str`

Use `.map()` when:

    you need custom Python logic

Use `.str` when:

    the operation is a standard string operation


# 74. `.apply()`

Apply a function along an axis.

For a DataFrame:

    df.apply(function)

By default:

    axis=0

meaning function is applied column-wise.

Example:

    df.apply(
        pd.to_numeric,
        errors="coerce"
    )


# 75. `.apply(..., axis=1)`

Apply a function row-by-row.

    df.apply(
        function,
        axis=1
    )

Not heavily needed in the final reference implementation, but important to recognize.


# 76. `.where()`

Keep values where condition is True; otherwise replace them.

    s.where(condition, replacement)


# 77. `.where()` Pattern from Q23

    cat_train[col] = (
        cat_train[col]
        .astype(object)
        .where(
            cat_train[col].notna(),
            fill
        )
        .astype(str)
    )

Meaning:

    if value is not missing:
        keep it

    if value is missing:
        use fill


# 78. `.dropna()` vs `.where()`

    dropna()
        → removes rows/values

    where()
        → preserves shape, replaces failing values


# 79. `.mode()`

Most frequent value(s).

    s.mode()

Returns a Series.

Example:

    mode = s.mode()

If multiple values tie, multiple values may be returned.


# 80. Getting the First Mode

    mode.iloc[0]

This appeared in Q23.

Typical:

    mode = s.dropna().mode()

    if len(mode):
        fill = str(mode.iloc[0])


# 81. `.median()`

Median of a Series.

    s.median()

For numeric data:

    df["age"].median()


# 82. `.mean()`

Mean.

    s.mean()

or:

    df["age"].mean()


# 83. `.min()` / `.max()`

Minimum:

    s.min()

Maximum:

    s.max()


# 84. `.quantile()`

Quantile of a Series.

    s.quantile(0.25)

    s.quantile(0.75)

Used for:

    Q1
    Q3

Alternative NumPy form:

    np.nanpercentile(...)


# 85. `.sum()`

Sum.

    s.sum()

Also used to count Boolean values:

    s.isna().sum()


# 86. `.count()`

Count non-missing values.

    s.count()

Important distinction:

    s.count()
        → number of non-missing values

    len(s)
        → total number of values


# 87. `.any()`

Check whether any Boolean value is True.

    mask.any()


# 88. `.all()`

Check whether all Boolean values are True.

    mask.all()


# 89. `.between()`

Check whether values lie within a range.

    s.between(lower, upper)

Returns Boolean Series.

Example:

    valid_age = age.between(18, 100)


# 90. Important `.between()` Boundary Behavior

Default:

    inclusive="both"

So:

    s.between(18, 100)

means:

    18 <= x <= 100


# 91. `.isin()`

Check membership in a collection.

    s.isin(values)

Example:

    df["city"].isin(
        ["Delhi", "Mumbai"]
    )


# 92. Negating `.isin()`

    ~df["city"].isin(
        ["Delhi", "Mumbai"]
    )

means values NOT in the list.


# 93. `.duplicated()`

Detect duplicate rows.

    df.duplicated()

Returns Boolean Series:

    True
        → duplicate

    False
        → first occurrence / unique


# 94. `.duplicated().sum()`

Count duplicate rows.

    df.duplicated().sum()


# 95. `.duplicated(subset=...)`

Check duplicates based only on selected columns.

    df.duplicated(
        subset=["name", "email"]
    )


# 96. `.sort_values()`

Sort DataFrame/Series.

    df.sort_values("age")


# 97. Sort by Multiple Columns

    df.sort_values(
        by=["count", "value"]
    )


# 98. Different Sort Directions

    df.sort_values(
        by=["count", "value"],
        ascending=[False, True]
    )

Meaning:

    count → descending
    value → ascending


# 99. `.head()`

First n rows.

    df.head()

Default:

    5 rows

Specific:

    df.head(10)


# 100. `.tail()`

Last n rows.

    df.tail()


# 101. `.reset_index()`

Reset the DataFrame index.

    df.reset_index()

Very common after sorting/grouping/filtering.

To avoid preserving the old index:

    df.reset_index(drop=True)


# 102. `.reset_index(drop=True)`

The new index becomes:

    0, 1, 2, ...

The old index is discarded.


# 103. `.groupby()`

One of the most important Pandas concepts.

    df.groupby("city")

Creates groups based on city.


# 104. Grouping by Multiple Columns

    df.groupby(
        ["city", "birth_year"]
    )

Creates groups based on the combination:

    city + birth_year


# 105. Iterating Through Groups

    groups = df.groupby("city")

    for key, group in groups:
        ...

`key` = group identifier

`group` = DataFrame containing that group.


# 106. Group Index

Inside a group:

    group.index

gets the original row labels.

Convert to NumPy:

    group.index.to_numpy()


# 107. `.groupby(...)[column]`

Can group and select a specific column:

    df.groupby("city")["age"]


# 108. Group Aggregation

Common:

    df.groupby("city")["age"].mean()

Other aggregations:

    .sum()
    .mean()
    .median()
    .min()
    .max()
    .count()


# 109. `.groupby().size()`

Number of rows in each group.

    df.groupby("city").size()


# 110. `.groupby().agg()`

Multiple aggregations.

    df.groupby("city")["age"].agg(
        ["mean", "median", "min", "max"]
    )

Not central to the reference solution, but useful to recognize.


# 111. Grouped Missingness Rate

This pattern is important for Q19:

    temp.groupby(
        "cond",
        dropna=False
    )["target_missing"].mean()

Why mean?

Because:

    True  = 1
    False = 0

Therefore mean(Boolean) = proportion True.

So this calculates:

    missingness rate per group


# 112. `dropna=False` in groupby

Normally missing group keys may be excluded.

Use:

    groupby(
        "column",
        dropna=False
    )

when you want NaN itself to be treated as a group.


# 113. `.max()` and `.min()` on Group Rates

If:

    rates = ...

then:

    rates.max()
    rates.min()

and:

    rates.max() - rates.min()

gives the range of rates.

This was used in Q19.


# 114. `pd.api.types`

Pandas provides type-checking utilities.

Example:

    pd.api.types.is_numeric_dtype(s)

Returns whether a Series is numeric.

Used in Q18.


# 115. `select_dtypes()`

Select columns based on dtype.

Numeric:

    df.select_dtypes(
        include=np.number
    )

Non-numeric:

    df.select_dtypes(
        exclude=np.number
    )


# 116. `pd.isna()` vs `.isna()`

These are conceptually equivalent.

    pd.isna(x)

works with a value/object.

    s.isna()

is the Series method.

Similarly:

    pd.notna(x)
    s.notna()


# 117. `pd.notna()`

Check whether a scalar/value is non-missing.

Example:

    if pd.notna(x):
        ...


# 118. `pd.isna()` for Scalar Values

Extremely useful inside custom functions.

Example:

    def normalize(x):
        if pd.isna(x):
            return np.nan

This prevents:

    str(np.nan)

from accidentally becoming:

    "nan"


# 119. Missing vs Invalid

VERY IMPORTANT CONCEPT.

Missing:

    value is absent

Invalid:

    value exists but violates expected rules

Example:

    age = NaN
        → missing

    age = -500
        → invalid

    age = "hello"
        → invalid for numeric age

Pandas gives tools to detect both, but they are not the same thing.


# 120. `pd.to_numeric(..., errors="coerce")` for Validation

Suppose:

    age = pd.to_numeric(
        customers["age"],
        errors="coerce"
    )

Now:

    valid numeric value
        → numeric

    invalid string
        → NaN

Then you can use:

    age.notna()

to distinguish actual numeric entries.


# 121. Boolean Masks with Index

Important pattern:

    customers.index[mask]

This selects the original row labels where mask is True.


# 122. `.to_numpy(int)`

Convert selected index to NumPy integer array:

    customers.index[
        mask
    ].to_numpy(int)


# 123. `.to_numpy(bool)`

Convert a Boolean Series to a NumPy Boolean array.

    mask.to_numpy(bool)


# 124. Preserving Original Row Indices

This is important in the duplicate/entity tasks.

Suppose:

    group = df.groupby(...)

Then:

    group.index

refers to the original DataFrame indices.

Do NOT confuse:

    group position

with:

    original row index


# 125. `frame.iloc[...]`

In Q23:

    train = frame.iloc[
        np.asarray(
            train_indices,
            dtype=int
        )
    ]

This means:

    use integer positions
    specified by train_indices


# 126. `.copy(deep=True)`

Used in Q21:

    augmented = imputed.copy(
        deep=True
    )

Creates an independent DataFrame copy.


# 127. Adding Missingness Indicators

Pattern:

    was_missing = original[col].isna()

Then:

    augmented[
        f"{col}_was_missing"
    ] = was_missing.to_numpy(bool)

Important concept:

Use the ORIGINAL data to determine missingness.

Why?

Because after imputation:

    NaN

is gone.


# 128. Accessing Specific Cells

    original.at[idx, col]

    imputed.at[idx, col]

Used in Q21 to construct audit records.


# 129. DataFrame Construction from a List of Dicts

Pattern:

    rows = []

    rows.append({
        "row_index": idx,
        "column": col,
        "old_value": old,
        "new_value": new
    })

    audit = pd.DataFrame(rows)

Very useful for output tables.


# 130. DataFrame Column Order

When exact output is required:

    pd.DataFrame(
        rows,
        columns=[
            "row_index",
            "column",
            "old_value",
            "new_value"
        ]
    )

Explicit `columns=` controls the output order.


# 131. `.sort_values()` + `.reset_index()`

Common finalization pattern:

    df.sort_values(
        ["row_index", "column"]
    ).reset_index(drop=True)


# 132. `.dropna(subset=...)`

Example:

    temp = pd.DataFrame({
        "cond": s,
        "target_missing": target_missing
    }).dropna(
        subset=["cond"]
    )

Meaning:

    remove rows where cond is missing

while leaving target_missing untouched.


# 133. `DataFrame.get()`

Retrieve a column/key if present.

    df.get("age")

Useful when a column may not exist.

Also:

    dictionary.get("key")


# 134. `Series.loc`

Series also supports `.loc`.

    s.loc[index]

Useful when working with labeled Series.


# 135. `.items()`

Iterate over Series:

    for index, value in s.items():
        ...


# 136. `.columns`

Iterate through column names:

    for col in df.columns:
        ...


# 137. `.shape`

For DataFrame:

    rows, columns = df.shape


# 138. `.empty`

Check whether DataFrame/Series is empty.

    df.empty

Not heavily used, but useful to recognize.


# 139. `.copy()` vs View

Important practical idea:

    df2 = df.copy()

creates an independent object.

Do not casually assume slicing always creates a fully independent copy.

For exam code, when modifying a subset:

    subset = df[...].copy()

is the safe pattern.


# 140. String Normalization with Pandas

Typical Q14 workflow:

    df["name"] = df["name"].map(
        normalize_text
    )

where normalize_text does:

    pd.isna(x)
    str(x)
    ...
    return x


# 141. Pandas + Unicode Normalization

Pandas itself does not perform the Unicode normalization.

Instead:

    df["name"].map(normalize_text)

and inside:

    unicodedata.normalize(
        "NFKC",
        x
    )


# 142. Pandas + Regex

Regex validation can be applied through:

    Series.map()

Example:

    email.map(
        lambda x:
            bool(pattern.match(str(x).strip()))
    )


# 143. `.str` vs Python String Methods

Pandas:

    s.str.lower()

operates on an entire Series.

Python:

    x.lower()

operates on one string.

Therefore:

    df["name"].str.lower()

vs:

    df["name"].map(
        lambda x: x.lower()
    )

Both can perform similar operations, but `.map()` is better when custom missing-value logic is needed.


# 144. Chaining Operations

Pandas allows:

    df[
        condition
    ].sort_values(
        "age"
    ).reset_index(
        drop=True
    )

Read it left to right:

    filter
      ↓
    sort
      ↓
    reset index


# 145. `in` with Columns

Check whether a column exists:

    if "email" in df:
        ...


# 146. Iterating Through Columns

    for col in ["dob", "signup_date"]:
        if col in df:
            ...


# 147. `DataFrame.columns`

    df.columns

can be used to:

    inspect schema
    iterate columns
    construct output


# 148. DataFrame Schema Mental Model

A DataFrame has:

    index
    columns
    values
    dtypes

Think:

    DataFrame
    ├── index
    ├── columns
    ├── values
    └── dtypes


# 149. Series Mental Model

A Series has:

    index
    values
    dtype

Think:

    Series
    ├── index
    ├── values
    └── dtype


# 150. Pandas → NumPy → sklearn

A very common exam pipeline:

    DataFrame
        ↓
    pd.to_numeric(...)
        ↓
    .to_numpy(float)
        ↓
    NumPy ndarray
        ↓
    sklearn


# 151. Pandas → NumPy Example

    X = (
        df[numeric_columns]
        .apply(
            pd.to_numeric,
            errors="coerce"
        )
        .to_numpy(float)
    )

Breakdown:

    df[numeric_columns]
        ↓
    select columns

    .apply(pd.to_numeric, ...)
        ↓
    convert values to numeric

    errors="coerce"
        ↓
    invalid → NaN

    .to_numpy(float)
        ↓
    NumPy float array


# 152. Train/Test Selection

Given:

    train_indices
    test_indices

use:

    train = frame.iloc[
        np.asarray(train_indices, dtype=int)
    ]

    test = frame.iloc[
        np.asarray(test_indices, dtype=int)
    ]

Important:

`.iloc` means integer positions.


# 153. Training-Only Statistics

Critical ML preprocessing principle:

    train
      ↓
    learn parameters

    test
      ↓
    only transform

Examples:

    median
    scaler mean
    scaler standard deviation
    categorical mode
    one-hot categories

must be learned from training data only.


# 154. Pandas Role in Q23

Numerical:

    train[numeric_columns]
    .apply(pd.to_numeric, errors="coerce")
    .to_numpy(float)

Categorical:

    train[categorical_columns].copy()

Missing category:

    .dropna().mode()

Fill:

    .where(...)

Then:

    sklearn preprocessing


# 155. Groupby Mental Model

Think:

    DataFrame
       ↓
    groupby(key)
       ↓
    many smaller DataFrames
       ↓
    aggregate/analyze each group


# 156. Missingness Diagnostic Mental Model

Q19:

    target_missing = frame[target_column].isna()

Then for each conditioning column:

    numeric?
        ↓
    mean difference

or:

    categorical?
        ↓
    group missingness rates
        ↓
    max(rate) - min(rate)


# 157. Boolean Mean Trick

Suppose:

    missing = [True, False, True, True]

Then:

    missing.mean()

conceptually:

    (1 + 0 + 1 + 1) / 4
    = 0.75

Therefore:

    mean(Boolean)

can represent a rate/proportion.

This is why:

    groupby()["target_missing"].mean()

gives missingness rates.


# 158. Pandas API Quick Reference

## Creation

    pd.DataFrame()
    pd.Series()

## Inspection

    df.shape
    df.columns
    df.index
    df.dtypes
    len(df)

## Selection

    df["col"]
    df[["col1", "col2"]]

    df.loc[]
    df.iloc[]
    df.at[]
    df.iat[]

## Missing data

    pd.isna()
    pd.notna()

    .isna()
    .notna()

    .dropna()
    .fillna()

## Types/conversion

    pd.to_numeric()
    pd.to_datetime()

    .astype()
    .to_numpy()

## Datetime

    .dt
    .dt.year
    .dt.month
    .dt.day
    .dt.strftime()

## Strings

    .str
    .str.lower()
    .str.upper()
    .str.strip()
    .str.split()
    .str[-4:]

## Custom functions

    .map()
    .apply()
    .where()

## Statistics

    .sum()
    .mean()
    .median()
    .min()
    .max()
    .count()
    .quantile()
    .mode()

## Distinct/categories

    .unique()
    .nunique()
    .value_counts()

## Filtering

    boolean masks
    .isin()
    .between()

## Duplicates

    .duplicated()

## Grouping

    .groupby()
    .size()
    .agg()

## Sorting

    .sort_values()

## Index

    .reset_index()
    .copy()

## Type checking

    pd.api.types.is_numeric_dtype()
    df.select_dtypes()

## Construction

    pd.DataFrame(rows)
    pd.DataFrame(rows, columns=[...])

---

# 159. EXAM RECALL — MUST KNOW

If you remember nothing else, remember these.

## Selection

    df["col"]
    df[["col1", "col2"]]

    df.loc[]
    df.iloc[]

    df.at[]
    df.iat[]

## Missing data

    df.isna()
    df.notna()

    df.isna().sum()

    df.dropna()
    df.fillna()

## Conversion

    pd.to_numeric(
        x,
        errors="coerce"
    )

    pd.to_datetime(
        x,
        errors="coerce"
    )

## Strings

    s.str.lower()
    s.str.strip()
    s.str[-4:]

## Custom transformation

    s.map(function)

## Categories

    s.value_counts()
    s.unique()
    s.nunique()

## Statistics

    s.mean()
    s.median()
    s.min()
    s.max()
    s.quantile()
    s.mode()

## Filtering

    df[mask]

    s.isin(values)
    s.between(a, b)

## Duplicates

    df.duplicated()

## Grouping

    df.groupby(...)
    group.index

## Sorting

    df.sort_values(...)

## Index reset

    df.reset_index(drop=True)

## NumPy transition

    df.to_numpy()

## Type selection

    df.select_dtypes(...)

## Type checking

    pd.api.types.is_numeric_dtype(s)


# 160. EXAM MENTAL MODEL

When you see a Pandas problem, ask:

1. Am I working with a Series or DataFrame?

2. Do I need one column or multiple columns?

    df["x"]        → Series
    df[["x"]]      → DataFrame

3. Do I need labels or positions?

    .loc  → labels
    .iloc → positions

4. Am I detecting, removing, or replacing missing data?

    isna   → detect
    dropna → remove
    fillna → replace

5. Do I need a custom function per value?

    .map()

6. Do I need standard string operations?

    .str

7. Am I converting dirty values?

    pd.to_numeric(..., errors="coerce")

8. Am I parsing dates?

    pd.to_datetime(..., errors="coerce")

9. Do I need groups?

    groupby()

10. Do I need frequencies?

    value_counts()

11. Do I need distinct values?

    unique()
    nunique()

12. Do I need sorting?

    sort_values()

13. Do I need original row indices?

    df.index
    group.index

14. Am I creating a NumPy array for sklearn?

    .to_numpy(float)

15. Am I modifying a DataFrame derived from another one?

    .copy()


# 161. THE 15 PANDAS PATTERNS MOST LIKELY TO SAVE YOU TOMORROW

1. Count missing values:

    df.isna().sum()

2. Count non-missing:

    df.notna().sum()

3. Select one column:

    df["col"]

4. Select multiple columns:

    df[["a", "b"]]

5. Filter rows:

    df[mask]

6. Negate a mask:

    df[~mask]

7. Convert dirty numeric values:

    pd.to_numeric(
        s,
        errors="coerce"
    )

8. Parse dates:

    pd.to_datetime(
        s,
        errors="coerce"
    )

9. Extract year:

    pd.to_datetime(
        s,
        errors="coerce"
    ).dt.year

10. Apply custom value-level logic:

    s.map(function)

11. Count categories:

    s.value_counts()

12. Group:

    df.groupby(...)

13. Get original row indices:

    group.index.to_numpy()

14. Convert to NumPy:

    df.to_numpy(float)

15. Reset index after sorting/filtering:

    df.reset_index(drop=True)


# 162. THE MOST IMPORTANT PANDAS DISTINCTIONS

## Series vs DataFrame

    df["x"]       → Series
    df[["x"]]     → DataFrame


## loc vs iloc

    .loc  → labels
    .iloc → positions


## isna vs dropna vs fillna

    isna   → detect
    dropna → remove
    fillna → replace


## map vs str

    map → arbitrary/custom function
    str  → vectorized string operations


## unique vs nunique

    unique()
        → actual unique values

    nunique()
        → number of unique values


## count vs len

    s.count()
        → non-missing values

    len(s)
        → total values


## index vs position

    df.index
        → actual labels

    iloc
        → integer positions


## DataFrame vs NumPy

    DataFrame
        → labeled/tabular

    ndarray
        → numerical array