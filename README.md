# Fraud-Risk-Modeling-for-Financial-Transactions

# 🌟 Overview  

## **🎯Problem Statement and Objective**  



## **📂Dataset**  
- **Data Description**: 
- **Key Features**  
- **Source**: 

## **🛠️ Process Overview**  
- I started with exploratory data analysis (EDA); the goal is to uncover masked features, anonymized for privacy, by classifying them into categorical and numerical types and inferring their meanings through distribution patterns. This provides essential insights for effective feature engineering.
- I prioritize feature engineering since many raw features don't capture interactions crucial for fraud detection. I create a unique user identification number (UID) by combining the card number, address, and account details, enabling accurate user identification across multiple cards. This method assumes the card prefix, user address, and card usage duration accurately identify individual users, helping us analyze fraud patterns across multiple transactions. Then, I develop interaction features based on business understanding, such as tracking daily transactions per product to detect unusual spikes indicating fraud.
- For modeling, I selected XGBoost as a baseline due to its capability to model nonlinear relationships, enhanced efficiency with LightGBM due to its histogram-based optimization, and leveraged CatBoost to handle categorical features without extensive preprocessing. These models do not rely on assumptions such as linearity, normality, or homoscedasticity, which makes them well-suited for our project.
- Hyperparameters were tuned using Bayesian Optimization for its efficiency and probabilistic guidance. To avoid information leakage and overfitting, I implemented group k-fold cross-validation, ensuring user data doesn't overlap between training and testing sets.
- Finally, I used Accuracy, Precision, F1 Score, and AUC to evaluate the model. Accuracy provides an overall performance measure, while Precision reduces false positives. F1 Score balances precision and recall, and AUC assesses the model's ability to distinguish fraudulent transactions, ensuring effective fraud detection.


---


# 💡 Key EDA Insights & Feature Engineering

## EDA
EDA insights guide the feature engineering and model selection, ensuring models align with business objectives and deliver improved forecasting accuracy.
Here is the sample of EDA: https://github.com/katarinaa19/Fraud-Risk-Modeling-for-Financial-Transactions/blob/main/key_eda_insights.md
Findings: 
- Time-Based and Interaction Features: Create features to capture fraud patterns during low-activity periods; Develop rolling-window features for time-dependent fraud behaviors.
- Amount-Related Features:  Generate interaction terms involving transaction amounts and categorical variables;  Create aggregations (mean, median, sum) over specified periods.
- Product Category Features: Apply frequenct/target encoding and generate time-based features.
- V Columns Strategy: Select representative V features based on EDA insights.
- ID Columns Processing: Split into categorical and categorical subsets; then apply normalization and appropriate encoding.
- Card Features Engineering: Create time-delta features, aggregations of transaction amounts and frequency encoding.
- Unique User Identifier (Most Important): Construct a unique identifier by combining card number, address, and open account date.


## Feature Engineering

| **Category**       | **Feature Name**                                    | **Description**                                                                                   | **Purpose** |
|--------------------|-----------------------------------------------------|---------------------------------------------------------------------------------------------------|-------------|
| **User Identification** | `uid`                                            | Identify same users by combining `card1`, `address`, and `card open date`                         | Identify fraudulent users and analyze their behaviors. |
| **Time-Based**     | `days_since_start`                                  | Days since the start date (based on `TransactionDT`)                                              | Used for creating time-based interaction features. |
|                    | `weeks_since_start`                                 | Weeks since the start date (based on `TransactionDT`)                                             |             |
|                    | `months_since_start`                                | Months since the start date (from 2017)                                                           |             |
| **Product & Time** | `ProductCD_C_Day`                                  | Daily count of transactions for `ProductCD = 'C'`                                                 | Certain categories, like Product H and R, show sharp fraud peaks at specific times, indicating that fraud is time-sensitive. These features help capture these patterns. |
|                    | `ProductCD_R_Day`                                  | Daily count of transactions for `ProductCD = 'R'`                                                 |             |
|                    | `ProductCD_H_Day`                                  | Daily count of transactions for `ProductCD = 'H'`                                                 |             |
|                    | `ProductCD_S_Day`                                  | Daily count of transactions for `ProductCD = 'S'`                                                 |             |
| **Device & User**  | `device_hash`                                      | Unique anonymized string representation for each device                                           | Since fraudsters change or share devices across multiple accounts to avoid detection, this helps analyze user behavior based on device usage. |
|                    | `uid_device_nunique`                               | Count of unique devices per user (`uid1`)                                                         |             |
|                    | `device_uid_nunique`                              | Count of unique users per device (`device_hash`)                                                  |             |
| **Time-Based**     | `D1_revised, D2_revised, D4_revised, D6_revised, D10_revised, D11_revised, D12_revised, D14_revised, D15_revised` | Normalized values of time-based features across different time periods | Distributions of `timedelta` vary greatly; max scaling ensures comparability, mitigates time shift effects, and reduces extreme value impacts. |
| **Email**         | `email_domain_match`                               | Indicates if the payer and receiver email domains match                                           | Fraudulent transactions often use uncommon or disposable email domains. This feature helps perform a match check. |
|                    | `P_emaildomain_count`                             | Count encoding for payer's email domain                                                           |             |
|                    | `R_emaildomain_count`                             | Count encoding for receiver's email domain                                                        |             |
| **ID Statistics**  | `{col}_uid1_mean`                                  | Mean of numerical features grouped by `uid1`                                                      | These features capture user-specific transaction patterns, variability, and category frequencies, helping detect fraud based on behavior and counts. |
|                    | `{col}_uid1_std`                                   | Standard deviation of numerical features grouped by `uid1`                                        |             |
|                    | `{col}_count_enc`                                 | Count encoding for categorical features grouped by `cat_id` and `binary_id`                       |             |
| **Card-Related**   | `card1_addr1`                                     | Combined `card1` and `addr1`                                                                      | These features capture fraud patterns by combining identifiers, encoding card information, and linking transaction data to fraud likelihood. |
|                    | `card1_addr1_P_emaildomain`                      | Combined `card1_addr1` and `P_emaildomain`                                                        |             |
|                    | `card2_FE`                                        | Frequency encoding for `card2`                                                                    |             |
|                    | `card3_target_enc`                               | Target encoding for `card3` (mapping to `isFraud` mean)                                           |             |
|                    | `card3_FE`                                        | Frequency encoding for `card3`                                                                    |             |
|                    | `card4_*`                                         | One-hot encoding for `card4`                                                                      |             |
|                    | `card5_target_enc`                               | Target encoding for `card5` (mapping to `isFraud` mean)                                           |             |
|                    | `card6_*`                                         | One-hot encoding for `card6`                                                                      |             |
| **Transaction Metrics** | `day_count`                                  | Daily count of transactions                                                                      | Accounts for time-dependent properties of fraudulent activities. |
|                    | `hour_count`                                      | Hourly count of transactions                                                                     |             |
| **Product & Transaction** | `ProductID`                                | Combined transaction amount and product category                                                 | Accounts for product-dependent properties of fraudulent activities and unusual spending patterns by product. |
|                    | `ProductID_count`                                | Count encoding for combined `ProductID`                                                          |             |
|                    | `TransactionAmt__ProductCD_count`                | Count encoding for combined transaction amount and product category                              |             |


---

## 🔍 Implementation Steps  
1️⃣ **Data Collection & Preprocessing** – Cleaning and structuring the dataset for analysis.  
2️⃣ **Exploratory Data Analysis (EDA)** – Identifying seasonal patterns and trends.  
3️⃣ **Feature Engineering** – Creating meaningful variables to improve model accuracy.  
4️⃣ **Model Selection & Training** – Testing various forecasting models.  
5️⃣ **Evaluation & Optimization** – Measuring accuracy and fine-tuning the best-performing model.  
6️⃣ **Deployment & Insights** – Providing actionable insights for Walmart’s decision-makers.  

---

## 🎯 Expected Outcomes  
✅ **Accurate sales predictions** to enhance decision-making.  
✅ **Optimized stock levels** to minimize excess inventory costs.  
✅ **Improved revenue forecasting** for financial planning.  
✅ **Strategic business insights** for Walmart’s leadership team.  

---

## 🚀 Future Enhancements  
🚀 Expand the model to include **other product categories**.  
📊 Integrate **macroeconomic factors and competitor pricing** into predictions.  
⚡ Automate the forecasting process with **real-time data updates**.  

---

