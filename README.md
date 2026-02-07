# Customer Segmentation & RFM Analysis Dashboard (Power BI)

This project is an end‑to‑end **Customer Segmentation and RFM (Recency, Frequency, Monetary) Analysis** built on the popular **Global Superstore** dataset using **Power BI**. It demonstrates how to turn raw transactional data into clear, actionable insights for marketing, sales, and customer success teams.

---

## 📌 Project Overview

The goal of this project is to identify and understand different customer groups based on their purchasing behavior, and to build an interactive dashboard that helps answer questions such as:

- Who are our **best (champion) customers**?
- Which customers are **loyal**, **new**, **at risk**, or **lost**?
- How much revenue does each segment contribute?
- What are the overall **recency**, **frequency**, and **monetary** patterns in our customer base?

The final result is a **Power BI dashboard** that supports customer‑centric decision‑making and can be used as a portfolio project for data / BI roles.

---

## 🧩 Dataset

**Dataset:** Global Superstore (Excel)

**Source tables:**

- `Orders` – Main transactional table (Order Date, Customer ID, Sales, Profit, etc.)
- `People` – Regional managers by Region
- `Returns` – Returned orders by Order ID

> Note: There is **no separate Customer table** in the dataset. Customer information (Customer ID, Customer Name, Segment) comes from the `Orders` table.

---

## 🏗️ Data Model

The data model follows a **star‑schema‑style** design:

- **Fact table**
  - `Orders`

- **Dimension / helper tables**
  - `DateTable` – Calendar table created in DAX
  - `People`
  - `Returns`
  - `RFM_Calculations` – Customer‑level RFM table created in DAX

**Relationships:**

- `Orders[Order Date]` → `DateTable[Date]`
- `Orders[Region]` → `People[Region]`
- `Orders[Order ID]` → `Returns[Order ID]`
- `Orders[Customer ID]` → `RFM_Calculations[Customer ID]`

---

## 🔢 RFM Logic

RFM is calculated at **customer level** in the `RFM_Calculations` table:

- **Recency (R):** Days since the customer’s last order (based on the max order date in the dataset)
- **Frequency (F):** Number of orders placed by the customer
- **Monetary (M):** Total revenue (Sales) from the customer

Each metric is converted into a **score from 1 to 5** (1 = worst, 5 = best), using business‑friendly thresholds.

Example scoring (simplified):

- **R_Score**
  - 5: last purchase within 30 days
  - 4: 31–90 days
  - 3: 91–180 days
  - 2: 181–365 days
  - 1: >365 days

- **F_Score**
  - 5: ≥12 orders
  - 4: 8–11 orders
  - 3: 5–7 orders
  - 2: 2–4 orders
  - 1: 1 order

- **M_Score**
  - 5: ≥ 5000
  - 4: 2000–4999
  - 3: 500–1999
  - 2: 100–499
  - 1: 0–99

These scores are then combined into **customer segments**, such as:

- Champions  
- Loyal Customers  
- Potential Loyalists  
- New Customers  
- At‑Risk  
- Need Attention  
- Lost Customers  

---

## 📊 Key Measures

Some of the DAX measures used in the model include:

### Core KPIs

- `Total_Revenue`
- `Total_Customers`
- `Total_Orders`
- `Average_Order_Value`
- `Average_Customer_Value`
- `Profit_Margin_Percent`

### RFM‑specific

- `Avg_Recency_Days`
- `Avg_Frequency`
- `Avg_Monetary`
- `Champions_Count`, `Champions_Revenue`, `Champions_Revenue_Pct`
- `Loyal_Count`, `AtRisk_Count`, `Lost_Count`, `New_Customers_Count`
- Segment‑wise revenue measures (e.g. `AtRisk_Revenue`, `Loyal_Revenue`, etc.)

Measures are organized into folders for clean navigation:

- `Core Metrics`
- `RFM Averages`
- `RFM Segments\Counts`
- `RFM Segments\Revenue`

---

## 📈 Dashboard Pages & Highlights

The dashboard is designed in multiple pages (can be adapted):

1. **Overview**
   - High‑level KPIs (Revenue, Orders, Customers, Profit Margin)
   - Segment distribution (donut or bar chart)
   - Revenue contribution by segment

2. **RFM Segmentation**
   - R vs M scatter plot (bubble size = Frequency)
   - Average R, F, M cards
   - Table of customers by segment with key stats

3. **Customer Insights**
   - Top customers by revenue or frequency
   - Drill‑down by segment, region, and time
   - Trend charts (e.g. revenue by month, segment performance over time)

4. **Returns / Profitability (optional)**
   - Impact of returns on profit
   - Metrics by region or category

Slicers typically include:

- Year / Month
- Region / Country
- Segment
- Category / Sub‑Category

---

## 🛠️ Tech Stack

- **Power BI Desktop**
- **DAX (Data Analysis Expressions)**
- Power Query (basic data cleaning)
- Global Superstore Excel dataset

---

## 🚀 How to Use This Repo

1. **Clone or download** the repository.
2. Open the `.pbix` file in **Power BI Desktop**.
3. Make sure the path to the Global Superstore dataset is correct (or update the data source if you store the Excel file in a different location).
4. Refresh the data if needed.
5. Explore the report pages and interact with slicers and visuals.

---

## 📚 Learning Outcomes

By going through this project, you can demonstrate:

- Building a **star‑like data model** from a transactional dataset.
- Creating **date tables**, calculated columns, and complex **DAX measures**.
- Implementing **RFM analysis** and customer segmentation in Power BI.
- Designing a **clean, business‑oriented dashboard**.
- Telling a clear **data story** around customer value and retention.

This makes it a strong portfolio project for roles such as:

- Data Analyst  
- Business Intelligence (BI) Developer  
- Power BI Developer  
- Business / Marketing Analyst  

---

## ✅ Future Improvements

Some possible enhancements:

- Add **dynamic RFM thresholds** based on percentiles instead of fixed cut‑offs.
- Incorporate **customer lifetime value (LTV)** estimates.
- Add **cohort analysis** and **churn analysis**.
- Connect to a **database** or data warehouse instead of Excel.
- Parameterize the **analysis period** (e.g. last 12 months only).

---

## 🧑‍💻 Author

- Built with a focus on **Power BI, DAX, customer analytics, and data storytelling**.
- Open to feedback, collaboration, and suggestions for improvement.

If you use or adapt this project, a star ⭐ on the repo is always appreciated!
