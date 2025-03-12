# Key EDA Insights

## Table of Contents
- [Class Distribution](#class-distribution)
- [Correlation Analysis](#correlation-analysis)
- [Transaction Timedelta Analysis](#transaction-timedelta-analysis)
- [Transaction Amount](#transaction-amount)
- [Product Category Analysis](#product-category-analysis)
- [V Column Analysis](#v-column-analysis)
- [ID Column Analysis](#id-column-analysis)
- [Card Column Analysis](#card-column-analysis)

---

## Class Distribution

The dataset exhibits a strong **class imbalance**, with **fraudulent transactions accounting for only 3.5%**, while **96.5% are non-fraudulent**. This may bias the model towards predicting the majority class, requiring **imbalance-handling techniques**.

![image](https://github.com/user-attachments/assets/94394a54-2e38-4fa4-a864-ab8d587962e3)

---

## Correlation Analysis  

The **top 20 features most correlated with "isFraud"** are `V` features, whose exact meanings are masked. Understanding their potential significance is **crucial for fraud differentiation**.

![image](https://github.com/user-attachments/assets/4ff18f26-3465-409c-bd8b-c4b9f862ba61)

---

## Transaction Timedelta Analysis  

Fraudulent transactions may spike during specific **weekdays or time periods**. We convert `TransactionDT` into an actual timestamp (assuming `2018-01-01` as the reference date) to extract:
- **Weekday (0-6, where 0 = Monday)**
- **Hour (0-23)**

### Mean Fraud Rate and Transaction Count by Date  

Fraud rates **fluctuate sharply**, while transaction volume remains stable. Higher fraud rates often align with **low transaction volume**, suggesting fraudsters exploit quiet periods.

![image](https://github.com/user-attachments/assets/77dd721f-61c8-479b-8166-1ec857d3cdd8)

### Fraud Distribution by Weekday, Month, and Hour  

- **Weekday Trends:** Higher fraud on **weekends (Friday-Sunday)**.  
- **Monthly Trends:** Fraud peaks in **June** and remains high in early months.  
- **Hourly Trends:** Fraud is highest between **5-9 AM**, likely due to **low user activity**.  

![image](https://github.com/user-attachments/assets/096e5dd0-72a0-4432-bd41-50f7145d0e31)

---

## Transaction Amount  

Fraudulent transactions (`isFraud=1`, red) are mostly **below $1,000**, while transaction amounts **cluster near zero** with a **long tail beyond $30,000**.  
A **log transformation** may improve fraud/non-fraud separation for **linear models**.

![image](https://github.com/user-attachments/assets/1bf5b90f-e634-4274-8072-8398d6393d1b)

**Transaction amount after log transformation:**

![image](https://github.com/user-attachments/assets/a14e2615-cfd0-41a5-b39d-0a2995b0d829)

---

## Product Category Analysis  

### Fraud by Product Category  

- **Category C** has the **highest fraud rate**.  
- **R, S, and W** have **lower fraud risk**.  

![image](https://github.com/user-attachments/assets/31bb248c-1c02-4b37-8b7c-5a4814e0ff53)

### Time-Based Properties for Each Product Category  

- Some categories, such as **Product H & R**, show sharp fraud peaks at **specific hours or days**, indicating fraud is **not evenly distributed over time**.
- Time-sensitive fraud behavior requires **features like DT_W, DT_M, or rolling averages**.

Instead of simple **label encoding**, categorical encoding is needed to capture:  
- **Fraud trends per product over time** (`ProductCD_W_Day`, `ProductCD_C_Day`, etc.).  
- **Transaction count trends per product per day** to measure **popularity or unusual spikes**.

![image](https://github.com/user-attachments/assets/52881c6b-8587-46da-ba4e-6f8db4c57265)

---

## V Column Analysis  

With **300+ `V` features**, many of which are **correlated** and contain **NaNs**, direct correlation analysis is challenging.  
To address this:
1. **Group features with the same number of NaNs.**  
2. **Identify correlated sub-groups.**  
3. **Select representative features based on uniqueness and completeness.**

Example selection: `[1, 3, 4, 6, 8, 11]`  
![image](https://github.com/user-attachments/assets/3453ce37-e443-4358-acc5-c396c393a9b8)

---

## ID Column Analysis  

The **`id_` columns (`id_01` to `id_38`)** contain **identity-related data** (device/network information). Since their meanings are **masked**, we analyze **distribution plots** to classify **numerical vs. categorical** features.

**Numerical Features:**  
`["id_01", "id_02", ..., "id_32"]`  
Might represent **risk scores or transaction signals**.

![image](https://github.com/user-attachments/assets/5f482f9e-b304-4cf0-ad11-1508aca1a1ac)

**Categorical Features:**  
`["id_12", "id_15", ..., "id_34"]`  
For example, `id_30` = **Operating System (OS)**, `id_31` = **Browser**, `id_32` = **Screen resolution**.

![image](https://github.com/user-attachments/assets/ca5ad76d-0e4e-41a5-bc40-51cb9c8d107e)

---

## Card Column Analysis  

`card1` to `card6` relate to the **payment card** used in transactions.  
We classify them based on **unique values and histograms**.

| Card Feature | Unique Values |
|-------------|--------------|
| card1       | 13,553       |
| card2       | 500          |
| card3       | 114          |
| card4       | 4            |
| card5       | 119          |
| card6       | 4            |

![image](https://github.com/user-attachments/assets/45d0f46b-e190-4ffe-aadc-0a84609b6cde)

### Feature Engineering Insights  

**Card1:** Might be a **hashed identifier**  
- Fraudsters may use the same card across multiple addresses/emails.  
- **Encoding:** Frequency Encoding  

**Card2:** Likely **Identification Number of the Card**  
- Similar to `card1`, use **Frequency Encoding**.  

**Card3:** Possibly **CVV (Card Verification Value)**  
- **Target Encoding**, **Frequency Encoding**  

**Card4:** Likely **Card Type (Visa, MasterCard, etc.)**  
- **One-Hot Encoding**  

**Card5:** Possibly **Bank Identification Number (BIN)**  
- **Target Encoding**  

**Card6:** Likely **Card Type (Credit/Debit/Prepaid)**  
- **One-Hot Encoding**  

---

This structured format ensures **clarity, readability, and easy navigation** for key EDA insights. 🚀
