
# Air Quality vs Temperature Analysis in Calabar, Nigeria

This project analyzes the relationship between surface temperature and PM2.5 concentrations over Calabar, Nigeria using air quality CSV data and temperature data in NetCDF format.

---

## 📁 Data Sources

- **Temperature (2m)**: ERA5 reanalysis data (netCDF4 format)
- **Air Quality (PM2.5)**: OpenAQ measurements (CSV)

---

## 🛠️ Tools and Libraries

Ensure the following Python libraries are installed:

```bash
!pip install xarray netCDF4 pandas matplotlib seaborn scipy
```

Or install them via Anaconda:

```bash
conda install xarray netCDF4 pandas matplotlib seaborn scipy
```

---

## 📍 Study Region

**Bounding Box**:  
Latitude: `4.95° to 5.0°`  
Longitude: `8.20° to 8.25°`  
(This represents a 1°×1° area around Calabar, Nigeria.)

---

## 📊 Analysis Steps

### 1. Load Data

- Load temperature data from a `.nc` NetCDF file using `xarray`.
- Load PM2.5 data from `.csv` using `pandas`.

### 2. Subset Temperature Data

```python
subset = ds.sel(latitude=slice(4.95, 5.0), longitude=slice(8.20, 8.25))
subset = subset.drop_vars('valid_time')  # Optional cleanup
```

### 3. Filter for 30 Days in April

```python
subset['time'] = pd.to_datetime(subset['time'].values)
recent_subset = subset.sel(time=slice(pd.Timestamp.now() - pd.Timedelta(days=30), None))
```
Datetime was filtered before downloading therefore, there's no need for the python code above.


### 4. Compute Spatial-Temporal Mean Temperature

```python
mean_temp_celsius = (recent_subset['t2m'].mean(dim=['latitude', 'longitude', 'time']) - 273.15).values
```

### 5. Merge and Align Datasets

- Ensure timestamps match.
- Interpolate temperature to match PM2.5 timestamps if needed.

### 6. Covariance Analysis

```python
cov_matrix = merged_df_clean[['temperature_2m', 'pm25']].cov()
```

### 7. Visualizations

- Covariance Metrix
- Line Regression Plot with Confidence Interval
- Time Series Comparison Plot
- Heatmap of Correlation
- Box Plots across Temperature Quartiles

### 8. Interpret X-axis Intercept

The x-axis intercept in PM2.5 vs. Temperature regression:
\[
	ext{Intercept } = -rac{b}{m}
\]
Shows the theoretical temperature where PM2.5 would be zero (not physically meaningful but useful as a trend indicator).


---

## 🔗 Correlation Plot

In addition to the covariance matrix, a **correlation heatmap** provides a standardized view of how strongly two variables move together.

### 🔍 Why Use Correlation?

| Metric       | Description                                  | Units              | Scale     |
|--------------|----------------------------------------------|---------------------|-----------|
| **Covariance** | Measures how two variables vary together      | Depends on variables | Unbounded |
| **Correlation** | Standardized covariance (range -1 to 1)      | Unitless             | [-1, 1]   |

Correlation is easier to interpret:
- **+1** = perfect positive correlation
- **0** = no correlation
- **-1** = perfect negative correlation

---

## 📌 Notes

- PM2.5 and temperature must be sampled over the same spatial and temporal resolution for valid statistical analysis.
- Temperature in Kelvin or Celsius yields the same covariance due to linear shift invariance.

---

## 📈 Optional Extensions

- Correlation or regression modeling
- Non-linear fits (polynomial, exponential)
- Mapping with Cartopy or Folium

---

## 👨‍🔬 Author

This analysis was performed interactively using extract from CAN fellowship class, open sources and Python libraries.
