## **Exploratory Data Analysis (EDA) Overview**

#### **1. Initial Exploration**

- The course focuses on **Exploratory Data Analysis (EDA)**, a process for understanding data before deeper analysis.
- Instructors: **Izzy** (teaching chapters 1 and 3) and **George** (teaching chapters 2 and 4).

#### **2. Understanding Exploratory Data Analysis (EDA)**

- EDA is about **exploring, cleaning, and reviewing data** to extract meaningful insights.
- Important questions to ask:
  - Is the data **reliable**?
  - What **questions** can it answer?
- Key EDA objectives:
  - Compute **descriptive statistics** (mean, median, standard deviation).
  - Identify **correlations** and patterns.
  - Decide whether the data is useful for analysis, machine learning, or if it needs replacement.

#### **3. Examining the Data with Pandas**

- **Loading Data:** Use `pd.read_csv()` to import data as a DataFrame.
- **First Look:** `.head()` displays the first few rows.
- **Summarizing Data:**
  - `.info()` shows **missing values**, **data types**, and **memory usage**.
  - `.describe()` provides statistics for numerical columns like **mean, min, max, and quartiles**.
  - `.value_counts()` helps count occurrences in categorical columns.

#### **4. Visualizing Numerical Data**

- **Histograms** show distributions of numerical data.
  - Use `sns.histplot(data=books, x="rating")` to visualize book ratings.
  - Adjust bin size using `binwidth=0.1` for better readability.

#### **5. Data Validation**

- **Ensuring data types are correct** (`.dtypes` or `.info()`).
  - Example: Convert `year` from float to integer using `.astype(int)`.
- **Validating categorical data**:
  - Use `.isin()` to check if values belong to expected categories.
  - Use `~` (tilde) operator to invert the check.
  - Apply Boolean indexing to filter valid categories.
- **Validating numerical data**:
  - Use `.min()` and `.max()` to check data range.
  - Boxplots (`sns.boxplot`) help visualize data distribution.

#### **6. Grouping and Summarizing Data**

- **Grouping with `.groupby()`** to analyze subsets of data.
  - Example: `books.groupby("genre")["rating"].mean()` shows average rating by genre.
- **Aggregation with `.agg()`**:
  - Apply multiple functions simultaneously (e.g., mean and standard deviation).
  - Use a dictionary to specify column-wise aggregations.
- **Visualizing grouped data**:
  - **Bar plots** (`sns.barplot`) show the mean and confidence intervals.
  - Helps compare ratings across different genres.

---

### **Key Takeaways**

- **EDA is essential** to understand data structure, quality, and insights before deeper analysis.
- **Pandas methods** like `.head()`, `.info()`, `.describe()`, `.value_counts()`, and `.groupby()` help in quick exploration.
- **Seaborn visualizations** provide meaningful graphical representations of data distributions and summaries.
- **Data validation** ensures accuracy by checking data types, missing values, and value consistency.
- **Grouping and aggregations** help summarize and compare data across categories.

---

## **Handling Missing Data**

### **1. Addressing Missing Data**

- The video introduces the topic of missing data and strategies to handle it.

### **2. Why is Missing Data a Problem?**

- Missing data affects statistical distributions.
- Example: Collecting student heights but missing data from older (taller) students will reduce the sample mean, making the data unrepresentative.
- Incorrect conclusions can be drawn if data isn’t proportionately represented.

### **3. Missing Data in a Data Professionals Dataset**

- Example dataset includes:
  - Year obtained
  - Job title
  - Experience level
  - Type of employment
  - Location
  - Company size
  - Time spent working remotely
  - Salary (in USD)
- Missing data can impact exploratory analysis.

### **4. Salary by Experience Level**

- A comparison of full vs. incomplete datasets shows that missing values can significantly reduce the highest recorded salary by $150,000.

### **5. Checking for Missing Values**

- Use **pandas** methods to check for missing values:
  ```python
  salaries.isna().sum()
  ```
- The output shows all columns have missing values, with **Salary_USD missing 60 values**.

### **6. Strategies for Addressing Missing Data**

- **Dropping data:** If missing values are ≤5%, removing them is acceptable.
- **Imputation:** Replace missing values with a summary statistic (mean, median, or mode).
- **Group-based Imputation:** If salary varies by experience, use different imputation values for different experience levels.

### **7. Dropping Missing Values**

- Compute a missing values threshold:
  ```python
  threshold = len(salaries) * 0.05  # 5% of dataset
  ```

### **8. Dropping Missing Values**

- **Boolean indexing** to identify columns with missing values under threshold:
  ```python
  cols_to_drop = salaries.columns[salaries.isna().sum() <= threshold]
  salaries.dropna(subset=cols_to_drop, inplace=True)
  ```

### **9. Imputing a Summary Statistic**

- Loop through three categorical columns to fill missing values with mode:
  ```python
  for col in categorical_columns:
      salaries[col].fillna(salaries[col].mode()[0], inplace=True)
  ```

### **10. Checking the Remaining Missing Values**

- Missing values in **Salary_USD** reduced from **60 to 41**.
- Some rows had missing values in multiple columns, causing them to be dropped.

### **11. Imputing by Sub-group**

- Compute median salary by experience level and store as a dictionary:
  ```python
  median_salaries = salaries.groupby("Experience")["Salary_USD"].median().to_dict()
  ```

### **12. Imputing by Sub-group**

- Map the median values to fill missing Salary_USD values:
  ```python
  salaries["Salary_USD"].fillna(salaries["Experience"].map(median_salaries), inplace=True)
  ```

### **13. No More Missing Values!**

- Now, there are **no missing values** in the dataset.

---

## **Working with Numeric Data**

### **1. Introduction**

- Focus on numeric data transformation and analysis.

### **2. The Original Salaries Dataset**

- **Salary_USD** column is absent, but **Salary_In_Rupees** is available.
- Salary data needs conversion.

### **3. Converting Strings to Numbers**

- Remove commas:
  ```python
  salaries["Salary_In_Rupees"] = salaries["Salary_In_Rupees"].str.replace(",", "")
  ```
- Convert to **float** and convert currency:
  ```python
  salaries["Salary_USD"] = salaries["Salary_In_Rupees"].astype(float) * 0.012
  ```

### **4. Adding Summary Statistics**

- Use **groupby** to calculate the mean salary by company size:
  ```python
  salaries.groupby("Company_Size")["Salary_USD"].mean()
  ```
- **Standard deviation** by experience level:
  ```python
  salaries["std_dev"] = salaries.groupby("Experience")["Salary_USD"].transform(lambda x: x.std())
  ```

---

## **Converting and Analyzing Categorical Data**

### **1. Introduction**

- Creating and analyzing categorical data.

### **2. Previewing the Data**

- Use **select_dtypes** to filter non-numeric columns:
  ```python
  salaries.select_dtypes(exclude=["number"]).head()
  ```

### **3. Job Titles Analysis**

- Count unique job titles:
  ```python
  salaries["Designation"].nunique()
  ```

### **4. Extracting Value from Categories**

- Use **str.contains** to filter job titles:
  ```python
  salaries[salaries["Designation"].str.contains("Scientist")]
  ```

### **5. Creating a Categorical Column**

- Use **NumPy select** to classify job roles:
  ```python
  import numpy as np
  conditions = [
      salaries["Designation"].str.contains("Data Scientist"),
      salaries["Designation"].str.contains("Analyst")
  ]
  choices = ["Data Scientist", "Analyst"]
  salaries["Job_Category"] = np.select(conditions, choices, default="Other")
  ```

### **6. Visualizing Job Categories**

- Use **Seaborn countplot** to visualize job frequencies:
  ```python
  import seaborn as sns
  import matplotlib.pyplot as plt
  sns.countplot(data=salaries, x="Job_Category")
  plt.show()
  ```

---

## **Handling Outliers - Detailed Notes**

### **1. What is an Outlier?**

- **Definition**: An outlier is an observation that significantly differs from other data points.
- **Example**: In a house price dataset with a median of **$400,000**, a house priced at **$5 million** would be an outlier.
- **Considerations**: Factors like **location, number of bedrooms, and overall size** can impact whether a value is truly an outlier.

---

### **2. Using Descriptive Statistics**

- The **`describe()`** method in Pandas provides summary statistics.
- Example: The **maximum salary** is **4 times greater** than the mean and median, suggesting extreme values.

---

### **3. Using the Interquartile Range (IQR)**

- The **IQR** is the range between the **75th percentile (Q3) and the 25th percentile (Q1)**.
- Formula:
  \[
  IQR = Q3 - Q1
  \]

---

### **4. Box Plots and IQR**

- Box plots **visualize the IQR** and identify outliers.
- The **box** represents the middle 50% of the data.
- **Outliers appear as diamonds** (or dots) outside the box.

---

### **5. Calculating Outlier Thresholds**

- **Upper Outlier Limit**:
  \[
  Q3 + 1.5 \times IQR
  \]
- **Lower Outlier Limit**:
  \[
  Q1 - 1.5 \times IQR
  \]
- Example Calculation:

  ```python
  Q1 = salaries["Salary_USD"].quantile(0.25)
  Q3 = salaries["Salary_USD"].quantile(0.75)
  IQR = Q3 - Q1

  upper_limit = Q3 + 1.5 * IQR
  lower_limit = Q1 - 1.5 * IQR
  print(lower_limit, upper_limit)
  ```

---

### **6. Identifying Outliers**

- **Using thresholds**, we filter outliers:
  ```python
  outliers = salaries[(salaries["Salary_USD"] < lower_limit) |
                      (salaries["Salary_USD"] > upper_limit)]
  ```
- In the dataset:
  - **Lower limit** was below **zero**, which isn't possible for salaries.
  - **Nine individuals** had a **salary above the upper limit**.
  - **None were entry-level**, and **all were based in the US**.

---

### **7. Why Detect Outliers?**

- **Outliers impact statistical analysis**:
  - They can **skew the mean and standard deviation**.
  - Machine learning models often assume a **normal distribution**.
  - Incorrect handling can lead to **biased insights**.

---

### **8. What to Do About Outliers?**

- **Keep or Remove?**
  - **Valid outliers** (e.g., high salaries due to experience) → **Keep**.
  - **Data errors** (e.g., incorrect entries) → **Remove**.

---

### **9. Dropping Outliers**

- **Filter only valid values**:
  ```python
  salaries_no_outliers = salaries[
      (salaries["Salary_USD"] > lower_limit) &
      (salaries["Salary_USD"] < upper_limit)
  ]
  ```
- **Impact**:
  - **Mean salary dropped by $5,000**.
  - **Maximum salary significantly reduced**.

---

### **10. Visualizing the Impact**

- **Original Salary Distribution** (before removing outliers):

  - **Right-skewed** due to **high outliers**.

  ```python
  salaries["Salary_USD"].hist(bins=30)
  ```

- **After Removing Outliers**:
  - **More normally distributed**.
  ```python
  salaries_no_outliers["Salary_USD"].hist(bins=30)
  ```

---

### **Key Takeaways**

✅ **Outliers** are extreme values that differ significantly from others.  
✅ The **IQR method** helps define and detect outliers.  
✅ Outliers **skew statistical metrics**, which can **affect data analysis and models**.  
✅ Outliers should be **analyzed for validity** before deciding to **keep or remove them**.  
✅ **Dropping outliers** can make the data **more normally distributed**.

This ensures a **clean, unbiased dataset** for further analysis! 🚀

## #**Summary**

- **Missing data**: Identified, imputed, or dropped based on thresholds.
- **Numeric data**: Transformed, converted, and enriched with statistics.
- **Categorical data**: Filtered, grouped, and visualized.
- **Outliers**: Detected using IQR and analyzed statistically.

## **Patterns Over Time - Detailed Notes**

### **1. Understanding Patterns Over Time**

- **Definition**: When data includes **dates or time values**, it’s crucial to examine whether **trends or patterns** exist over time.
- **Importance**:
  - Helps in **forecasting and decision-making**.
  - Identifies **seasonality and trends** in the data.

---

### **2. Example Dataset: Divorce Filings in Mexico (2000-2015)**

- **Contains information on**:
  - **Marriage dates**
  - **Marriage duration (in years)**
- **Goal**: Analyze **patterns in marriage and divorce trends** over time.

---

### **3. Importing DateTime Data in Pandas**

- When a **CSV file** is loaded, date and time data are often **interpreted as strings**.
- Example:

  ```python
  import pandas as pd

  df = pd.read_csv("divorce_data.csv")
  print(df.dtypes)  # Marriage_Date is a string (object)
  ```

#### **Fix: Convert to DateTime While Importing**

- Use `parse_dates` in `read_csv()` to convert date columns:
  ```python
  df = pd.read_csv("divorce_data.csv", parse_dates=["Marriage_Date"])
  print(df.dtypes)  # Marriage_Date is now a DateTime object
  ```
- **Benefits of DateTime format**:
  - Enables **date-based analysis** (yearly, monthly, daily trends).
  - Allows **time-based operations** (filtering, sorting, grouping).

---

### **4. Converting String Data to DateTime After Import**

- If the date column was **not converted during import**, we can use:
  ```python
  df["Marriage_Date"] = pd.to_datetime(df["Marriage_Date"])
  ```
- Ensures that **date-related operations** can be performed.

---

### **5. Creating DateTime Data from Separate Columns**

- **Scenario**: Some datasets store **year, month, and day** in separate columns.
- **Solution**: Use `pd.to_datetime()` to combine them into a single DateTime column.
  ```python
  df["Marriage_Date"] = pd.to_datetime(df[["year", "month", "day"]])
  ```
- **Requirement**: Column names **must be "year", "month", and "day"**, but **can appear in any order**.

---

### **6. Extracting Specific Date Components**

- If a dataset already has a **DateTime column**, we can extract specific parts:
  ```python
  df["Marriage_Year"] = df["Marriage_Date"].dt.year
  df["Marriage_Month"] = df["Marriage_Date"].dt.month
  df["Marriage_Day"] = df["Marriage_Date"].dt.day
  ```
- **Why extract date components?**
  - Identify **trends by year or month**.
  - Understand **seasonality effects**.

---

### **7. Visualizing Patterns Over Time**

- **Line plots** help analyze **trends over time**.

#### **Example: Relationship Between Marriage Month & Divorce Duration**

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.lineplot(data=df, x="Marriage_Month", y="Marriage_Duration")
plt.show()
```

- **Interpretation**:
  - The **blue line** represents the **mean marriage duration** per month.
  - The **shaded region** is the **95% confidence interval (CI)**:
    - **Narrow CI** → More confidence in estimates.
    - **Wide CI** → More variability; suggests further analysis is needed.

---

### **8. Key Takeaways**

✅ **Patterns Over Time** reveal **trends, seasonality, and anomalies** in the data.  
✅ **DateTime conversion** is crucial for **effective time-series analysis**.  
✅ **Extracting date components** helps analyze **yearly, monthly, and daily trends**.  
✅ **Line plots in Seaborn** visualize trends, while **confidence intervals indicate reliability**.

This approach ensures **data-driven insights** for better decision-making! 🚀

## **Detailed Notes on Correlation**

### **1. Understanding Correlation**

- **Definition**: Correlation measures **the direction and strength of the relationship** between two variables.
- **Importance**:
  - Helps **identify relationships** in data.
  - Supports **predictive modeling**.
  - Avoids **incorrect assumptions** in data analysis.

---

### **2. Pearson Correlation & Pandas `corr()` Method**

- **Pearson correlation coefficient (r)**:
  - **r = 1** → **Strong positive** correlation (both increase together).
  - **r = -1** → **Strong negative** correlation (one increases, the other decreases).
  - **r = 0** → No correlation (random relationship).
- **Example in Pandas**:

  ```python
  import pandas as pd

  df.corr()  # Returns a correlation matrix
  ```

- **Use case**: Helps quickly identify **which numeric variables are related**.

---

### **3. Correlation Heatmaps**

- **Why use heatmaps?**
  - Adds **color coding** to correlation values.
  - Quickly highlights **strong and weak relationships**.
- **Example using Seaborn**:

  ```python
  import seaborn as sns
  import matplotlib.pyplot as plt

  sns.heatmap(df.corr(), annot=True, cmap="coolwarm")
  plt.show()
  ```

- **Interpretation**:
  - **Deep purple** → Strong **negative** correlation.
  - **Beige** → Strong **positive** correlation.
  - **Neutral colors** → Weak or no correlation.
- **Example Insight**:
  - **Marriage year & marriage duration** have a **strong negative correlation**.
  - Later marriages tend to be **shorter** (likely due to dataset constraints: 2000–2015).

---

### **4. Correlation in Context**

- **Key point**: Correlation **must be interpreted within its context**.
- Example:
  - The dataset only includes **marriages that ended between 2000-2015**.
  - Naturally, **earlier marriages** will have **longer durations**.
  - This **doesn’t mean** marriages are getting shorter **in general**.

---

### **5. Limitations of Pearson Correlation**

- **Pearson correlation only captures **linear** relationships**.
- **Problems**:
  - **Strong non-linear relationships** might show **r ≈ 0** (incorrectly suggesting no correlation).
  - **Quadratic relationships** might appear **linear** but require different modeling.
- **Solution**: Always **visualize relationships** using scatter plots!

---

### **6. Scatter Plots**

- **Why use scatter plots?**
  - Helps confirm whether a **correlation coefficient** correctly represents a relationship.
- **Example: Checking Monthly Income Relationship**
  ```python
  sns.scatterplot(data=df, x="Female_Income", y="Male_Income")
  plt.show()
  ```
- **Key Observation**:
  - The heatmap showed **0.32 correlation** between **male and female incomes** at the time of divorce.
  - The scatter plot **confirms a weak positive relationship**.

---

### **7. Pairplots for Deeper Analysis**

- **What is a pairplot?**
  - A Seaborn visualization that plots **all pairwise relationships** between numerical variables.
  - **Diagonal plots** show the **distribution** of each variable.
- **Example**:
  ```python
  sns.pairplot(df)
  plt.show()
  ```
- **Issues**:
  - **Difficult to interpret with large datasets**.
  - Small labels make analysis harder.

---

### **8. Refining Pairplots for Clarity**

- **Solution**: Limit variables using the `vars` argument.
  ```python
  sns.pairplot(df, vars=["Male_Income", "Female_Income", "Marriage_Duration"])
  plt.show()
  ```
- **Key Observations**:
  - Income variables and marriage duration **have weak correlations**.
  - **Marriage duration distribution** is **right-skewed** (more short marriages, fewer long ones).

---

### **9. Key Takeaways**

✅ **Correlation measures strength & direction of relationships**.  
✅ **Heatmaps provide a quick visual representation**.  
✅ **Scatter plots confirm relationships and detect non-linearity**.  
✅ **Pairplots help explore multiple relationships simultaneously**.  
✅ **Always interpret correlation within the dataset context**.

Using these techniques ensures **accurate and meaningful** data insights! 🚀

## **Factor Relationships and Distributions – Detailed Notes**

### **1. Understanding Categorical Variable Relationships**

- Unlike numerical variables, categorical variables (**factors**) have **distinct categories**.
- **Example**: Education level, occupation, marital status, etc.
- **Goal**: Identify **patterns** in categorical data and how they relate to numerical variables (e.g., marriage duration).

---

### **2. Analyzing Education Level of Male Partners**

- Use `.value_counts()` to check category distribution.
  ```python
  df["education_man"].value_counts()
  ```
- **Observation**:
  - Most male partners have an education level between **Primary and Professional**.
  - Fewer men fall into **"None"** or **"Other"** categories.

---

### **3. Exploring Categorical Relationships using Visualizations**

- Since categorical variables **aren’t easily summarized numerically**, visualization is key.
- **Example Analysis**: Relationship between **Marriage Duration** & **Male Partner’s Education**.

#### **Histogram for Marriage Duration**

- Helps visualize the **distribution** of marriage duration.

  ```python
  import seaborn as sns
  import matplotlib.pyplot as plt

  sns.histplot(data=df, x="marriage_duration", hue="education_man", multiple="stack")
  plt.show()
  ```

- **Issue**:
  - Categories are **stacked**, making it hard to analyze individual education levels.

---

### **4. Kernel Density Estimate (KDE) Plots – Smoother Distributions**

- KDE plots **improve interpretability** compared to histograms.
- Example KDE Plot:
  ```python
  sns.kdeplot(data=df, x="marriage_duration", hue="education_man")
  plt.show()
  ```
- **Benefits**:
  - **Clear peaks** for each education level.
  - Shows **how marriage duration varies** across education categories.
- **Downside**:
  - KDE plots use **smoothing**, which can introduce **impossible values** (e.g., negative marriage duration).

---

### **5. Fixing KDE Plot Issues (Impossible Values)**

- **Example Problem**: The curve might extend beyond valid values (e.g., showing negative marriage durations).
- **Solution**: Use `cut=0` to **restrict KDE to valid ranges**.
  ```python
  sns.kdeplot(data=df, x="marriage_duration", hue="education_man", cut=0)
  plt.show()
  ```
- **Impact**:
  - KDE plot now only **includes real values**.
  - Ensures **accurate interpretation** of distributions.

---

### **6. Cumulative KDE Plots – Understanding Probabilities**

- **What is a Cumulative KDE Plot?**
  - Shows **the probability** that marriage duration is **≤ a given value** for each education level.
- **Use Case**: Helps compare **which education groups** tend to have shorter/longer marriages.
  ```python
  sns.kdeplot(data=df, x="marriage_duration", hue="education_man", cumulative=True)
  plt.show()
  ```
- **Key Insight**:
  - Helps **compare trends** across categories.
  - Example: If **higher-educated partners** have a slower-rising cumulative curve, it suggests **longer marriages**.

---

### **7. Relationship Between Marriage Age & Education**

- **Question**: Do people with higher education levels **get married later**?
- **Calculate Approximate Marriage Age**:
  ```python
  df["marriage_age_man"] = df["marriage_year"] - df["birth_year_man"]
  df["marriage_age_woman"] = df["marriage_year"] - df["birth_year_woman"]
  ```
- **Scatter Plot for Marriage Age**
  ```python
  sns.scatterplot(data=df, x="marriage_age_man", y="marriage_age_woman")
  plt.show()
  ```
- **Observation**:
  - A **positive correlation (r = 0.69)** suggests that older couples tend to marry older partners.

---

### **8. Scatter Plot with Categorical Variables (Introducing Education Level)**

- **Problem**: Scatter plots **require numerical values** on X and Y axes.
- **Solution**: Use the `hue` argument to **color points based on education level**.
  ```python
  sns.scatterplot(
      data=df,
      x="marriage_age_man",
      y="marriage_age_woman",
      hue="education_man"
  )
  plt.show()
  ```
- **Key Insights**:
  - **Orange dots (Professional Education)** suggest men with higher education **tend to marry later**.
  - **Lower education levels** might be associated with **earlier marriages**.

---

### **9. Key Takeaways**

✅ **Categorical variables require visual analysis** (histograms, KDE, scatter plots).  
✅ **KDE plots help show distributions smoothly but need `cut=0` to avoid errors**.  
✅ **Cumulative KDE plots reveal probabilities for comparisons**.  
✅ **Marriage age & education are correlated** – higher education may lead to later marriages.  
✅ **Using `hue` in scatter plots helps introduce categorical variables in numeric relationships**.

This approach ensures **accurate insights from categorical data**! 🚀

## **Considerations for Categorical Data – Detailed Notes**

### **1. Importance of Categorical Data in EDA**

- **Exploratory Data Analysis (EDA)** helps detect **patterns and relationships** in data.
- It is used to:
  - Generate **questions or hypotheses**.
  - Prepare data for **machine learning models**.
  - Identify **biases or inconsistencies** in the dataset.

---

### **2. Ensuring Data is Representative**

- Data must **accurately reflect the population** being studied.
- **Example**: If studying **education vs. income** in the **USA**, the dataset should only contain **USA residents**.
- **Common Issues**:
  - Data collected from **a different region** (e.g., France instead of the USA).
  - Overrepresentation of **certain groups**, leading to bias.

---

### **3. Understanding Categorical Classes**

- Categorical data is grouped into **classes (labels)**.
- **Example**:
  - If studying **attitudes toward marriage**, marital status can have **three classes**:
    - **Single**
    - **Married**
    - **Divorced**

---

### **4. Class Imbalance – A Common Issue**

- If one class appears **far more frequently**, results may be **skewed**.
- **Example**:
  - Data on **marital status**:
    - **Married: 50**
    - **Divorced: 700**
    - **Single: 250**
  - **Problem**:
    - **Divorced individuals** might **dominate responses**, leading to **biased conclusions**.
    - The dataset may **not reflect** real-world proportions.

---

### **5. Measuring Class Frequency**

- Use **`value_counts()`** to count how often each category appears.
  ```python
  df["marital_status"].value_counts()
  ```
- **Example**: Counting flight destinations:
  ```python
  df["destination"].value_counts()
  ```

---

### **6. Relative Class Frequency – Normalized Counts**

- To **compare distributions**, use **relative frequencies** instead of absolute counts.
- **Example**:
  - **40% of Indian flights go to Delhi**, but in our dataset, Delhi represents only **11.82%**.
  - This suggests our dataset is **not representative**.
- **Implementation**:
  ```python
  df["destination"].value_counts(normalize=True)
  ```
- **Benefit**: Helps **identify underrepresented or overrepresented** categories.

---

### **7. Cross-Tabulation – Analyzing Class Combinations**

- Used to **examine relationships** between two categorical variables.
- Example: Counting **flight routes (Source → Destination)**.

  ```python
  import pandas as pd

  pd.crosstab(df["Source"], df["Destination"])
  ```

- **Output**:
  ```
  Destination   Delhi  Mumbai  Bangalore
  Source
  Kolkata       4318    2500      1800
  Chennai       3000    2100      1600
  ```
- **Interpretation**:
  - The most common route is **Delhi → Cochin (4318 flights)**.

---

### **8. Enhancing Cross-Tabulation – Aggregating Values**

- Cross-tabulation can **summarize numeric values** (e.g., ticket prices).
- **Example**: Finding the **median price** for each flight route.
  ```python
  pd.crosstab(df["Source"], df["Destination"], values=df["Price"], aggfunc="median")
  ```
- **Use Case**:
  - Compare actual prices with **expected median prices** for flights.

---

### **9. Comparing Sample Data to Population Trends**

- If our dataset **overestimates or underestimates** values, it may be **unrepresentative**.
- Example:
  - **Flights from Bangalore to Delhi are more expensive** in our dataset than expected.
  - **Possible Issues**:
    - Sample was collected during **peak season**.
    - Data focuses on **business-class tickets** instead of **economy**.

---

### **10. Key Takeaways**

✅ **Ensure data is representative** of the target population.  
✅ **Identify class imbalances** to avoid biased conclusions.  
✅ Use **`value_counts()`** to check distributions.  
✅ Use **`crosstab()`** to analyze relationships between categorical variables.  
✅ Compare **sample data** with expected population trends to detect inconsistencies.

By following these steps, we can **transform EDA insights into meaningful actions**! 🚀

## **Feature Engineering – Creating New Insights from Data**

### **1. Why Generate New Features?**

- The format of raw data may **limit insights** or **reduce model performance**.
- **Feature Engineering** helps:
  - Reveal **hidden relationships** in data.
  - Improve **machine learning models**.

---

### **2. Identifying Relationships Using Correlation**

- Using a **correlation heatmap**, we see:
  - **Moderate positive correlation** between **Price and Duration**.
  - These are the **only numeric features** initially available.

---

### **3. Viewing Data Types for Hidden Numeric Features**

- Checking `df.dtypes`, we notice:
  - `Total_Stops` **should** be numeric.
  - Currently stored as a **string** with unnecessary text.

---

### **4. Converting `Total_Stops` to Numeric**

- **Issue**: It contains words like `"non-stop"` and `"2 stops"`.
- **Solution**:
  - Remove `" stops"` using `.str.replace()`.
  - Convert `"non-stop"` to `0`.
  - Convert the column to `int`.
  ```python
  df["Total_Stops"] = df["Total_Stops"].str.replace(" stops", "").replace("non-stop", "0").astype(int)
  ```
- **Result**: `Total_Stops` is now a numeric feature.

---

### **5. Checking Correlation Again**

- `Total_Stops` shows:
  - **Strong correlation** with `Duration`.
  - **Higher correlation** with `Price` than `Duration` does!
  - This indicates that **more stops generally mean higher ticket prices**.

---

### **6. Extracting Useful Information from Dates**

- **Datetime Features in Dataset**:
  - `Date_of_Journey`
  - `Dep_Time`
  - `Arrival_Time`
- **Feature Engineering Ideas**:
  - Extract **Month** from `Date_of_Journey`.
  - Extract **Day of the Week** (`Monday = 0`, `Sunday = 6`).
  ```python
  df["Journey_Month"] = df["Date_of_Journey"].dt.month
  df["Journey_Weekday"] = df["Date_of_Journey"].dt.weekday
  ```
- **Why?**
  - **Prices may vary per month/day** (e.g., higher during holidays).
  - **More demand on weekends** might mean **higher ticket prices**.

---

### **7. Extracting Departure & Arrival Hours**

- **Hypothesis**:
  - **Convenient flight times (morning/evening)** might be **more expensive**.
- **New Features**:
  ```python
  df["Dep_Hour"] = df["Dep_Time"].dt.hour
  df["Arrival_Hour"] = df["Arrival_Time"].dt.hour
  ```
- **Next Step**: Check correlation between these time-based features and `Price`.
  - **Result**: No strong new relationships found, but we wouldn’t have known without testing!

---

### **8. Creating Categorical Features from Numeric Data**

- **Grouping Prices into Ticket Categories**
- **Why?**
  - No existing column for **ticket class**.
  - We can **bin** `Price` into categories like:
    - **Economy**
    - **Premium Economy**
    - **Business Class**
    - **First Class**

---

### **9. Calculating Quartiles for Binning**

- **Quartiles define category boundaries**:
  ```python
  q1 = df["Price"].quantile(0.25)  # 25th percentile
  q2 = df["Price"].median()        # 50th percentile (median)
  q3 = df["Price"].quantile(0.75)  # 75th percentile
  q4 = df["Price"].max()           # Maximum price
  ```
- **Defining Bins**:
  ```python
  bins = [0, q1, q2, q3, q4]
  labels = ["Economy", "Premium Economy", "Business", "First Class"]
  ```

---

### **10. Applying `pd.cut()` to Assign Categories**

- **Using `pd.cut()` to label flights based on Price**:
  ```python
  df["Price_Category"] = pd.cut(df["Price"], bins=bins, labels=labels)
  ```
- **Result**: Each flight now has a **ticket class category**.

---

### **11. Visualizing Price Categories per Airline**

- **Plot count of ticket categories per airline** using Seaborn:

  ```python
  import seaborn as sns
  import matplotlib.pyplot as plt

  plt.figure(figsize=(10, 5))
  sns.countplot(x="Airline", hue="Price_Category", data=df)
  plt.xticks(rotation=45)
  plt.show()
  ```

- **Key Insights**:
  - **Jet Airways** has the most **First-Class** tickets.
  - **IndiGo and SpiceJet** mostly operate **Economy** flights.

---

### **12. Key Takeaways**

✅ **Feature engineering enhances data insights**.  
✅ Converting text-based data (e.g., `Total_Stops`) into **numeric features** improves analysis.  
✅ Extracting **date-based attributes** (month, weekday, hours) helps uncover **time-related trends**.  
✅ **Categorizing prices into classes** allows for **better grouping and comparison**.  
✅ **Visualization of ticket categories per airline** helps understand airline pricing strategies.

By applying these techniques, we **unlock new insights** and **improve model performance**! 🚀

## **Understanding Hypothesis Generation in Data Science**

### **1. Why Generate Hypotheses?**

- **Data scientists** use hypothesis generation to **validate observations** and **test assumptions**.
- It helps differentiate between **real relationships** and **coincidental patterns** in data.

---

### **2. What Do We Know So Far?**

- **Key insights from our dataset**:
  - **Jet Airways has a large proportion of expensive (First-Class) tickets**.
  - **Price, Duration, and Total_Stops show moderate correlation**.
  - **No other strong numerical relationships exist**.

---

### **3. Identifying Spurious Correlation**

- **Example:**
  - **Price vs. Duration scatter plot** (factoring in `Total_Stops`) shows:
    - `Total_Stops` **depends more on Duration than Price**.
    - **Misleading correlation**: We might falsely conclude `Total_Stops` influences `Price`, when in reality, it primarily maps to `Duration`.
- **Solution:**
  - Always **analyze multiple variables together** before making conclusions.

---

### **4. Checking Correlation for Different Stop Counts**

- **Finding**:
  - **Zero-stop flights show strong negative correlation** with `Price`.
  - **Three- and four-stop flights show no meaningful correlation**.
- **Implication**:
  - **Shorter flights (non-stop) tend to be cheaper**, but **additional stops don't necessarily increase price consistently**.

---

### **5. The Need for Hypothesis Testing**

- **Key Question**: **How do we confirm if an observation is universally true?**
- **Example Concern**:
  - If we collected **new flight data** from a different time period, would we observe the **same patterns**?
- **Solution**: Use **Hypothesis Testing**:
  - **Form a hypothesis** (e.g., "Jet Airways flights are longer on average than SpiceJet").
  - **Specify a statistical test** (e.g., t-test, ANOVA) before collecting data.
  - **Test the hypothesis with a structured approach** instead of relying solely on visual EDA.

---

### **6. Avoiding Data Snooping (p-Hacking)**

- **What is Data Snooping?**
  - Running **multiple tests** on the same dataset to **force significant results**.
  - Looking at data **without a predefined question** leads to **biased conclusions**.
- **Why is this a problem?**
  - If you run **enough tests**, some patterns will appear **just by chance**.
  - **Example**: Checking prices across multiple cities until one appears to be significantly higher (even if it’s not a real trend).
- **Solution:**
  - **Predefine hypotheses before analysis**.
  - Use **correct statistical methods** (adjusting for multiple tests).

---

### **7. Generating Hypotheses from EDA**

- **Hypothesis Example 1**:

  - **"Jet Airways flights last longer than SpiceJet flights."**
  - **Check:** Create a **bar plot** of **mean Duration per Airline**.

  ```python
  import seaborn as sns
  import matplotlib.pyplot as plt

  plt.figure(figsize=(8,5))
  sns.barplot(x="Airline", y="Duration", data=df)
  plt.xticks(rotation=45)
  plt.show()
  ```

- **Hypothesis Example 2**:
  - **"Flights to New Delhi are more expensive than other destinations."**
  - **Check:** Use a **box plot** to compare prices across destinations.
  ```python
  plt.figure(figsize=(10,5))
  sns.boxplot(x="Destination", y="Price", data=df)
  plt.xticks(rotation=45)
  plt.show()
  ```

---

### **8. Next Steps in Hypothesis Testing**

- **Once we have a hypothesis, we must design a structured test**:
  1. **Select a sample** (random selection to avoid bias).
  2. **Determine sample size** (large enough for statistical power).
  3. **Choose a statistical test** (t-test, ANOVA, chi-square, etc.).
  4. **Interpret results** (p-value, confidence intervals).

**Although full hypothesis testing is beyond this course, EDA gives us a strong foundation to ask the right questions!** 🚀
