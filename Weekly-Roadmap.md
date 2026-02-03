# Weekly Implementation Roadmap - Customer Segmentation & RFM Dashboard

## 📅 4-5 Week Project Timeline

Complete step-by-step breakdown of what to do each week to stay on track.

---

## WEEK 1: Foundation & Data Preparation

### Days 1-2: Environment Setup & Dataset Download
**Time Allocation:** 4-6 hours

**Tasks:**
- [ ] Install Power BI Desktop (latest version)
- [ ] Create Kaggle account (if not already done)
- [ ] Download Global Superstore dataset
  - Download link: https://www.kaggle.com/datasets/shekpaul/global-superstore/data
  - File: Global_Superstore.xls (~18MB)
- [ ] Extract/save dataset in project folder
- [ ] Create GitHub repository for project
  - Repo name: `customer-segmentation-rfm-dashboard`
  - Initialize with README.md

**Deliverables:**
- ✅ Folder structure created locally
- ✅ Dataset downloaded and verified (3 sheets: Orders, People, Returns)
- ✅ GitHub repo initialized

**Time Check:** You should finish by Day 2 EOD

---

### Days 3-4: Load Data & Initial Transformation
**Time Allocation:** 6-8 hours

**Tasks:**
- [ ] Open Power BI Desktop
- [ ] Load data from Excel
  1. File → Get Data → Excel
  2. Select Global_Superstore.xls
  3. Load Orders, People, Returns tables
  4. Click Transform Data

- [ ] In Power Query Editor:
  1. **Orders Table:**
     - Check all columns are loaded (29 columns)
     - Remove duplicate rows
     - Set correct data types:
       * Order Date: Date
       * Sales: Decimal
       * Quantity: Whole Number
       * Profit: Decimal
     - Create new column: `Order Year` = YEAR([Order Date])
     - Create new column: `Order Month` = MONTH([Order Date])
     - Remove unnecessary columns (Postal Code, etc. - keep essential ones)

  2. **People Table:**
     - Verify Region and Manager columns
     - Remove duplicates

  3. **Returns Table:**
     - Check Order ID and Reason columns
     - Remove any blanks

- [ ] Load all tables into Power BI model
  1. Click Close & Apply
  2. Verify all 3 tables appear in Fields pane

**Deliverables:**
- ✅ Data loaded into Power BI
- ✅ Data types corrected
- ✅ Duplicate rows removed
- ✅ All 3 tables visible in model

**Common Issues:**
- ❌ Columns not showing? Scroll right in Power Query preview
- ❌ Data type errors? Right-click column → Change Type
- ❌ Memory issues? Close other applications, restart Power BI

**Time Check:** You should finish by Day 4 EOD

---

### Days 5-7: Relationships & Date Table
**Time Allocation:** 4-6 hours

**Tasks:**
- [ ] Create Date Table (in Power BI, not Power Query)
  1. Modeling tab → New Table
  2. Paste DAX formula:
  ```
  DateTable = CALENDAR(MIN(Orders[Order Date]), MAX(Orders[Order Date]))
  ```
  3. Add calculated columns (see DAX guide for all formulas):
     - Year, Month, MonthName, Quarter, DayOfWeek, etc.

- [ ] Set relationships in Model View
  1. View → Model
  2. Create relationship: Orders[Customer ID] → Customer[Customer ID]
     (Right-click Orders table → Manage Relationships)
  3. Create relationship: Orders[Order Date] → DateTable[Date]
  4. Create relationship: Orders[Region] → People[Region]
  5. Create relationship: Returns[Order ID] → Orders[Order ID]

- [ ] Verify relationships
  1. Check Model View visually
  2. Ensure all relationships are 1:Many (except date)
  3. Look for any circular dependencies (shown in red)

- [ ] Test relationships
  1. Go to Report View
  2. Create sample table visual with fields from different tables
  3. Verify data displays correctly (no errors)

**Deliverables:**
- ✅ DateTable created with all calculated columns
- ✅ All relationships established correctly
- ✅ No circular dependencies
- ✅ Test visual shows correct data

**Time Check:** Finish by EOW (Friday)

**End of Week 1 Checklist:**
- ✅ Data loaded into Power BI
- ✅ Data cleaned and types set
- ✅ Date table created
- ✅ Relationships established
- ✅ Model validated with test visual

---

## WEEK 2: RFM Calculations & Segmentation

### Days 8-9: Create RFM Calculation Table
**Time Allocation:** 4-6 hours

**Tasks:**
- [ ] Create RFM_Calculations table
  1. Modeling → New Table
  2. Paste DAX formula from DAX guide (RFM_Calculations table)
  3. Let it run (may take 30 seconds for large dataset)
  4. Verify table shows all customer records

- [ ] Check RFM_Calculations table
  1. Click on table in Fields pane
  2. View in Model
  3. Should show: Customer ID, Recency, Frequency, Monetary columns

**Deliverables:**
- ✅ RFM_Calculations table created
- ✅ Table contains all unique customers
- ✅ Recency, Frequency, Monetary values calculated

**Time Check:** Finish by Day 9 EOD

---

### Days 10-11: Add RFM Scoring Formulas
**Time Allocation:** 4-5 hours

**Tasks:**
- [ ] Add calculated columns to RFM_Calculations table
  
  1. Right-click RFM_Calculations → New Column
  2. Add R_Score column (Recency scoring formula from DAX guide)
     - Formula assigns 1-5 score based on days since purchase
  3. Test: You should see scores 1-5 in new column
  
  4. Add F_Score column (Frequency scoring)
     - Formula assigns 1-5 score based on purchase count
  5. Test: You should see scores 1-5
  
  6. Add M_Score column (Monetary scoring)
     - Formula assigns 1-5 score based on total spent
  7. Test: You should see scores 1-5

- [ ] Verify scoring logic
  1. Sort RFM_Calculations table by Recency ascending
  2. Check: Most recent purchases (low days) should have R_Score=5
  3. Sort by Frequency descending
  4. Check: Most frequent customers should have F_Score=5
  5. Sort by Monetary descending
  6. Check: Highest spenders should have M_Score=5

**Deliverables:**
- ✅ R_Score column added with values 1-5
- ✅ F_Score column added with values 1-5
- ✅ M_Score column added with values 1-5
- ✅ All scores verified as correct

**Common Issues:**
- ❌ Scores all showing 1? Check IF ISBLANK logic
- ❌ Scores showing 0? May indicate NULL values in source data
- ❌ Formula error? Copy-paste from DAX guide carefully

**Time Check:** Finish by Day 11 EOD

---

### Days 12-14: Add Segmentation Logic
**Time Allocation:** 5-7 hours

**Tasks:**
- [ ] Add Segment column to RFM_Calculations
  1. Right-click RFM_Calculations → New Column
  2. Paste Segment formula from DAX guide
  3. Let formula process

- [ ] Verify segmentation
  1. Create table visual:
     * Rows: Segment
     * Values: COUNT of Customer ID
  2. Should see all 7-8 segment types:
     - Champions
     - Loyal Customers
     - At-Risk
     - Lost Customers
     - New Customers
     - Potential Loyalists
     - Need Attention
     - Other

- [ ] Check segment distribution
  1. Expected approximate distribution:
     - Loyal Customers: ~45-50% (largest group)
     - At-Risk: ~10-15%
     - Champions: ~5-10%
     - New Customers: ~10-15%
     - Lost Customers: ~5-10%
     - Others: remainder

- [ ] Test segment assignments
  1. Sort by R_Score, F_Score, M_Score all descending
  2. Top customer(s) should be "Champions"
  3. Sort by R_Score ascending (oldest customers)
  4. Should see "Lost Customers" segment

**Deliverables:**
- ✅ Segment column created
- ✅ All 7 segment types present in data
- ✅ Segmentation logic verified
- ✅ Distribution looks reasonable

**Time Check:** Finish by Friday EOD

**End of Week 2 Checklist:**
- ✅ RFM calculation table created
- ✅ R, F, M scores calculated (1-5 scale)
- ✅ Segment classification complete
- ✅ All 7 customer segments identified
- ✅ Data ready for visualization

---

## WEEK 3: Dashboard Pages & Visualizations

### Days 15-16: Page 1 - Executive Summary
**Time Allocation:** 4-6 hours

**Tasks:**
- [ ] Create new page (right-click page tab → New page)
  - Rename to "Executive Summary"

- [ ] Add KPI cards at top
  1. Visual → Card
  2. Total Customers measure
  3. Total Revenue measure
  4. Average Order Value measure
  5. Profit Margin % measure
  6. Format: Large font, prominent placement

- [ ] Add Donut charts (left side)
  1. Visual → Donut Chart
  2. Legend (Items): Segment
  3. Values: DISTINCTCOUNT(Customer ID)
  4. Title: "Customers by Segment"
  5. Add data labels with percentages

  6. Copy this chart
  7. Change Values to [Total_Revenue]
  8. Rename: "Revenue by Segment"

- [ ] Add Clustered Column chart (center)
  1. Axis: Segment
  2. Values: DISTINCTCOUNT(Customer ID)
  3. Add second value: SUM(Orders[Sales])
  4. Title: "Segment Performance"

- [ ] Add Table visual (right)
  1. Fields: Segment, Count, Revenue, Avg Value, Avg Recency
  2. Format: Alternating row colors, headers bold

- [ ] Add Slicers
  1. Visual → Slicer
  2. Field: Orders[Order Date] → select Date hierarchy → Year/Month
  3. Display as dropdown
  4. Position at top of page

  5. Add Region slicer
  6. Add Category slicer
  7. Connect all visuals to slicers

**Deliverables:**
- ✅ Executive Summary page created
- ✅ 4 KPI cards display correctly
- ✅ 2 donut charts show segment breakdown
- ✅ Column chart shows performance
- ✅ Table shows segment metrics
- ✅ 3 slicers filtering all visuals

**Time Check:** Finish by Day 16 EOD

---

### Days 17-18: Page 2 - RFM Matrix
**Time Allocation:** 4-6 hours

**Tasks:**
- [ ] Create new page "RFM Analysis"

- [ ] Create RFM Scatter Plot (main visual)
  1. Visual → Scatter Chart
  2. X Axis: [Frequency] from RFM_Calculations
  3. Y Axis: [Monetary] from RFM_Calculations
  4. Size: [Recency] (smaller bubble = more recent)
  5. Color: Segment (use color for each segment)
  6. Tooltip: Add Customer ID, R/F/M scores
  7. Title: "RFM Matrix - Frequency vs Monetary"

- [ ] Create secondary Scatter (Recency vs Frequency)
  1. Copy previous chart
  2. X Axis: [Recency]
  3. Y Axis: [Frequency]
  4. Title: "Recency vs Frequency"

- [ ] Add RFM Score Distribution (Stacked Bar)
  1. Visual → Stacked Bar Chart
  2. X Axis: Segment
  3. Y Axis: Count of R_Score, F_Score, M_Score
  4. Legend: Shows R, F, M
  5. Title: "RFM Score Distribution by Segment"

- [ ] Add Summary Table
  1. Visual → Table
  2. Columns: Segment, Count, Avg Recency, Avg Frequency, Avg Monetary, % Revenue
  3. Sort by Avg Monetary descending
  4. Format currency values

**Deliverables:**
- ✅ RFM Analysis page created
- ✅ Scatter plot showing F vs M
- ✅ Scatter plot showing R vs F
- ✅ RFM score distribution visible
- ✅ Summary table with metrics

**Time Check:** Finish by Day 18 EOD

---

### Days 19-21: Page 3 - Segmentation Details
**Time Allocation:** 5-7 hours

**Tasks:**
- [ ] Create new page "Segment Details"

- [ ] Add Segment Slicer at top
  1. Visual → Slicer
  2. Field: Segment from RFM_Calculations
  3. Display as buttons
  4. Allow multi-select

- [ ] Add Segment KPIs (4 cards)
  1. Customers in Segment
  2. Revenue from Segment
  3. Avg Order Value
  4. % of Total Revenue

- [ ] Create Customer Details Table
  1. Rows: Customer ID
  2. Values: Last Purchase Date, Purchase Count, Total Spent, R_Score, F_Score, M_Score, City, Country
  3. Enable search functionality
  4. Add conditional formatting to scores (color scale 1-5)

- [ ] Add Geographic Map
  1. Visual → Map
  2. Location: Country/City
  3. Color saturation: Customer Count or Revenue
  4. Title: "Customer Distribution by Location"

- [ ] Add Product Category Chart
  1. Visual → Clustered Bar
  2. Axis: Category
  3. Values: Sum of Sales
  4. Filter: Show top 10
  5. Title: "Top Categories by Segment"

**Deliverables:**
- ✅ Segment Details page created
- ✅ Segment selector slicer added
- ✅ 4 KPI cards show segment metrics
- ✅ Customer table displays detailed records
- ✅ Geographic map shows distribution
- ✅ Product category chart shows preferences

**Time Check:** Finish by Friday EOD

**End of Week 3 Checklist:**
- ✅ 3 dashboard pages created
- ✅ 10+ visualizations designed
- ✅ Slicers and filters working
- ✅ All pages formatted professionally
- ✅ Dashboard is interactive

---

## WEEK 4: Advanced Features & Polish

### Days 22-23: Page 4 - Time Series & Trends
**Time Allocation:** 4-5 hours

**Tasks:**
- [ ] Create new page "Trends & Growth"

- [ ] Revenue Trend by Segment (Line Chart)
  1. Visual → Line Chart
  2. Axis: Month (from DateTable)
  3. Lines: Segment (multiple lines, one per segment)
  4. Values: Sum of Sales
  5. Title: "Monthly Revenue Trend by Segment"
  6. Add forecasting: Analytics pane → Forecast

- [ ] Customer Growth (Column Chart)
  1. Axis: Month
  2. Columns: Count of Customers by Segment
  3. Stacked column
  4. Title: "Customer Count by Segment Over Time"

- [ ] Churn Analysis (Column Chart)
  1. Show customers lost per month
  2. Axis: Month
  3. Values: Count of customers who moved to "Lost"
  4. Title: "Customer Churn by Month"

- [ ] New vs Returning (Area Chart)
  1. Axis: Month
  2. Area 1: New customers
  3. Area 2: Returning customers
  4. Stacked area
  5. Title: "New vs Returning Customers"

**Deliverables:**
- ✅ Trends page created
- ✅ Revenue trend line chart shows all segments
- ✅ Customer growth stacked column visible
- ✅ Churn analysis chart shows losses
- ✅ New vs Returning area chart visible

**Time Check:** Finish by Day 23 EOD

---

### Days 24-25: Page 5 - Insights & Recommendations
**Time Allocation:** 3-4 hours

**Tasks:**
- [ ] Create new page "Insights & Recommendations"

- [ ] Add Key Insights Text Boxes
  1. Visual → Text Box
  2. Add these insights (format with percentages):
     - "Champions represent X% of customers but generate Y% of revenue"
     - "At-Risk customers: Z customers showing declining patterns"
     - "Lost customers: A% of original base"
     - "New customer retention rate: B%"

- [ ] Create Recommendations Table
  1. Segment | Issue | Action | Expected Impact
  2. Champions | Churn risk | VIP program | Retain 95%+
  3. Loyal | Growth opportunity | Cross-sell | +15% AOV
  4. At-Risk | Declining activity | Win-back campaign | Recover 30%
  5. Lost | Inactive | Reactivation | Win back 10%
  6. New | Prove value | Onboarding email | 50% retention
  7. Etc.

- [ ] Add Drill-through setup (Optional but impressive)
  1. Create new page "Customer Details Drill-through"
  2. Add Visual filters: Customer ID
  3. Add visuals: Customer history, purchases, etc.
  4. On previous pages: Right-click data point → drill-through

**Deliverables:**
- ✅ Insights page created
- ✅ Key metrics highlighted
- ✅ Recommendations table added
- ✅ Drill-through functionality (if included)

**Time Check:** Finish by Day 25 EOD

---

### Days 26-28: Formatting & Polish
**Time Allocation:** 5-7 hours

**Tasks:**
- [ ] Overall formatting
  1. Consistent color scheme across all pages
  2. Use segment colors: Champions (Gold), Loyal (Blue), At-Risk (Red), etc.
  3. Fonts: Segoe UI size 11 body, 14 titles
  4. All visuals have clear titles
  5. Remove default visual labels where cluttered

- [ ] Add navigation
  1. Create buttons for page navigation
  2. Add "Home" button on each page
  3. Add "Next" and "Previous" buttons
  4. Use consistent placement (top corners)

- [ ] Performance check
  1. Refresh all data
  2. Check load time for each page (<3 seconds)
  3. Test all slicers
  4. Test all drill-throughs
  5. Close/reopen file to confirm save

- [ ] Mobile layout
  1. View → Mobile layout
  2. Adjust for mobile viewing
  3. Stack visuals vertically
  4. Keep slicers accessible

- [ ] Add visual consistency
  1. All card backgrounds same color
  2. All table borders consistent
  3. All chart colors match palette
  4. Legend placement consistent

**Deliverables:**
- ✅ Professional color scheme applied
- ✅ Navigation buttons added
- ✅ Performance optimized
- ✅ Mobile layout created
- ✅ All formatting consistent

**Time Check:** Finish by Friday EOD

**End of Week 4 Checklist:**
- ✅ 5 complete dashboard pages
- ✅ 20+ visualizations
- ✅ Advanced features (trends, insights)
- ✅ Professional formatting
- ✅ Navigation system
- ✅ Performance optimized
- ✅ Dashboard production-ready

---

## WEEK 5: Deployment & Documentation

### Days 29-30: Publish & GitHub Setup
**Time Allocation:** 3-4 hours

**Tasks:**
- [ ] Save Power BI file locally
  1. File → Save
  2. Name: "Customer_Segmentation_RFM_Dashboard.pbix"
  3. Save to project folder

- [ ] Publish to Power BI Service
  1. File → Publish
  2. Select workspace
  3. Wait for upload (2-5 minutes)
  4. Open in web (https://app.powerbi.com)

- [ ] Setup GitHub repository
  1. Create /Documentation folder
  2. Create /Screenshots folder
  3. Create /Data folder

- [ ] Add files to GitHub
  1. Upload Dashboard file (.pbix)
  2. Upload Dataset (or link to Kaggle)
  3. Upload documentation files

**Deliverables:**
- ✅ Power BI file saved locally
- ✅ Dashboard published to Power BI Service
- ✅ GitHub repo structured
- ✅ All files uploaded

**Time Check:** Finish by Day 30 EOD

---

### Days 31-32: Documentation & README
**Time Allocation:** 3-4 hours

**Tasks:**
- [ ] Create comprehensive README.md
  1. Project overview
  2. Dataset description
  3. Key findings
  4. Technical stack
  5. How to use
  6. Screenshot gallery
  7. Contact info

- [ ] Create DAX Guide (copy from provided DAX-Formulas.md)

- [ ] Create RFM Methodology Guide
  1. Explain RFM concept
  2. Show scoring logic
  3. Segment definitions

- [ ] Add screenshot gallery
  1. Take screenshots of each page
  2. Save as PNG in /Screenshots
  3. Reference in README

- [ ] Create blog post (optional)
  1. Write 300-500 word summary
  2. Key insights
  3. Technical approach
  4. Post on LinkedIn

**Deliverables:**
- ✅ README.md complete
- ✅ DAX guide uploaded
- ✅ RFM methodology documented
- ✅ Screenshots added
- ✅ Professional presentation ready

**Time Check:** Finish by Day 32 EOD

---

### Days 33-35: Testing & Final Tweaks
**Time Allocation:** 4-5 hours

**Tasks:**
- [ ] Final testing
  1. Open dashboard in Power BI Service
  2. Test all slicers
  3. Test all drill-throughs
  4. Test on mobile view
  5. Check load times

- [ ] Bug fixes
  1. Fix any broken visuals
  2. Update any incorrect formulas
  3. Adjust formatting if needed

- [ ] Create test data checklist
  - [ ] Total customers: ~10K ✓
  - [ ] Total orders: ~50K ✓
  - [ ] Revenue range: $X - $Y ✓
  - [ ] Date range: 2012-2015 ✓
  - [ ] Segments: 7 types present ✓
  - [ ] Top segment: Champions ✓

- [ ] Final LinkedIn post
  1. Write professional post
  2. Mention key metrics
  3. Add dashboard screenshot
  4. Include project link
  5. Tag CDAC and relevant people

- [ ] Update resume
  1. Add project section
  2. 3-4 bullet points
  3. Include link to GitHub
  4. Mention key metrics/skills

**Deliverables:**
- ✅ All testing completed
- ✅ Dashboard bug-free
- ✅ Documentation finalized
- ✅ LinkedIn post published
- ✅ Resume updated

**Time Check:** Finish by Friday EOD

---

## 🎯 Project Completion Milestone

**By end of Week 5, you will have:**

✅ Complete RFM Analysis Dashboard with 5 pages  
✅ 20+ interactive visualizations  
✅ Professional formatting and navigation  
✅ Published to Power BI Service  
✅ GitHub repository with documentation  
✅ LinkedIn post highlighting project  
✅ Updated resume with project details  

---

## 📊 Success Metrics

**Dashboard Quality:**
- ✅ All 5 pages loading within 3 seconds
- ✅ All visualizations displaying correctly
- ✅ All slicers/filters functional
- ✅ Mobile layout responsive
- ✅ Zero error messages

**Data Quality:**
- ✅ RFM scores validated
- ✅ Segment distribution reasonable
- ✅ No null values in key columns
- ✅ Date ranges correct
- ✅ Revenue totals match source

**Documentation Quality:**
- ✅ README comprehensive
- ✅ All DAX formulas documented
- ✅ Methodology clear
- ✅ Screenshots professional
- ✅ GitHub repo organized

---

## 🎓 Skills Demonstrated

After completing this project, you can confidently say:

**Technical Skills:**
- ✅ Power BI Desktop (data modeling, visualization)
- ✅ Power Query (ETL, data transformation)
- ✅ DAX (50+ formulas, complex calculations)
- ✅ Data Relationships & star schema
- ✅ Dashboard Design & UX

**Business Skills:**
- ✅ RFM Analysis methodology
- ✅ Customer segmentation
- ✅ Churn prediction & retention
- ✅ Business intelligence
- ✅ Data-driven insights

**Soft Skills:**
- ✅ Project management
- ✅ Documentation
- ✅ Presentation
- ✅ Problem-solving
- ✅ Communication

---

## 🚀 Interview Questions You'll Be Ready For

1. "Walk us through your RFM analysis project"
   - Explain segmentation methodology
   - Show dashboard
   - Discuss business insights

2. "How did you calculate RFM scores?"
   - Show DAX formulas
   - Explain scoring logic
   - Discuss edge cases

3. "What insights did you gain?"
   - 45% revenue from 18% champions
   - At-risk customer identification
   - Churn prediction

4. "What was challenging?"
   - DAX formula optimization
   - Handling large datasets
   - Mobile responsiveness

5. "How would you enhance this?"
   - ML for churn prediction
   - Real-time data refresh
   - Automated alerts

---

## 💡 Pro Tips for Success

1. **Commit daily** to GitHub - Shows progress
2. **Test as you go** - Don't leave testing to end
3. **Take screenshots** - Document each milestone
4. **Save backups** - Cloud storage + external drive
5. **Share progress** - Post on LinkedIn weekly
6. **Ask for feedback** - Share with peers/mentors
7. **Iterate quickly** - Fix issues immediately
8. **Document thoroughly** - Future you will thank you

---

## 📞 Need Help?

**Common Issues & Solutions:**

**Q: Dashboard is slow**
- A: Reduce visual count, use aggregations, optimize DAX

**Q: Formulas showing blanks**
- A: Check data types, verify relationships, test with simple formula first

**Q: Segments not appearing**
- A: Verify RFM_Calculations table, check segment formula, ensure all scores exist

**Q: GitHub won't accept .pbix file**
- A: File is large (~50MB+), add to .gitignore, link to cloud storage instead

**Q: Mobile layout looks bad**
- A: Go to View → Mobile layout, reorganize visuals vertically, simplify slicers

---

**You've got this! 💪 Follow this roadmap week-by-week and you'll have an amazing portfolio project.**

**Good luck! 🚀**
