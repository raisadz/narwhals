# Quick start

## Prerequisites

Please start by following the [installation instructions](installation.md)

Then, please install the following:

- [pandas](https://pandas.pydata.org/docs/getting_started/install.html)
- [Polars](https://pola-rs.github.io/polars/user-guide/installation/)

## Simple example

Create a Python file `t.py` with the following content:

```python
import pandas as pd
import polars as pl
import narwhals as nw


def my_function(df_any):
    df = nw.DataFrame(df_any)
    column_names = df.column_names
    return column_names


df_pandas = pd.DataFrame({'a': [1, 2, 3], 'b': [4, 5, 6]})
df_polars = pl.DataFrame({'a': [1, 2, 3], 'b': [4, 5, 6]})

print('pandas result: ', my_function(df_pandas))
print('Polars result: ', my_function(df_polars))
```

If you run `python t.py` and your output looks like this:
```
pandas result: ['a', 'b']
Polars result: ['a', 'b']
```

then all your installations worked perfectly.

Let's learn about what you just did, and what Narwhals can do for you.
