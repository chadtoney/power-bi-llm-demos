# Power BI 101 with LLM Integration: Step-by-Step Demo Walkthrough

**Audience:** DoD Power BI 101 Users  
**Goal:** Demonstrate how to leverage LLMs (M365 Copilot, Gemini) in coordination with Power BI features for advanced analytics without Copilot-in-Power-BI or Fabric

---

## 📋 Table of Contents

1. [Overview & Setup](#overview--setup)
2. [Demo 1: Power Query M Code with LLM Assistance](#demo-1-power-query-m-code-with-llm-assistance)
3. [Demo 2: DAX Measures via LLM](#demo-2-dax-measures-via-llm)
4. [Demo 3: Theme JSON Customization](#demo-3-theme-json-customization)
5. [Demo 4: Sample Data Creation with LLM](#demo-4-sample-data-creation-with-llm)
6. [Demo 5: Power BI as Code (PBIP)](#demo-5-power-bi-as-code-pbip)
7. [Best Practices & Tips](#best-practices--tips)

---

## Overview & Setup

### What We're Building
A workflow that treats Power BI components as **code artifacts** that can be enhanced, debugged, and generated using LLMs. This approach provides:
- ✅ Code-based version control for Power BI projects
- ✅ LLM assistance for complex Power Query and DAX
- ✅ Custom theme generation and refinement
- ✅ Rapid sample data creation for testing

### Prerequisites
- Power BI Desktop (latest version)
- M365 Copilot or Gemini account
- Git (optional, for PBIP version control)
- Text editor (VS Code recommended)

---

## Demo 1: Power Query M Code with LLM Assistance

**What is Power Query M?**  
M is the functional language behind Power Query. Writing M code directly (instead of only using the GUI) lets you get precise help from an LLM and reuse transformations as code.

### Step-by-Step Walkthrough

#### Step 1.1: Open Power Query Editor
1. In your Power BI report, click **Transform Data → Launch Power Query Editor**
2. Create a new blank query or select an existing one

#### Step 1.2: View or Edit M Code
You can view and edit M code in two ways:

**Method A: Formula Bar**
- Shows the M code for the selected step in **Applied Steps**
- Good for quick, small edits

**Method B: Advanced Editor** (preferred for complex edits)
1. Home tab → **Advanced Editor** (or right-click any step → Edit Settings)
2. See the full query logic at once, edit, then click **Done**

#### Step 1.3: Simple M Code Example
```m
let
   Source = Excel.Workbook(File.Contents("C:\\Data\\sales.xlsx"), null, true),
   SalesData = Source{[Item="SalesTable"]}[Data],
   #"Changed Type" = Table.TransformColumnTypes(SalesData,{{"Date", type date}, {"Amount", Int64.Type}}),
   #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Amount] > 1000)
in
   #"Filtered Rows"
```

**Why this matters:**
- `let ... in` wraps all steps; `Source` is your connection; each step transforms the table; `in` returns the final result.

### Why Use M Code + LLM?
- **Precision:** Describe complex transforms more clearly than clicking UI steps
- **Reusability:** Copy/paste M code across queries and reports
- **Debugging:** LLMs can spot logic issues and propose fixes
- **Documentation:** Clear step names serve as inline docs
- **Version Control:** PBIP stores M as code you can diff and track

### Use Case 1: Data Cleaning & Transformation

#### Prompt 1.1: Clean Messy CSV Data
```
Add to this power query the following steps:
1. Remove rows where Sales Amount is null or blank
2. Combine First Name and Last Name into Full Name (trim spaces)
3. Remove duplicates based on ID

My M code is:

[PASTE YOUR CURRENT M CODE HERE]

The file is at: C:\\data\\sales.csv

IMPORTANT: Include the complete M code with all steps AND the final "in" statement that returns the transformed table.
```

**Expected Output:** Clean, deduplicated table with proper data types.

#### Prompt 1.2: Create Status Lookup Table
```
Create an M query from a blank query that has:
1. A Status column with values: completed, pending, cancelled
2. An OrderBy column with values: 1, 2, 3 (respectively)

The result should be a two-column table that can be used for sorting or filtering other queries.
```

**Expected Output:** A small lookup table with Status names and their sort order.

#### Prompt 1.3: Add Descriptive Names to Applied Steps
```
I have this M code that loads CSV data and applies multiple transformations. The step names are auto-generated 
(like #"Promoted Headers", #"Filtered Rows1", etc.) and make the code hard to read and maintain.

Please rewrite this M code to:
1. Replace auto-generated step names with clear, descriptive names that explain what each step does
2. Add a comment above each step explaining its purpose
3. Keep the same logic and transformations - only improve naming and add comments

Current M code:

let
    Source = Csv.Document(File.Contents("C:\\Users\\chadtoney\\OneDrive - Microsoft\\DoD Power BI Event 2025 - Content Dump\\Dec 25 - Chat-Driven Analytics\\Demo\\sample_sales_data.csv"),[Delimiter=",", Columns=9, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"ID", Int64.Type}, {"Name", type text}, {"Date", type date}, {"Sales Amount", Currency.Type}, {"Region", type text}, {"Product", type text}, {"Quantity", type text}, {"Status", type text}, {"", type text}}),
    #"Removed Other Columns" = Table.SelectColumns(#"Changed Type",{"ID", "Name", "Sales Amount", "Product", ""}),
    #"Filtered Rows" = Table.SelectRows(#"Removed Other Columns", each [#""] <> ""),
    #"Filtered Rows1" = Table.SelectRows(#"Filtered Rows", each [Product] = "North")
in
    #"Filtered Rows1"
```

Include the complete code from `let` to `in`.
```

**Expected Output:** Refactored M code with readable step names like `HeadersPromoted`, `TypesConverted`, `UnnecessaryColumnsRemoved`, `BlankRowsFiltered`, `ProductFiltered`, etc., and explanatory comments.

### Pro Tips for M Code + LLM
- **Be specific:** Include sample data or column names in your prompt
- **Request incrementally:** Start with data loading, then add filters, then transformations
- **Test early:** Verify each transformation step works before moving to the next
- **Error handling:** Ask LLM to add error handling for production queries

---

## Demo 2: DAX Measures via LLM

**What is DAX?**  
DAX (Data Analysis Expressions) is the formula language for Power BI calculations, measures, and columns.

### Step-by-Step Walkthrough

#### Step 2.1: Open the Data Model
1. Go to **Modeling** tab in Power BI Desktop
2. Click **New Measure** or **New Column**

#### Step 2.2: Simple DAX Example
Here's a basic measure:

```dax
Total Sales = SUM('Sales Gemini'[Sales Amount])
```

#### Step 2.3: Create the Measure in Power BI
1. In **Modeling** tab, click **New Measure**
2. Paste the DAX code from LLM
3. Click the checkmark to save
4. Add the measure to a visual to test

#### Step 2.4: Debugging DAX with LLM
If your DAX isn't working:

**Prompt to LLM:**
```
This DAX measure isn't working correctly:
[PASTE YOUR DAX CODE]

The problem is: [DESCRIBE UNEXPECTED BEHAVIOR]

These are my tables:
- [LIST TABLE NAMES & KEY COLUMNS]

Can you help fix it?
```

### Use Case 1: DAX Calendar Table

#### Prompt 1.1: Create a Basic Calendar Table
```
Write a DAX calculated table for a basic Calendar table for Power BI.

The table should:
1. Generate dates from 2020-01-01 to 2025-12-31
2. Include these columns: Date, Month, Month Number, Quarter, Year, Year-Month (text format like "2025-01")
3. Use simple date functions, no complex logic

Provide just the DAX code that I can paste into a new table.
```

**Sample Output:** A Calendar table with ~2,200 rows spanning 2020-2025, ready to use as a dimension for connecting to fact tables.

### Use Case 2: Time-Based Calculations

#### Prompt 2.1: Year-over-Year & Month-over-Month Growth
```
Write DAX measures in Power BI for:

1. YoY Growth % - Compares current year sales to previous year
2. MoM Growth % - Compares current month sales to previous month
3. YoY Growth $ - Dollar amount difference year over year
4. Rank by YoY Growth - Rank regions/products by growth percentage

Tables:
- Sales Gemini (Date, Sales Amount, Region, Product)
- Calendar (Date, Month, Month Number, Quarter, Year, Year-Month)

The measures should:
- Respect date filters in the report
- Return BLANK() if prior period has no data
- Work for any column context (Region, Product, etc.)

Example: If current filter is "2025 YTD vs 2024 YTD", YoY Growth % should show the percentage change.
```

**Sample Output:**
```dax
YoY Growth % =
VAR CurrentSales = [Total Sales]
VAR PriorSales = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Calendar'[Date]))
RETURN
    IF(
        ISBLANK(PriorSales) || PriorSales = 0,
        BLANK(),
        DIVIDE(CurrentSales - PriorSales, PriorSales)
    )
```

#### Prompt 2.2: Rolling Period Calculations
```
Write DAX measures for:

1. 12-Month Rolling Total - Sum of last 12 months of sales
2. 90-Day Rolling Average - Average daily sales over last 90 days
3. Quarter-to-Date (QTD) - Sales from start of quarter to today
4. Year-to-Date (YTD) - Sales from start of year to today
5. Previous 12 Months - Same period last year (for comparison)

Tables:
- Sales (OrderDate, Amount, Customer, Region)
- Calendar (Date, DayOfMonth, DayOfYear, DayOfWeek, IsToday, IsCurrentMonth, IsCurrentYear)

Assumptions:
- Today is marked in Calendar[IsToday]
- Reports filter to a specific date or date range
- Need ability to compare "This month YTD" vs "Same month last year YTD"

The calculations should automatically adjust based on current date filters.
```

#### Prompt 2.3: Seasonal Decomposition
```
Write DAX measures to:

1. Average Sales for Same Month in Prior Years 
   (e.g., Jan 2025 compared to Jan 2024, Jan 2023)
2. Seasonal Index = Current Month Sales / Average of Same Month (all years)
3. Deseasonalized Sales = Current Sales / Seasonal Index
4. Trend Component - Linear trend of sales over time for current month

Tables:
- Sales (OrderDate, Amount, Store, Category)
- Calendar (Date, Month, MonthNumber, Year)

Goal: Identify seasonal patterns (e.g., Q4 is always 40% higher) 
and remove them to see underlying trends.

Include measures for each major product category.
```

### Use Case 3: Ranking & Segmentation

#### Prompt 3.1: Top N Ranking with Ties
```
Write DAX measures for ranking and segmentation:

1. Rank by Sales - Rank regions by total sales (handle ties properly)
2. Percentile Rank - Show what percentile each region is in (0-100)
3. Segment - Categorize as "Top Performer" (top 20%), "Mid-Tier" (20-60%), "Low Performer" (bottom 40%)
4. Sales vs Top Performer - How much each region's sales differ from the top performer

Tables:
- Sales (OrderDate, Amount, Region, ProductID)
- Regions (RegionID, RegionName, Manager)

Context: Report filters by Year and Quarter
Need ability to dynamically segment based on current filter context.
```

**Sample Output:**
```dax
Rank by Sales = 
RANKX(
    ALL(Regions[Region]),
    [Total Sales],,,DESC
)

Top Performer Segment = 
VAR TotalSales = [Total Sales]
VAR PercentileRank = PERCENTRANK.INC(
    ALL(Regions[Region]),
    [Total Sales]
)
RETURN
    IF(
        PercentileRank >= 0.8,
        "Top Performer",
        IF(PercentileRank >= 0.2, "Mid-Tier", "Low Performer")
    )
```

### Use Case 4: Complex Business Logic

#### Prompt 4.1: Customer Lifetime Value (CLV) & Churn
```
Write DAX measures for customer analytics:

1. Total Customer Revenue - Sum of all purchases by customer
2. Purchase Frequency - Count of orders per customer
3. Average Order Value - Total Revenue / Order Count

Tables:
- Orders (OrderDate, OrderID, CustomerID, Amount)
- Customers (CustomerID, AcquisitionDate, IsActive)
- Calendar (Date, IsToday)

Goal: Segment customers for targeted retention/reactivation campaigns.
```

**Sample Output:**
```dax
Days Since Last Purchase = 
INT(
    IF(
        ISBLANK(MAX(Orders[OrderDate])),
        BLANK(),
        TODAY() - MAX(Orders[OrderDate])
    )
)

Churn Risk = 
VAR DaysSincePurchase = [Days Since Last Purchase]
RETURN
    IF(
        ISBLANK(DaysSincePurchase),
        BLANK(),
        IF(DaysSincePurchase > 180, "Churned",
        IF(DaysSincePurchase > 90, "High",
        IF(DaysSincePurchase > 60, "Medium", "Low")))
    )
```

---

## Demo 3: Theme JSON Customization

**What are Power BI Themes?**  
Themes are JSON files that control the visual appearance of Power BI reports (colors, fonts, etc.).

### Step-by-Step Walkthrough

#### Step 3.1: Select and Export Built-In Theme
1. In Power BI Desktop, go to **View** tab → **Themes**
2. Select **Innovate** theme from the gallery
3. Go to **View → Themes → Customize current theme → Export current theme**
4. Save the JSON file as `Innovate_Original.json` in your Demo folder

#### Step 3.2: Inspect the Theme JSON
Open the exported `Innovate_Original.json` in VS Code. You'll see structure like:

```json
{
  "name": "Innovate",
  "dataColors": [
    "#118DFF",
    "#12239E",
    "#FD625E",
    "#F2CC1F"
  ],
  "background": {
    "color": "#FFFFFF"
  },
  "foreground": {
    "color": "#000000"
  },
  "tableAccent": "#118DFF"
}
```

#### Step 3.3: Modify Theme with LLM

**Workflow:**
1. Copy the entire JSON content from `Innovate_Original.json`
2. Paste it into M365 Copilot with your modification prompt
3. Save the LLM's response as a new JSON file
4. Import it back into Power BI to see the changes

**Example Modification 1: Change All Fonts**

**Prompt to M365 Copilot:**
```
Change all font references in this Power BI theme JSON to "Times New Roman".

[PASTE THE JSON HERE]

Return the complete updated JSON.
```

**Steps:**
1. Copy the JSON output from Copilot
2. Save it as `Innovate_TimesNewRoman.json`
3. In Power BI: **View → Themes → Browse for themes**
4. Select `Innovate_TimesNewRoman.json`
5. All visuals now use Times New Roman font

**Example Modification 2: Change Color Palette**

**Prompt to M365 Copilot:**
```
Change all the dataColors in this Power BI theme JSON to different shades of green (use a variety from dark green to light green, maintaining good contrast).

[PASTE THE CURRENT JSON HERE]

Return the complete updated JSON.
```

**Steps:**
1. Copy the JSON output from Copilot
2. Save it as `Innovate_TimesNewRoman_Green.json`
3. In Power BI: **View → Themes → Browse for themes**
4. Select the new file
5. All data visuals now use green color palette

### Use Case 1: Iterative Theme Refinement

#### Prompt 1.1: DoD Compliance Dark Theme
```
Generate a Power BI theme JSON for a DoD environment that:
1. Uses a dark background for accessibility (long hours of viewing)
2. Military blue (#003A70) as primary color
3. High contrast colors for visibility
4. Professional sans-serif fonts
5. 6 distinct data colors for multiple series
6. Accessible color palette (pass WCAG AAA standards)
7. Color-blind friendly (avoid red-green only differentiation)

The theme should be suitable for secure facilities and formal briefings.
Return as valid JSON ready to import into Power BI.
```

**Sample Output:**
```json
{
  "name": "DoD Dark Professional",
  "dataColors": [
    "#003A70",
    "#E81B23",
    "#FFB81C",
    "#00A4EF",
    "#7FBA00",
    "#FF6B35"
  ],
  "background": {
    "color": "#1A1A1A"
  },
  "foreground": {
    "color": "#FFFFFF"
  },
  "tableAccent": "#003A70",
  "fonts": {
    "family": "'Segoe UI', Arial, sans-serif"
  }
}
```


---

## Demo 4: Sample Data Creation with LLM

**Why Sample Data?**  
Demo environments, testing, and development often need realistic sample data without exposing real DoD data.

### Step-by-Step Walkthrough

#### Step 4.1: Download Sample Data

A pre-generated sample sales dataset is available in this repository:
- **File:** [`sample_sales_data.csv`](https://github.com/chadtoney/power-bi-llm-demos/blob/main/sample_sales_data.csv)
- **Direct Download:** [Raw CSV](https://raw.githubusercontent.com/chadtoney/power-bi-llm-demos/main/sample_sales_data.csv)
- **Rows:** 100 sales transactions
- **Columns:** ID, Name, Date, Sales Amount, Region, Product, Quantity, Status
- **Date Range:** January-March 2025
- **Regions:** North, South, East, West
- **Products:** Widget A, Widget B, Widget C, Service X

**To use this file:**
1. Click the "Direct Download" link above to download the CSV
2. In Power BI Desktop, go to **Home → Get Data → Text/CSV**
3. Select the downloaded `sample_sales_data.csv` file
4. Click **Load** or **Transform Data** to import

This dataset is useful for testing Power Query transformations (Demo 1) and DAX measures (Demo 2).

#### Step 4.2: Use Case 1 - Generate Custom Sample Data with LLM

If you need different data, use an LLM to generate it:

##### Prompt 1.1: Basic Sales Dataset
```
Generate sample sales data for testing a Power BI report.

Requirements:
1. 500 rows of data
2. Columns: TransactionID, Date, Region, Product, SalesAmount, Quantity
3. Date range: January 1, 2024 to December 31, 2024
4. Regions: North, South, East, West
5. Products: Widget A, Widget B, Widget C, Service X
6. SalesAmount: $100 to $10,000
7. Quantity: 1 to 100

Format as CSV with headers.
The data should be realistic with some patterns (e.g., seasonal variation).
```

**Sample Output (first 10 rows):**
```csv
TransactionID,Date,Region,Product,SalesAmount,Quantity
TXN001,2024-01-05,North,Widget A,2500.00,15
TXN002,2024-01-08,South,Widget B,5000.00,25
TXN003,2024-01-12,East,Service X,1200.00,8
TXN004,2024-01-15,West,Widget C,3500.00,18
TXN005,2024-01-20,North,Widget A,4200.00,22
TXN006,2024-01-22,South,Service X,1800.00,12
TXN007,2024-01-25,East,Widget B,2800.00,14
TXN008,2024-01-28,West,Widget C,6000.00,30
TXN009,2024-02-01,North,Service X,2100.00,16
TXN010,2024-02-05,South,Widget A,3700.00,20
...
```


### Step 4.9: Advanced Use Cases with Business Logic

##### Prompt 5.1: Realistic Customer Behavior Simulation
```
Write Power Query M code that generates 1000 rows of synthetic customer data with:
1. CustomerID (sequential, padded: CUST001-CUST100 repeated 10 times for behavior)
2. Name (realistic first and last names)
3. RegistrationDate (random between 2022 and 2024)
4. TotalPurchases (sum total in dollars, $0-$50,000)
5. LastPurchaseDate (within 90 days of today or null)
6. Region (North, South, East, West)
7. CustomerSegment (derived: Premium if TotalPurchases > $10,000, Standard if > $1,000, else Basic)
8. LTVPrediction (estimated future value based on historical spend)

Include variations by region (e.g., higher average purchases in North region).
Include churn indicators (null LastPurchaseDate = inactive customer).
```



---

## Demo 5: Power BI as Code (PBIP)

**What is PBIP?**  
PBIP (Power BI Project) stores Power BI files as folders with source files instead of a single `.pbix` binary. This enables:
- Git version control
- Code-level diffing
- Team collaboration
- LLM-assisted modifications
- Agentic automation (PowerShell scripts can build reports)

### Why PBIP Matters

Once you understand M code, DAX, and Themes, PBIP lets you:
- **Version control** entire reports in Git
- **Collaborate** using standard DevOps practices
- **Automate** report creation with scripts
- **Modify reports programmatically** using LLM-generated JSON

### Step-by-Step Walkthrough

#### Step 5.1: Enable PBIP Format
1. Open Power BI Desktop
2. Go to **File → Options and Settings → Options**
3. Navigate to **Preview features** (left side panel)
4. Check **"Power BI Project (.pbip) save option"**
5. Click **OK** and restart Power BI Desktop

#### Step 5.2: Save Your Report as PBIP
1. Open an existing Power BI report (or create a new one with sample data)
2. Go to **File → Save As**
3. In the "Save as type" dropdown, select **Power BI Project (.pbip)**
4. Choose a folder location (e.g., `C:\\Demo\\MyFirstPBIP`)
5. Click **Save**

Power BI creates a folder structure like this:
```
MyFirstPBIP/
├── MyFirstPBIP.pbip (project file - double-click to open in Power BI)
├── MyFirstPBIP.Report/
│   ├── definition.pbir (report layout and visuals - JSON)
│   ├── report.json
│   └── ...
├── MyFirstPBIP.Dataset/
│   ├── definition.pbdm (data model, tables, relationships - JSON/TMDL)
│   ├── model.bim
│   └── ...
└── .pbiworkspace (workspace metadata)
```

#### Step 5.3: Open PBIP in a Text Editor or IDE

**Option 1: VS Code (Recommended)**
1. Right-click the **MyFirstPBIP** folder
2. Select **"Open with Code"** (if VS Code is installed)
3. Browse the folder structure in the Explorer pane
4. Open `definition.pbir` or `definition.pbdm` to see JSON/TMDL code

**Option 2: Any Text Editor**
1. Navigate to the PBIP folder in File Explorer
2. Go into `MyFirstPBIP.Report` folder
3. Right-click `definition.pbir` → Open With → Notepad (or your preferred editor)
4. You'll see the JSON structure of your report

**What You'll See:**
- **definition.pbir**: Report pages, visual positions, formatting, themes
- **definition.pbdm** or **model.bim**: Tables, columns, relationships, measures (DAX)
- **M code files**: Power Query transformations for each query

#### Step 5.4: Inspect the Code Structure

Open these key files to understand the structure:

**1. Report Definition (`definition.pbir`)**
```json
{
  "version": "1.0",
  "pages": [
    {
      "name": "ReportSection1",
      "displayName": "Sales Overview",
      "visualContainers": [...]
    }
  ]
}
```

**2. Data Model (`definition.pbdm` or `model.bim`)**
```json
{
  "model": {
    "tables": [
      {
        "name": "Sales",
        "columns": [...],
        "measures": [...]
      }
    ],
    "relationships": [...]
  }
}
```

**3. Power Query Files (`.m` files)**
Each query has its own `.m` file with the M code you saw in Power Query Editor.

#### Step 5.5: Add DAX Measures Directly in the IDE

**Option 1: Edit model.bim or definition.pbdm**

1. In VS Code, navigate to the `.Dataset` folder
2. Open `model.bim` (or `definition.pbdm` depending on format)
3. Find the table where you want to add a measure
4. Locate the `"measures"` array within that table
5. Add your new measure to the array

**Example: Adding a Total Sales measure**

Find the Sales table in your `model.bim`:
```json
{
  "name": "Sales",
  "columns": [
    {"name": "OrderID", "dataType": "string"},
    {"name": "Amount", "dataType": "decimal"}
  ],
  "measures": [
    {
      "name": "Total Revenue",
      "expression": "SUM('Sales'[Amount])"
    }
  ]
}
```

Add a new measure to the `measures` array:
```json
{
  "name": "Sales",
  "columns": [
    {"name": "OrderID", "dataType": "string"},
    {"name": "Amount", "dataType": "decimal"}
  ],
  "measures": [
    {
      "name": "Total Revenue",
      "expression": "SUM('Sales'[Amount])"
    },
    {
      "name": "Average Order Value",
      "expression": "AVERAGE('Sales'[Amount])",
      "formatString": "$#,##0.00"
    },
    {
      "name": "Total Orders",
      "expression": "COUNTROWS('Sales')"
    }
  ]
}
```

**Option 2: Use LLM to Generate the JSON**

**Prompt:**
```
I have this Sales table definition from my Power BI model.bim file.
I want to add these three new DAX measures:
1. Year-over-Year Growth: Compare current year sales to prior year
2. Running Total: Cumulative sum of sales ordered by date
3. Sales Rank: Rank regions by total sales

Current table structure:
[PASTE YOUR SALES TABLE JSON]

Show me the updated JSON with these measures added to the "measures" array.
Include proper DAX expressions and format strings.
```

**LLM will return updated JSON like:**
```json
{
  "name": "YoY Sales Growth",
  "expression": [
    "VAR CurrentSales = SUM('Sales'[Amount])",
    "VAR PriorYearSales = CALCULATE(SUM('Sales'[Amount]), SAMEPERIODLASTYEAR('Calendar'[Date]))",
    "RETURN DIVIDE(CurrentSales - PriorYearSales, PriorYearSales)"
  ],
  "formatString": "0.00%"
}
```

#### Step 5.6: Save and Reload in Power BI

1. Save the `model.bim` file in your text editor (Ctrl+S)
2. Go back to Power BI Desktop
3. Close the `.pbip` file (if open)
4. Reopen the `.pbip` file from File Explorer
5. Your new measures appear in the Fields pane!
6. Verify they work by adding them to a visual

**Pro Tip:** If you get errors, check:
- JSON syntax is valid (matching brackets, commas)
- Table and column names are correct
- DAX expression syntax is valid

#### Step 5.7: Make Changes and Reload

1. Edit a file in your text editor (e.g., change a measure name in `model.bim`)
2. Save the file
3. Go back to Power BI Desktop
4. Click **Refresh** or close and reopen the `.pbip` file
5. Your changes are now reflected in the report!

### Use the LLM to Modify PBIP Files

#### Prompt 1: Add New Visualization
```
I have this Power BI report definition (JSON). 
I want to add a new visualization that shows sales by region. 
Can you show me the JSON structure I need to add? 
Here's my current definition:

[PASTE definition.pbir CONTENT]
```

#### Prompt 2: Modify Data Model Structure
```
I need to add a new table "DimCustomer" to my Power BI data model.
It should have these columns: CustomerID, CustomerName, Region, Segment.
Here's my current data model definition:

[PASTE definition.pbdm CONTENT]

Show me the JSON I need to add for this new table, including proper relationships to my Sales table.
```

#### Prompt 3: Change Report Page Layout
```
I want to modify my report page to have a 2-column layout instead of single column.
Currently, I have 4 visuals arranged vertically.
Can you show me the JSON changes to reposition them as 2 columns with 2 rows?

Current page definition:
[PASTE CURRENT PAGE JSON]
```

#### Prompt 4: Auto-Generate Multiple Measures with GitHub Copilot

**Use Case:** Let GitHub Copilot analyze your data model and automatically create relevant measures.

**Prompt to GitHub Copilot (in VS Code chat):**
```
Look at the tables and add 5 new relevant measures. Include in the name of the measures that they were made by GHCP
```

**What GitHub Copilot Does:**
1. Analyzes your existing table structure (columns, relationships)
2. Identifies relevant metrics based on your data model
3. Generates 5 new DAX measures with proper formatting
4. Adds "[GHCP]" prefix to each measure name for tracking
5. Inserts them directly into your TMDL file

**Example Output:**
GitHub Copilot will add measures like:
- `[GHCP] YoY Growth %` - Year-over-year growth percentage
- `[GHCP] Running Total Sales` - Cumulative sales over time
- `[GHCP] Total Transactions` - Count of all transactions
- `[GHCP] Average Transaction Value` - Average per transaction
- `[GHCP] Sales per Weekday` - Weekday-only averages

**Why This Is Powerful:**
- Copilot understands your model context (Calendar table, Sales table, relationships)
- Generates contextually appropriate measures automatically
- Saves time on repetitive DAX coding
- Creates properly formatted TMDL syntax with backticks
- You can iterate: "Now add 3 more measures for product analysis"

---

## Best Practices & Tips

### ✅ Do's

1. **Start Small:** Test LLM-generated code on small datasets first
2. **Iterate:** Use multiple prompts to refine results
3. **Document:** Add comments to LLM-generated code for team clarity
4. **Version Control:** Use PBIP + Git to track changes
5. **Validate:** Always review LLM output before using in production
6. **Be Specific:** The more details in your prompt, the better the output
7. **Include Patterns:** Request realistic seasonality, trends, and anomalies
8. **Specify Distributions:** Lognormal for spending, Poisson for event counts, etc.

### ❌ Don'ts

1. **Don't use sensitive data in prompts** (DoD data restrictions)
2. **Don't trust LLM output blindly** — test and verify
3. **Don't share PBIP files with embedded credentials** — use separate connection files
4. **Don't generate identical sample data** — vary it to catch edge cases
5. **Don't rely solely on LLM** — maintain Power BI expertise on your team
6. **Don't ignore data quality** — verify generated data matches real-world patterns
7. **Don't hardcode values** — use parameters for flexible, reusable code

### Prompt Engineering Tips for Power BI

#### 1. **Provide Context**
```
❌ Poor: "Write a DAX measure for revenue"
✅ Good: "Write a DAX measure that calculates total revenue for each month, 
respecting current month/year filters in the report, and returning 0 if no data exists"
```

#### 2. **Show Your Data Structure**
```
❌ Poor: "Transform my data"
✅ Good: "Here are my source columns: Date (text, format 'MM/DD/YYYY'), Amount (text, $5,000.00), Region (text). 
I need to convert Date to date type, Amount to currency, and filter for regions 'North' and 'South' only"
```

#### 3. **Request Explanations**
```
❌ Poor: "Give me the code"
✅ Good: "Give me the M code and explain what each step does so my team can understand and modify it"
```

#### 4. **Specify Output Format**
```
❌ Poor: "Generate test data"
✅ Good: "Generate 200 rows of test data as CSV format with headers in the first row. 
Include columns: OrderID, OrderDate, Amount, Status"
```

### Workflow for Teams

1. **Developer creates PBIP** in source control
2. **Designer uses LLM** to generate theme JSON
3. **Analyst uses LLM** to write M code for ETL
4. **Developer reviews** all LLM-generated code
5. **Commit changes** to Git with descriptions
6. **Team collaborates** on refinements

### Troubleshooting Common Issues

| Issue | Solution |
|-------|----------|
| M code gives "unknown identifier" error | Check table/column names match your data source exactly |
| DAX measure returns BLANK | Use error checking; ask LLM to add IFERROR() wrapper |
| Theme colors not applying | Verify JSON syntax is valid; ask LLM to validate the JSON structure |
| Sample data doesn't load | Check CSV format is clean; no stray quotes or special characters |
| PBIP file won't open | Ensure `.pbiworkspace` file exists; try re-opening the folder |

---

## Conclusion & Next Steps

This workflow transforms Power BI from a UI-only tool into a **code-first platform** that leverages AI assistance. Your team can now:

- ✅ Use LLMs to generate Power Query and DAX code
- ✅ Maintain reports as version-controlled code (PBIP + Git)
- ✅ Create custom themes programmatically
- ✅ Generate realistic sample data for testing
- ✅ Collaborate more effectively through code reviews

**Start with one demo** (PBIP or M code), get comfortable, then expand to the full workflow.

---

## Appendix: Quick Reference Prompts

### M Code Help
```
Write Power Query M code to [GOAL].
Source data: [DESCRIBE]
Expected output: [DESCRIBE]
```

### DAX Help
```
Write a DAX measure that [GOAL].
My tables: [TABLE NAMES & KEY COLUMNS]
Filter context: [WHAT FILTERS APPLY]
```

### Theme Help
```
Generate a Power BI theme JSON with:
- Color scheme: [DESCRIBE]
- Background: [LIGHT/DARK/CUSTOM]
- Fonts: [SPECIFY]
```

### Sample Data Help
```
Generate [N] rows of sample data as CSV for:
- Columns: [COLUMN NAMES]
- Dates: [DATE RANGE]
- Values: [VALUE RANGES]
- Patterns: [REALISTIC PATTERNS]
```

### PBIP Modification Help
```
I have this Power BI definition:
[PASTE JSON]
I want to add/modify [GOAL].
Can you show me the JSON changes?
```

---

**Happy Power BI + LLM Building! 🚀**
