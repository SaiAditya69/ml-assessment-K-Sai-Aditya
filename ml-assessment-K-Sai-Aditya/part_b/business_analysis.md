## B1. Problem Formulation
### (a) Machine Learning Formulation

This can be formulated as a supervised learning regression problem.

Target variable:
items_sold — the number of items sold at a store in a given month.
Input features:
Promotion type (Flat Discount, BOGO, etc.)
Store characteristics (store size, location type)
Customer behavior indicators (footfall, basket size if available)
External factors (competition density)
Temporal features (month, weekend, festival indicators)
Type of problem:
Regression, because the target variable is continuous.

Justification:
The objective is to predict a numerical outcome (items sold) based on multiple influencing factors, which makes regression the most appropriate approach.

### (b) Why items_sold is better than revenue

Revenue can be misleading because it is influenced by pricing changes, discounts, and promotion types. For example, a heavy discount may increase items sold but reduce total revenue.

In contrast, items sold directly reflects customer demand and response to promotions, making it a more reliable measure of promotion effectiveness.

Broader principle:
The target variable in a machine learning problem should closely align with the actual business objective. Choosing the wrong target can lead to models that optimise the wrong outcome.

### (c) Alternative to a single global model

Using a single global model may ignore important differences between stores in different locations.

A better approach would be:

Segment-based modelling, where stores are grouped (e.g., urban vs rural) and separate models are trained
or
A single model with interaction features (e.g., promotion × location)

Justification:
Different stores respond differently to the same promotion due to customer demographics and local competition. Capturing this variation improves prediction accuracy and relevance.

## B2. Data and EDA Strategy
### (a) Data Joining and Dataset Design

The four tables would be combined using common keys:

store_id → to join store attributes
transaction_date → to join calendar data
promotion identifiers → to join promotion details

Final dataset grain:
One row per store per month

Aggregations before modelling:

Total items_sold per store per month
Average basket size
Total number of transactions
Promotion applied during that period

This ensures the dataset is aligned with the business decision level (monthly store-level decisions).

This ensures that the dataset matches the decision-making level of the business (monthly store-level promotion planning).

### (b) Exploratory Data Analysis (EDA)

Before modelling, the following analyses would be performed:

Distribution of items_sold
To check skewness and outliers
Helps decide if transformation is needed
Promotion type vs items sold (bar plot)
To compare effectiveness of different promotions
Helps identify strong or weak promotions
Time series trends (monthly sales over time)
To detect seasonality and trends
Helps in feature engineering (month, festivals)
Correlation heatmap
To understand relationships between variables
Helps identify important predictors and remove redundant features

These insights guide feature selection and model choice.

### (c) Handling imbalance (80% no promotion)

If most data has no promotion, the model may learn to ignore promotion effects entirely.

To address this:

Create a promotion indicator feature
Ensure evaluation includes promotion-specific performance
Consider balanced sampling or weighting

This ensures the model properly learns the impact of promotions.

## B3. Model Evaluation and Deployment
### (a) Train-Test Split and Metrics

The data should be split based on time, not randomly.

Train on earlier months (e.g., first 80%)
Test on recent months (last 20%)

Why not random split?
A random split mixes past and future data, leading to data leakage and unrealistic performance estimates.

Evaluation metrics:

RMSE (Root Mean Squared Error): penalises large prediction errors
MAE (Mean Absolute Error): gives average error magnitude

Interpretation:
Lower values indicate better predictions. RMSE is useful when large errors are costly, while MAE is easier to interpret in business terms.

### (b) Explaining different recommendations

Feature importance helps identify which variables influenced the model’s decision.

For example:

In December, features like festival season or high demand may increase the effectiveness of loyalty-based promotions
In March, factors like lower demand or competition may make discounts more effective

To communicate this:

Show feature importance rankings
Highlight how key factors differ across months

This helps the marketing team understand that recommendations are driven by changing conditions, not randomness.

This explanation can be supported using feature importance plots or SHAP values to visually demonstrate the impact of each feature.

### (c) Deployment and Monitoring

Deployment process:

Train the model and save it using joblib or pickle
Each month, collect new data (store info, promotions, calendar features)
Apply the same preprocessing pipeline
Generate predictions for each store

Monitoring:

Track RMSE/MAE over time
Monitor changes in input data (data drift)
Compare predicted vs actual sales

Retraining trigger:

Significant drop in performance
Change in business patterns (e.g., new promotion types)

This ensures the model remains accurate and reliable over time.