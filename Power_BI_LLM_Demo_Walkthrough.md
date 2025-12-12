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
   Source = Excel.Workbook(File.Contents("C:\Data\sales.xlsx"), null, true),
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

The file is at: C:\data\sales.csv

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

Include the complete code from let to in.
```

**Expected Output:** Refactored M code with readable step names and explanatory comments.

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
2. Include these columns: Date, Month, Month Number, Quarter, Year, Year-Month
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

Tables:
- Sales Gemini (Date, Sales Amount, Region, Product)
- Calendar (Date, Month, Month Number, Quarter, Year, Year-Month)
```

### Use Case 3: Ranking & Segmentation

#### Prompt 3.1: Top N Ranking with Ties

```
Write DAX measures for ranking and segmentation:
1. Rank by Sales - Rank regions by total sales
2. Percentile Rank - Show percentile ranking
3. Segment - Categorize as "Top Performer", "Mid-Tier", "Low Performer"
```

### Use Case 4: Complex Business Logic

#### Prompt 4.1: Customer Lifetime Value (CLV) & Churn

```
Write DAX measures for customer analytics:
1. Total Customer Revenue - Sum of all purchases by customer
2. Purchase Frequency - Count of orders per customer
3. Average Order Value - Total Revenue / Order Count
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

### Use Case 1: Iterative Theme Refinement

#### Prompt 1.1: DoD Compliance Dark Theme

```
Generate a Power BI theme JSON for a DoD environment that:
1. Uses a dark background for accessibility
2. Military blue (#003A70) as primary color
3. High contrast colors for visibility
4. Professional sans-serif fonts
5. 6 distinct data colors for multiple series
6. Accessible color palette (WCAG AAA standards)
7. Color-blind friendly (avoid red-green only)
```

---

## Demo 4: Sample Data Creation with LLM

**Why Sample Data?**  
Demo environments, testing, and development often need realistic sample data without exposing real DoD data.

### Step-by-Step Walkthrough

#### Step 4.1: Use LLM to Generate Sample Data

#### Step 4.2: Use Case 1 - Sales & Revenue Data

##### Prompt 1.1: Basic Sales Dataset

```
Generate sample sales data for testing a Power BI report.

Requirements:
1. 500 rows of data
2. Columns: TransactionID, Date, Region, Product, SalesAmount, Quantity
3. Date range: January 1, 2024 to December 31, 2024
4. Regions: North, South, East, West
5. Format as CSV with headers
```

---

## Demo 5: Power BI as Code (PBIP)

**What is PBIP?**  
PBIP (Power BI Project) stores Power BI files as folders with source files instead of a single `.pbix` binary. This enables:
- Git version control
- Code-level diffing
- Team collaboration
- LLM-assisted modifications

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
4. Choose a folder location
5. Click **Save**

#### Step 5.3: Open PBIP in a Text Editor or IDE

**Option 1: VS Code (Recommended)**
1. Right-click the **MyFirstPBIP** folder
2. Select **"Open with Code"** (if VS Code is installed)
3. Browse the folder structure in the Explorer pane
4. Open `definition.pbir` or `definition.pbdm` to see JSON/TMDL code

**Option 2: Any Text Editor**
1. Navigate to the PBIP folder in File Explorer
2. Right-click `definition.pbir` → Open With → Notepad
3. You'll see the JSON structure of your report

#### Step 5.5: Add DAX Measures Directly in the IDE

**Option 1: Edit model.bim or definition.pbdm**

1. In VS Code, navigate to the `.Dataset` folder
2. Open `model.bim` (or `definition.pbdm` depending on format)
3. Find the table where you want to add a measure
4. Locate the `"measures"` array within that table
5. Add your new measure to the array

#### Step 5.6: Save and Reload in Power BI

1. Save the `model.bim` file in your text editor (Ctrl+S)
2. Go back to Power BI Desktop
3. Close the `.pbip` file (if open)
4. Reopen the `.pbip` file from File Explorer
5. Your new measures appear in the Fields pane!

### Use the LLM to Modify PBIP Files

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
- Copilot understands your model context
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

### ❌ Don'ts

1. **Don't use sensitive data in prompts** (DoD data restrictions)
2. **Don't trust LLM output blindly** — test and verify
3. **Don't share PBIP files with embedded credentials** — use separate connection files
4. **Don't generate identical sample data** — vary it to catch edge cases
5. **Don't rely solely on LLM** — maintain Power BI expertise on your team

### Troubleshooting Common Issues

| Issue | Solution |
|-------|----------|
| M code gives "unknown identifier" error | Check table/column names match your data source exactly |
| DAX measure returns BLANK | Use error checking; ask LLM to add IFERROR() wrapper |
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

**Happy Power BI + LLM Building! 🚀**