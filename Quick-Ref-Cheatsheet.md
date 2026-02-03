# Quick Reference Cheat Sheet - RFM Dashboard Project

## 🎯 At-a-Glance Project Summary

**Project:** Customer Segmentation & RFM Analysis Dashboard  
**Duration:** 4-5 weeks  
**Tools:** Power BI Desktop  
**Difficulty:** Beginner-Intermediate  
**Skills Gained:** Power BI, DAX, ETL, Customer Analytics  

---

## 📋 QUICK CHECKLIST

### ✅ Week 1 (Foundation)
- [ ] Download Global Superstore dataset (Kaggle)
- [ ] Load 3 tables (Orders, People, Returns) into Power BI
- [ ] Clean data in Power Query
- [ ] Create DateTable with calculated columns
- [ ] Establish relationships (4 relationships total)
- [ ] Verify relationships in Model View

### ✅ Week 2 (RFM Calculations)
- [ ] Create RFM_Calculations table with SUMMARIZE
- [ ] Add R_Score column (Recency 1-5 scoring)
- [ ] Add F_Score column (Frequency 1-5 scoring)
- [ ] Add M_Score column (Monetary 1-5 scoring)
- [ ] Add Segment column (7 segment types)
- [ ] Verify all segments present in data

### ✅ Week 3 (Dashboard Pages 1-3)
- [ ] Page 1: Executive Summary (KPIs, donut charts, slicers)
- [ ] Page 2: RFM Matrix (scatter plots, distribution)
- [ ] Page 3: Segment Details (table, map, category analysis)
- [ ] Add slicers (Date, Region, Category)
- [ ] Format colors by segment

### ✅ Week 4 (Pages 4-5 & Polish)
- [ ] Page 4: Trends & Growth (line, area, column charts)
- [ ] Page 5: Insights & Recommendations (text, table)
- [ ] Add navigation buttons
- [ ] Professional formatting
- [ ] Performance optimization
- [ ] Test all interactivity

### ✅ Week 5 (Publish & Document)
- [ ] Publish to Power BI Service
- [ ] Create GitHub repository
- [ ] Write comprehensive README
- [ ] Add screenshots
- [ ] Create LinkedIn post
- [ ] Update resume

---

## 🔑 KEY CONCEPTS

### RFM Metrics
```
R (Recency):     Days since last purchase       [0-365+]
F (Frequency):   # of purchases in 12 months    [1-50+]
M (Monetary):    Total $ spent                  [$100-$10,000+]
```

### RFM Scoring
```
Score 5 = Best performers
Score 4 = High performers
Score 3 = Medium performers
Score 2 = Low performers
Score 1 = Very low performers
```

### 7 Customer Segments
```
1. Champions          → R:High, F:High, M:High       [Best customers]
2. Loyal Customers    → R:Med,  F:Med,  M:Med        [Regular buyers]
3. Potential Loyalists→ R:High, F:Med,  M:Med        [Soon loyal?]
4. At-Risk           → R:Low,  F:High, M:High       [Churn warning]
5. Need Attention    → R:Low,  F:Med,  M:Low        [Engagement needed]
6. New Customers     → R:High, F:Low,  M:Low        [Onboard]
7. Lost Customers    → R:VLow, F:Low,  M:Low        [Try to win back]
```

---

## 💻 POWER BI QUICK COMMANDS

### Create New Table
```
Modeling → New Table → Paste DAX formula
```

### Create Calculated Column
```
Right-click Table → New Column → Write formula
```

### Create Measure
```
Modeling → New Measure → Write formula (for visuals)
```

### Create Relationship
```
Model View → Drag column to related table
OR
Manage Relationships → New → Select columns
```

### Create Visual
```
Insert → Choose visual type → Add fields → Format
```

### Add Slicer
```
Insert → Slicer → Choose field → Configure
```

---

## 📊 ESSENTIAL DAX FORMULAS (Quick Copy-Paste)

### RFM_Calculations Table
```DAX
RFM_Calculations = 
VAR MaxDate = MAX(Orders[Order Date])
RETURN SUMMARIZE(Orders, Orders[Customer ID],
  "Recency", INT(MaxDate - MAX(Orders[Order Date])),
  "Frequency", COUNTA(Orders[Order ID]),
  "Monetary", SUM(Orders[Sales]))
```

### R_Score (Add Column)
```DAX
R_Score = IF(ISBLANK([Recency]), 0,
  SWITCH(TRUE(), [Recency]<=30, 5, [Recency]<=90, 4,
  [Recency]<=180, 3, [Recency]<=365, 2, 1))
```

### F_Score (Add Column)
```DAX
F_Score = IF(ISBLANK([Frequency]), 0,
  SWITCH(TRUE(), [Frequency]>=12, 5, [Frequency]>=8, 4,
  [Frequency]>=5, 3, [Frequency]>=2, 2, 1))
```

### M_Score (Add Column)
```DAX
M_Score = IF(ISBLANK([Monetary]), 0,
  SWITCH(TRUE(), [Monetary]>=5000, 5, [Monetary]>=2000, 4,
  [Monetary]>=500, 3, [Monetary]>=100, 2, 1))
```

### Segment (Add Column)
```DAX
Segment = SWITCH(TRUE(),
  AND([R_Score]>=4, [F_Score]>=4, [M_Score]>=4), "Champions",
  AND([R_Score]>=3, [F_Score]>=3, [M_Score]>=3), "Loyal Customers",
  AND([R_Score]<3, [F_Score]>=4, [M_Score]>=3), "At-Risk",
  AND([R_Score]<2, [F_Score]<3, [M_Score]<3), "Lost Customers",
  AND([R_Score]>=4, [F_Score]<3, [M_Score]<3), "New Customers",
  "Other")
```

### Key Measures
```DAX
Total_Customers = DISTINCTCOUNT(Orders[Customer ID])
Total_Revenue = SUM(Orders[Sales])
Average_Order_Value = [Total_Revenue]/COUNTA(Orders[Order ID])
Profit_Margin = SUM(Orders[Profit])/[Total_Revenue]
```

---

## 🎨 COLOR SCHEME (Segment Colors)

```
Champions:          Gold/Green      #00B050
Loyal Customers:    Blue            #4472C4
Potential Loyalists: Orange         #FFC000
At-Risk:            Red             #C00000
New Customers:      Purple          #70AD47
Need Attention:     Yellow          #FFC000
Lost Customers:     Gray            #808080
```

---

## 📈 DASHBOARD PAGE STRUCTURE

### Page 1: Executive Summary
```
Top: 4 KPI cards (Total Customers, Revenue, AOV, Profit %)
Left: Donut - Customers by Segment
Center: Column - Segment Performance
Right: Table - Segment Metrics
Top: 3 Slicers (Date, Region, Category)
```

### Page 2: RFM Matrix
```
Main: Scatter - Frequency vs Monetary (bubble size=Recency)
Secondary: Scatter - Recency vs Frequency
Bottom: Stacked Bar - RFM Score Distribution
Table: Segment Summary with R/F/M averages
```

### Page 3: Segment Details
```
Top: Segment Selector (Button Slicer)
Below: 4 KPIs (Customers, Revenue, AOV, % of Total)
Center: Table - Detailed customer records
Right: Map - Geographic distribution
Bottom: Chart - Product preferences by segment
```

### Page 4: Trends & Growth
```
Top: Line Chart - Monthly revenue by segment (with forecast)
Middle Left: Column - Customer count by segment (stacked)
Middle Right: Column - Churn by month
Bottom: Area - New vs Returning customers
```

### Page 5: Insights & Recommendations
```
Top: Text boxes - 4-5 key insights with percentages
Bottom: Table - Segment → Issue → Action → Expected Impact
```

---

## 🐛 TROUBLESHOOTING QUICK FIXES

| Problem | Solution |
|---------|----------|
| Data not loading | Check Excel file format, verify columns exist |
| Relationships broken | Go to Model view, check columns match |
| RFM table empty | Verify Orders[Order Date] and Customer ID exist |
| Scores all showing 1 | Check IF ISBLANK logic, verify data types |
| Segments not appearing | Verify all R/F/M scores calculated first |
| Visual blank | Check slicer filter, verify field in visual |
| Slow performance | Reduce visual count, use SUMMARIZE, optimize DAX |
| Mobile view broken | Use mobile layout view, reorganize vertically |

---

## 📱 SEGMENT QUICK REFERENCE

**CHAMPIONS (Best Customers)**
- Last purchase: Within 30 days
- Purchase frequency: 12+ times/year
- Total spent: $5,000+
- Action: VIP program, loyalty rewards, exclusive offers

**LOYAL CUSTOMERS (Regular Buyers)**
- Last purchase: Within 90 days
- Purchase frequency: 5-11 times/year
- Total spent: $500-$5,000
- Action: Cross-sell, upsell, thank you emails

**AT-RISK (Used to Buy Frequently)**
- Last purchase: >90 days ago
- Purchase frequency: 8+ times historically
- Total spent: $2,000+
- Action: Win-back campaign, special offer

**NEW CUSTOMERS (Recent, Unproven)**
- Last purchase: Within 30 days
- Purchase frequency: 1-2 times
- Total spent: $100-$500
- Action: Onboard, nurture, educational content

**LOST CUSTOMERS (Haven't Bought in Long Time)**
- Last purchase: >365 days ago
- Purchase frequency: 1-3 times
- Total spent: <$500
- Action: Reactivation campaign, discount offer

---

## 📊 EXPECTED DATA DISTRIBUTION (Global Superstore)

```
Total Customers:           ~10,000
Total Orders:             ~50,000
Total Revenue:         ~$2-3 Million
Date Range:            2012-2015 (4 years)

Champion customers:        ~5-10% (450-1000)
  Contributing:           40-50% of revenue

Loyal customers:           ~40-50% (4000-5000)
  Contributing:           30-40% of revenue

At-Risk customers:         ~10-15% (1000-1500)
  Contributing:           10-15% of revenue

New customers:             ~10-15% (1000-1500)
  Contributing:           5-10% of revenue

Lost customers:            ~5-10% (500-1000)
  Contributing:           0-2% of revenue
```

---

## 🎯 INTERVIEW TALKING POINTS

**When asked about project:**
- "Analyzed 10K+ customers and 50K+ orders using RFM methodology"
- "Built interactive Power BI dashboard with 5 pages and 20+ visualizations"
- "Identified Champions (5% of base) generating 45% of revenue"
- "Detected 1000+ at-risk customers for targeted retention campaigns"
- "Used advanced DAX formulas including SWITCH, CALCULATE, FILTER"

**Technical depth:**
- "Modeled star schema with 6 tables and proper relationships"
- "Created RFM calculation table using SUMMARIZE and aggregations"
- "Implemented dynamic segmentation logic using nested IF/SWITCH"
- "Optimized dashboard performance for 50K+ record dataset"

**Business impact:**
- "Segmentation enables 30% improvement in marketing ROI"
- "Early churn detection allows proactive retention strategies"
- "VIP program targeting increases customer lifetime value"

---

## 📚 RESOURCES TO BOOKMARK

**Learning:**
- DAX Functions: https://dax.guide/
- Power BI Docs: https://learn.microsoft.com/en-us/power-bi/
- Kaggle: https://www.kaggle.com/

**Tools:**
- Power BI Desktop: https://powerbi.microsoft.com/
- DAX Formatter: https://www.daxformatter.com/
- Markdown Editor: https://www.markdowntutorial.com/

**Inspiration:**
- Power BI Gallery: https://app.powerbi.com/community/
- GitHub: Search "Power BI RFM Dashboard"

---

## ⏱️ TIME MANAGEMENT

**Recommended Daily Schedule:**
```
Day 1-7 (Week 1):    2-3 hours/day    = 14-21 hours total
Day 8-14 (Week 2):   2-3 hours/day    = 14-21 hours total
Day 15-21 (Week 3):  3-4 hours/day    = 21-28 hours total
Day 22-28 (Week 4):  3-4 hours/day    = 21-28 hours total
Day 29-35 (Week 5):  2-3 hours/day    = 14-21 hours total

TOTAL: ~85-120 hours over 5 weeks
```

**Breaking it down:**
- Data preparation: 20-25 hours
- RFM calculations: 15-20 hours
- Dashboard design: 30-40 hours
- Polish & documentation: 20-30 hours
- Deployment: 5-10 hours

---

## 🚀 SUCCESS INDICATORS

When project is complete, you should be able to:

✅ Explain RFM methodology and scoring logic  
✅ Walk through dashboard pages and findings  
✅ Show DAX formulas and explain logic  
✅ Discuss business insights and recommendations  
✅ Demo interactive filtering and drill-through  
✅ Explain segmentation business impact  
✅ Answer follow-up technical questions  

---

## 💡 BONUS ENHANCEMENTS

Once basic dashboard is complete:

1. **Add ML Model:** Use Python/R to predict churn for At-Risk segment
2. **Real-time Data:** Connect to live SQL database instead of static Excel
3. **Automated Alerts:** Set up alerts when customers move to At-Risk
4. **Mobile App:** Build mobile-first dashboard layout
5. **Advanced Analytics:** Add clustering, propensity modeling
6. **Competitive Analysis:** Compare metrics across regions/categories

---

## 🎓 WHAT RECRUITERS WANT TO SEE

From this project, showcase:

✅ **Technical Excellence:** Clean code, proper relationships, optimized queries  
✅ **Business Understanding:** Clear insights, actionable recommendations  
✅ **Communication:** Well-documented, professional presentation  
✅ **Problem-Solving:** Handled edge cases, optimized performance  
✅ **Portfolio Quality:** Production-ready dashboard, GitHub repo, README  

---

## 📞 QUICK REFERENCE LINKS

**Dataset:** https://www.kaggle.com/datasets/shekpaul/global-superstore/data  
**Power BI Desktop:** https://powerbi.microsoft.com/en-us/desktop/  
**GitHub Repo:** https://github.com/[yourname]/customer-segmentation-rfm-dashboard  
**Power BI Service:** https://app.powerbi.com/  

---

**Print this sheet! Keep it nearby while building. Good luck! 🎯**
