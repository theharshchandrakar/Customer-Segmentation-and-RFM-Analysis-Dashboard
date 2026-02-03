# TROUBLESHOOTING GUIDE - Customer Table Confusion

## 🚨 THE PROBLEM

**Error Message:**
"Cannot create relationship because Customer table doesn't exist"

**Why This Happens:**
The original guides assumed a separate Customer dimension table exists, but the **Global Superstore dataset does NOT have one**.

---

## ✅ THE SOLUTION

### 1. **Understand the ACTUAL Dataset Structure**

**The Global Superstore Excel file contains ONLY 3 sheets:**

| Sheet Name | Rows | Purpose |
|------------|------|---------|
| **Orders** | 51,290 | Main transaction data (includes customer info) |
| **People** | 24 | Regional sales managers |
| **Returns** | 1,079 | Returned order tracking |

**That's it! No Customer table exists.**

---

### 2. **Where is Customer Data?**

**All customer information is in the Orders table:**

```
Orders Table Contains:
├── Customer ID (column)
├── Customer Name (column)
├── Segment (column - Consumer/Corporate/Home Office)
├── Order Date
├── Sales
├── Profit
└── ...all other transaction data
```

---

### 3. **How to Create RFM_Calculations (Without Customer Table)**

**The RFM_Calculations table is built FROM Orders table:**

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
- Extracts unique customers from Orders table
- Groups Orders by Customer ID
- Calculates RFM metrics per customer
- Creates NEW table called RFM_Calculations

---

### 4. **Correct Relationships**

**You will create ONLY 4 relationships:**

**Relationship 1: Time Intelligence**
```
Orders[Order Date] ←→ DateTable[Date]
```

**Relationship 2: Regional Manager**
```
Orders[Region] ←→ People[Region]
```

**Relationship 3: Returns Tracking**
```
Orders[Order ID] ←→ Returns[Order ID]
```

**Relationship 4: RFM Analysis**
```
Orders[Customer ID] ←→ RFM_Calculations[Customer ID]
```

**Note:** Relationship 4 is created AFTER you create RFM_Calculations table!

---

## 🔧 STEP-BY-STEP FIX

### **If you see "Customer table not found" error:**

**Step 1: Verify Your Data Model**
- Go to Model View in Power BI
- Count your tables
- You should see: Orders, People, Returns (and DateTable if created)
- **Do you see a "Customer" table?** → You shouldn't!

**Step 2: Check What Tables You Have**
```
Expected Tables from Dataset:
✅ Orders (main table)
✅ People (dimension table)
✅ Returns (dimension table)

Tables You'll Create:
✅ DateTable (using DAX)
✅ RFM_Calculations (using DAX)

TOTAL: 5 tables (not 6!)
```

**Step 3: Verify Customer ID Location**
- Click on Orders table in Data View
- Scroll through columns
- Find "Customer ID" column → It's here!
- Find "Customer Name" column → It's here too!

**Step 4: Create RFM_Calculations (If Not Done Yet)**
- Go to: Modeling → New Table
- Paste the RFM_Calculations DAX (from above)
- Press Enter
- Verify table appears with ~3,000-5,000 rows

**Step 5: Create Relationship**
- Go to Model View
- Drag from Orders[Customer ID]
- Drop on RFM_Calculations[Customer ID]
- Relationship should create automatically

---

## 🎯 COMMON MISTAKES & FIXES

### **Mistake 1: Looking for Customer Table**

**Problem:**
"I can't find the Customer table in my dataset"

**Solution:**
It doesn't exist! Customer data is embedded in Orders table. This is a denormalized data model (common in transactional datasets).

---

### **Mistake 2: Trying to Create Customer Dimension**

**Problem:**
"Should I create a Customer table in Power Query?"

**Solution:**
No need! RFM_Calculations serves this purpose. It's created using DAX, not Power Query.

**Workflow:**
```
Orders Table (has Customer ID)
      ↓
   (DAX SUMMARIZE)
      ↓
RFM_Calculations (unique customers + metrics)
```

---

### **Mistake 3: Wrong Relationship Count**

**Problem:**
"Guides say 5-6 relationships but I only have 3"

**Solution:**
You should have exactly 4 relationships (after completing all steps):
1. Orders ↔ DateTable
2. Orders ↔ People
3. Orders ↔ Returns
4. Orders ↔ RFM_Calculations

---

### **Mistake 4: Can't Create RFM_Calculations**

**Problem:**
"RFM_Calculations table errors out"

**Possible Causes:**
1. **Orders table not loaded correctly**
   - Check: Does Orders table exist in Fields pane?
   - Check: Does it have Customer ID column?

2. **Column name mismatch**
   - Your column might be: "Customer ID" or "CustomerID" or "Cust_ID"
   - Check exact spelling in Orders table
   - Update DAX to match your column name

3. **Date column wrong type**
   - Check: Is Order Date actually Date type?
   - Fix: Go to Power Query, change to Date type

---

## 📊 VERIFICATION CHECKLIST

**After completing setup, verify:**

- [ ] **Tables Count: 5 total**
  - [ ] Orders (from dataset)
  - [ ] People (from dataset)
  - [ ] Returns (from dataset)
  - [ ] DateTable (you created)
  - [ ] RFM_Calculations (you created)

- [ ] **Orders Table Has:**
  - [ ] Customer ID column
  - [ ] Customer Name column
  - [ ] Segment column
  - [ ] Order Date column (Date type)
  - [ ] Sales column (Number type)

- [ ] **RFM_Calculations Table Has:**
  - [ ] Customer ID column
  - [ ] Customer Name column
  - [ ] Recency column (numbers)
  - [ ] Frequency column (numbers)
  - [ ] Monetary column (numbers)
  - [ ] ~3,000-5,000 rows (unique customers)

- [ ] **Relationships: 4 total**
  - [ ] Orders → DateTable (via Order Date)
  - [ ] Orders → People (via Region)
  - [ ] Orders → Returns (via Order ID)
  - [ ] Orders → RFM_Calculations (via Customer ID)

---

## 🔍 VISUAL VERIFICATION

### **Model View Should Look Like This:**

```
        DateTable
            |
            | [Order Date → Date]
            |
         ORDERS ←──── People [Region → Region]
        (center)
            |
            | [Order ID → Order ID]
            |
         Returns

         ORDERS [Customer ID] ←→ [Customer ID] RFM_Calculations
```

**Key Points:**
- Orders is the CENTER (fact table)
- Everything connects TO or FROM Orders
- No Customer dimension table exists
- RFM_Calculations connects via Customer ID (which is in Orders!)

---

## 💡 WHY THIS DESIGN?

**Question:** "Why doesn't Global Superstore have a Customer table?"

**Answer:**
The dataset is a **denormalized transactional export**. Common reasons:

1. **Simplicity:** Single table contains all data needed for analysis
2. **Real-world scenario:** Many data exports come this way
3. **Learning opportunity:** Practice creating dimensional models from flat data

**In Power BI, you CREATE dimensions from facts using DAX:**
- DateTable → Created from Order Date
- RFM_Calculations → Created from Customer ID grouping

This teaches you how to build star schema from denormalized data!

---

## 📝 QUICK REFERENCE COMMANDS

### **Check if Customer ID exists in Orders:**
```DAX
// Test measure - should return number > 0
Test_CustomerCount = DISTINCTCOUNT(Orders[Customer ID])
```

### **View unique customers:**
```DAX
// New table to verify
Customer_List = 
SUMMARIZE(
    Orders,
    Orders[Customer ID],
    Orders[Customer Name]
)
```

### **Verify Orders has customer data:**
```DAX
// New measure - should show customer names
Test_CustomerNames = 
CONCATENATEX(
    VALUES(Orders[Customer Name]),
    Orders[Customer Name],
    ", ",
    Orders[Customer Name],
    ASC
)
```

---

## 🆘 STILL STUCK?

### **Before asking for help, provide:**

1. **Your table list:**
   - Screenshot of Tables pane (left sidebar)
   - List all table names you see

2. **Orders table columns:**
   - Screenshot showing Orders column list
   - Verify Customer ID exists here

3. **Error message:**
   - Exact error text
   - Which step caused error?

4. **Your data source:**
   - Confirm: "Global Superstore" from Kaggle?
   - Check: File is `Global_Superstore.xls`?

---

## ✅ SUCCESS CRITERIA

**You know you're on track when:**

✅ You understand Customer table doesn't exist
✅ You see Customer ID in Orders table
✅ RFM_Calculations table created successfully
✅ Model View shows 5 tables, 4 relationships
✅ No relationship errors
✅ Ready to add score columns to RFM_Calculations

---

## 🎯 NEXT STEPS

**After resolving Customer table confusion:**

1. **Verify Orders[Customer ID] exists** ✓
2. **Create RFM_Calculations table** ✓
3. **Add score columns (R_Score, F_Score, M_Score)** → Next
4. **Add Segment column** → Next
5. **Create measures** → Next
6. **Build dashboard** → Next

**Use the CORRECTED guides provided (CORRECTED-Guide.md and Weekly-Roadmap.md) which have fixed this issue!**

---

**The Customer table confusion is the #1 beginner mistake with this dataset. Now you know the truth: Customer data lives in Orders table, and RFM_Calculations is created from it!** 🎉
