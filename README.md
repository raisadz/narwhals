# Narwhals

<h1 align="center">
	<img
		width="400"
		alt="narwhals_small"
		src="https://github.com/MarcoGorelli/narwhals/assets/33491632/26be901e-5383-49f2-9fbd-5c97b7696f27">
</h1>

Extremely lightweight compatibility layer between Polars, pandas, and more.

Seamlessly support both, without depending on either!

- ✅ **Just use** a subset of **the Polars API**, no need to learn anything new
- ✅ **No dependencies** (not even Polars), keep your library lightweight
- ✅ Support both **lazy** and eager execution
- ✅ Use Polars **Expressions**

**Note: this is work-in-progress, and a bit of an experiment, don't take it too seriously**.

## Installation

```
pip install narwhals
```
Or just vendor it, it's only a bunch of pure-Python files.

## Usage

There are three steps to writing dataframe-agnostic code using Narwhals:

1. use `narwhals.DataFrame` to wrap a pandas or Polars DataFrame to a Narwhals DataFrame
2. use the subset of the Polars API supported by Narwhals. Some methods are only available
   if you initialised `narwhals.DataFrame` with `features=['eager']`, or `features=['lazy']`
3. use `narwhals.to_native` to return an object to the user in its original
   dataframe flavour. For example:

   - if you started with a pandas DataFrame, you'll get a pandas DataFrame back
   - if you started with a Polars DataFrame, you'll get a Polars DataFrame back
   - if you started with a Polars LazyFrame, you'll get a Polars LazyFrame back (unless
     you called `.collect`!)
   
## Example

Here's an example of a dataframe agnostic function:

```python
from typing import Any
import pandas as pd
import polars as pl

import narwhals as nw


def my_agnostic_function(
    suppliers_native,
    parts_native,
):
    suppliers = nw.DataFrame(suppliers_native)
    parts = nw.DataFrame(parts_native)

    result = (
        suppliers.join(parts, left_on="city", right_on="city")
        .filter(
            nw.col("color").is_in(["Red", "Green"]),
            nw.col("weight") > 14,
        )
        .group_by("s", "p")
        .agg(
            weight_mean=nw.col("weight").mean(),
            weight_max=nw.col("weight").max(),
        )
    ).with_columns(nw.col("weight_max").cast(nw.Int64))
    return nw.to_native(result)

```
You can pass in a pandas or Polars dataframe, the output will be the same!
Let's try it out:

```python

suppliers = {
    "s": ["S1", "S2", "S3", "S4", "S5"],
    "sname": ["Smith", "Jones", "Blake", "Clark", "Adams"],
    "status": [20, 10, 30, 20, 30],
    "city": ["London", "Paris", "Paris", "London", "Athens"],
}
parts = {
    "p": ["P1", "P2", "P3", "P4", "P5", "P6"],
    "pname": ["Nut", "Bolt", "Screw", "Screw", "Cam", "Cog"],
    "color": ["Red", "Green", "Blue", "Red", "Blue", "Red"],
    "weight": [12.0, 17.0, 17.0, 14.0, 12.0, 19.0],
    "city": ["London", "Paris", "Oslo", "London", "Paris", "London"],
}

print("pandas output:")
print(
    my_agnostic_function(
        pd.DataFrame(suppliers),
        pd.DataFrame(parts),
    )
)
print("\nPolars output:")
print(
    my_agnostic_function(
        pl.DataFrame(suppliers),
        pl.DataFrame(parts),
    )
)
print("\nPolars lazy output:")
print(
    my_agnostic_function(
        pl.LazyFrame(suppliers),
        pl.LazyFrame(parts),
    ).collect()
)
```

```
pandas output:
    s   p  weight_mean
0  S1  P6         19.0
1  S2  P2         17.0
2  S3  P2         17.0
3  S4  P6         19.0

Polars output:
shape: (4, 3)
┌─────┬─────┬─────────────┐
│ s   ┆ p   ┆ weight_mean │
│ --- ┆ --- ┆ ---         │
│ str ┆ str ┆ f64         │
╞═════╪═════╪═════════════╡
│ S1  ┆ P6  ┆ 19.0        │
│ S3  ┆ P2  ┆ 17.0        │
│ S4  ┆ P6  ┆ 19.0        │
│ S2  ┆ P2  ┆ 17.0        │
└─────┴─────┴─────────────┘

Polars lazy output:
shape: (4, 3)
┌─────┬─────┬─────────────┐
│ s   ┆ p   ┆ weight_mean │
│ --- ┆ --- ┆ ---         │
│ str ┆ str ┆ f64         │
╞═════╪═════╪═════════════╡
│ S1  ┆ P6  ┆ 19.0        │
│ S3  ┆ P2  ┆ 17.0        │
│ S4  ┆ P6  ┆ 19.0        │
│ S2  ┆ P2  ┆ 17.0        │
└─────┴─────┴─────────────┘
```
Magic! 🪄 

## Scope

- Do you maintain a dataframe-consuming library?
- Is there a Polars function which you'd like Narwhals to have, which would make your job easier?

If, I'd love to hear from you!

**Note**: You might suspect that this is a secret ploy to infiltrate the Polars API everywhere.
Indeed, you may suspect that.

## Why "Narwhals"?

Because they are so awesome.
