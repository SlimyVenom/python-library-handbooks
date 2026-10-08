
# Small Libraries + Python Core Ultimate API Handbook
## Data Cleaning Assignment — Q1–Q24

This handbook covers:

- RapidFuzz
- `re`
- `unicodedata`
- Python strings
- Python sets
- Python dictionaries
- Python lists / tuples
- Boolean logic
- `zip`
- sorting
- basic typing syntax
- a few small patterns repeatedly used in Q13–Q24

---

# 1. RapidFuzz

RapidFuzz is used for **string similarity / fuzzy matching**.

Main imports from the assignment:

```python
from rapidfuzz.distance import Levenshtein
from rapidfuzz.distance import JaroWinkler
````

---

# 2. Levenshtein Distance

```
Levenshtein.distance(a, b)
```

Measures the minimum number of single-character edits required to transform one string into another.

Allowed operations conceptually:

```
insert
delete
substitute
```

Example:

```
Levenshtein.distance(
    "cat",
    "cut"
)
```

The answer is:

```
1
```

because:

```
cat → cut
```

requires one substitution.

---

# 3. Levenshtein normalized similarity

```
Levenshtein.normalized_similarity(a, b)
```

Returns a normalized similarity score.

Conceptually:

```
0 → completely different
1 → identical
```

Used in Q5 and Q16.

Example:

```
sim = Levenshtein.normalized_similarity(
    email_a,
    email_b
)
```

---

# 4. Levenshtein distance vs similarity

VERY IMPORTANT.

```
Levenshtein.distance(a, b)
```

→ distance

```
Levenshtein.normalized_similarity(a, b)
```

→ similarity

So:

```
distance
    smaller = more similar

similarity
    larger = more similar
```

---

# 5. Jaro-Winkler

Import:

```
from rapidfuzz.distance import JaroWinkler
```

Similarity:

```
JaroWinkler.similarity(a, b)
```

Returns a similarity score.

Conceptually:

```
0 → dissimilar
1 → identical
```

Used in:

```
Q6
Q16
```

---

# 6. Jaro-Winkler intuition

Jaro-Winkler is useful for comparing strings where:

- characters may be transposed
- strings may have small differences
- prefixes may be important

For example, names:

```
"mohammed"
"mohamad"
```

can receive a high similarity despite not being identical.

You generally do NOT need to implement Jaro-Winkler yourself.

Just recognize:

```
JaroWinkler.similarity(a, b)
```

---

# 7. Missing strings + RapidFuzz

Important assignment pattern:

```
if pd.isna(a) or pd.isna(b):
    score = np.nan
else:
    score = Levenshtein.normalized_similarity(a, b)
```

Why?

Because:

```
missing ≠ disagreement
```

Missing means:

> We don't have evidence.

Whereas:

```
similarity = 0
```

means:

> We compared them and they are completely dissimilar.

---

# 8. `re` — Regular Expressions

Import:

```
import re
```

Your assignment uses regex mainly for **validation / pattern matching**.

---

# 9. re.fullmatch()

Most important regex function here:

```
re.fullmatch(pattern, string)
```

It checks whether the **entire string** matches the pattern.

Example:

```
re.fullmatch(
    r"\d{10}",
    phone
)
```

means:

> Does the entire string consist of exactly 10 digits?

---

# 10. Why fullmatch instead of search?

Consider:

```
re.search(r"\d{10}", "abc1234567890xyz")
```

It can find a matching substring.

But:

```
re.fullmatch(r"\d{10}", "abc1234567890xyz")
```

fails because the entire string is not 10 digits.

For validation:

```
fullmatch
    ↓
usually what you want
```

---

# 11. Important regex symbols

## `\d`

Digit:

```
0–9
```

Example:

```
r"\d"
```

---

## `\w`

Word character.

Generally includes:

```
letters
digits
underscore
```

---

## `\s`

Whitespace.

---

## `.`

Any character (with usual regex semantics).

---

# 12. Quantifiers

## `{n}`

Exactly n occurrences.

```
r"\d{10}"
```

means:

```
exactly 10 digits
```

---

## `{n,m}`

Between n and m occurrences.

```
r"\d{8,10}"
```

---

## `+`

One or more.

```
r"\d+"
```

---

## `*`

Zero or more.

```
r"\d*"
```

---

## `?`

Zero or one.

```
r"\d?"
```

---

# 13. Character classes

```
r"[0-9]"
```

one digit.

```
r"[A-Za-z]"
```

one ASCII letter.

```
r"[abc]"
```

one of:

```
a
b
c
```

---

# 14. Regex anchors

```
^
```

start of string.

```
$
```

end of string.

For example:

```
r"^\d{10}$"
```

means exactly 10 digits.

But if you're doing complete-string validation, this is often cleaner:

```
re.fullmatch(r"\d{10}", s)
```

---

# 15. re.sub()

Replace regex matches:

```
re.sub(pattern, replacement, string)
```

Example:

```
re.sub(
    r"\D",
    "",
    phone
)
```

means:

> Remove every non-digit character.

So:

```
"+91-98765-43210"
```

becomes:

```
"919876543210"
```

---

# 16. `\D`

Opposite of `\d`.

```
\D → non-digit
```

Therefore:

```
re.sub(r"\D", "", phone)
```

is a very common phone-normalization pattern.

---

# 17. Regex return values

`re.fullmatch()` returns:

```
Match object
```

if successful.

Otherwise:

```
None
```

Therefore:

```
if re.fullmatch(r"\d{10}", phone):
    ...
```

works naturally.

---

# 18. `unicodedata`

Import:

```
import unicodedata
```

Used in Q14.

---

# 19. unicodedata.normalize()

Syntax:

```
unicodedata.normalize(
    form,
    string
)
```

Common forms:

```
NFC
NFD
NFKC
NFKD
```

Your assignment specifically uses:

```
unicodedata.normalize(
    "NFKC",
    value
)
```

---

# 20. What is NFKC?

NFKC = Unicode Compatibility Normalization.

It converts visually/compatibility-equivalent Unicode representations into a canonical compatibility form.

The important assignment-level idea:

```
different Unicode representations
            ↓
        normalize
            ↓
more consistent text
```

---

# 21. Q14 normalization pipeline

The general pattern is:

```
raw string
   ↓
Unicode normalization
   ↓
whitespace normalization
   ↓
case normalization
   ↓
field-specific normalization
```

Example:

```
value = unicodedata.normalize(
    "NFKC",
    value
)
```

---

# 22. Python string `.strip()`

```
s.strip()
```

Removes leading and trailing whitespace.

Example:

```
"   Delhi   "
        ↓
"Delhi"
```

---

# 23. Python string `.split()`

```
s.split()
```

Splits on whitespace.

Example:

```
"hello   world".split()
```

produces:

```
["hello", "world"]
```

Notice multiple spaces are naturally collapsed when using default whitespace splitting.

---

# 24. `" ".join(...)`

Very important Q14 pattern:

```
" ".join(s.split())
```

This normalizes arbitrary whitespace.

Example:

```
"   New     Delhi   "
        ↓
"New Delhi"
```

Mental model:

```
split
 ↓
remove whitespace runs
 ↓
join with ONE space
```

---

# 25. `.lower()

```
s.lower()
```

Converts letters to lowercase.

Example:

```
"Delhi"
 ↓
"delhi"
```

Useful for canonicalization.

---

# 26. `.upper()

```
s.upper()
```

Converts letters to uppercase.

Not heavily used in the assignment, but useful to recognize.

---

# 27. `.replace()

```
s.replace(old, new)
```

Example:

```
"hello world".replace(
    " ",
    "_"
)
```

produces:

```
hello_world
```

---

# 28. `.isdigit()

```
s.isdigit()
```

Checks whether the string consists of digits.

Example:

```
"12345".isdigit()
```

→ `True`

```
"123a".isdigit()
```

→ `False`

---

# 29. `.startswith()

```
s.startswith(prefix)
```

Example:

```
"hello".startswith("he")
```

→ `True`

---

# 30. `.endswith()

```
s.endswith(suffix)
```

Example:

```
"hello@example.com".endswith(
    ".com"
)
```

→ `True`

---

# 31. Python sets

Sets are heavily useful in Q15, Q18 and Q7-style string operations.

Create:

```
s = set(...)
```

Example:

```
s = {"Delhi", "Mumbai", "Pune"}
```

---

# 32. Set membership

```
x in s
```

Example:

```
"Delhi" in cities
```

→ `True`

---

# 33. Set union

```
A | B
```

or:

```
A.union(B)
```

Example:

```
A = {"a", "b"}
B = {"b", "c"}

A | B
```

→

```
{"a", "b", "c"}
```

---

# 34. Set intersection

```
A & B
```

or:

```
A.intersection(B)
```

Example:

```
A = {a,b}
B = {b,c}

A & B
→ {b}
```

---

# 35. Set difference

```
A - B
```

means:

> elements in A but not B.

---

# 36. Jaccard similarity using sets

Q7 concept:

```
A = set(tokens_a)
B = set(tokens_b)

similarity = len(A & B) / len(A | B)
```

Formula:

```
Jaccard =
|intersection|
--------------
|union|
```

Edge case:

```
A = empty
B = empty
```

Then union size is zero, so you must decide the assignment's specified behavior rather than blindly divide by zero.

---

# 37. Sets for duplicate blocking

Q15 uses:

```
pairs = set()
```

Then:

```
pairs.add((i, j))
```

Why a set?

Because duplicate candidate pairs should collapse automatically.

For example:

```
(1, 4)
(1, 4)
(1, 4)
```

stored in a set becomes only:

```
{(1, 4)}
```

---

# 38. Set of tuples

Very useful:

```
pairs = set()

pairs.add((i, j))
```

Each element is a tuple.

Later:

```
sorted(pairs)
```

can produce deterministic ordering.

---

# 39. Python dictionaries

Create:

```
d = {}
```

Add:

```
d[key] = value
```

Example:

```
city_aliases = {
    "bombay": "mumbai",
    "delhi ncr": "delhi"
}
```

---

# 40. Dictionary lookup

```
d[key]
```

Example:

```
city_aliases["bombay"]
```

→

```
mumbai
```

But missing keys raise `KeyError`.

---

# 41. Dictionary `.get()

Safer lookup:

```
d.get(key)
```

or:

```
d.get(key, default)
```

Example:

```
city_aliases.get(
    city,
    city
)
```

Meaning:

> Use the alias if present; otherwise keep the original city.

---

# 42. Dictionary `.items()

```
for key, value in d.items():
    ...
```

Useful when iterating through mappings.

---

# 43. Dictionary `.keys()

```
d.keys()
```

Returns dictionary keys.

---

# 44. Dictionary `.values()

```
d.values()
```

Returns dictionary values.

---

# 45. Dictionary comprehension

Example:

```
squares = {
    x: x*x
    for x in range(5)
}
```

Produces:

```
{
    0: 0,
    1: 1,
    2: 4,
    3: 9,
    4: 16
}
```

Useful for constructing lookup structures.

---

# 46. Python lists

Create:

```
items = []
```

Append:

```
items.append(x)
```

Extend:

```
items.extend(other)
```

---

# 47. List indexing

```
items[0]
```

first element.

```
items[-1]
```

last element.

---

# 48. List slicing

```
items[start:stop]
```

`stop` is exclusive.

Example:

```
items[1:4]
```

gets:

```
indices 1, 2, 3
```

---

# 49. `list(...)`

Convert an iterable into a list:

```
list(some_iterable)
```

Example:

```
list(range(5))
```

→

```
[0, 1, 2, 3, 4]
```

---

# 50. Tuples

Create:

```
x = (1, 2)
```

Tuples are immutable.

Very useful for:

```
(i, j)
```

candidate pairs.

---

# 51. Tuple unpacking

```
i, j = pair
```

If:

```
pair = (3, 7)
```

then:

```
i = 3
j = 7
```

---

# 52. `zip()`

Combines iterables element-by-element.

Example:

```
for a, b in zip(A, B):
    ...
```

If:

```
A = [1,2,3]
B = [4,5,6]
```

then:

```
(1,4)
(2,5)
(3,6)
```

---

# 53. `enumerate()`

Very useful when you need both index and value:

```
for i, value in enumerate(items):
    ...
```

Example:

```
i = 0, value = first
i = 1, value = second
...
```

---

# 54. `range()`

```
range(n)
```

produces:

```
0, 1, ..., n-1
```

Example:

```
for i in range(len(df)):
    ...
```

---

# 55. `len()`

Returns number of elements.

```
len(items)
```

For a DataFrame:

```
len(df)
```

means:

> number of rows.

For a dictionary:

```
len(d)
```

means:

> number of key-value pairs.

---

# 56. `sorted()`

Returns a NEW sorted list.

```
sorted(items)
```

Doesn't modify the original iterable.

Example:

```
pairs = {(3,4), (1,2), (2,3)}

sorted(pairs)
```

---

# 57. `sorted(..., key=...)`

Sort according to a key.

Example:

```
sorted(
    values,
    key=lambda x: x[1]
)
```

---

# 58. `sorted(..., reverse=True)`

Descending order:

```
sorted(
    values,
    reverse=True
)
```

---

# 59. `min()` and `max()`

```
min(values)
max(values)
```

Can also use:

```
min(values, key=...)
max(values, key=...)
```

---

# 60. `sum()`

```
sum(values)
```

Very common for counting Boolean conditions.

Because:

```
True  = 1
False = 0
```

So:

```
sum(mask)
```

counts how many `True`s are present.

---

# 61. Boolean values as integers

Python:

```
True == 1
False == 0
```

Therefore:

```
sum([True, False, True])
```

→ `2`

This is why:

```
s.isna().sum()
```

counts missing values.

---

# 62. `any()`

```
any(values)
```

Returns `True` if at least one element is truthy.

Example:

```
any([False, False, True])
```

→ `True`

---

# 63. `all()`

```
all(values)
```

Returns `True` only if every element is truthy.

Example:

```
all([True, True, True])
```

→ `True`

---

# 64. `np.nan` awareness

Missing numeric value:

```
np.nan
```

Important:

```
np.nan != np.nan
```

Therefore don't use:

```
x == np.nan
```

to test missingness.

Use:

```
pd.isna(x)
```

or:

```
pd.isna(series)
```

---

# 65. `float()`

Convert to floating-point:

```
float(x)
```

Example:

```
float("12.5")
```

→

```
12.5
```

---

# 66. `int()`

Convert to integer:

```
int(x)
```

Example:

```
int("42")
```

→

```
42
```

---

# 67. `str()`

Convert to string:

```
str(x)
```

Important in Q23:

```
cat_train[col].astype(str)
```

This makes categorical values string-like before encoding.

---

# 68. `set()`

Convert iterable to set:

```
set(iterable)
```

Useful for:

- deduplication
- membership testing
- intersections/unions
- FK validation

Example:

```
valid_ids = set(customers["customer_id"])
```

Then:

```
transaction_id not in valid_ids
```

can detect a foreign-key violation.

---

# 69. `isinstance()`

Checks object type:

```
isinstance(x, SomeType)
```

Example:

```
isinstance(x, str)
```

→ `True`/`False`

Useful when functions need to behave differently for strings/numbers/etc.

---

# 70. `lambda`

Small anonymous function.

```
lambda x: x * 2
```

Example:

```
sorted(
    values,
    key=lambda x: x[1]
)
```

Equivalent conceptually to defining:

```
def key_function(x):
    return x[1]
```

---

# 71. `try / except`

Useful for compatibility across sklearn versions.

Pattern:

```
try:
    obj = NewAPI(...)
except TypeError:
    obj = OldAPI(...)
```

Your reference uses exactly this idea for:

```
SimpleImputer
OneHotEncoder
```

---

# 72. `raise`

You can explicitly report invalid input:

```
raise ValueError("invalid input")
```

Not central to the assignment, but useful to recognize.

---

# 73. Type hints

Your functions use things like:

```
def f(
    x: pd.DataFrame,
    k: int
) -> dict:
    ...
```

These are **type hints**.

They do not automatically enforce the types at runtime.

---

# 74. `list[str]`

Means:

> a list whose elements are intended to be strings.

Example:

```
columns: list[str]
```

---

# 75. `np.ndarray`

Type hint for NumPy arrays:

```
x: np.ndarray
```

---

# 76. Optional / None

You may encounter:

```
x = None
```

`None` means:

> no value / absence of a value.

Don't confuse it with:

```
np.nan
```

or:

```
pd.NA
```

They have different semantics.

---

# 77. `copy.deepcopy`

For independent nested structures:

```
import copy

new = copy.deepcopy(old)
```

In the assignment, Pandas usually handles copying with:

```
df.copy(deep=True)
```

---

# 78. `DataFrame.copy(deep=True)`

Although this belongs primarily to Pandas, remember the idea:

```
copy = df.copy(deep=True)
```

means:

> create an independent DataFrame copy rather than modifying the original directly.

Q21 uses this idea for the imputed audit.

---

# 79. `pd.isna()` vs `None`

When working with mixed Pandas data, prefer:

```
pd.isna(x)
```

because missing values can be represented by:

```
NaN
None
pd.NA
NaT
```

---

# 80. Common exam patterns

## Pattern 1 — normalize whitespace

```
" ".join(s.split())
```

---

## Pattern 2 — lowercase

```
s.lower()
```

---

## Pattern 3 — remove non-digits

```
re.sub(r"\D", "", s)
```

---

## Pattern 4 — validate exact format

```
re.fullmatch(pattern, s)
```

---

## Pattern 5 — Unicode normalization

```
unicodedata.normalize(
    "NFKC",
    s
)
```

---

## Pattern 6 — unique values

```
set(values)
```

---

## Pattern 7 — deduplicate pairs

```
pairs = set()
pairs.add((i, j))
```

---

## Pattern 8 — deterministic pair ordering

```
sorted(pairs)
```

---

## Pattern 9 — count condition

```
sum(condition)
```

---

## Pattern 10 — dictionary fallback

```
mapping.get(key, default)
```

---

# 81. Q14 normalization toolbox

Remember this chain:

```
Unicode
   ↓
NFKC
   ↓
whitespace
   ↓
case
   ↓
field-specific normalization
```

Typical pieces:

```
unicodedata.normalize("NFKC", s)

" ".join(s.split())

s.lower()

re.sub(r"\D", "", phone)

pd.to_datetime(...)
```

---

# 82. Q15 candidate-pair toolbox

Core Python:

```
pairs = set()

pairs.add((i, j))

sorted(pairs)
```

Pandas:

```
groupby(...)
group.index.to_numpy()
```

NumPy:

```
np.asarray(...)
np.array(...)
```

The key Python idea:

> use a set to eliminate duplicate candidate pairs.

---

# 83. Q16 comparison toolbox

RapidFuzz:

```
JaroWinkler.similarity(a, b)

Levenshtein.normalized_similarity(a, b)
```

Missing:

```
if pd.isna(a) or pd.isna(b):
    score = np.nan
```

Exact agreement:

```
int(a == b)
```

when both values are known.

---

# 84. Q17 classification toolbox

Dictionary:

```
weights.get(field, 0)
```

Boolean logic:

```
if score >= upper:
    ...
elif score < lower:
    ...
else:
    ...
```

Remember the boundary:

```
score >= upper → match

score < lower → nonmatch

otherwise → review
```

---

# 85. Q18 golden-record toolbox

Sets:

```
set(...)
```

Grouping:

```
groupby(...)
```

Median:

```
series.median()
```

Mode:

```
series.mode()
```

Sorting/tie-breaking:

```
sorted(
    candidates,
    key=lambda x: (...)
)
```

Important conceptual rule from the assignment:

```
numeric
→ median

nonnumeric
→ mode
→ tie: longest string
→ then lexicographically smallest
```

---

# 86. Q19 missingness-diagnostics toolbox

Boolean masks:

```
target_missing = df[target].isna()
```

Then:

```
df[target_missing]
df[~target_missing]
```

Grouping:

```
df.groupby(column)[target_missing].mean()
```

Important:

```
Boolean mean = proportion True
```

So:

```
group_missing.mean()
```

can represent a missingness rate.

---

# 87. Q20 masking toolbox

Boolean mask:

```
mask = ...
```

Then:

```
X_masked = X.copy()
X_masked[mask] = np.nan
```

Later evaluate:

```
predicted[mask]
actual[mask]
```

The key idea:

> evaluate imputation only where values were deliberately hidden.

---

# 88. Q21 audit toolbox

Original data:

```
original = frame
```

Imputed data:

```
augmented = imputed.copy(deep=True)
```

Missingness:

```
was_missing = original[col].isna()
```

Audit rows can be built using:

```
for row_index in ...:
    ...
```

Important:

> detect missingness from the ORIGINAL data.

Do not detect it after imputation.

---

# 89. Q22 outlier toolbox

Robust statistics:

```
median
MAD
IQR
```

scikit-learn:

```
MinCovDet(...)
LocalOutlierFactor(...)
```

Python:

```
sum(mask)
```

NumPy:

```
np.abs(...)
np.nanmedian(...)
np.nanpercentile(...)
```

---

# 90. Q23 preprocessing toolbox

Training:

```
imp.fit_transform(train)

scaler.fit_transform(train)

encoder.fit_transform(train)
```

Testing:

```
imp.transform(test)

scaler.transform(test)

encoder.transform(test)
```

Feature names:

```
encoder.get_feature_names_out(...)
```

---

# 91. Q24 audit toolbox

Mostly Python/Pandas:

```
len(...)
sum(...)
```

Dictionary:

```
{
    "baseline_rows": ...,
    "candidate_pair_count": ...,
    ...
}
```

The important thing is that Q24 is an aggregation of previous results rather than a new cleaning algorithm.

---

# 92. FINAL SMALL-API CHEAT SHEET

## RapidFuzz

```
Levenshtein.distance(a, b)

Levenshtein.normalized_similarity(a, b)

JaroWinkler.similarity(a, b)
```

---

## Regex

```
re.fullmatch(pattern, s)

re.sub(pattern, replacement, s)
```

Important:

```
r"\d"
r"\D"
r"\s"
r"\d{10}"
r"+"
r"*"
r"?"
```

---

## Unicode

```
unicodedata.normalize(
    "NFKC",
    s
)
```

---

## Strings

```
s.strip()
s.split()
" ".join(...)
s.lower()
s.upper()
s.replace(a, b)
s.isdigit()
s.startswith(x)
s.endswith(x)
```

---

## Sets

```
set(x)

A | B       # union
A & B       # intersection
A - B       # difference

x in A
```

---

## Dictionaries

```
d[key]

d.get(key)

d.get(key, default)

d.items()
d.keys()
d.values()
```

---

## Core Python

```
len(x)
sum(x)
min(x)
max(x)
any(x)
all(x)

sorted(x)

enumerate(x)
zip(a, b)
range(n)

int(x)
float(x)
str(x)
set(x)
list(x)
```

---

# 93. THE BIG PICTURE

At this point your entire assignment toolbox is basically:

```
                    DATA CLEANING
                         │
        ┌────────────────┼────────────────┐
        │                │                │
      Pandas           NumPy          sklearn
        │                │                │
   tables/missing    numerical       imputation
   grouping          operations      scaling
   strings           arrays          encoding
   datetime          statistics     outliers
        │                │                │
        └────────────────┼────────────────┘
                         │
                    SciPy / RapidFuzz
                         │
                 distances / similarity
                         │
                 Python + regex
                         │
              glue everything together
```

The important thing is that you now don't need to memorize 500 random functions.

You need to recognize:

```
"What kind of problem am I solving?"
            ↓
"Which library owns that concept?"
            ↓
"What is the API pattern?"
```

Examples:

```
missing values
    → sklearn.impute

scaling
    → sklearn.preprocessing

categorical encoding
    → OneHotEncoder

Euclidean distance
    → scipy.spatial.distance

string similarity
    → RapidFuzz

Unicode normalization
    → unicodedata

pattern validation
    → re

tables/grouping
    → Pandas

numerical arrays/statistics
    → NumPy
```

# END

That finishes the **API handbook phase**.

You now have the five buckets:

1. **NumPy**
2. **Pandas**
3. **SciPy**
4. **scikit-learn**
5. **Small stuff: RapidFuzz + regex + Unicode + Python core**

The next useful step is **not another handbook**. It's to turn these into a **Q1–Q24 “what do I recognize in the question → what API/code pattern do I write?” exam sheet**. That'll be much more useful for tomorrow's actual implementation.