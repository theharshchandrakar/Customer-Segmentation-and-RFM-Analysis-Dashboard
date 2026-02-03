# WEEK-BY-WEEK ROADMAP - Global Superstore RFM Analysis (CORRECTED)

## 🎯 PROJECT OVERVIEW

**Duration:** 4 weeks (28 days)
**Goal:** Build complete RFM Customer Segmentation Dashboard in Power BI
**Dataset:** Global Superstore (Orders, People, Returns - NO separate Customer table!)

---

## 📅 WEEK 1: DATA FOUNDATION (Days 1-7)

### **Day 1: Project Setup & Dataset Download**

**Tasks:**
- [ ] Download Global Superstore from Kaggle
- [ ] Extract and open `Global_Superstore.xls`
- [ ] Verify 3 sheets exist: Orders, People, Returns
- [ ] Create project folder structure

**Verification:**
- Orders table has ~51,000 rows
- Customer ID column exists IN Orders table
- Orders table has columns: Order Date, Sales, Profit, Customer ID

**Time:** 1 hour

---

### **Day 2: Understand the Dataset Structure**

**Tasks:**
- [ ] Open Orders sheet - examine all 25 columns
- [ ] Identify customer columns: Customer ID, Customer Name, Segment
- [ ] Note: Customer data is IN Orders table, not separate table!
- [ ] Review People sheet (24 regional managers)
- [ ] Review Returns sheet (returned orders)

**Questions to answer:**
- How many unique customers? (use Excel: =COUNTA(UNIQUE(range)))
- Date range of orders? (2012-2015)
- What are the segments? (Consumer, Corporate, Home Office)

**Time:** 2 hours

---

### **Day 3-4: Load Data into Power BI**

**Tasks:**

**Day 3:**
- [ ] Open Power BI Desktop (download if needed)
- [ ] File → Get Data → Excel
- [ ] Select all 3 sheets: Orders, People, Returns
- [ ] Click "Transform Data"

**Day 4:**
- [ ] In Power Query - Orders table:
  - Change Order Date to Date type
  - Change Sales to Decimal Number
  - Change Profit to Decimal Number
  - Remove Row ID column
  - Remove Postal Code (often has errors)
- [ ] Verify Customer ID column exists (it's here!)
- [ ] Click "Close & Apply"

**Verification:**
- All 3 tables loaded successfully
- Orders table shows correct data types
- No errors in Applied Steps

**Time:** 3 hours

---

### **Day 5-6: Create DateTable**

**Tasks:**

**Day 5:**
- [ ] Go to Modeling → New Table
- [ ] Create DateTable with CALENDAR function
- [ ] Add Year, Month, Quarter columns
- [ ] Mark as Date Table

**DateTable DAX:**
```DAX
DateTable = 
CALENDAR(
    DATE(2012, 1, 1),
    DATE(2015, 12, 31)
)
```

**Add columns:**
```DAX
Year = YEAR(DateTable[Date])
Month = MONTH(DateTable[Date])
MonthName = FORMAT(DateTable[Date], "mmmm")
Quarter = "Q" & ROUNDUP(MONTH(DateTable[Date])/3, 0)
```

**Day 6:**
- [ ] Mark DateTable as Date Table
- [ ] Create relationship: Orders[Order Date] → DateTable[Date]
- [ ] Test: Create simple visual with Year and Sales

**Verification:**
- DateTable has 1,461 rows (2012-2015)
- Relationship created successfully
- Visual shows data by year

**Time:** 3 hours

---

### **Day 7: Create Base Relationships**

**Tasks:**
- [ ] Go to Model View
- [ ] Create relationship: Orders[Region] → People[Region]
- [ ] Create relationship: Orders[Order ID] → Returns[Order ID]
- [ ] Verify all 3 relationships work

**Final Model Check:**
- Orders table is center (fact table)
- DateTable connected via Order Date
- People connected via Region
- Returns connected via Order ID

**Deliverable:** Working data model with 4 tables and 3 relationships

**Time:** 1 hour

---

## 📊 WEEK 2: RFM CALCULATIONS (Days 8-14)

### **Day 8-9: Create RFM_Calculations Table**

**Tasks:**

**Day 8:**
- [ ] Understand RFM concept:
  - R = Recency (days since last purchase)
  - F = Frequency (number of orders)
  - M = Monetary (total revenue)
- [ ] Plan: Extract unique customers FROM Orders table

**Day 9:**
- [ ] Go to Modeling → New Table
- [ ] Create RFM_Calculations table using SUMMARIZE
- [ ] Verify table created with correct columns

**RFM_Calculations DAX:**
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

**Verification:**
- Table has 5 columns: Customer ID, Customer Name, Recency, Frequency, Monetary
- ~3,000-5,000 rows (unique customers)
- No blanks in Recency, Frequency, Monetary

**Time:** 4 hours

---

### **Day 10: Add R_Score Column**

**Tasks:**
- [ ] Understand scoring logic (1-5 scale)
- [ ] Right-click RFM_Calculations → New Column
- [ ] Create R_Score column
- [ ] Verify scores are 1-5

**R_Score DAX:**
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

**Verification:**
- R_Score column shows values 1-5
- No blanks or errors
- Most customers have score 1-2 (older dataset)

**Time:** 1 hour

---

### **Day 11: Add F_Score Column**

**Tasks:**
- [ ] Create F_Score column
- [ ] Verify scores distribution

**F_Score DAX:**
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

**Verification:**
- F_Score shows 1-5 values
- Most customers score 1-2 (low frequency)

**Time:** 1 hour

---

### **Day 12: Add M_Score Column**

**Tasks:**
- [ ] Create M_Score column
- [ ] Verify monetary scoring

**M_Score DAX:**
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

**Verification:**
- M_Score shows 1-5 values
- Distribution makes sense with Sales data

**Time:** 1 hour

---

### **Day 13-14: Add Segment Column**

**Tasks:**

**Day 13:**
- [ ] Understand 7 segment logic
- [ ] Create Segment calculated column
- [ ] Test with small sample

**Day 14:**
- [ ] Verify all 7 segments appear
- [ ] Check segment distribution
- [ ] Create test visual: Segment + Count of Customer ID

**Segment DAX:**
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

**Create Relationship:**
- [ ] Go to Model View
- [ ] Drag Orders[Customer ID] → RFM_Calculations[Customer ID]

**Verification:**
- All 7 segments show up
- Segment distribution reasonable
- Final Model View shows 5 tables total

**Deliverable:** Complete RFM_Calculations table with scores and segments

**Time:** 3 hours

---

## 📈 WEEK 3: CREATE MEASURES (Days 15-21)

### **Day 15-16: Core Business Measures**

**Tasks:**

**Day 15:**
```DAX
Total_Customers = DISTINCTCOUNT(Orders[Customer ID])
Total_Revenue = SUM(Orders[Sales])
Total_Orders = COUNTROWS(Orders)
```

**Day 16:**
```DAX
Average_Order_Value = 
DIVIDE([Total_Revenue], [Total_Orders], 0)

Average_Customer_Value = 
DIVIDE([Total_Revenue], [Total_Customers], 0)

Profit_Margin_Percent = 
VAR TotalProfit = SUM(Orders[Profit])
VAR TotalSales = [Total_Revenue]
RETURN
IF(TotalSales = 0, 0, DIVIDE(TotalProfit, TotalSales, 0))
```

**Test Each Measure:**
- Create card visual
- Drag measure onto card
- Verify value makes sense

**Time:** 3 hours

---

### **Day 17-18: RFM Aggregate Measures**

**Day 17:**
```DAX
Avg_Recency_Days = AVERAGE(RFM_Calculations[Recency])
Avg_Frequency = AVERAGE(RFM_Calculations[Frequency])
Avg_Monetary = AVERAGE(RFM_Calculations[Monetary])
```

**Day 18:**
```DAX
Champions_Count = 
CALCULATE(
    DISTINCTCOUNT(RFM_Calculations[Customer ID]),
    RFM_Calculations[Segment] = "Champions"
)

Champions_Revenue = 
CALCULATE(
    [Total_Revenue],
    RFM_Calculations[Segment] = "Champions"
)

Champions_Revenue_Pct = 
DIVIDE([Champions_Revenue], [Total_Revenue], 0)
```

**Test:**
- Create cards for Champions metrics
- Verify counts and percentages

**Time:** 3 hours

---

### **Day 19-20: Segment-Specific Measures**

**Day 19:**
```DAX
AtRisk_Count = 
CALCULATE(
    DISTINCTCOUNT(RFM_Calculations[Customer ID]),
    RFM_Calculations[Segment] = "At-Risk"
)

Lost_Count = 
CALCULATE(
    DISTINCTCOUNT(RFM_Calculations[Customer ID]),
    RFM_Calculations[Segment] = "Lost Customers"
)

New_Customers_Count = 
CALCULATE(
    DISTINCTCOUNT(RFM_Calculations[Customer ID]),
    RFM_Calculations[Segment] = "New Customers"
)
```

**Day 20:**
- [ ] Create measures for remaining segments
- [ ] Test all measures in visuals
- [ ] Organize measures in folders

**Time:** 3 hours

---

### **Day 21: Measure Organization & Testing**

**Tasks:**
- [ ] Create measure folders:
  - "Core Metrics"
  - "RFM Averages"
  - "Segment Metrics"
- [ ] Test each measure in appropriate visual
- [ ] Document measure purposes
- [ ] Create calculation reference sheet

**Deliverable:** 15+ working measures organized in folders

**Time:** 2 hours

---

## 🎨 WEEK 4: BUILD DASHBOARD (Days 22-28)

### **Day 22: Dashboard Planning**

**Tasks:**
- [ ] Sketch dashboard layout on paper
- [ ] Decide on 6-8 key visuals
- [ ] Plan color scheme
- [ ] Identify KPIs for top of page

**Suggested Layout:**
```
+--------------------------------------------------+
|  KPI Cards: Total Customers | Revenue | Orders  |
+--------------------------------------------------+
|  Segment Distribution (Donut) | RFM Heatmap      |
+--------------------------------------------------+
|  Revenue by Segment (Bar) | Customers by Segment|
+--------------------------------------------------+
|  Revenue Trend (Line) | Top Customers (Table)   |
+--------------------------------------------------+
```

**Time:** 2 hours

---

### **Day 23-24: Build Core Visuals**

**Day 23:**
- [ ] Add KPI cards at top
- [ ] Create segment distribution donut chart
- [ ] Create revenue by segment bar chart

**Day 24:**
- [ ] Create RFM scatter plot (R vs M, size=F)
- [ ] Create customers by segment column chart
- [ ] Add slicers: Year, Segment, Region

**Time:** 4 hours

---

### **Day 25-26: Advanced Visuals**

**Day 25:**
- [ ] Create revenue trend line chart (by month)
- [ ] Create top 10 customers table
- [ ] Create segment performance matrix

**Day 26:**
- [ ] Create geographic map (if needed)
- [ ] Create segment comparison table
- [ ] Add conditional formatting

**Time:** 4 hours

---

### **Day 27: Dashboard Formatting**

**Tasks:**
- [ ] Apply consistent color scheme
- [ ] Format all titles and labels
- [ ] Align and size all visuals
- [ ] Add background and borders
- [ ] Configure slicer interactions
- [ ] Test all filters and interactions

**Time:** 3 hours

---

### **Day 28: Testing & Documentation**

**Tasks:**
- [ ] Test all visuals with different filters
- [ ] Verify calculations are correct
- [ ] Check for data quality issues
- [ ] Create user guide document
- [ ] Export dashboard as PDF
- [ ] Save .pbix file

**Final Checklist:**
- [ ] All measures working
- [ ] All relationships correct
- [ ] No errors in visuals
- [ ] Dashboard is intuitive
- [ ] Performance is good (loads quickly)

**Deliverable:** Complete RFM Customer Segmentation Dashboard

**Time:** 3 hours

---

## ✅ FINAL MODEL STRUCTURE

**Your Power BI Model will have:**

```
5 TABLES:
1. Orders (fact table - 51,290 rows)
   - Contains: Customer ID, Customer Name, Sales, Dates, etc.
   
2. DateTable (dimension - 1,461 rows)
   - Calendar table for time intelligence
   
3. People (dimension - 24 rows)
   - Regional sales managers
   
4. Returns (dimension - 1,079 rows)
   - Returned orders tracking
   
5. RFM_Calculations (analysis table - ~3,000-5,000 rows)
   - Customer-level RFM scores and segments

4 RELATIONSHIPS:
- Orders[Order Date] → DateTable[Date]
- Orders[Region] → People[Region]
- Orders[Order ID] → Returns[Order ID]
- Orders[Customer ID] → RFM_Calculations[Customer ID]

15+ MEASURES:
- Business KPIs (Revenue, Customers, Orders)
- RFM Averages (Avg Recency, Frequency, Monetary)
- Segment Metrics (Champions, At-Risk, Lost counts)
```

---

## 🎯 SUCCESS METRICS

**By end of Week 1:**
- ✅ All data loaded correctly
- ✅ DateTable created and marked
- ✅ Base relationships established
- ✅ No Customer table confusion

**By end of Week 2:**
- ✅ RFM_Calculations table working
- ✅ All score columns (R, F, M) calculated
- ✅ 7 segments identified
- ✅ Final relationship created

**By end of Week 3:**
- ✅ 15+ measures created
- ✅ All measures tested
- ✅ Measures organized in folders

**By end of Week 4:**
- ✅ Complete dashboard with 6-8 visuals
- ✅ Formatted and professional looking
- ✅ All interactions working
- ✅ Project documented

---

## 🚨 KEY REMINDERS

**1. NO CUSTOMER TABLE!**
- Customer data is IN Orders table
- Customer ID is in Orders[Customer ID]
- RFM_Calculations is created FROM Orders

**2. ONLY 5 TABLES TOTAL**
- Orders (from dataset)
- People (from dataset)
- Returns (from dataset)
- DateTable (you create)
- RFM_Calculations (you create)

**3. VERIFY AS YOU GO**
- After each DAX formula, check results
- Test measures in visuals immediately
- Save your .pbix file frequently

**4. COMMON PITFALLS**
- ❌ Looking for Customer table (doesn't exist)
- ❌ Creating 6 tables (only need 5)
- ❌ Wrong column references
- ❌ Not testing formulas before moving on

---

## 📚 RESOURCES

**DAX Help:**
- DAX Formatter: https://www.daxformatter.com/
- DAX Guide: https://dax.guide/

**Power BI Help:**
- Official Docs: https://docs.microsoft.com/power-bi/
- Community: https://community.powerbi.com/

**Dataset:**
- Kaggle: https://www.kaggle.com/datasets/apoorvaappz/global-super-store-dataset

---

**Follow this week-by-week roadmap exactly as written. By Day 28, you'll have a complete, professional RFM Customer Segmentation Dashboard!** 🎉
