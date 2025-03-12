# 💡 Key EDA Insights

**Table of Contents**
- [Class Distribution](#class-distribution)
- [Correlation Analysis](#correlation-analysis)
- [Transaction Timedelta Analysis](#transaction-timedelta-analysis)
- [Transaction Amount](#transaction-amount)
- [Product Category Analysis](#product-category-analysis)
- [V Column Analysis](#v-column-analysis)
- [ID Column Analysis](#id-column-analysis)
- [Card Column Analysis](#card-column-analysis)
- [Summary of Insights](#summary-of-insights-for-feature-engineering-process)

---

## Class Distribution

The dataset exhibits a strong class imbalance, with fraudulent transactions accounting for only 3.5% of total transactions, while 96.5% are non-fraudulent. The model may be biased toward predicting the majority class, so tools for handling class imbalance are needed.

![image](https://github.com/user-attachments/assets/94394a54-2e38-4fa4-a864-ab8d587962e3)

---

## Correlation Analysis  

The top 20 features most correlated with "isFraud" are `V` features, whose exact meanings are masked. Therefore, exploring their potential significance and understanding their relationship with fraudulent transactions is crucial for capturing fraud differentiation effectively. We will analyze each `V` features one by one later.

![image](https://github.com/user-attachments/assets/4ff18f26-3465-409c-bd8b-c4b9f862ba61)

---

## Transaction Timedelta Analysis  

Fraudulent transactions may be more frequent on certain weekdays or time periods. To analyze this, we convert TransactionDT into an actual timestamp, assuming 2018-01-01 as the reference date. Since it represents a timedelta from a reference datetime, this transformation enables the extraction of weekday (0-6, where 0 = Monday) and hour (0-23) features.

### Mean Fraud Rate and Transaction Count by Date  

- Fraud rates fluctuate with sharp spikes, while transaction volume remains stable with periodic peaks. 
- Higher fraud rates often align with lower transaction volume, suggesting fraudsters exploit low-activity periods. 
- Incorporating interaction features with timedelta variables can help capture these time-dependent fraud patterns.

![image](https://github.com/user-attachments/assets/77dd721f-61c8-479b-8166-1ec857d3cdd8)

### Fraud Distribution by Weekday, Month, and Hour  

- Weekday Trends: Fraud rates tend to be higher on weekends (Friday-Sunday)。
- Monthly Trends: Fraud peaks in June and remains high in early months, possibly due to seasonal patterns or targeted fraud campaigns.
- Hourly Trends: Fraud is most prevalent during early morning hours (5-9 AM), suggesting fraudsters operate when user activity is low, making detection harder.
Therefore, we might need to create time-based features in feature engineering process to capture fraud's time-dependent properties.

![image](https://github.com/user-attachments/assets/096e5dd0-72a0-4432-bd41-50f7145d0e31)

---

## Transaction Amount  

- The plots show that fraudulent transactions (isFraud=1, red) are concentrated at lower amounts, primarily below $1,000. 
- Additionally, most transactions have small amounts clustered near zero, with a long tail extending beyond $30,000. 
- This heavy-tailed distribution suggests that a log transformation would be beneficial for better fraud/non-fraud separation if we want to use linear-based models like logistic regression.

![image](https://github.com/user-attachments/assets/1bf5b90f-e634-4274-8072-8398d6393d1b)

- Transaction amount after log transformation:

![image](https://github.com/user-attachments/assets/a14e2615-cfd0-41a5-b39d-0a2995b0d829)

---

## Product Category Analysis  

### Fraud by Product Category  

- Category C has the highest fraud rate.  
- R, S, and W have lower fraud risk.  

![image](https://github.com/user-attachments/assets/31bb248c-1c02-4b37-8b7c-5a4814e0ff53)

### Time-Based Properties for Each Product Category  

- From the plots, we can see:
  1. Some categories, such as Product H & R, show sharp peaks in fraud at specific times (e.g., certain hours or days), which suggests that fraud is not evenly distributed across time.
  2. If fraud behavior is time-sensitive, we might need time-based features like DT_W, DT_M, or other rolling averages to capture this behavior.

- Therefore, instead of using simple label encoding, which treats ProductCD as an ordinal variable, we need categorical encoding that captures:
  - Fraud trends per product over time (ProductCD_W_Day, ProductCD_C_Day, etc.).
  - Transaction count trends per product per day (to measure popularity or unusual spikes).

- Why Not Use Simple Label Encoding?
  - Label Encoding assumes an ordinal relationship between categories, but ignore time-dependent properties.
  - One-Hot Encoding would create too many sparse features.

![image](https://github.com/user-attachments/assets/52881c6b-8587-46da-ba4e-6f8db4c57265)

---

## V Column Analysis  

- With over 300 V features, many of which are correlated and contain NaNs, direct correlation analysis is challenging due to high dimensionality, excessive missing values, and categorical nature. To address this, we first group features with the same number of NaNs, then identify correlated sub-groups and select the most representative feature based on uniqueness and completeness. Features with more unique values are prioritized; if similar, the one with fewer NaNs is chosen, reducing redundancy while preserving fraud patterns.

- For example, in one such group, the selected features [1, 3, 4, 6, 8, 11] balance information retention and feature diversity. Correlated pairs like V2-V3, V4-V5, V6-V8, and V10-V11 were identified, with the most unique and complete feature retained from each pair. V1 was kept for independence, while V3, V4, and V6 were chosen for diversity. V11 was preferred over V10 due to near-identical correlation but better completeness. This approach optimizes feature selection, ensuring efficient modeling without excessive redundancy. For other V features, refer to the EDA and FE.ipynb

![image](https://github.com/user-attachments/assets/3453ce37-e443-4358-acc5-c396c393a9b8)

---

## ID Column Analysis  

- The id_ columns (id_01 to id_38) contain identity-related information derived primarily from device and network data. Since their exact meanings are masked, we analyze distribution plots to distinguish numerical and categorical features, helping determine which to encode and which to normalize.

- Here are the numerical features: ["id_01", "id_02", "id_03", "id_04", "id_05", "id_06", "id_07", "id_08", "id_09", "id_10", "id_11", "id_13", "id_17", "id_18", "id_19", "id_20", "id_21", "id_22", "id_24", "id_25", "id_26", "id_32"].
- id_01 to id_11 might represent various risk scores or signals associated with the transaction or the user's behavior.

![image](https://github.com/user-attachments/assets/5f482f9e-b304-4cf0-ad11-1508aca1a1ac)

- Here are the numerical features: ["id_12", "id_15", "id_16", "id_23", "id_27", "id_28","id_29", "id_30", "id_31", "id_33", "id_34"].
- Now we can pinpoint meanings of some features; For example, id_30 represents Operating System (OS); id_31 is browser type; id_32 is screen resolution, etc.

![image](https://github.com/user-attachments/assets/ca5ad76d-0e4e-41a5-bc40-51cb9c8d107e)

---

## Card Column Analysis  

- `card1` to `card6` are related to the payment card used in the transaction. While their exact meanings are masked, they are likely associated with the card type, issuer, and transaction patterns. 
- We identify categorical and numerical columns based on their unique values and histograms.

| Card Feature | Unique Values |
|-------------|--------------|
| card1       | 13,553       |
| card2       | 500          |
| card3       | 114          |
| card4       | 4            |
| card5       | 119          |
| card6       | 4            |

![image](https://github.com/user-attachments/assets/45d0f46b-e190-4ffe-aadc-0a84609b6cde)

After investigation, here is the findings for card columns:
- Card1: Might be a Hashed Identifier  
  - A single card1 linked to multiple addresses and emails might indicate stolen or resold cards.  
    - card1_addr1 → Frequency Encoding  
    - card1_addr1_P_emaildomain → Frequency Encoding  
  - Sudden changes in transaction amount can signal fraudulent activity.  
    - TransactionAmt_card1_mean  
    - TransactionAmt_card1_std  
    - TransactionAmt_card1_addr1_mean  
    - TransactionAmt_card1_addr1_std  
    - TransactionAmt_card1_addr1_P_emaildomain_mean  
    - TransactionAmt_card1_addr1_P_emaildomain_std  
  - Fraudsters often use stolen cards quickly before they get blocked.  
    - D9_card1_mean, D9_card1_std  
    - D9_card1_addr1_mean, D9_card1_addr1_std  
    - D9_card1_addr1_P_emaildomain_mean, D9_card1_addr1_P_emaildomain_std  
    - D11_card1_mean, D11_card1_std  
    - D11_card1_addr1_mean, D11_card1_addr1_std  
    - D11_card1_addr1_P_emaildomain_mean, D11_card1_addr1_P_emaildomain_std  

- Card2: Might be the Identification Number of the Card  
  - Pretty similar to card1 information, so use:  
    - Frequency Encoding  

- Card3: Might be the CVV (Card Verification Value)  
  - Encoding techniques:  
    - Target Encoding  
    - Frequency Encoding  

- Card4: Might be the Card Type  
  - One-Hot Encoding  

- Card5: Might be the Bank Identification Number (BIN)  
  - Target Encoding  

- Card6: Might be the Card Type (Credit/Debit/Prepaid)  
  - One-Hot Encoding  

---

## Summary of Insights for Feature Engineering Process

Based on the EDA, here are the key insights to guide our feature engineering process:

**Time-Based Features**
- Create weekday, hour, and month features from TransactionDT
- Develop interaction features that capture fraud patterns during low-activity periods
- Consider rolling window features to capture time-dependent fraud behaviors

**Amount-Related Features**
- Create amount-based interaction features with other categorical variables
- Develop statistical aggregations (mean, std) of transaction amounts by various groupings

**Product Category Features**
- Avoid simple label encoding for ProductCD
- Create time-based aggregations per product category (ProductCD_W_Day, ProductCD_C_Day)
- Develop frequency and target encoding for product categories

**V Column Strategy**
- Group features with similar NaN patterns
- Select representative features from correlated groups based on uniqueness and completeness
- Prioritize features with more unique values and fewer NaNs

**ID Column Processing**
- Split ID columns into numerical (id_01-id_11, id_13, etc.) and categorical groups
- Apply normalization to numerical ID features
- Use appropriate encoding for categorical ID features like OS and browser type

**Card Features Engineering**
- For card1 (Hashed Identifier):
 - Create frequency encodings for card1-address combinations
 - Develop statistical aggregations of transaction amounts by card1 groupings
 - Generate time-delta features to capture quick usage patterns
- For other card features:
 - Apply frequency encoding for card2 and card3
 - Use one-hot encoding for card4 and card6
 - Apply target encoding for card5

**General Strategies**
- Address class imbalance in modeling approach
- Create interaction features between correlated variables
- Develop aggregation features that capture unusual patterns or outliers
- Consider entity embeddings for high-cardinality categorical features
