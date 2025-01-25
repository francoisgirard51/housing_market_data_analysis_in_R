# Housing Price Analysis (in R)

This project walks through a data analysis of a housing dataset, focusing on understanding the relationships between various features and the **Median.Home.Value**.

---

## Table of Contents
1. [Introduction](#introduction)  
2. [Data Overview](#data-overview)  
3. [Data Cleaning and Preprocessing](#data-cleaning-and-preprocessing)  
4. [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)  
5. [Correlation Analysis](#correlation-analysis)  
6. [Handling Missing Values](#handling-missing-values)  
7. [Data Visualization](#data-visualization)  
8. [Hypothesis Testing (Linear Regression)](#hypothesis-testing-linear-regression)  
9. [Conclusion](#conclusion)

---

## Introduction
The goal of this project is to analyze housing data and identify the most influential factors affecting the **Median.Home.Value**. The steps include data loading, exploratory data analysis (EDA), missing value imputation, correlation analysis, visualization, and a simple linear regression model to test the hypothesis that **Average.Rooms** is a significant predictor of **Median.Home.Value**.

---

## Data Overview
- **Observations/rows**: 506  
- **Variables**:
  1. **Crime.Rate**: Local crime rate per capita  
  2. **Average.Rooms**: Average number of rooms in homes  
  3. **Public.Transport.Access**: Proximity to public transport (1–10)  
  4. **Number.of.Schools**: Number of local schools  
  5. **Median.Home.Value**: Median value of homes (target variable)

A quick statistical summary and structure check revealed:
- Missing values in **Crime.Rate** (25) and **Average.Rooms** (15).
- A high correlation (0.89) between **Average.Rooms** and **Median.Home.Value**.
- Relatively weak correlations for the other variables with the target.

---

## Data Cleaning and Preprocessing
1. **Load the Data**  
   ```r
   housing_data <- read.csv("housing_data.csv")
   head(housing_data)
   str(housing_data)
   ```
2. **Overview and Summary**  
   ```r
   summary(housing_data)
   ```
   This revealed the presence of missing values and provided initial descriptive statistics for each variable.

---

## Exploratory Data Analysis
- **Statistical Summaries**:  
  We calculated **mean**, **median**, and **mode** for each variable to gain insights into their central tendencies and distributions.
- **Findings**:  
  - **Crime.Rate** has a mean of ~3.14 and median of ~3.03.  
  - **Average.Rooms** has a mean of ~6.03 and median of ~6.03.  
  - **Median.Home.Value** has a mean of ~47.10 and median of ~46.91.  

These statistics suggested that **Average.Rooms** aligns closely around its mean and median, whereas **Crime.Rate** might be slightly skewed, indicated by its mode differing from mean/median.

---

## Correlation Analysis
We used a correlation matrix to understand how the variables relate to each other:

```r
correlation_matrix <- cor(housing_data, use = "pairwise.complete.obs")
print(correlation_matrix)
```

We note that:
- **Average.Rooms** has the strongest correlation (0.89) with **Median.Home.Value**.  
- **Crime.Rate**, **Public.Transport.Access**, and **Number.of.Schools** exhibit weaker relationships with **Median.Home.Value**.

---

## Handling Missing Values
We replaced missing values with the **median** of their respective columns:

```r
housing_data <- housing_data %>%
  mutate(
    Crime.Rate = ifelse(is.na(Crime.Rate), median(Crime.Rate, na.rm = TRUE), Crime.Rate),
    Average.Rooms = ifelse(is.na(Average.Rooms), median(Average.Rooms, na.rm = TRUE), Average.Rooms),
    Public.Transport.Access = ifelse(is.na(Public.Transport.Access), median(Public.Transport.Access, na.rm = TRUE), Public.Transport.Access),
    Number.of.Schools = ifelse(is.na(Number.of.Schools), median(Number.of.Schools, na.rm = TRUE), Number.of.Schools),
    Median.Home.Value = ifelse(is.na(Median.Home.Value), median(Median.Home.Value, na.rm = TRUE), Median.Home.Value)
  )
```

After this step, there are no missing values left in the dataset.

---

## Data Visualization
We plotted histograms and scatter plots to visualize the distribution and relationships of variables. For instance:

```r
ggplot(housing_data, aes(x = Median.Home.Value)) +
  geom_histogram(binwidth = 5, fill = "blue", color = "black") +
  geom_vline(aes(xintercept = mean(Median.Home.Value, na.rm = TRUE)), 
             color = "red", linetype = "dashed") +
  labs(title = "Distribution of Median Home Values",
       x = "Median Home Value",
       y = "Frequency") +
  theme_minimal()
```

- The **Median.Home.Value** distribution appeared relatively balanced, with a dashed line indicating the mean.  

---

## Hypothesis Testing (Linear Regression)
A simple linear regression was performed to test whether **Average.Rooms** significantly predicts **Median.Home.Value**:

```r
linear_model <- lm(Median.Home.Value ~ Average.Rooms, data = housing_data)
summary(linear_model)
```

- **Coefficient**: ~7.19  
  Each additional room corresponds to a ~7.19 unit increase in the median home value.  
- **p-value**: < 2e-16  
  Highly significant predictor.  
- **R-squared**: ~0.76  
  This model explains ~76% of the variance in median home values.

The regression line plot (with `geom_smooth`) visually confirms the strong positive relationship between **Average.Rooms** and **Median.Home.Value**.

---

## Conclusion
1. **Data Quality**: Missing values in **Crime.Rate** and **Average.Rooms** were successfully handled via median imputation.  
2. **Key Insights**: **Average.Rooms** emerges as the most influential variable for predicting **Median.Home.Value**, showing a strong linear relationship.  
3. **Implications**: While this model focuses on **Average.Rooms**, other variables (e.g., **Crime.Rate**, **Public.Transport.Access**, **Number.of.Schools**) appeared less predictive of home values in this dataset.  
4. **Next Steps**: Incorporating additional features or conducting a multiple regression could offer a deeper understanding of the factors influencing housing prices.

The analysis demonstrates that the average number of rooms is a significant and robust predictor of median home value in this dataset, though real-world housing markets typically involve many more nuanced factors.
