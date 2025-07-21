#  SyriaTel Customer Churn Analysis & Prediction

**Author:** Rose Miriti

---
## Business Understanding

### Business Problem
Customer churn directly impacts SyriaTel’s revenue by reducing the Average Revenue Per User (ARPU) and incurring additional customer acquisition costs. In a competitive market with low barriers to switching, even a small increase in churn can substantially affect profitability. This project treats churn as a binary classification problem to identify at-risk subscribers early, allowing retention teams to intervene with targeted offers or service adjustments before a customer cancels their service.

### Stakeholders   
- **Customer Retention Team:** needs to know which customers to target with retention offers  
- **Marketing Department:** wants to allocate budget efficiently on high-risk segments  
- **Finance & Executive Leadership:** cares about the financial impact of reducing churn ie monitors ROI of retention efforts and P&L impact  

### Specific Objectives  
1. Load and clean the SyriaTel churn dataset, ensuring all key fields (tenure, billing, service calls, geography, etc.) are ready for analysis.  
2. Explore and visualize relationships between customer attributes and churn to uncover the strongest risk factors.  
3. Engineer new features such as support call counts, usage ratios, tenure buckets, and region level flags to boost model signal.  
4. Build and compare classification models (logistic regression, random forest, gradient boosting) to find the best predictor of churn.  

### Research Questions  
1. Does a high volume of calls or tickets correlate with higher churn risk?  
2. How does actual usage (minutes, texts, data) versus plan allowance predict churn?  
3. Are there specific regions or ZIP codes where churn is significantly above average?  
4. How do contract length and tenure buckets (e.g. 0–6 mo, 6–12 mo, > 12 mo) interact to affect churn probability?  
5. Can we derive other early warning features (e.g. months since last plan change, add-on uptake, billing anomalies)?  

### Success Metrics
* **Recall:** Achieve a recall of at least 80% for the churn class to capture the majority of customers who are likely to leave.
* **ROC-AUC:** Obtain a ROC-AUC score of at least 0.75, indicating a strong ability to distinguish between churners and non-churners.
* **Business Impact:** The model's predictions should aim to contribute to a 5% net reduction in customer churn over a six-month period.

---

## Data Understanding and Preparation

### Dataset Overview
The analysis was performed on the `syria_telco_churn.csv` dataset, which contains 3,333 customer records and 21 features. The data includes:
* **Identifier:** `phone number`
* **Tenure:** `account length`
* **Location:** `state`, `area code`
* **Service Plans:** `international plan`, `voice mail plan`
* **Usage Metrics:** Total minutes, calls, and charges for day, evening, night, and international services.
* **Support Interaction:** `customer service calls`
* **Target:** `churn`

The dataset was found to be clean, with no missing values or duplicate entries. The target class was moderately imbalanced, with 14.5% of customers having churned.

### Data Preparation Steps
* **Irrelevant Columns:** The `phone_number` and `area_code` columns were dropped as they provided no predictive value.
* **Encoding:** Categorical features (`international_plan`, `voice_mail_plan`, `state`) were converted into a numeric format using binary and one-hot encoding. The boolean `churn` variable was mapped to integers (0 for False, 1 for True).
* **Feature Engineering:** A `tenure_bucket` feature was created by grouping `account_length` into three categories: '0–12mo', '13–48mo', and '49+mo'. These buckets were then one-hot encoded.
* **Train-Test Split:** The data was split into an 80% training set and a 20% testing set. A stratified split was used to ensure the proportion of churners was consistent in both sets.
* **Scaling:** All numeric features in the training and testing sets were standardized using `StandardScaler` to ensure that features with larger scales did not disproportionately influence the model.

---

## Exploratory Data Analysis (EDA) & Key Insights

The EDA phase focused on answering key research questions and uncovering the primary drivers of churn:

* **Customer Service Calls:** Customers who churned made a significantly higher number of calls to customer service (median of 2) compared to those who stayed (median of 1). This suggests that frequent support interactions are a strong early warning sign.
* **Usage vs. Plan Allowance:** Under utilization of plan minutes was a strong predictor of churn. Customers who churned consistently used fewer day, evening, and night minutes than those who remained with the service.
* **Geographic Churn Patterns:** Churn rates were significantly above the average in certain states, including New Jersey (NJ) and California (CA), which had churn rates around 26%. This indicates that geography is a relevant factor.
* **Tenure Effects:** Customer tenure had a minor but noticeable relationship with churn. Customers with very short or very long tenures showed a slightly elevated risk of churning.
* **Early Warning Features:** Enrollment in an **international plan** was a strong positive correlate with churn. Conversely, having a **voicemail plan** was associated with higher retention.

---

## Modeling and Evaluation

Three primary classification models were built and evaluated to predict customer churn.

### Baseline: Logistic Regression
A Logistic Regression model with balanced class weights served as the baseline.
* **Recall:** 0.70
* **Precision:** 0.33
* **ROC-AUC:** 0.80
While it captured 70% of churners, its low precision indicated a high rate of false positives.

### Random Forest Classifier
A Random Forest model was trained to capture more complex, non-linear patterns.
* **Recall:** 0.64
* **Precision:** 0.73
* **ROC-AUC:** 0.89
This model significantly improved precision but at the cost of a slight reduction in recall.

### Final Model: Gradient Boosting Classifier
The `HistGradientBoostingClassifier` provided the best overall performance and was selected as the final model.
* **Recall:** 0.78
* **Precision:** 0.80
* **F1-Score:** 0.79
* **ROC-AUC:** 0.90
This model successfully balanced the need to identify a high percentage of true churners while minimizing incorrect classifications.

### Model Performance Summary

| Model               | Recall     | Precision  | F1-Score   | ROC-AUC    |
| :------------------ | :--------- | :--------- | :--------- | :--------- |
| Logistic Regression | 0.70       | 0.33       | 0.44       | 0.80       |
| Random Forest       | 0.64       | 0.73       | 0.68       | 0.89       |
| **Gradient Boosting** | **0.78** | **0.80** | **0.79** | **0.90** |

---

## Recommendations

### Final Model Selection
The **Gradient Boosting Classifier** is recommended for deployment due to its superior performance, particularly its high recall (78%) and precision (80%).

### Actionable Recommendations

1.  **Deploy the Gradient Boosting Model:**
    * Implement the model to produce a weekly prioritized list of at-risk customers for the **Customer Retention Team**.

2.  **Enhance Proactive Retention Marketing:**
    * **High-Touch Alerts:** The **Retention Team** should receive automated alerts when a customer makes their second service call in a quarter.
    * **Usage-Based Campaigns:** The **Marketing Department** should target customers with low usage for "plan right-sizing" consultations.
    * **Geographic Targeting:** Focus retention marketing budgets on "hotspot" states like New Jersey and California.
    * **International Plan Review:** The **Marketing Department** should reassess the pricing and value proposition of the international plan.

---

## Conclusion

This project successfully identified key behavioral and account-level drivers of customer churn at SyriaTel. The developed Gradient Boosting model provides a highly accurate and reliable tool for proactively identifying at-risk customers. By implementing the data-driven recommendations, SyriaTel can enhance its retention strategies, reduce revenue loss, and improve profitability.

---

## Limitations and Future Work

* **Limitations:** The current model does not account for external factors like competitor actions. With 80% precision, 20% of retention efforts may still be directed at non-churning customers.
* **Future Work:**
    * **Threshold Optimization:** Fine-tune the model's prediction threshold based on a cost-benefit analysis.
    * **Continuous Monitoring:** Regularly monitor model performance and retrain it quarterly.
    * **Advanced Modeling:** Explore more advanced techniques such as XGBoost, LightGBM, and model ensembling.