# Fraud-Risk-Modeling-for-Financial-Transactions

# 🌟 Overview  

## Objective  
The main goal is to identify whether each transaction is fraudulent. Among them, the training set sample is about 590,000 (3.5% of fraud), and the test set sample is about 500,000.
The data is mainly divided into 2 categories which are joined by TransactionID. Not all transactions have corresponding identity information.
- transaction data
- identity data.
  
## Process Overview 
- I started with exploratory data analysis (EDA), aiming to uncover the true nature of masked features that had been anonymized for privacy reasons. I classified features into categorical and numerical types and inferred their meanings based on distribution patterns, laying the foundation for effective feature engineering.

- Feature engineering was a priority, as many raw features lacked interactions critical for fraud detection. I created a unique user identification number (UID) by combining card number, address, and account information. This approach assumes that card prefixes, addresses, and account longevity can reliably identify individual users, enabling more accurate fraud pattern detection across transactions. Based on business understanding, I also engineered interaction features, such as daily transaction counts per product, to capture abnormal spikes that may indicate fraudulent behavior.


- For modeling, I initially selected XGBoost as a baseline because of its strong performance on large, high-dimensional, and sparse datasets. However, the initial results were not satisfactory, prompting a second round of focused feature engineering.
  - The primary goal of this feature engineering phase was to shift the prediction target from individual transactions to identifying potentially fraudulent users. By predicting at the user level rather than the transaction level, the model can more efficiently and reliably detect all related fraudulent activities associated with the same user.
  - To achieve this, I engineered a rich set of time-based, product-based, and user-based interaction features designed to capture subtle anomalies in credit card activity patterns. For example, I tracked the number and amount of transactions per user over different time windows, created features representing the diversity of products purchased, and monitored transaction behaviors across days and hours. A typical fraud pattern — small frequent charges escalating into large withdrawals — was modeled through features like cumulative spend per user, transaction count acceleration, and spending volatility.
  - By embedding these multi-dimensional interaction patterns, the model gained the ability to recognize complex user behaviors that precede large-scale fraud, leading to significantly better generalization and earlier fraud detection.

- In terms of model development, given the dataset contained over 250 categorical features, I leveraged CatBoost for its ability to handle categorical variables directly without extensive preprocessing, preserving category information while simplifying the pipeline. Also, to efficiently handle the large dataset size, I also applied LightGBM, which uses a histogram-based algorithm and a leaf-wise tree growth strategy. This allowed for faster training, lower memory consumption, and no compromise in predictive performance.

- Hyperparameter tuning was performed using Bayesian Optimization for its efficient exploration of the parameter space and probabilistic modeling. To prevent information leakage and overfitting, I implemented Group K-Fold Cross-Validation, using the UID (user ID) as the group key to ensure the same user did not appear across both training and validation sets simultaneously.
  
- Finally, model evaluation was conducted using multiple metrics: Precision (minimizing false positives), F1 Score (balancing precision and recall), and AUC (assessing the model's ability to distinguish fraudulent transactions). This multi-metric approach ensures a robust and reliable fraud detection system.


---


# 💡 Key EDA Insights & Feature Engineering

## EDA
EDA insights guide the feature engineering and model selection, ensuring models align with business objectives and deliver improved forecasting accuracy.
Here is the sample of EDA: https://github.com/katarinaa19/Fraud-Risk-Modeling-for-Financial-Transactions/blob/main/key_eda_insights.md
Findings: 
- **Time-Based and Interaction Features**: Create features to capture fraud patterns during low-activity periods; Develop rolling-window features for time-dependent fraud behaviors.
- **Amount-Related Features**:  Generate interaction terms involving transaction amounts and categorical variables;  Create aggregations (mean, median, sum) over specified periods.
- **Product Category Features**: Apply frequenct/target encoding and generate time-based features.
- **V Columns Strategy**: Select representative V features based on EDA insights.
- **ID Columns Processing**: Split into categorical and categorical subsets; then apply normalization and appropriate encoding.
- **Card Features Engineering**: Create time-delta features, aggregations of transaction amounts and frequency encoding.
- **Unique User Identifier (Most Important)**: Construct a unique identifier by combining card number, address, and open account date.


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

##  Model Results
| Model     | Public Score | Private Score | Cross-Validation AUC | 
|-----------|--------------|---------------|----------------------|
| CatBoost  | 0.94         | 0.92          | 0.97                 | 
| LightGBM  | 0.93         | 0.91          | 0.96                    | 
| XGBoost   | 0.91         | 0.88          | 0.8822                 | 


