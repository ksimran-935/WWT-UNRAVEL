# Wings R Us – Robust Product Recommendation System

## 📌 Project Overview
This repository contains the implementation of a **robust, noise-filtered recommendation engine** designed for Wings R Us.  
The system leverages **item co-occurrence analysis** and **Positive Pointwise Mutual Information (PPMI)** to generate **personalized product recommendations** from historical order data.

The primary goal is to:
- **Increase upsell and cross-sell opportunities**.
- **Improve relevance** of recommendations.
- Ensure recommendations are **clean** by removing irrelevant system-generated items.

---

## 🎯 Business Problem
Wings R Us wanted to improve their **digital ordering experience** by suggesting relevant items to customers **at checkout**.  
However, their raw historical order data contained:
- Noise entries like `"Order Memo Paid"` or `"Order Blankline 2"`.
- Unstructured item formats (JSON strings, free-text, delimiters).
- Multiple store and region variations.

The challenge was to **parse, clean, and model** this data to produce **accurate, scalable, and business-friendly** recommendations.

---

## 🚀 Features
### **1. Advanced Data Parsing**
- Supports **multiple input formats**:
  - JSON objects with nested `item_name` fields.
  - Regex extraction from semi-structured strings.
  - Delimiter-based splitting for free text.
- Deduplicates items in each order.

### **2. Noise Filtering**
- Removes **administrative / non-sellable** items.
- Uses regex-based pattern matching for terms like:
  - `"memo"`, `"blankline"`, `"paid"`, `"coupon"`, `"discount"`, `"tip"`.
- Filters out extremely short, meaningless tokens.

### **3. Co-occurrence Matrix**
- Counts how often each item is purchased with others.
- Filters by **minimum co-occurrence threshold**.

### **4. Positive Pointwise Mutual Information (PPMI)**
- Captures **strong associations** between items beyond random chance.
- Prevents over-recommending overly popular but irrelevant products.

### **5. Business Rules Integration**
- If the cart contains wings, **boost sides and drinks** in the ranking.
- Ensures variety in recommendations by avoiding duplicates.

### **6. Popularity Fallback**
- In case of sparse data, falls back to **globally popular items**.

---

## 🧠 Recommendation Algorithm – Step by Step
1. **Data Ingestion**  
   Load `order_data.csv` and `test_data_question.csv`.

2. **Parsing & Normalization**  
   Extract clean display names, convert to lowercase normalized keys, and map back to most frequent display forms.

3. **Noise Removal**  
   Drop items matching the noise pattern list.

4. **Frequency & Co-occurrence Counting**  
   Count individual item occurrences and pairwise co-occurrences.

5. **PPMI Calculation**  
   - Compute probability of item occurrence:  
     `p(a) = freq(a) / total_items`
   - Compute probability of co-occurrence:  
     `p(a, b) = freq(a, b) / total_items`
   - Calculate:  
     `PPMI(a, b) = max( log( p(a, b) / (p(a) * p(b)) ), 0 )`

6. **Recommendation Scoring**
   - For each cart item, score candidates as:  
     `score = α * PPMI * log1p(co-occurrence) + γ * popularity`
   - Merge and rank results.

7. **Business Rule Adjustment**
   - If wings detected, move sides/drinks higher.

8. **Final Selection**
   - Pick top-3 unique, non-noise items.

---

## 📊 Exploratory Data Analysis (EDA) Summary
From historical `order_data.csv`:
- **Top-selling items:** Classic Wings, Fries, Ranch Dip, Soda.
- **Most frequent co-pairs:** Wings + Fries, Wings + Ranch Dip, Fries + Soda.
- **Order Occasion:** Majority are "ToGo" orders; "Delivery" orders tend to be larger.
- **Store variations:** Certain dips are more popular in specific store regions.

---

## 📂 Input / Output Files
### **Input**
- `order_data.csv` – Historical transaction logs with `ORDERS` column.
- `test_data_question.csv` – Test cases for generating recommendations.

### **Output**
- `recommendations_output.csv` – Contains:
  - Original test order columns.
  - `RECOMMENDATION 1`
  - `RECOMMENDATION 2`
  - `RECOMMENDATION 3`

---


