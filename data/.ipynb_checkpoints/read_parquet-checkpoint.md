---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.7
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

```{code-cell} ipython3
# csv_to_parquet.py
# https://stackoverflow.com/posts/45618618/revisions
#
import pandas as pd
from pathlib import Path
import matplotlib.pyplot as plt
import cartopy.crs as ccrs
import cartopy.feature as cfeature
```

```{code-cell} ipython3
parquet_file = next(Path().glob("*.pq"))
print(f"{parquet_file=}")
```

```{code-cell} ipython3
df_ocean = pd.read_parquet(parquet_file)
```

```{code-cell} ipython3
df_ocean.iloc[:,:15]
```

```{code-cell} ipython3
print(df_ocean.columns.tolist())
```

```{code-cell} ipython3
ocean_data = df_ocean[[ 'G2cruise', 'G2region', 'G2year', 'G2month','G2day',
                       'G2hour',  'G2minute', 'G2latitude','G2longitude', 'G2depth', 'G2temperature', 'G2tco2', 'G2talk', 
                       'G2phts25p0', 'G2phtsinsitutp', 'G2fco2', 'G2pressure']]

ocean_data.head()

# 'G2tco2' -- Total Dissolved Inorganic Carbon (TCO₂)
# 'G2talk' -- total alkalinity 
# 'G2phts25p0' -- 	pH at 25°C, total scale
# G2phtsinsitup -- 	In-situ pH at measurement T/P
# G2fco2 -- fugacity of CO₂ Surface air-sea CO₂ exchange indicator
```

```{code-cell} ipython3
surface = ocean_data[ocean_data['G2depth'] <= 10]  # Adjust as needed

# Drop rows with missing pH
data = surface.dropna()

grouped_data = surface.groupby(['G2region', 'G2cruise']).mean().reset_index()

grouped_data.head()

```

```{code-cell} ipython3
# Plot
plt.figure(figsize=(12, 6))
ax = plt.axes(projection=ccrs.PlateCarree())
sc = ax.scatter(grouped_data['G2longitude'], grouped_data['G2latitude'], c=grouped_data['G2phtsinsitutp'],
                cmap='viridis', s=10, transform=ccrs.PlateCarree())
ax.coastlines()
ax.add_feature(cfeature.BORDERS, linewidth=0.5)
ax.gridlines(draw_labels=True)
plt.colorbar(sc, label='pH')
plt.title("Observed Ocean Surface pH")
plt.show()

# Does this data used for model intialization 
# How different the initial conditions between moelds

# How different models are from observation?
# How different models are form each other? 

# Why models are different -- see the intializaiton? # oce
```

```{code-cell} ipython3
dups = ocean_data.columns[ocean_data.columns.duplicated()]
print(dups)
```

```{code-cell} ipython3
print(surface['G2phtsinsitutp'].describe())
```

```{code-cell} ipython3

```
