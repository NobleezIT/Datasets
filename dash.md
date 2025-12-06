# **COMPLETE LESSON: Building a Video Game Analytics Dashboard with Streamlit**

## **Table of Contents**

1. [Introduction to Streamlit](#1-introduction-to-streamlit)
2. [Setting Up the Dashboard](#2-setting-up-the-dashboard)
3. [Data Loading & Caching](#3-data-loading--caching)
4. [Dashboard Layout Design](#4-dashboard-layout-design)
5. [Interactive Filters](#5-interactive-filters)
6. [Data Visualization with Plotly](#6-data-visualization-with-plotly)
7. [Advanced Features](#7-advanced-features)
8. [Deployment & Next Steps](#8-deployment--next-steps)

---

## **1. Introduction to Streamlit**

### **What is Streamlit?**
Streamlit is an **open-source Python framework** for building web applications for data science and machine learning. Think of it as **PowerPoint for data apps** - you write Python code, and Streamlit turns it into a beautiful, interactive web application.

### **Why Use Streamlit?**
- **No web development needed** (HTML, CSS, JavaScript)
- **Fast prototyping** - Go from script to app in minutes
- **Built-in widgets** - Sliders, buttons, selects, etc.
- **Automatic reactivity** - Updates when users interact
- **Free deployment** on Streamlit Community Cloud

### **The Streamlit Philosophy**
```python
# Traditional web app: Complex
HTML + CSS + JavaScript + Backend + Database

# Streamlit app: Simple
Python script → Beautiful web app
```

---

## **2. Setting Up the Dashboard**

### **2.1 Importing Required Libraries**
```python
import streamlit as st      # Dashboard framework
import pandas as pd         # Data manipulation
import plotly.express as px # Interactive charts
from pathlib import Path    # File path handling
```

**Teaching Analogy:**
- **Streamlit** = The car chassis and dashboard
- **Pandas** = The engine (processes data)
- **Plotly** = The GPS and entertainment system (visualizations)
- **Pathlib** = The navigation system (finds files)

### **2.2 Page Configuration**
```python
st.set_page_config(
    page_title="Video Game Analytics Dashboard",
    page_icon="🎮",
    layout="wide",
    initial_sidebar_state="expanded"
)
```

| Parameter | Purpose | Example |
|-----------|---------|---------|
| `page_title` | Browser tab title | "Video Game Analytics Dashboard" |
| `page_icon` | Browser tab icon | 🎮, 📊, 🔍 |
| `layout` | Screen layout | "wide" or "centered" |
| `initial_sidebar_state` | Filter panel state | "expanded" or "collapsed" |

### **2.3 Custom Styling**
```python
st.markdown("""
<style>
    .main-header {
        font-size: 2.5rem;
        color: #FF6B6B;
    }
    .metric-card {
        background-color: #f0f2f6;
        padding: 20px;
        border-radius: 10px;
        margin: 10px 0;
    }
</style>
""", unsafe_allow_html=True)
```

**CSS Basics for Data Apps:**
- `font-size: 2.5rem` = 2.5 × default font size
- `color: #FF6B6B` = Hex color code (coral red)
- `padding: 20px` = Space inside element
- `border-radius: 10px` = Rounded corners
- `margin: 10px 0` = Space outside element

---

## **3. Data Loading & Caching**

### **3.1 The Caching Concept**

**Problem:** Without caching, Streamlit reloads data on EVERY interaction
**Solution:** `@st.cache_data` decorator

```python
@st.cache_data
def load_data():
    # This runs once and gets remembered
    return processed_data
```

**Visual Analogy:**
```
First load: Raw vegetables → Chopping → Cooking → Serving (SLOW)
Subsequent: Already cooked meal → Serving (FAST)
```

### **3.2 Complete Data Loading Function**
```python
@st.cache_data
def load_data():
    """Load and clean the video game sales data."""
    # 1. Load from file or URL
    data_path = 'vgsales.csv'
    df = pd.read_csv(data_path)
    
    # 2. Data cleaning pipeline
    df['Year'] = pd.to_numeric(df['Year'], errors='coerce')  # Convert to numbers
    df = df.dropna(subset=['Year'])                          # Remove missing years
    df['Year'] = df['Year'].astype(int)                      # Convert to integers
    df['Publisher'] = df['Publisher'].fillna('Unknown')      # Fill missing publishers
    
    return df  # Return cleaned DataFrame

# Call the function
df = load_data()  # Streamlit shows loading spinner automatically
```

**Data Cleaning Steps Explained:**

| Step | Code | Purpose | Example |
|------|------|---------|---------|
| 1 | `pd.to_numeric()` | Convert text to numbers | "1985" → 1985 |
| 2 | `errors='coerce'` | Handle errors gracefully | "N/A" → NaN |
| 3 | `dropna()` | Remove missing values | Remove rows without year |
| 4 | `astype(int)` | Convert to integer | 1985.0 → 1985 |
| 5 | `fillna()` | Replace missing values | NaN → "Unknown" |

---

## **4. Dashboard Layout Design**

### **4.1 Title and Description**
```python
st.markdown('<h1 class="main-header">🎮 Video Game Sales Dashboard</h1>', 
            unsafe_allow_html=True)
st.markdown("Explore 30+ years of video game sales data from around the world.")
```

**HTML Tags in Streamlit:**
- `<h1>` to `<h6>` = Headings (h1 is largest)
- `<p>` = Paragraph
- `<div>` = Division/container
- `<span>` = Inline container

### **4.2 Key Metrics Display**
```python
# Create 4 equal columns
col1, col2, col3, col4 = st.columns(4)

with col1:
    st.metric("Total Games", f"{len(df):,}")
with col2:
    st.metric("Years Covered", f"{df['Year'].min()} - {df['Year'].max()}")
with col3:
    st.metric("Total Sales", f"{df['Global_Sales'].sum():.0f}M")
with col4:
    st.metric("Unique Publishers", df['Publisher'].nunique())
```

**Column Layout Pattern:**
```python
col1, col2 = st.columns(2)  # Create slots
with col1:                  # Put things in first slot
    st.write("Left content")
with col2:                  # Put things in second slot
    st.write("Right content")
```

### **4.3 Divider for Visual Separation**
```python
st.divider()  # Creates a horizontal line
```

---

## **5. Interactive Filters**

### **5.1 Creating the Sidebar**
```python
with st.sidebar:  # Everything indented goes in sidebar
    st.header("🎛️ Filter Controls")
```

**Sidebar Best Practices:**
- Put filters that don't change often
- Keep main area for visualizations
- Group related controls together

### **5.2 Filter Widgets**

#### **Slider (Range Selection)**
```python
min_year = int(df['Year'].min())  # Get earliest year (1980)
max_year = int(df['Year'].max())  # Get latest year (2020)

year_range = st.slider(
    "Select Year Range:",      # Label
    min_year, max_year,        # Min and max values
    value=(2000, 2015)         # Default selection
)
# Returns: (2000, 2015)
```

#### **Selectbox (Single Choice)**
```python
# Prepare options: ["All Genres", "Action", "Adventure", ...]
genres = ['All Genres'] + sorted(df['Genre'].unique().tolist())

selected_genre = st.selectbox(
    "Select Genre:",  # Label
    genres            # Options list
)
```

#### **Multiselect (Multiple Choices)**
```python
platforms = sorted(df['Platform'].unique().tolist())

selected_platforms = st.multiselect(
    "Select Platforms:",           # Label
    platforms,                     # Options
    default=['PS4', 'XOne', 'PC', 'Wii']  # Pre-selected
)
# Returns: ['PS4', 'PC'] or [] if none selected
```

#### **Slider with Float Values**
```python
min_sales = st.slider(
    "Minimum Global Sales (millions):",
    0.0,                          # Min value
    float(df['Global_Sales'].max()),  # Max value
    1.0                           # Default value
)
```

### **5.3 Common Streamlit Widgets**

| Widget | Purpose | Returns |
|--------|---------|---------|
| `st.slider()` | Range selection | Number or tuple |
| `st.selectbox()` | Dropdown menu | Single value |
| `st.multiselect()` | Multiple selection | List of values |
| `st.text_input()` | Text input | String |
| `st.number_input()` | Number input | Number |
| `st.checkbox()` | Yes/No toggle | Boolean |
| `st.radio()` | Exclusive choice | Single value |
| `st.button()` | Action button | Boolean (clicked?) |

---

## **6. Data Visualization with Plotly**

### **6.1 Applying Filters to Data**
```python
# Start with all data
filtered_df = df.copy()

# Apply year filter
filtered_df = filtered_df[
    (filtered_df['Year'] >= year_range[0]) & 
    (filtered_df['Year'] <= year_range[1])
]

# Apply genre filter (if not "All Genres")
if selected_genre != 'All Genres':
    filtered_df = filtered_df[filtered_df['Genre'] == selected_genre]

# Apply platform filter (if any selected)
if selected_platforms:  # Checks if list is not empty
    filtered_df = filtered_df[filtered_df['Platform'].isin(selected_platforms)]
```

**Boolean Indexing Explained:**
```python
# Creates True/False mask
mask = df['Year'] >= 2000
# Applies mask to select rows
filtered = df[mask]
```

### **6.2 Creating Tabs for Organization**
```python
tab1, tab2, tab3 = st.tabs(["📈 Overview", "📊 Details", "🎯 Top Performers"])
```

**Tab Structure:**
```python
with tab1:
    # Content for first tab
    st.write("Overview charts here")
    
with tab2:
    # Content for second tab
    st.write("Detailed data here")
```

### **6.3 Chart 1: Horizontal Bar Chart (Top Genres)**
```python
with tab1:
    col1, col2, col3 = st.columns(3)
    
    with col1:
        # 1. Aggregate data
        genre_data = filtered_df.groupby('Genre')['Global_Sales'].sum()
        
        # 2. Convert to DataFrame and sort
        genre_data = genre_data.reset_index()
        genre_data = genre_data.sort_values('Global_Sales', ascending=False)
        
        # 3. Take top 10
        genre_data = genre_data.head(10)
        
        # 4. Create chart
        fig1 = px.bar(
            genre_data,
            x='Global_Sales',      # X-axis values
            y='Genre',             # Y-axis categories
            orientation='h',       # Horizontal bars
            title=f"Top Genres ({len(filtered_df)} games)",
            color='Global_Sales'   # Color by value
        )
        
        # 5. Display in Streamlit
        st.plotly_chart(fig1, use_container_width=True)
```

**Data Aggregation Steps:**
1. **Group**: `groupby('Genre')` - Split data by genre
2. **Aggregate**: `['Global_Sales'].sum()` - Sum sales per group
3. **Reset Index**: `.reset_index()` - Convert to DataFrame
4. **Sort**: `.sort_values()` - Order by sales
5. **Select**: `.head(10)` - Take top 10

### **6.4 Chart 2: Line Chart (Sales Trend)**
```python
with col2:
    # Group by year and sum sales
    yearly_data = filtered_df.groupby('Year')['Global_Sales'].sum().reset_index()
    
    fig2 = px.line(
        yearly_data,
        x='Year',            # Time on x-axis
        y='Global_Sales',    # Values on y-axis
        title="Sales Trend Over Time",
        markers=True         # Add dots on data points
    )
    st.plotly_chart(fig2, use_container_width=True)
```

**When to Use Line Charts:**
- ✅ Time series data
- ✅ Trends over time
- ✅ Comparing multiple lines
- ❌ Categorical comparisons

### **6.5 Chart 3: Donut Chart (Regional Distribution)**
```python
with col3:
    # Select regional columns
    region_cols = ['NA_Sales', 'EU_Sales', 'JP_Sales', 'Other_Sales']
    
    # Sum each region's sales
    region_data = filtered_df[region_cols].sum()
    
    fig3 = px.pie(
        values=region_data.values,           # Numerical values
        names=['North America', 'Europe', 'Japan', 'Other'],  # Labels
        title="Regional Sales Distribution",
        hole=0.4  # Creates donut chart (0 = pie, 0.5 = thick donut)
    )
    st.plotly_chart(fig3, use_container_width=True)
```

**Pie/Donut Chart Guidelines:**
- Use for 3-7 categories max
- Donut charts are easier to read
- Include percentages or values
- Consider bar charts for many categories

---

## **7. Advanced Features**

### **7.1 Searchable Data Table**
```python
with tab2:
    # Search input
    search_term = st.text_input("🔍 Search game names:", "")
    
    # Apply search filter
    if search_term:
        display_df = filtered_df[filtered_df['Name'].str.contains(
            search_term, 
            case=False,   # Case insensitive
            na=False      # Handle missing values
        )]
    else:
        display_df = filtered_df
    
    # Display interactive table
    st.dataframe(
        display_df[['Name', 'Platform', 'Year', 'Genre', 'Publisher', 'Global_Sales']]
        .sort_values('Global_Sales', ascending=False),
        use_container_width=True,
        height=400  # Fixed height with scroll
    )
```

**String Operations in Pandas:**
- `.str.contains()` = Search text
- `.str.lower()` = Convert to lowercase
- `.str.replace()` = Replace text
- `.str.extract()` = Extract patterns

### **7.2 Download Button**
```python
# Convert DataFrame to CSV string
csv = display_df.to_csv(index=False)  # Don't include row numbers

st.download_button(
    label="📥 Download filtered data as CSV",
    data=csv,                        # The file content
    file_name="filtered_game_sales.csv",  # Suggested filename
    mime="text/csv"                  # File type
)
```

### **7.3 Top Performers Analysis**
```python
with tab3:
    # Top 10 games
    top_games = filtered_df.sort_values('Global_Sales', ascending=False).head(10)
    
    fig4 = px.bar(
        top_games,
        x='Global_Sales',
        y='Name',
        orientation='h',
        color='Platform',  # Color code by platform
        title="Top 10 Games by Sales",
        labels={'Global_Sales': 'Sales (millions)', 'Name': 'Game Title'}
    )
    st.plotly_chart(fig4, use_container_width=True)
```

**Customizing Plotly Charts:**
```python
px.bar(
    data_frame=df,
    x='values',
    y='categories',
    color='group_column',      # Color by category
    title='Chart Title',
    labels={'x': 'X Label', 'y': 'Y Label'},  # Custom axis labels
    orientation='h',           # Horizontal or vertical
    color_continuous_scale='Viridis'  # Color scheme
)
```

---

## **8. Deployment & Next Steps**

### **8.1 Running Your Dashboard**
```bash
# In terminal/command prompt
streamlit run dashboard.py

# Output:
# Local URL: http://localhost:8501
# Network URL: http://192.168.x.x:8501
```

### **8.2 Deploying to Streamlit Community Cloud**

1. **Push code to GitHub**
2. **Go to** [share.streamlit.io](https://share.streamlit.io)
3. **Connect GitHub repository**
4. **Select file** (`dashboard.py`)
5. **Click Deploy** (takes 1-2 minutes)

### **8.3 Next Learning Steps**

#### **Advanced Streamlit Features**
```python
# Session State (remember user data)
if 'counter' not in st.session_state:
    st.session_state.counter = 0

# Forms (batch inputs)
with st.form("my_form"):
    name = st.text_input("Name")
    submitted = st.form_submit_button("Submit")

# Progress bars
import time
progress_bar = st.progress(0)
for i in range(100):
    time.sleep(0.1)
    progress_bar.progress(i + 1)
```

#### **Connect to Databases**
```python
import sqlite3
conn = sqlite3.connect('games.db')
df = pd.read_sql_query("SELECT * FROM games", conn)
```

#### **Add Authentication**
```python
# Using Streamlit-Authenticator
import streamlit_authenticator as stauth

authenticator = stauth.Authenticate(
    credentials,
    "app_name",
    "cookie_name",
    30  # Days to expire
)
```

---

## **Appendix A: Common Errors & Solutions**

| Error | Cause | Solution |
|-------|-------|----------|
| `NameError: name '__file__' is not defined` | Running in notebook | Use `streamlit run script.py` |
| Charts not updating | Forgetting to filter | Check filter logic |
| Slow performance | No caching | Add `@st.cache_data` |
| Widgets not working | Wrong indentation | Check `with` statements |
| Data not loading | Wrong file path | Use absolute paths or URLs |

---

## **Appendix B: Best Practices Checklist**

### **✅ Data Handling**
- [ ] Use `@st.cache_data` for expensive operations
- [ ] Clean data in loading function
- [ ] Handle missing values appropriately
- [ ] Validate data types

### **✅ User Interface**
- [ ] Clear, descriptive titles
- [ ] Logical filter grouping
- [ ] Responsive layout (test on mobile)
- [ ] Accessible colors and fonts

### **✅ Performance**
- [ ] Limit data to what's needed
- [ ] Use appropriate chart types
- [ ] Implement pagination for large tables
- [ ] Consider data sampling for exploration

### **✅ User Experience**
- [ ] Provide defaults for filters
- [ ] Show loading indicators
- [ ] Include data download options
- [ ] Add help text or tooltips

---

## **Appendix C: Exercise Projects**

### **Beginner: Sales Tracker**
- Add monthly sales trend
- Compare two genres side-by-side
- Add platform comparison chart

### **Intermediate: Game Recommendation Engine**
- Filter by multiple criteria
- Show similar games
- Add user rating system

### **Advanced: Predictive Analytics Dashboard**
- Sales forecasting
- Genre trend analysis
- Market share predictions

---

## **Resources & References**

### **Official Documentation**
- [Streamlit Documentation](https://docs.streamlit.io)
- [Plotly Express Reference](https://plotly.com/python/plotly-express/)
- [Pandas User Guide](https://pandas.pydata.org/docs/user_guide/index.html)

### **Learning Platforms**
- [Streamlit Tutorials](https://docs.streamlit.io/get-started/tutorials)
- [DataCamp: Streamlit Course](https://www.datacamp.com/courses/streamlit)
- [Coursera: Data Apps with Streamlit](https://www.coursera.org)

### **Community**
- [Streamlit Forum](https://discuss.streamlit.io)
- [Stack Overflow (#streamlit)](https://stackoverflow.com/questions/tagged/streamlit)
- [GitHub Issues](https://github.com/streamlit/streamlit/issues)



---


**Happy Dashboard Building! 🎮📊🚀**
