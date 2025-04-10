### **Comprehensive Summary of EDA Topics with Commands**

---

### **1. Initial Data Exploration**

**Objective**: Load and inspect data structure.  
**Commands**:

```python
import pandas as pd
df = pd.read_csv("data.csv")  # Load data
df.head()                     # First 5 rows
df.info()                     # Data types & missing values
df.describe()                 # Summary stats (numerical cols)
df.value_counts()             # Count unique values (categorical)
```

---

### **2. Handling Missing Data**

**Objective**: Identify and address missing values.  
**Commands**:

```python
df.isna().sum()               # Count missing values per column
df.dropna(thresh=len(df)*0.95, axis=1)  # Drop cols with >5% missing
df.fillna(df.mode()[0])       # Impute categorical with mode
df.fillna(df.median())        # Impute numerical with median
# Group-based imputation (e.g., fill salary by experience level):
median_salaries = df.groupby("Experience")["Salary"].median().to_dict()
df["Salary"] = df["Salary"].fillna(df["Experience"].map(median_salaries))
```

---

### **3. Numeric Data Analysis**

**Objective**: Transform and analyze numerical features.  
**Commands**:

```python
# Convert strings to numbers (e.g., "$1,000" → 1000):
df["Price"] = df["Price"].str.replace("$", "").str.replace(",", "").astype(float)
# Add summary stats (e.g., std dev by group):
df["std_dev"] = df.groupby("Category")["Value"].transform("std")
# Binning numeric data (e.g., price categories):
bins = [0, 25, 50, 75, 100]
labels = ["Low", "Medium", "High", "Premium"]
df["Price_Category"] = pd.cut(df["Price"], bins=bins, labels=labels)
```

---

### **4. Categorical Data Analysis**

**Objective**: Filter, group, and visualize categories.  
**Commands**:

```python
# Filter job titles containing "Data Scientist":
df[df["Job"].str.contains("Data Scientist")]
# Create a new categorical column:
conditions = [df["Job"].str.contains("Scientist"), df["Job"].str.contains("Analyst")]
choices = ["Data Scientist", "Analyst"]
df["Role"] = np.select(conditions, choices, default="Other")
# Visualize categories:
sns.countplot(data=df, x="Role")  # Frequency plot
```

---

### **5. Outlier Detection**

**Objective**: Identify and handle extreme values.  
**Commands**:

```python
# IQR Method:
Q1 = df["Salary"].quantile(0.25)
Q3 = df["Salary"].quantile(0.75)
IQR = Q3 - Q1
lower, upper = Q1 - 1.5*IQR, Q3 + 1.5*IQR
outliers = df[(df["Salary"] < lower) | (df["Salary"] > upper)]
# Remove outliers:
df_clean = df[(df["Salary"] >= lower) & (df["Salary"] <= upper)]
# Visualize with a boxplot:
sns.boxplot(data=df, x="Salary")
```

---

### **6. Time-Series Analysis**

**Objective**: Extract trends from datetime data.  
**Commands**:

```python
# Convert to datetime:
df["Date"] = pd.to_datetime(df["Date"])
# Extract components:
df["Year"] = df["Date"].dt.year
df["Month"] = df["Date"].dt.month
df["Weekday"] = df["Date"].dt.weekday  # Monday=0, Sunday=6
# Plot trends over time:
sns.lineplot(data=df, x="Month", y="Sales")
```

---

### **7. Correlation Analysis**

**Objective**: Measure relationships between variables.  
**Commands**:

```python
# Correlation matrix:
df.corr()
# Heatmap:
sns.heatmap(df.corr(), annot=True, cmap="coolwarm")
# Scatter plot (check non-linear relationships):
sns.scatterplot(data=df, x="Price", y="Demand")
# Pairplot (all numerical relationships):
sns.pairplot(df, vars=["Price", "Demand", "Income"])
```

---

### **8. Feature Engineering**

**Objective**: Create new features for better insights.  
**Commands**:

```python
# Extract flight stops (e.g., "2 stops" → 2):
df["Stops"] = df["Stops"].str.replace(" stops", "").replace("non-stop", "0").astype(int)
# Extract hour from time:
df["Departure_Hour"] = pd.to_datetime(df["Dep_Time"]).dt.hour
# Binning (e.g., age groups):
df["Age_Group"] = pd.cut(df["Age"], bins=[0, 18, 35, 60, 100], labels=["Child", "Young", "Adult", "Senior"])
```

---

### **9. Hypothesis Generation**

**Objective**: Formulate testable questions from EDA.  
**Commands**:

```python
# Example hypothesis: "Flights with more stops are cheaper."
sns.boxplot(data=df, x="Stops", y="Price")  # Visual check
# Statistical test (t-test):
from scipy.stats import ttest_ind
group1 = df[df["Stops"] == 0]["Price"]
group2 = df[df["Stops"] == 1]["Price"]
ttest_ind(group1, group2)  # Check p-value
```

---

### **Key Takeaways**

- **EDA**: Use `.head()`, `.info()`, `.describe()` for quick insights.
- **Cleaning**: Handle missing data with `dropna()`/`fillna()` and outliers with IQR.
- **Visualization**: Leverage `sns.histplot()`, `sns.boxplot()`, `sns.heatmap()`.
- **Feature Engineering**: Create new features (e.g., bins, time splits) with `pd.cut()` and `dt` accessor.
- **Hypotheses**: Validate observations statistically to avoid bias.
