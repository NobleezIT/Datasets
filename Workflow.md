# The Complete Data Analyst Workflow - Video Game Sales Case Study

## 📊 Workshop Introduction: The Data Analyst Journey

Welcome! Today we'll walk through the **complete data analyst workflow** using real video game sales data. You'll learn not just *how* to write code, but *why* each step matters in professional data analysis.

### 🚀 The 6-Step Data Analysis Workflow
1. **Collect** → Get the data
2. **Inspect** → Understand what we have
3. **Clean** → Fix problems
4. **Explore** → Find patterns
5. **Visualize** → Communicate insights
6. **Report** → Share findings

---

## 📥 STEP 1: COLLECT - Gathering Your Data

### Cell 1: Imports & Setup
```python
import pandas as pd
import plotly.express as px
import plotly.io as pio
pio.renderers.default = 'notebook'
```

Why this matters:

· Real-world context: Analysts spend 5-15% of their time collecting data
· Key principle: Always start with the right tools
· Pro tip: Set visualization defaults early to avoid display issues later

Teaching Points:

· pandas = Your data Swiss Army knife
· plotly = Your presentation canvas
· Setting defaults = Being proactive about potential problems

Analyst Thinking:

"Before I touch any data, I need to ensure my environment is ready. What libraries will I need? How should visualizations appear? This is like setting up your workshop before building."

---

🔍 STEP 2: INSPECT - Understanding Your Data

Cell 2: Load and First Look

```python
df = pd.read_csv('vgsales.csv')
print(f"We have {df.shape[0]:,} games and {df.shape[1]} columns")
print("\nFirst look:")
df.head()
```

The Analyst's Inspection Checklist:

1. Volume: How much data do we have? (16,598 rows)
2. Scope: What dimensions are available? (11 columns)
3. Sample: What does actual data look like? (first 5 rows)
4. Initial observations: Any immediate patterns?

Key Business Questions Answered Here:

· "Is this dataset large enough for analysis?" ✓ Yes, 16K+ records
· "What aspects of games can we analyze?" ✓ Platform, Year, Genre, Publisher, Sales
· "What time period does this cover?" We'll check next...

Teaching Moment:

"Inspect before you analyze. A good analyst spends time understanding the data's structure, just like a mechanic understands an engine before fixing it."

---

Cell 3: Deep Inspection - Finding Problems

```python
print("Missing values:")
print(df.isnull().sum())

print("\nData types:")
print(df.dtypes)

print("\nWeird year values:")
print(df['Year'].unique()[:10])
```

The Professional Analyst's Diagnostic:

· Missing Data Audit: 271 missing years = potential bias in time-based analysis
· Data Type Verification: Year as float = potential calculation errors
· Value Sanity Check: NaN in years = data quality issue

Critical Thinking Questions for Students:

1. "Why might years be missing for some games?"
2. "What business impact could 271 missing years have?"
3. "Should we fix, remove, or flag this data?"

Real-World Context:

· Missing data is common in business datasets
· Your job: Document issues and decide on remediation strategy
· Always calculate percentage: 271/16598 = 1.6% missing

---

🧹 STEP 3: CLEAN - Fixing Data Issues

The Data Cleaning Philosophy

"Cleaning isn't about making data perfect; it's about making it reliable enough for decision-making."

Cell 4: Systematic Cleaning - Year Column

```python
# Show the problem
print(f"Before cleaning: {df['Year'].isnull().sum()} missing years")

# Fix it
df_clean = df.copy()  # ⭐ GOLDEN RULE: Never modify original data
df_clean['Year'] = pd.to_numeric(df_clean['Year'], errors='coerce')
df_clean = df_clean.dropna(subset=['Year'])  # Strategic removal
df_clean['Year'] = df_clean['Year'].astype(int)  # Type correction

print(f"After cleaning: {df_clean['Year'].isnull().sum()} missing years")
print(f"Removed {len(df) - len(df_clean)} rows")
print(f"Year range: {df_clean['Year'].min()} to {df_clean['Year'].max()}")
```

The Analyst's Cleaning Decision Framework:

Issue Options Our Choice Why
Missing Years Delete rows, Impute years, Flag as "Unknown" Delete Only 1.6% of data; preserves data integrity
Float Year Keep as float, Convert to int Convert to int Years should be whole numbers
Data Integrity Clean all at once, Clean incrementally Incremental Easier to debug; track changes

Teaching the Thought Process:

1. Diagnose: "We have 271 NaN values in Year"
2. Evaluate: "That's 1.6% of our data - relatively small"
3. Decide: "Removing them won't significantly bias our analysis"
4. Execute: "Let's convert to numeric, drop NaN, and convert to integer"
5. Verify: "Check: 0 missing, years 1980-2020, 271 rows removed"

---

Cell 5: Strategic Imputation - Publisher Column

```python
print("How many games are missing publisher info?")
print(f"Missing publishers: {df_clean['Publisher'].isnull().sum()}")

# Solution:
df_clean['Publisher'] = df_clean['Publisher'].fillna('Unknown')
print(f"Now missing: {df_clean['Publisher'].isnull().sum()}")
```

Different Problem, Different Solution:

· Years: Deleted (1.6% missing, critical for time analysis)
· Publishers: Filled with "Unknown" (0.2% missing, categorical data)

Analyst Decision Making:

"For publishers, 'Unknown' is better than deletion because:

1. It's only 36 games (0.2% - negligible)
2. We keep the sales data for those games
3. 'Unknown' publisher is actually informative - it tells us data is incomplete"

---

Cell 6: Data Standardization

```python
df_clean['Name'] = df_clean['Name'].str.title()  # Proper case
```

Why Standardize?

· Prevents "Nintendo", "nintendo", "NINTENDO" from being treated as different
· Makes grouping and filtering predictable
· Improves data quality for future analyses

Real-World Example:

· Marketing department runs campaign for "Nintendo games"
· Without standardization, they miss games labeled "nintendo"
· Result: Incomplete campaign analysis

---

🔎 STEP 4: EXPLORE - Finding Patterns

(Note: The notebook stops before exploration - let's add this critical step!)

What an Analyst Would Explore Next:

```python
# Let's add exploration cells to complete the workflow
print("=== EXPLORATORY ANALYSIS ===")

# 1. Basic Statistics
print("\n1. Sales Statistics (in millions):")
print(df_clean[['NA_Sales', 'EU_Sales', 'JP_Sales', 'Global_Sales']].describe())

# 2. Top Performers
print("\n2. Top 10 Games by Global Sales:")
print(df_clean[['Name', 'Global_Sales']].sort_values('Global_Sales', ascending=False).head(10))

# 3. Market Composition
print("\n3. Sales by Region (% of Global):")
regional_pct = df_clean[['NA_Sales', 'EU_Sales', 'JP_Sales', 'Other_Sales']].sum() / df_clean['Global_Sales'].sum() * 100
print(regional_pct.round(1))

# 4. Temporal Trends
print("\n4. Games Released by Year:")
print(df_clean['Year'].value_counts().sort_index().tail(10))
```

The Exploration Mindset:

· Look for central tendencies (averages, medians)
· Identify outliers (Wii Sports at 82M vs average ~0.2M)
· Understand distributions (most games sell modestly, few are blockbusters)
· Spot trends (sales patterns over years)

---

📈 STEP 5: VISUALIZE - Communicating Insights

(The Plotly code demonstrates this, but let's frame it as part of the workflow)

Why Visualization Matters:

1. Pattern Recognition: Humans see patterns in charts faster than in numbers
2. Storytelling: Charts tell the data's story
3. Decision Support: Executives make decisions based on visuals
4. Communication: Bridge between technical analysis and business understanding

Analyst's Visualization Strategy:

1. Start simple: Basic charts to understand distributions
2. Add context: Compare against benchmarks or averages
3. Highlight insights: Use colors and annotations
4. Iterate: Refine based on what the data reveals

Example Visualization Questions:

· "Which platform has the highest total sales?"
· "How has the gaming market evolved over time?"
· "What genres are most popular in different regions?"

---

📋 STEP 6: REPORT - Delivering Findings

(The final, often overlooked step)

What a Good Report Includes:

1. Executive Summary: Top 3 findings in plain language
2. Methodology: How we cleaned and analyzed
3. Key Insights: Supported by data
4. Recommendations: Actionable next steps
5. Limitations: What our analysis can't tell us
6. Data Quality: Notes on cleaning decisions

Sample Report Outline for This Data:

```
VIDEO GAME SALES ANALYSIS REPORT
=================================

EXECUTIVE SUMMARY:
• Nintendo dominates with 4 of top 5 games
• North America accounts for 49% of global market
• 1980-2020 shows clear console generation cycles

KEY FINDINGS:
1. Market Leadership: Wii Sports (82M) outsells #2 by 2x
2. Regional Preferences: Japan favors RPGs; NA prefers Sports
3. Platform Evolution: Wii peak in 2009, mobile emerging post-2015

RECOMMENDATIONS:
1. Focus on NA market for new releases
2. Consider platform lifecycle in release planning
3. Investigate "Unknown" publisher games for data improvement

LIMITATIONS:
• Data missing for 271 games (1.6%)
• Pre-1980 data not included
• Digital sales may be underrepresented
```

---

🔄 The Complete Workflow in Practice

Teaching This as a Process:

Before Class: Emphasize this isn't random coding - it's a methodology:

1. "First, we COLLECT our tools and data" (Cell 1-2)
2. "Then we INSPECT to understand what we're working with" (Cell 3)
3. "Next, we CLEAN to fix problems" (Cells 4-6)
4. "Now we EXPLORE to find stories in the data" (Added exploration)
5. "We VISUALIZE to communicate clearly" (Plotly section)
6. "Finally, we REPORT to drive decisions" (Summary)

Common Analyst Pitfalls to Warn About:

1. Jumping to analysis without inspection → Garbage insights
2. Over-cleaning → Removing meaningful variation
3. Under-cleaning → Letting errors propagate
4. Visualization without context → Pretty but useless charts
5. Analysis without actionable recommendations → Academic exercise

Real-World Connections:

· Marketing Analyst: Uses this to plan game launches
· Business Intelligence: Builds dashboards for executives
· Product Manager: Decides which features to develop
· Investor: Evaluates company performance

---

🎯 Your Turn: Practice the Workflow

Exercise for Students:
"Imagine you're a data analyst at Nintendo.Your manager asks: 'Which game genre should we invest in for the North American market?'

Walk through the 6-step workflow:

1. Collect: What data would you need?
2. Inspect: What would you check first?
3. Clean: What issues might you encounter?
4. Explore: What calculations would you run?
5. Visualize: What charts would tell the story?
6. Report: What would you tell your manager?"

Sample Answer Path:

1. Collect sales data by genre and region
2. Inspect for missing NA sales data
3. Clean any inconsistent genre categorizations
4. Explore average NA sales per genre, year-over-year growth
5. Visualize with bar chart (sales by genre) and trend line
6. Report: "Action games show highest NA sales (X million), with 15% annual growth. Recommend increasing action game development."

---

📚 Key Takeaways for Your Class

1. Process Over Code: The workflow matters more than any single function
2. Document Everything: Your cleaning decisions are part of the analysis
3. Think Business Impact: Every analysis should answer a business question
4. Communicate Clearly: Insights are useless if stakeholders don't understand them
5. Iterate: Data analysis is rarely linear - expect to revisit steps

Career Relevance:

· Entry Level: Can execute each step with guidance
· Mid Level: Designs and implements the workflow
· Senior Level: Teaches the workflow and ensures quality
· Manager: Uses outputs to make business decisions

---

🚀 Next Steps After This Workshop

1. Practice: Try the workflow with a different dataset
2. Deepen: Learn SQL for data collection, statistics for exploration
3. Specialize: Choose visualization (Tableau), reporting (Power BI), or analytics (Python/R)
4. Portfolio: Document 2-3 complete analyses using this workflow

Remember: Every data analyst follows some version of this workflow. Master the process, and you can analyze anything!

---

"Data is just numbers until you apply a thoughtful process. That process turns numbers into insights, and insights into impact."

```
