# **Exploratory Data Analysis (EDA) - Quick Reference Guide**

## **1. What is EDA?**

- **Goal**: Understand data structure, quality, and patterns before deeper analysis.
- **Key Steps**:
  - Load & inspect data (`head()`, `info()`, `describe()`).
  - Clean data (handle missing values, outliers).
  - Visualize distributions (histograms, box plots).
  - Analyze relationships (correlation, grouping).

---

## **2. Data Inspection & Cleaning**

### **Loading Data**

- `pd.read_csv("file.csv", parse_dates=["date_column"])` → Load CSV with datetime conversion.
- `df.head()`, `df.tail()` → View first/last rows.

### **Summarizing Data**

- `df.info()` → Columns, data types, missing values.
- `df.describe()` → Stats (mean, min, max, quartiles) for numeric columns.
- `df.value_counts()` → Count unique values in categorical columns.

### **Handling Missing Data**

- **Check missing values**: `df.isna().sum()`.
- **Strategies**:
  - **Drop if ≤5% missing**: `df.dropna(subset=columns_to_keep)`.
  - **Impute**:
    - Numeric: `fillna(mean/median)`.
    - Categorical: `fillna(mode)`.
  - **Group-based imputation**:
    ```python
    median_by_group = df.groupby("category")["numeric_col"].median().to_dict()
    df["numeric_col"].fillna(df["category"].map(median_by_group), inplace=True)
    ```

---

## **3. Data Validation**

### **Check Data Types**

- `df.dtypes` → Verify columns are correctly typed (e.g., `int`, `float`, `datetime`).
- Convert types: `df["col"] = df["col"].astype(int)`.

### **Validate Values**

- **Numeric ranges**: `df["col"].min()`, `df["col"].max()`.
- **Categorical checks**:
  ```python
  valid_categories = ["A", "B", "C"]
  df[~df["category"].isin(valid_categories)]  # Invalid entries
  ```

---

## **4. Visualizing Data**

### **Numerical Data**

- **Histogram**: `sns.histplot(data=df, x="col", bins=30)`.
- **Box Plot (outliers)**: `sns.boxplot(data=df, x="col")`.

### **Categorical Data**

- **Count Plot**: `sns.countplot(data=df, x="category")`.
- **Bar Plot (grouped means)**:
  ```python
  sns.barplot(data=df, x="category", y="numeric_col")
  ```

### **Time Series Data**

- **Line Plot**: `sns.lineplot(data=df, x="date", y="value")`.
- **Extract time features**:
  ```python
  df["year"] = df["date"].dt.year
  df["month"] = df["date"].dt.month
  df["weekday"] = df["date"].dt.weekday
  ```

---

## **5. Handling Outliers**

Outliers are extreme values that distort analysis. Detect them using the IQR method `(Q1 - 1.5×IQR to Q3 + 1.5×IQR)`. Remove or analyze them based on context (e.g., valid high salaries vs. data errors).

### **Detect Outliers (IQR Method)**

```python
Q1 = df["col"].quantile(0.25)
Q3 = df["col"].quantile(0.75)
IQR = Q3 - Q1
lower_limit = Q1 - 1.5 * IQR
upper_limit = Q3 + 1.5 * IQR
outliers = df[(df["col"] < lower_limit) | (df["col"] > upper_limit)]
```

### **Remove Outliers**

```python
df_clean = df[(df["col"] >= lower_limit) & (df["col"] <= upper_limit)]
```

---

## **6. Correlation Analysis**

Measures relationships between variables. Pearson correlation (-1 to 1) shows linear trends.
**Heatmaps** visualize strength, while scatter plots confirm patterns. Avoid mistaking correlation for causation.

### **Pearson Correlation**

- `df.corr()` → Correlation matrix.
- **Heatmap**:
  ```python
  sns.heatmap(df.corr(), annot=True, cmap="coolwarm")
  ```

### **Scatter Plots (Check Linearity)**

```python
sns.scatterplot(data=df, x="col1", y="col2")
```

### **Pair Plots (Multi-Variable Relationships)**

```python
sns.pairplot(df, vars=["col1", "col2", "col3"])
```

---

## **7. Grouping & Aggregation**

### **GroupBy**

```python
df.groupby("category")["numeric_col"].mean()
```

### **Multi-Aggregation**

```python
df.groupby("category").agg({"col1": "mean", "col2": "std"})
```

---

## **8. Feature Engineering**

### **Creating New Features**

- **From text**:
  ```python
  df["stops_numeric"] = df["stops_text"].str.replace(" stops", "").replace("non-stop", "0").astype(int)
  ```
- **From dates**:
  ```python
  df["hour"] = df["time"].dt.hour
  ```
- **Binning numeric data**:
  ```python
  bins = [0, 25, 50, 75, 100]
  labels = ["Low", "Medium", "High", "Very High"]
  df["binned_col"] = pd.cut(df["numeric_col"], bins=bins, labels=labels)
  ```

---

## **9. Hypothesis Generation**

- **Avoid p-hacking**: Predefine hypotheses before testing.
- **Example Hypotheses**:
  - "Flights with more stops are more expensive."
  - "Weekend flights cost more than weekday flights."
- **Test with visualization**:
  ```python
  sns.boxplot(data=df, x="weekday", y="price")
  ```

---

## **Key Takeaways**

✅ **Always inspect data first** (`head()`, `info()`, `describe()`).  
✅ **Clean missing data** (drop or impute wisely).  
✅ **Visualize distributions** (histograms, box plots).  
✅ **Check correlations** (heatmaps, scatter plots).  
✅ **Group & aggregate** for deeper insights.  
✅ **Feature engineering** unlocks hidden patterns.  
✅ **Form hypotheses** before testing to avoid bias.

🚀 **EDA is the foundation of great data analysis!**

---

**✅ Exploratory Data Analysis - Real-World Guide with Techniques and Commands**

| #   | EDA Step                   | When to Use                                                                                                                                                                          | Real-World Examples                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Specific Techniques/Commands Used                                                                                                                         |
| --- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Data Inspection            | - Beginning of every analysis<br>- Validating schema post data ingestion<br>- Checking for expected data types<br>- Spotting anomalies early<br>- Exploring a new dataset            | - `df.head()` to inspect first rows of eCommerce order data<br>- `df.columns` to confirm schema in Twitter API pull<br>- `df.info()` to assess null values in hospital data<br>- `df.dtypes` for education survey data type review<br>- `df.shape` to verify structure of Excel uploads<br>- `df.tail()` to view recent IoT sensor data<br>- `df.sample()` to randomly check call center logs<br>- `df.nunique()` to count unique responses in poll data<br>- `df.memory_usage()` to estimate large data load<br>- `df.index`, `df.columns` to inspect scraped job listings                                                       | `df.head()`, `df.tail()`, `df.columns`, `df.shape`, `df.info()`, `df.dtypes`, `df.sample()`, `df.nunique()`, `df.memory_usage()`                          |
| 2   | Missing Value Handling     | - Missing entries in any column<br>- Cleaning messy survey or scraped data<br>- Before statistical modeling<br>- When values are partially recorded<br>- Merging datasets with nulls | - `df.fillna(df.mean())` to impute missing temperatures<br>- `df.dropna(subset=['transaction_id'])` to clean retail logs<br>- `df['region'].fillna(method='ffill')` for demographic imputation<br>- `groupby('category').transform('median')` for rating gaps<br>- `df.fillna('Unknown')` in survey job titles<br>- `df.interpolate()` for sensor readings<br>- Replace mode in product category using `df.mode().iloc[0]`<br>- Flag missing revenue with `df['revenue'].isna()`<br>- Use conditional fill by segment: `df.loc[df['type']=='A', 'val'] = val_A`<br>- Drop columns with too many nulls via `df.dropna(thresh=...)` | `df.isna()`, `df.fillna()`, `df.dropna()`, `groupby().transform()`, `interpolate()`, `mode()`, `isnull()`, `dropna(thresh=...)`                           |
| 3   | Summary Statistics         | - Understanding data ranges<br>- Before applying transformations<br>- Checking distribution assumptions<br>- Detecting skew or spread<br>- Validating outlier assumptions            | - `df.describe()` to summarize weekly delivery times<br>- `df['clicks'].mean()` to understand ad engagement<br>- `df.groupby('city')['age'].median()` for demographic insights<br>- `df.std()` to view spread in car mileage<br>- `df['sales'].max()` and `.min()` to find retail extremes<br>- `df['revenue'].quantile([0.25,0.75])` to detect skew<br>- `df['duration'].mode()` for trip planning<br>- `value_counts()` for payment type frequency<br>- Use `.agg(['mean','median'])` on grouped GPA data<br>- `df['loan'].sum()` for total finance analysis                                                                    | `df.describe()`, `mean()`, `median()`, `std()`, `quantile()`, `agg()`, `value_counts()`, `sum()`                                                          |
| 4   | Data Type Validation       | - After loading CSV/JSON<br>- Before model training<br>- Validating API/Excel parsed types<br>- Converting dates/IDs<br>- Fixing object to numeric conversions                       | - `pd.to_datetime(df['date'])` for shipment records<br>- `df['income'] = df['income'].astype(float)` in loan data<br>- Convert ZIP codes to string for joining with census<br>- `df['amount'] = df['amount'].str.replace(',','').astype(float)`<br>- `pd.to_numeric(df['score'], errors='coerce')` for test scores<br>- Convert booleans: `df['subscribed'].map({'Yes':1,'No':0})`<br>- Handle categorical inputs with `astype('category')`<br>- Validate types post-merge using `df.dtypes`<br>- Fix dates from string in crime dataset<br>- Change flags from text to binary in fraud data                                      | `astype()`, `pd.to_datetime()`, `pd.to_numeric()`, `map()`, `str.replace()`                                                                               |
| 5   | Outlier Detection          | - When values deviate abnormally<br>- Preparing for modeling<br>- Identifying data entry errors<br>- Evaluating fraud indicators<br>- Removing noise in KPIs                         | - Use IQR method for filtering house prices<br>- `sns.boxplot(df['income'])` to check outliers in tax data<br>- Detect latency spikes with `zscore()` on server logs<br>- Visualize outliers in rainfall with `df.plot.box()`<br>- Flag 0s in delivery time using `df[df['delivery']==0]`<br>- Check suspicious expense claims in HR logs<br>- Remove points outside 3 std in sensor values<br>- Flag extremely high discounts<br>- Clip outlier customer spend via `df.clip()`<br>- Create outlier column with logical checks: `df['flag'] = df['amount'] > threshold`                                                           | `quantile()`, `zscore`, `clip()`, `sns.boxplot()`, `df[df[col] > X]`, `plot.box()`                                                                        |
| 6   | Distribution Visualization | - Visualize skewness and spread<br>- Check normality before tests<br>- Identify modes and frequency<br>- Spot gaps in values<br>- Compare groups visually                            | - `sns.histplot(df['age'])` for customer segmentation<br>- `df['sales'].plot.hist()` for product trend<br>- `sns.boxplot(data=df, x='region', y='delivery_time')`<br>- `sns.kdeplot(df['score'])` for exam distribution<br>- `sns.countplot(df['gender'])` for demographic split<br>- `df['rating'].value_counts().plot(kind='bar')`<br>- `sns.violinplot(x='city', y='income', data=df)`<br>- `plt.hist(df['downloads'], bins=20)`<br>- Plot retention curve of mobile app users<br>- Revenue by time plotted with `lineplot()`                                                                                                  | `sns.histplot()`, `boxplot()`, `violinplot()`, `countplot()`, `kdeplot()`, `plt.hist()`                                                                   |
| 7   | Correlation Analysis       | - Detect relationships<br>- Understand multicollinearity<br>- Before regression analysis<br>- Find trends between variables<br>- Feature selection guidance                          | - `df.corr()` to analyze student scores vs. study hours<br>- `sns.heatmap(df.corr())` for health indicators<br>- `sns.scatterplot(x='price', y='quantity')` in sales data<br>- `pairplot()` for housing data features<br>- Visual correlation between CO2 vs. vehicle age<br>- Map income to loan amounts<br>- Compare crime rate vs. poverty index<br>- Plot rainfall vs. crop yield<br>- Correlate ad spend with web visits<br>- Check pollution vs. traffic volume                                                                                                                                                             | `df.corr()`, `sns.heatmap()`, `sns.scatterplot()`, `pairplot()`                                                                                           |
| 8   | Grouping & Aggregation     | - Summarizing by categories<br>- KPI calculations<br>- Country/region/product wise analysis<br>- Preparing dashboards<br>- Comparing group-wise trends                               | - `groupby('region')['sales'].sum()`<br>- `groupby(['month','product']).mean()` for seasonal trends<br>- `pivot_table()` to summarize education performance<br>- `groupby('hotel')['reviews'].count()`<br>- `groupby('customer_segment')['churn'].mean()`<br>- `groupby('airline')['delay_time'].median()`<br>- `groupby('road_type')['accidents'].sum()`<br>- `df.pivot_table(index='city', values='income')`<br>- Count complaints by category<br>- Aggregate fuel efficiency by manufacturer                                                                                                                                   | `groupby()`, `agg()`, `pivot_table()`, `mean()`, `sum()`                                                                                                  |
| 9   | Feature Engineering        | - Enriching data before ML<br>- Extracting info from text/time<br>- Creating categories/bins<br>- Converting raw inputs<br>- Enhancing models                                        | - `df['year'] = pd.to_datetime(df['date']).dt.year`<br>- `pd.cut(df['age'], bins=[0,18,35,60,100], labels=...)`<br>- Create `stop_count` from flight type<br>- Extract domain: `df['domain'] = df['email'].str.split('@').str[1]`<br>- Calculate duration: `end - start`<br>- Parse hashtags using regex<br>- Encode weekend flags<br>- Bin income ranges<br>- Calculate product_review_score<br>- Word count in review text                                                                                                                                                                                                      | `str.split()`, `str.extract()`, `pd.cut()`, `apply()`, `map()`, `dt.year`, `regex`                                                                        |
| 10  | Hypothesis Generation      | - Before launching statistical tests<br>- When designing experiments<br>- Validate business assumptions<br>- During brainstorming<br>- When performing A/B testing                   | - "Mobile users convert 20% less than desktop"<br>- "Morning deliveries are faster than evening"<br>- "Women purchase more beauty products"<br>- "Discounts increase cart adds"<br>- "New visitors bounce more than returning"<br>- "SEO posts drive more engagement than paid ads"<br>- "Tablet users spend longer per visit"<br>- "Referrals churn less than organic users"<br>- "Premium plans reduce support calls"<br>- "Urban users complain more than rural"                                                                                                                                                               | Use `groupby().mean()` to compare<br>`sns.barplot()` for visual support<br>Confirm using t-tests, chi-squared tests<br>Formulate with logical assumptions |
