# Fraud-Risk-Modeling-for-Financial-Transactions

# 🌟 Overview  

## **🎯Problem Statement and Objective**  



## **📂Dataset**  
- **Data Description**: 
- **Key Features**  
- **Source**: 

## **🛠️ Process Overview**  
I started with exploratory data analysis (EDA); the goal  is to uncover masked features, anonymized for privacy, by classifying them into categorical and numerical types and inferring their meanings through distribution patterns. This provides essential insights for effective feature engineering.

I prioritize feature engineering since many raw features don't capture interactions crucial for fraud detection. I create a unique user identification number (uid) by combining card number, address, and account details, enabling accurate user identification across multiple cards. This method assumes the card prefix, user address, and card usage duration accurately identify individual users, helping us analyze fraud patterns across multiple transactions. Then, I develop interaction features based on business understanding, such as tracking daily transactions per product to detect unusual spikes indicating fraud.

For modeling, I selected XGBoost as a baseline due to its capability to model nonlinear relationships, enhanced efficiency with LightGBM due to its histogram-based optimization, and leveraged CatBoost to handle categorical features without extensive preprocessing. These models do not rely on assumptions such as linearity, normality, or homoscedasticity, which are good for out projects.

Hyperparameters Ire tuned using Bayesian Optimization for its efficiency and probabilistic guidance. To avoid information leakage and overfitting, I implemented group k-fold cross-validation, ensuring user data doesn't overlap betIen training and testing sets.

Finally, I used Accuracy, Precision, F1 Score, and AUC to evaluate the model. Accuracy gives an overall performance measure, while Precision reduces false positives. F1 Score balances precision and recall, and AUC assesses the model's ability to distinguish fraudulent transactions, ensuring effective fraud detection.

---


# 💡 Key EDA Insights & Feature Engineering

## EDA
EDA insights guide the feature engineering and model selection, ensuring models align closely with business objectives and deliver improved forecasting accuracy.
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

