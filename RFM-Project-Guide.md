# CORRECTED Implementation Guide - Global Superstore RFM Analysis

## ⚠️ CRITICAL CORRECTION

**The Global Superstore dataset does NOT have a separate Customer table!**

All customer information is contained in the **Orders table**.

---

## 📦 DATASET STRUCTURE (ACTUAL)

The Global Superstore Excel file contains **3 sheets only:**

### 1. **Orders Table** (51,290 rows, 25 columns)
Contains all transaction and customer data:
- **Order Information:** Order ID, Order Date, Ship Date, Ship Mode
- **Customer Information:** Customer ID, Customer Name, Segment
- **Geographic Information:** Country, Region, State, City, Postal Code
- **Product Information:** Product ID, Category, Sub-Category, Product Name
- **Transaction Metrics:** Sales, Quantity, Discount, Profit, Shipping Cost

### 2. **People Table** (24 rows, 2 columns)
Contains regional sales managers:
- Person (sales person name)
- Region

### 3. **Returns Table** (1,079 rows, 3 columns)
Contains returned orders:
- Returned (Yes/No)
- Order ID
- Market

---

## 🔧 CORRECTED DATA MODEL

**Your data model will have:**
- Orders table (main fact table)
- People table (dimension table)
- Returns table (dimension table)
- DateTable (created by you)
- RFM_Calculations table (created by you)

**Total: 5 tables (NOT 6!)**

---

## ✅ CORRECTED RELATIONSHIPS

**Create these relationships ONLY:**

### Relationship 1: Orders → DateTable
```
Orders[Order Date] → DateTable[Date]
Cardinality: Many-to-One (*)
Direction: Single (→)
```

### Relationship 2: Orders → People
```
Orders[Region] → People[Region]
Cardinality: Many-to-One (*)
Direction: Single (→)
```

### Relationship 3: Orders → Returns
```
Orders[Order ID] → Returns[Order ID]
Cardinality: One-to-Many (*)
Direction: Single (→)
```

### Relationship 4: Orders → RFM_Calculations
```
Orders[Customer ID] → RFM_Calculations[Customer ID]
Cardinality: Many-to-One (*)
Direction: Both (bidirectional) or Single
```

**That's it! Only 4 relationships needed.**

---

## 📊 WEEK 1: DATA PREPARATION (CORRECTED)

### Days 1-2: Download & Setup

**Steps:**
1. Download Global Superstore from Kaggle
2. Extract `Global_Superstore.xls` file
3. Open in Excel to verify 3 sheets: Orders, People, Returns

**Expected columns in Orders table:**
- Row ID
- Order ID
- Order Date
- Ship Date
- Ship Mode
- Customer ID
- Customer Name
- Segment
- City
- State
- Country
- Postal Code
- Market
- Region
- Product ID
- Category
- Sub-Category
- Product Name
- Sales
- Quantity
- Discount
- Profit
- Shipping Cost
- Order Priority

---

### Days 3-5: Load Data into Power BI

**Steps:**

1. **Open Power BI Desktop**

2. **Get Data:**
   - File → Get Data → Excel
   - Select `Global_Superstore.xls`
   - You'll see 3 sheets: Orders, People, Returns
   - Select ALL 3 sheets
   - Click "Transform Data"

3. **In Power Query Editor - Orders Table:**
   ```
   Actions:
   - Verify Customer ID column exists (it's here, not in separate table!)
   - Check Customer Name column exists
   - Change Order Date type to Date
   - Change Sales type to Decimal Number
   - Change Profit type to Decimal Number
   - Change Quantity type to Whole Number
   - Remove "Row ID" column (not needed)
   - Remove "Postal Code" column (often has errors)
   ```

4. **In Power Query Editor - People Table:**
   ```
   Actions:
   - Verify 2 columns: Person, Region
   - No changes needed (keep as is)
   ```

5. **In Power Query Editor - Returns Table:**
   ```
   Actions:
   - Verify 3 columns: Returned, Order ID, Market
   - Keep all columns
   ```

6. **Click "Close & Apply"**

---

### Days 5-7: Create DateTable & Relationships

**Create DateTable:**

1. Go to: Modeling → New Table
2. Paste this DAX:

```DAX
DateTable = 
CALENDAR(
    DATE(2012, 1, 1),
    DATE(2015, 12, 31)
)
```

3. Add calculated columns:

```DAX
Year = YEAR(DateTable[Date])
Month = MONTH(DateTable[Date])
MonthName = FORMAT(DateTable[Date], "mmmm")
Quarter = "Q" & ROUNDUP(MONTH(DateTable[Date])/3, 0)
```

4. **Mark as Date Table:**
   - Right-click DateTable → Mark as Date Table
   - Select [Date] column as the date column

**Create Relationships:**

1. **Go to Model View** (left sidebar)

2. **Create Relationship 1:**
   - Drag Orders[Order Date] → DateTable[Date]
   - Should create automatically

3. **Create Relationship 2:**
   - Drag Orders[Region] → People[Region]

4. **Create Relationship 3:**
   - Drag Orders[Order ID] → Returns[Order ID]

5. **Verify in Model View:**
   - You should see 3 tables connected to Orders
   - Orders is the center (fact table)
   - All arrows point to Orders

---

## 📊 WEEK 2: RFM CALCULATIONS (CORRECTED)

### Days 8-9: Create RFM_Calculations Table

**Important:** RFM table is created FROM Orders table, using Customer ID

**Go to: Modeling → New Table**

```DAX
RFM_Calculations = 
VAR MaxDate = MAX(Orders[Order Date])
RETURN
ADDCOLUMNS(
    SUMMARIZE(
        Orders,
        Orders[Customer ID],
        Orders[Customer Name]
    ),
    "Recency", INT(MaxDate - CALCULATE(MAX(Orders[Order Date]))),
    "Frequency", CALCULATE(COUNTROWS(Orders)),
    "Monetary", CALCULATE(SUM(Orders[Sales]))
)
```

**What this does:**
- Groups Orders by Customer ID and Customer Name
- Calculates Recency (days since last order from most recent date)
- Calculates Frequency (count of orders per customer)
- Calculates Monetary (total sales per customer)

**After creating, verify:**
- Click on RFM_Calculations table
- Should show ~3,000-5,000 rows (unique customers)
- Should have 5 columns: Customer ID, Customer Name, Recency, Frequency, Monetary

---

### Days 10-14: Add Score Columns & Segments

**Add these calculated columns to RFM_Calculations:**

**R_Score:**
```DAX
R_Score = 
IF(
    ISBLANK(RFM_Calculations[Recency]),
    0,
    SWITCH(
        TRUE(),
        RFM_Calculations[Recency] <= 30, 5,
        RFM_Calculations[Recency] <= 90, 4,
        RFM_Calculations[Recency] <= 180, 3,
        RFM_Calculations[Recency] <= 365, 2,
        1
    )
)
```

**F_Score:**
```DAX
F_Score = 
IF(
    ISBLANK(RFM_Calculations[Frequency]),
    0,
    SWITCH(
        TRUE(),
        RFM_Calculations[Frequency] >= 12, 5,
        RFM_Calculations[Frequency] >= 8, 4,
        RFM_Calculations[Frequency] >= 5, 3,
        RFM_Calculations[Frequency] >= 2, 2,
        1
    )
)
```

**M_Score:**
```DAX
M_Score = 
IF(
    ISBLANK(RFM_Calculations[Monetary]),
    0,
    SWITCH(
        TRUE(),
        RFM_Calculations[Monetary] >= 5000, 5,
        RFM_Calculations[Monetary] >= 2000, 4,
        RFM_Calculations[Monetary] >= 500, 3,
        RFM_Calculations[Monetary] >= 100, 2,
        1
    )
)
```

**Segment:**
```DAX
Segment = 
SWITCH(
    TRUE(),
    RFM_Calculations[R_Score] >= 4 && RFM_Calculations[F_Score] >= 4 && RFM_Calculations[M_Score] >= 4, "Champions",
    RFM_Calculations[R_Score] >= 3 && RFM_Calculations[F_Score] >= 3 && RFM_Calculations[M_Score] >= 3, "Loyal Customers",
    RFM_Calculations[R_Score] < 3 && RFM_Calculations[F_Score] >= 4 && RFM_Calculations[M_Score] >= 3, "At-Risk",
    RFM_Calculations[R_Score] < 2 && RFM_Calculations[F_Score] < 3 && RFM_Calculations[M_Score] < 3, "Lost Customers",
    RFM_Calculations[R_Score] >= 4 && RFM_Calculations[F_Score] < 3 && RFM_Calculations[M_Score] < 3, "New Customers",
    RFM_Calculations[R_Score] >= 2 && RFM_Calculations[R_Score] < 4 && RFM_Calculations[F_Score] >= 3 && RFM_Calculations[M_Score] >= 3, "Potential Loyalists",
    RFM_Calculations[R_Score] < 3 && RFM_Calculations[F_Score] >= 3 && RFM_Calculations[M_Score] < 3, "Need Attention",
    "Other"
)
```

**Create final relationship:**

4. **Go to Model View**
5. **Drag Orders[Customer ID] → RFM_Calculations[Customer ID]**

---

## ✅ FINAL DATA MODEL VERIFICATION

**Your final Model View should show:**

```
        DateTable
            |
            | (Order Date → Date)
            |
         Orders ←─── People (Region → Region)
            |
            | (Order ID → Order ID)
            |
         Returns

Orders[Customer ID] ←→ RFM_Calculations[Customer ID]
```

**Count your tables: 5 total**
1. Orders (fact table - center)
2. DateTable (dimension)
3. People (dimension)
4. Returns (dimension)
5. RFM_Calculations (analysis table)

---

## 🎯 KEY TAKEAWAYS

**What was wrong in original guide:**
❌ Mentioned Customer table (doesn't exist)
❌ Said 6 tables (actually 5)
❌ Incorrect relationship instructions

**What's correct now:**
✅ Orders table contains all customer data
✅ Only 5 tables total
✅ Only 4 relationships needed
✅ RFM_Calculations created FROM Orders table
✅ Customer ID exists in Orders, not separate table

---

## 📋 QUICK REFERENCE

**Where is customer data?**
- Customer ID → Orders table
- Customer Name → Orders table
- Customer Segment → Orders table

**What tables does the dataset have?**
- Orders (main table with everything)
- People (regional managers)
- Returns (returned orders)

**What tables will you create?**
- DateTable (for time intelligence)
- RFM_Calculations (for segmentation)

---

**This corrected guide eliminates the Customer table confusion. Follow this version for accurate implementation!** 🚀
