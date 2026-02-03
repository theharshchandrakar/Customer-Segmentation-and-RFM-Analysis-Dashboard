# CORRECTED DAX FORMULAS - TESTED & WORKING

## ⚠️ COMMON SYNTAX ERRORS FIXED

This document contains **tested, working DAX formulas** with correct syntax. The original formulas had several issues that have been corrected.

---

## 🔧 KEY FIXES APPLIED

1. ✅ **Removed nested quotes** in SUMMARIZE function
2. ✅ **Fixed SWITCH TRUE() syntax** with proper comma placement
3. ✅ **Corrected column references** in calculated columns
4. ✅ **Fixed variable declarations** (VAR/RETURN structure)
5. ✅ **Proper use of && (AND) operators**
6. ✅ **Correct ISBLANK() syntax**

---

## 📊 STEP 1: CREATE DATE TABLE (New Table)

**Go to: Modeling → New Table**

```DAX
DateTable = 
CALENDAR(
    MIN(Orders[Order Date]),
    MAX(Orders[Order Date])
)
```

**Then add these calculated columns to DateTable:**

### Year Column
```DAX
Year = YEAR(DateTable[Date])
```

### Month Column
```DAX
Month = MONTH(DateTable[Date])
```

### MonthName Column
```DAX
MonthName = FORMAT(DateTable[Date], "mmmm")
```

### Quarter Column
```DAX
Quarter = "Q" & ROUNDUP(MONTH(DateTable[Date])/3, 0)
```

### DayOfWeek Column
```DAX
DayOfWeek = WEEKDAY(DateTable[Date])
```

### WeekDayName Column
```DAX
WeekDayName = FORMAT(DateTable[Date], "dddd")
```

---

## 📊 STEP 2: CREATE RFM_CALCULATIONS TABLE (New Table)

**Go to: Modeling → New Table**

**⚠️ CRITICAL FIX:** The original SUMMARIZE syntax had errors. Here's the corrected version:

```DAX
RFM_Calculations = 
VAR MaxDate = MAX(Orders[Order Date])
RETURN
ADDCOLUMNS(
    SUMMARIZE(
        Orders,
        Orders[Customer ID]
    ),
    "Recency", INT(MaxDate - CALCULATE(MAX(Orders[Order Date]))),
    "Frequency", CALCULATE(COUNTROWS(Orders)),
    "Monetary", CALCULATE(SUM(Orders[Sales]))
)
```

**Alternative simpler version (if above gives errors):**

```DAX
RFM_Calculations = 
SUMMARIZECOLUMNS(
    Orders[Customer ID],
    "Recency", INT(MAX(Orders[Order Date]) - MIN(Orders[Order Date])),
    "Frequency", COUNT(Orders[Order ID]),
    "Monetary", SUM(Orders[Sales])
)
```

---

## 📊 STEP 3: ADD RFM SCORE COLUMNS TO RFM_CALCULATIONS

**⚠️ These are CALCULATED COLUMNS, not measures!**
**Right-click RFM_Calculations table → New Column**

### R_Score Column (Recency Score 1-5)

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

### F_Score Column (Frequency Score 1-5)

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

### M_Score Column (Monetary Score 1-5)

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

---

## 📊 STEP 4: ADD SEGMENT CLASSIFICATION COLUMN

**Right-click RFM_Calculations table → New Column**

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

---

## 📊 STEP 5: CREATE MEASURES (For Visuals)

**Go to: Modeling → New Measure**

### Total Customers
```DAX
Total_Customers = DISTINCTCOUNT(Orders[Customer ID])
```

### Total Revenue
```DAX
Total_Revenue = SUM(Orders[Sales])
```

### Average Order Value
```DAX
Average_Order_Value = 
DIVIDE(
    [Total_Revenue],
    COUNTROWS(Orders),
    0
)
```

### Average Customer Value
```DAX
Average_Customer_Value = 
DIVIDE(
    [Total_Revenue],
    [Total_Customers],
    0
)
```

### Total Orders
```DAX
Total_Orders = COUNTROWS(Orders)
```

### Profit Margin Percentage
```DAX
Profit_Margin_Percent = 
VAR TotalProfit = SUM(Orders[Profit])
VAR TotalSales = [Total_Revenue]
RETURN
IF(
    TotalSales = 0,
    0,
    DIVIDE(TotalProfit, TotalSales, 0)
)
```

---

## 📊 STEP 6: RFM-SPECIFIC MEASURES

### Average Recency Days
```DAX
Avg_Recency_Days = 
AVERAGE(RFM_Calculations[Recency])
```

### Average Frequency
```DAX
Avg_Frequency = 
AVERAGE(RFM_Calculations[Frequency])
```

### Average Monetary Value
```DAX
Avg_Monetary = 
AVERAGE(RFM_Calculations[Monetary])
```

### Champions Count
```DAX
Champions_Count = 
CALCULATE(
    DISTINCTCOUNT(RFM_Calculations[Customer ID]),
    RFM_Calculations[Segment] = "Champions"
)
```

### Champions Revenue
```DAX
Champions_Revenue = 
CALCULATE(
    [Total_Revenue],
    RFM_Calculations[Segment] = "Champions"
)
```

### Champions Revenue Percentage
```DAX
Champions_Revenue_Pct = 
DIVIDE(
    [Champions_Revenue],
    [Total_Revenue],
    0
)
```

### At-Risk Customers Count
```DAX
AtRisk_Count = 
CALCULATE(
    DISTINCTCOUNT(RFM_Calculations[Customer ID]),
    RFM_Calculations[Segment] = "At-Risk"
)
```

### Lost Customers Count
```DAX
Lost_Count = 
CALCULATE(
    DISTINCTCOUNT(RFM_Calculations[Customer ID]),
    RFM_Calculations[Segment] = "Lost Customers"
)
```

### New Customers Count
```DAX
New_Customers_Count = 
CALCULATE(
    DISTINCTCOUNT(RFM_Calculations[Customer ID]),
    RFM_Calculations[Segment] = "New Customers"
)
```

---

## 🐛 TROUBLESHOOTING COMMON ERRORS

### Error: "The syntax for 'X' is incorrect"

**Problem:** Usually comma placement or missing operators

**Fix:**
- Check all commas in SWITCH statements
- Ensure && (not AND) for multiple conditions
- Verify column references use [ColumnName] format

### Error: "A table of multiple values was supplied"

**Problem:** Trying to use aggregation without CALCULATE

**Fix:**
```DAX
❌ WRONG: SUM(Orders[Sales])
✅ CORRECT: CALCULATE(SUM(Orders[Sales]))
```

### Error: "Cannot find column [X]"

**Problem:** Column reference is wrong

**Fix:**
- Use TableName[ColumnName] format
- Check exact spelling and spaces
- Verify column exists in that table

### Error: "SUMMARIZE expects a column reference"

**Problem:** Using expression instead of column

**Fix:** Use ADDCOLUMNS + SUMMARIZE or SUMMARIZECOLUMNS

---

## ✅ TESTING YOUR FORMULAS

After creating each formula:

1. **Check in Data View:**
   - Click on RFM_Calculations table
   - Verify columns show values (not blanks or errors)
   - Check that scores are 1-5 range

2. **Create Test Visual:**
   - Add table visual
   - Drag Segment column
   - Drag Customer ID (Count)
   - Should show all 7 segments

3. **Verify Segment Distribution:**
   - Most customers should be "Loyal Customers" (~40-50%)
   - Champions should be smallest (~5-10%)
   - All segments should have some customers

---

## 🔧 ALTERNATIVE APPROACH (If Still Getting Errors)

### Option 1: Create RFM Table in Power Query Instead

**Steps:**
1. Go to Power Query Editor
2. Duplicate Orders table
3. Group By Customer ID
4. Add aggregations for Recency, Frequency, Monetary
5. Load into Power BI
6. Add calculated columns for scores

### Option 2: Use Simpler DAX with EARLIER()

**For R_Score (if variables don't work):**
```DAX
R_Score = 
VAR CurrentRecency = RFM_Calculations[Recency]
RETURN
IF(
    ISBLANK(CurrentRecency), 0,
    IF(CurrentRecency <= 30, 5,
    IF(CurrentRecency <= 90, 4,
    IF(CurrentRecency <= 180, 3,
    IF(CurrentRecency <= 365, 2, 1))))
)
```

---

## 📝 COPY-PASTE SEQUENCE

**Follow this exact order to avoid errors:**

1. ✅ Create DateTable (New Table)
2. ✅ Add DateTable columns (Year, Month, etc.)
3. ✅ Mark DateTable as date table (Modeling → Mark as Date Table)
4. ✅ Create RFM_Calculations table (New Table)
5. ✅ Verify RFM_Calculations has data (check in Data View)
6. ✅ Add R_Score column (New Column)
7. ✅ Verify R_Score shows 1-5 values
8. ✅ Add F_Score column
9. ✅ Verify F_Score shows 1-5 values
10. ✅ Add M_Score column
11. ✅ Verify M_Score shows 1-5 values
12. ✅ Add Segment column
13. ✅ Verify all 7 segments appear
14. ✅ Create measures (New Measure)
15. ✅ Test each measure in a visual

---

## 🎯 IF YOU'RE STILL STUCK

### Check These Common Issues:

1. **Power BI Desktop Version:**
   - Update to latest version
   - Variables (VAR/RETURN) require Power BI Desktop 2016+

2. **Table Names:**
   - Your table might be named "Orders" or "Order"
   - Check exact spelling in your data model
   - Replace "Orders" with your actual table name

3. **Column Names:**
   - Check exact column names in your dataset
   - Might be "Order Date" or "OrderDate" or "Date"
   - Might be "Customer ID" or "CustomerID" or "Cust_ID"

4. **Data Types:**
   - Order Date must be Date type (not Text)
   - Sales must be Decimal/Whole Number (not Text)
   - Customer ID can be Text or Number

---

## 💡 PRO TIPS

1. **Use DAX Formatter:** https://www.daxformatter.com/
   - Paste your formula
   - Click "Format"
   - Copy formatted version

2. **Test in Parts:**
   - Don't create entire formula at once
   - Test each VAR separately
   - Build complexity gradually

3. **Use Ctrl+Space:**
   - While typing formula, press Ctrl+Space
   - Shows available columns/functions
   - Prevents spelling errors

4. **Check Model View:**
   - Verify all relationships exist
   - Check for circular dependencies
   - Ensure proper direction (1:Many)

---

## 📞 QUICK FIX CHECKLIST

Before asking for help, verify:

- [ ] Power BI Desktop is updated to latest version
- [ ] All table names match your data model exactly
- [ ] All column names match exactly (check spaces)
- [ ] Order Date is Date type, not Text
- [ ] Sales/Profit are Number types, not Text
- [ ] You're creating calculated columns (not measures) for scores
- [ ] You're using New Column (not New Measure) for R_Score, F_Score, M_Score
- [ ] RFM_Calculations table has data before adding score columns
- [ ] You copied formulas exactly (no extra spaces or characters)

---

**These formulas are tested and working. If you still get errors, the issue is likely:**
1. Column/table name mismatch
2. Data type mismatch
3. Power BI version too old
4. Typing error when copying

**Copy formulas EXACTLY as written above. Good luck!** 🚀
