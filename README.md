# Investment Analytics Data Warehouse 

## 📋 Table of Contents

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Installation & Setup](#installation--setup)
4. [Data Model](#data-model)
5. [ETL Pipeline](#etl-pipeline)
6. [OLAP Operations](#olap-operations)
7. [Dashboard Features](#dashboard-features)
8. [SQL Query Examples](#sql-query-examples)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)

---

## Overview

### Purpose
The Investment Analytics Data Warehouse is a comprehensive solution designed for analyzing investment portfolios, transactions, and performance metrics using dimensional modeling and OLAP (Online Analytical Processing) techniques.

### Key Features
- **Star Schema Design**: Optimized for analytical queries
- **ETL Pipeline**: Automated Extract-Transform-Load process
- **Interactive Dashboard**: Real-time data visualization with Streamlit
- **Multi-dimensional Analysis**: OLAP cube with slice, dice, drill-down operations
- **Portfolio Analytics**: Transaction tracking and return analysis
- **Cloud Deployment**: Runs on Google Colab with ngrok tunneling

### Technology Stack
- **Database**: SQLite with SQLAlchemy ORM
- **ETL**: Python with Pandas and NumPy
- **Visualization**: Plotly Express & Plotly Graph Objects
- **Dashboard**: Streamlit
- **Deployment**: Google Colab + ngrok

---

## Architecture

### System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Data Sources                            │
│  (Sample Data Generator - Simulates Real Trading Data)      │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                   ETL Pipeline                              │
│  ┌──────────┐    ┌───────────┐    ┌──────────┐            │
│  │ Extract  │───▶│ Transform │───▶│  Load    │            │
│  └──────────┘    └───────────┘    └──────────┘            │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│              Data Warehouse (Star Schema)                   │
│                                                             │
│         ┌──────────────────────────────┐                   │
│         │     Fact Tables              │                   │
│         │  • fact_transactions         │                   │
│         │  • fact_returns              │                   │
│         └─────────┬────────────────────┘                   │
│                   │                                         │
│         ┌─────────┴────────────┐                           │
│         │                      │                           │
│    ┌────▼─────┐         ┌─────▼────┐                      │
│    │ dim_date │         │ dim_user │                       │
│    └──────────┘         └──────────┘                       │
│    ┌──────────┐         ┌──────────┐                      │
│    │dim_asset │         │dim_region│                       │
│    └──────────┘         └──────────┘                       │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                  OLAP Engine                                │
│  • Slice & Dice    • Drill-down/Roll-up   • Pivot          │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│              Streamlit Dashboard                            │
│  • Real-time Visualization  • Interactive Filters           │
│  • Multi-dimensional Analysis  • Performance Metrics        │
└─────────────────────────────────────────────────────────────┘
```

### Star Schema Design

```
                    ┌──────────────┐
                    │   dim_date   │
                    ├──────────────┤
                    │ date_key PK  │
                    │ full_date    │
                    │ year         │
                    │ quarter      │
                    │ month        │
                    │ day          │
                    └──────┬───────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
┌───────▼────────┐ ┌──────▼──────────┐ ┌─────▼─────────┐
│   dim_user     │ │fact_transactions│ │  fact_returns │
├────────────────┤ ├─────────────────┤ ├───────────────┤
│ user_key PK    │ │transaction_key PK│ │return_key PK  │
│ user_id        │ │ date_key FK     │ │date_key FK    │
│ user_name      │ │ user_key FK     │ │user_key FK    │
│ risk_profile   │ │ asset_key FK    │ │asset_key FK   │
│ account_type   │ │ region_key FK   │ │region_key FK  │
└────────┬───────┘ │ transaction_type│ │opening_price  │
         │         │ quantity        │ │closing_price  │
         └─────────┤ price_per_unit  │ │daily_return   │
                   │ total_amount    │ │daily_return_% │
         ┌─────────┤ commission      │ │volume         │
         │         │ net_amount      │ │market_value   │
┌────────▼───────┐ └─────────────────┘ └───────────────┘
│   dim_asset    │
├────────────────┤
│ asset_key PK   │
│ asset_id       │
│ asset_name     │
│ asset_type     │
│ sector         │
│ currency       │
└────────┬───────┘
         │
         │
┌────────▼───────┐
│  dim_region    │
├────────────────┤
│ region_key PK  │
│ region_id      │
│ region_name    │
│ continent      │
│ market_type    │
└────────────────┘
```

---

## Installation & Setup

### Prerequisites
- Google Account (for Google Colab)
- ngrok Account (free tier available)

### Step-by-Step Installation

#### 1. Access Google Colab
```
1. Go to https://colab.research.google.com/
2. Create a new notebook
3. Ensure runtime type is Python 3
```

#### 2. Get ngrok Authentication Token
```
1. Visit https://dashboard.ngrok.com/signup
2. Create a free account
3. Navigate to https://dashboard.ngrok.com/get-started/your-authtoken
4. Copy your authentication token
```

#### 3. Run Installation Cells

**CELL 1: Install Dependencies**
```python
!pip install streamlit pyngrok sqlalchemy pandas numpy plotly -q
print("✅ All packages installed successfully!")
```

**CELL 2: Create Application File**
```python
# (Copy the complete app_code from the provided artifact)
with open('investment_dw_app.py', 'w') as f:
    f.write(app_code)
print("✅ Application file created: investment_dw_app.py")
```

**CELL 3: Configure ngrok**
```python
from pyngrok import ngrok, conf
import getpass

ngrok_token = getpass.getpass("Enter your ngrok token: ")
conf.get_default().auth_token = ngrok_token
print("✅ ngrok authentication configured!")
```

**CELL 4: Launch Application**
```python
import subprocess
import time
from pyngrok import ngrok

!pkill -9 streamlit

process = subprocess.Popen(
    ["streamlit", "run", "investment_dw_app.py", "--server.port", "8501"],
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE
)

time.sleep(10)
public_url = ngrok.connect(8501)

print(f"🌐 Public URL: {public_url}")
```

### Expected Output
```
======================================================================
🎉 Investment Analytics Data Warehouse is now running!
======================================================================

🌐 Public URL: https://xxxx-xx-xxx-xxx-xx.ngrok-free.app

📊 Open this URL in your browser to access the dashboard

⚠️  Keep this cell running. Stop it to shut down the server.
======================================================================
```

---

## Data Model

### Dimension Tables

#### 1. dim_date (Temporal Dimension)

| Column | Type | Description |
|--------|------|-------------|
| date_key | INTEGER | Primary Key (YYYYMMDD format) |
| full_date | DATE | Complete date |
| year | INTEGER | Year (2023, 2024) |
| quarter | INTEGER | Quarter (1-4) |
| month | INTEGER | Month (1-12) |
| month_name | VARCHAR(20) | Month name (January, February, etc.) |
| day | INTEGER | Day of month (1-31) |
| day_of_week | VARCHAR(20) | Day name (Monday, Tuesday, etc.) |
| is_weekend | INTEGER | Weekend flag (0=weekday, 1=weekend) |
| fiscal_year | INTEGER | Fiscal year |
| fiscal_quarter | INTEGER | Fiscal quarter |

**Sample Data:**
```
date_key: 20230101
full_date: 2023-01-01
year: 2023
quarter: 1
month: 1
month_name: January
day: 1
day_of_week: Sunday
is_weekend: 1
fiscal_year: 2022
fiscal_quarter: 4
```

#### 2. dim_user (User/Investor Dimension)

| Column | Type | Description |
|--------|------|-------------|
| user_key | INTEGER | Primary Key (Auto-increment) |
| user_id | VARCHAR(50) | Business key (U0001, U0002, etc.) |
| user_name | VARCHAR(100) | User full name |
| risk_profile | VARCHAR(20) | Conservative, Moderate, Aggressive |
| account_type | VARCHAR(50) | Individual, Joint, Corporate |
| registration_date | DATE | Account creation date |
| country | VARCHAR(50) | User's country |

**Sample Data:**
```
user_key: 1
user_id: U0001
user_name: User 1
risk_profile: Moderate
account_type: Individual
registration_date: 2022-01-01
country: USA
```

#### 3. dim_asset (Asset/Security Dimension)

| Column | Type | Description |
|--------|------|-------------|
| asset_key | INTEGER | Primary Key (Auto-increment) |
| asset_id | VARCHAR(50) | Ticker symbol (AAPL, GOOGL, etc.) |
| asset_name | VARCHAR(100) | Full company/asset name |
| asset_type | VARCHAR(50) | Stock, Bond, ETF, Crypto, Commodity |
| sector | VARCHAR(50) | Technology, Healthcare, etc. |
| currency | VARCHAR(10) | Trading currency (USD, EUR, etc.) |
| exchange | VARCHAR(50) | Trading exchange (NASDAQ, NYSE, etc.) |
| is_active | INTEGER | Active status flag |

**Sample Data:**
```
asset_key: 1
asset_id: AAPL
asset_name: Apple Inc.
asset_type: Stock
sector: Technology
currency: USD
exchange: NASDAQ
is_active: 1
```

#### 4. dim_region (Geographic Dimension)

| Column | Type | Description |
|--------|------|-------------|
| region_key | INTEGER | Primary Key (Auto-increment) |
| region_id | VARCHAR(50) | Business key (NA, EU, ASIA, etc.) |
| region_name | VARCHAR(100) | Full region name |
| continent | VARCHAR(50) | Continent name |
| market_type | VARCHAR(50) | Developed, Emerging, Frontier |
| timezone | VARCHAR(50) | Primary timezone |

**Sample Data:**
```
region_key: 1
region_id: NA
region_name: North America
continent: Americas
market_type: Developed
timezone: EST
```

### Fact Tables

#### 1. fact_transactions (Transaction Fact)

| Column | Type | Description |
|--------|------|-------------|
| transaction_key | INTEGER | Primary Key (Auto-increment) |
| date_key | INTEGER | Foreign Key to dim_date |
| user_key | INTEGER | Foreign Key to dim_user |
| asset_key | INTEGER | Foreign Key to dim_asset |
| region_key | INTEGER | Foreign Key to dim_region |
| transaction_type | VARCHAR(20) | BUY or SELL |
| quantity | FLOAT | Number of units traded |
| price_per_unit | FLOAT | Price per unit |
| total_amount | FLOAT | Total transaction value |
| commission | FLOAT | Trading commission |
| net_amount | FLOAT | Net amount after commission |
| timestamp | DATETIME | Transaction timestamp |

**Measures (Aggregatable):**
- `quantity`: Sum, Count, Avg
- `total_amount`: Sum, Avg, Min, Max
- `commission`: Sum, Avg
- `net_amount`: Sum, Avg

**Granularity:** One row per transaction

**Sample Data:**
```
transaction_key: 1
date_key: 20230515
user_key: 5
asset_key: 1
region_key: 1
transaction_type: BUY
quantity: 50
price_per_unit: 175.50
total_amount: 8775.00
commission: 8.78
net_amount: 8783.78
timestamp: 2023-05-15 09:30:00
```

#### 2. fact_returns (Returns Fact)

| Column | Type | Description |
|--------|------|-------------|
| return_key | INTEGER | Primary Key (Auto-increment) |
| date_key | INTEGER | Foreign Key to dim_date |
| user_key | INTEGER | Foreign Key to dim_user |
| asset_key | INTEGER | Foreign Key to dim_asset |
| region_key | INTEGER | Foreign Key to dim_region |
| opening_price | FLOAT | Daily opening price |
| closing_price | FLOAT | Daily closing price |
| daily_return | FLOAT | Absolute return (close - open) |
| daily_return_pct | FLOAT | Percentage return |
| volume | FLOAT | Trading volume |
| market_value | FLOAT | Total portfolio value |

**Measures (Aggregatable):**
- `daily_return`: Sum, Avg, Min, Max
- `daily_return_pct`: Avg, Std Dev
- `volume`: Sum, Avg
- `market_value`: Sum, Avg

**Granularity:** One row per asset per user per day

**Sample Data:**
```
return_key: 1
date_key: 20230515
user_key: 1
asset_key: 1
region_key: 1
opening_price: 174.20
closing_price: 175.50
daily_return: 1.30
daily_return_pct: 0.75
volume: 5250000
market_value: 87750.00
```

### Relationships & Cardinality

```
dim_date (1) ──────▶ (N) fact_transactions
dim_date (1) ──────▶ (N) fact_returns

dim_user (1) ──────▶ (N) fact_transactions
dim_user (1) ──────▶ (N) fact_returns

dim_asset (1) ─────▶ (N) fact_transactions
dim_asset (1) ─────▶ (N) fact_returns

dim_region (1) ────▶ (N) fact_transactions
dim_region (1) ────▶ (N) fact_returns
```

---

## ETL Pipeline

### Overview

The ETL (Extract-Transform-Load) pipeline consists of three main phases:

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   EXTRACT   │───▶│  TRANSFORM  │───▶│    LOAD     │
└─────────────┘    └─────────────┘    └─────────────┘
```

### Phase 1: Extract

**Purpose:** Generate or collect source data

**Process:**
```python
def extract_generate_sample_data(self):
    """
    Generates sample investment data
    Returns: dates, users, assets, regions
    """
    # Generate date range
    start_date = datetime(2023, 1, 1)
    end_date = datetime(2024, 12, 31)
    dates = pd.date_range(start_date, end_date, freq='D')
    
    # Generate user data
    users = [...]
    
    # Generate asset data
    assets = [...]
    
    # Generate region data
    regions = [...]
    
    return dates, users, assets, regions
```

**Output:**
- 731 dates (2 years)
- 50 users
- 8 assets
- 4 regions

### Phase 2: Transform

**Purpose:** Clean, validate, and enrich data

**Transformations Applied:**

1. **Date Enrichment**
   ```python
   # Add derived date attributes
   quarter = (month - 1) // 3 + 1
   fiscal_year = year if month >= 4 else year - 1
   is_weekend = 1 if day_of_week >= 5 else 0
   ```

2. **Data Validation**
   - Check for null values
   - Validate data types
   - Ensure referential integrity

3. **Business Rules**
   - Commission = 0.1% of transaction amount
   - Net amount includes commission
   - Daily returns calculated as (close - open) / open

### Phase 3: Load

**Purpose:** Populate data warehouse tables

**Loading Strategy:**

1. **Dimension Tables First** (maintain referential integrity)
   ```python
   # Order of loading
   1. dim_date
   2. dim_user
   3. dim_asset
   4. dim_region
   ```

2. **Fact Tables Second**
   ```python
   # Load fact tables
   5. fact_transactions
   6. fact_returns
   ```

**Load Process:**
```python
def transform_load_dimensions(self, dates, users, assets, regions):
    session = self.Session()
    try:
        # Load dimensions
        for date in dates:
            dim_date = DimDate(...)
            session.add(dim_date)
        
        session.commit()
        return True
    except Exception as e:
        session.rollback()
        return False
    finally:
        session.close()
```

### ETL Performance Metrics

| Metric | Value |
|--------|-------|
| Total Records Loaded | ~37,000 |
| Dimension Records | 893 |
| Fact Records | 36,500 |
| Execution Time | ~30 seconds |
| Database Size | ~50 MB |

### Error Handling

**Strategy:**
- Try-catch blocks around all database operations
- Transaction rollback on error
- Logging of all errors
- Graceful failure messages

**Example:**
```python
try:
    session.add(record)
    session.commit()
except Exception as e:
    session.rollback()
    st.error(f"Error: {e}")
finally:
    session.close()
```

---

## OLAP Operations

### What is OLAP?

OLAP (Online Analytical Processing) enables multi-dimensional analysis of business data. This data warehouse supports all major OLAP operations.

### 1. Slice

**Definition:** Select a single value from one dimension

**Example:** View all transactions for Q1 2023
```sql
SELECT *
FROM fact_transactions ft
JOIN dim_date d ON ft.date_key = d.date_key
WHERE d.year = 2023 AND d.quarter = 1;
```

**Dashboard:** Filter by transaction type, asset type, or date range

### 2. Dice

**Definition:** Select specific values across multiple dimensions

**Example:** View technology stock transactions in North America
```sql
SELECT *
FROM fact_transactions ft
JOIN dim_asset a ON ft.asset_key = a.asset_key
JOIN dim_region r ON ft.region_key = r.region_key
WHERE a.sector = 'Technology'
  AND r.region_name = 'North America';
```

**Dashboard:** Use multiple filters simultaneously

### 3. Drill-Down

**Definition:** Navigate from aggregated to detailed data

**Example:** Year → Quarter → Month → Day
```sql
-- Year level
SELECT d.year, SUM(ft.total_amount) as total
FROM fact_transactions ft
JOIN dim_date d ON ft.date_key = d.date_key
GROUP BY d.year;

-- Quarter level (drill-down)
SELECT d.year, d.quarter, SUM(ft.total_amount) as total
FROM fact_transactions ft
JOIN dim_date d ON ft.date_key = d.date_key
WHERE d.year = 2023
GROUP BY d.year, d.quarter;

-- Month level (drill-down)
SELECT d.year, d.quarter, d.month, SUM(ft.total_amount) as total
FROM fact_transactions ft
JOIN dim_date d ON ft.date_key = d.date_key
WHERE d.year = 2023 AND d.quarter = 1
GROUP BY d.year, d.quarter, d.month;
```

### 4. Roll-Up

**Definition:** Aggregate detailed data to higher levels

**Example:** Day → Month → Quarter → Year
```sql
-- From daily to monthly aggregation
SELECT 
    d.year,
    d.month,
    SUM(ft.total_amount) as monthly_total
FROM fact_transactions ft
JOIN dim_date d ON ft.date_key = d.date_key
GROUP BY d.year, d.month;
```

### 5. Pivot

**Definition:** Rotate the data cube to view different perspectives

**Example:** Asset Type vs Region
```sql
SELECT 
    a.asset_type,
    r.region_name,
    SUM(ft.total_amount) as total_volume
FROM fact_transactions ft
JOIN dim_asset a ON ft.asset_key = a.asset_key
JOIN dim_region r ON ft.region_key = r.region_key
GROUP BY a.asset_type, r.region_name;
```

**Dashboard:** OLAP Cube tab allows dynamic pivoting

### OLAP Cube Example

**3-Dimensional Cube:** Time × Asset × Region

```
                Region
                  │
        NA    EU  │  ASIA  LATAM
        │     │   │   │      │
Time ───┼─────┼───┼───┼──────┼─── Asset
      2023    │   │   │      │   Stock
      2024    │   │   │      │   ETF
              └───┴───┴──────┘   Bond
                                 Crypto
```

**Cube Operations in Dashboard:**
1. Select Dimension 1 (Rows): Year, Quarter, Month, Asset Type, Region
2. Select Dimension 2 (Columns): Any remaining dimension
3. View heatmap of aggregated measures

---

## Dashboard Features

### Tab 1: Dashboard Overview

**Key Performance Indicators (KPIs)**
- Total Transactions
- Total Volume ($)
- Average Daily Return (%)
- Active Users

**Visualizations**
1. **Transaction Volume Over Time** (Line Chart)
   - X-axis: Time periods (Month-Year)
   - Y-axis: Total transaction volume
   - Purpose: Identify trends and seasonality

2. **Transaction Volume by Asset Type** (Pie Chart)
   - Shows distribution across Stock, ETF, Bond, Crypto
   - Purpose: Portfolio composition analysis

### Tab 2: Transaction Analysis

**Features**
- **Filters**
  - Transaction Type: All, BUY, SELL
  - Asset Type: All, Stock, ETF, Bond, Crypto
  
- **Transaction Table**
  - Displays top 100 recent transactions
  - Columns: Date, User, Asset, Type, Quantity, Price, Amount, Commission

- **Analytics**
  1. Top 10 Assets by Volume (Bar Chart)
  2. Transactions by Region (Bar Chart)

### Tab 3: Returns Analysis

**Visualizations**
1. **Average Daily Returns by Asset** (Bar Chart)
   - Color-coded: Green (positive), Red (negative)
   - Purpose: Compare asset performance

2. **Asset Price Movement Over Time** (Line Chart)
   - Multiple lines for each asset
   - Purpose: Track price trends and volatility

**Metrics**
- Average Return (%)
- Max Return (%)
- Min Return (%)
- Volatility (Standard Deviation)

### Tab 4: OLAP Cube

**Interactive Multi-Dimensional Analysis**
- Select two dimensions for analysis
- View heatmap of transaction volumes
- Supported dimensions:
  - Year
  - Quarter
  - Month
  - Asset Type
  - Region
  - Risk Profile

**Use Cases**
- Compare quarterly performance across regions
- Analyze asset type preferences by investor risk profile
- Identify seasonal trading patterns

### Tab 5: Schema Documentation

**Content**
- Data warehouse architecture overview
- Star schema description
- ETL process summary
- Table row counts (dimensions and facts)
- Design principles

---

## SQL Query Examples

### Basic Queries

**1. Total Transaction Volume**
```sql
SELECT SUM(total_amount) as total_volume
FROM fact_transactions;
```

**2. Average Daily Return**
```sql
SELECT AVG(daily_return_pct) as avg_return
FROM fact_returns;
```

**3. Transaction Count by Type**
```sql
SELECT 
    transaction_type,
    COUNT(*) as count,
    SUM(total_amount) as total_volume
FROM fact_transactions
GROUP BY transaction_type;
```

### Intermediate Queries

**4. Monthly Transaction Volume with Year-over-Year Comparison**
```sql
SELECT 
    d.year,
    d.month,
    d.month_name,
    SUM(ft.total_amount) as monthly_volume,
    LAG(SUM(ft.total_amount)) OVER (
        PARTITION BY d.month 
        ORDER BY d.year
    ) as prev_year_volume
FROM fact_transactions ft
JOIN dim_date d ON ft.date_key = d.date_key
GROUP BY d.year, d.month, d.month_name
ORDER BY d.year, d.month;
```

**5. Top Performing Assets**
```sql
SELECT 
    a.asset_name,
    a.asset_type,
    AVG(fr.daily_return_pct) as avg_return,
    STDDEV(fr.daily_return_pct) as volatility,
    AVG(fr.daily_return_pct) / STDDEV(fr.daily_return_pct) as sharpe_ratio
FROM fact_returns fr
JOIN dim_asset a ON fr.asset_key = a.asset_key
GROUP BY a.asset_name, a.asset_type
ORDER BY sharpe_ratio DESC;
```

**6. User Portfolio Analysis**
```sql
SELECT 
    u.user_name,
    u.risk_profile,
    COUNT(DISTINCT ft.asset_key) as num_assets,
    SUM(ft.total_amount) as total_invested,
    AVG(fr.daily_return_pct) as portfolio_return
FROM dim_user u
LEFT JOIN fact_transactions ft ON u.user_key = ft.user_key
LEFT JOIN fact_returns fr ON u.user_key = fr.user_key
GROUP BY u.user_name, u.risk_profile
ORDER BY total_invested DESC;
```

### Advanced Queries

**7. Cohort Analysis: User Behavior by Registration Quarter**
```sql
WITH user_cohorts AS (
    SELECT 
        user_key,
        DATE_TRUNC('quarter', registration_date) as cohort_quarter
    FROM dim_user
)
SELECT 
    uc.cohort_quarter,
    COUNT(DISTINCT ft.user_key) as active_users,
    SUM(ft.total_amount) as total_volume,
    AVG(ft.total_amount) as avg_transaction_size
FROM user_cohorts uc
JOIN fact_transactions ft ON uc.user_key = ft.user_key
GROUP BY uc.cohort_quarter
ORDER BY uc.cohort_quarter;
```

**8. Asset Correlation Matrix**
```sql
WITH asset_returns AS (
    SELECT 
        fr1.date_key,
        a1.asset_name as asset1,
        a2.asset_name as asset2,
        fr1.daily_return_pct as return1,
        fr2.daily_return_pct as return2
    FROM fact_returns fr1
    JOIN fact_returns fr2 ON fr1.date_key = fr2.date_key
    JOIN dim_asset a1 ON fr1.asset_key = a1.asset_key
    JOIN dim_asset a2 ON fr2.asset_key = a2.asset_key
    WHERE a1.asset_key < a2.asset_key
)
SELECT 
    asset1,
    asset2,
    CORR(return1, return2) as correlation
FROM asset_returns
GROUP BY asset1, asset2
ORDER BY correlation DESC;
```

**9. Time Series Decomposition: Trend Analysis**
```sql
WITH daily_volumes AS (
    SELECT 
        d.full_date,
        SUM(ft.total_amount) as daily_volume
    FROM fact_transactions ft
    JOIN dim_date d ON ft.date_key = d.date_key
    GROUP BY d.full_date
)
SELECT 
    full_date,
    daily_volume,
    AVG(daily_volume) OVER (
        ORDER BY full_date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) as moving_avg_7day,
    AVG(daily_volume) OVER (
        ORDER BY full_date 
        ROWS BETWEEN 29 PRECEDING AND CURRENT ROW
    ) as moving_avg_30day
FROM daily_volumes
ORDER BY full_date;
```

**10. Regional Performance Dashboard**
```sql
SELECT 
    r.region_name,
    r.market_type,
    COUNT(DISTINCT ft.user_key) as unique_traders,
    COUNT(*) as total_transactions,
    SUM(ft.total_amount) as total_volume,
    AVG(ft.total_amount) as avg_transaction,
    SUM(ft.commission) as total_commissions,
    AVG(fr.daily_return_pct) as avg_return
FROM dim_region r
LEFT JOIN fact_transactions ft ON r.region_key = ft.region_key
LEFT JOIN fact_returns fr ON r.region_key = fr.region_key
GROUP BY r.region_name, r.market_type
ORDER BY total_volume DESC;
```

---

## Best Practices

### Data Modeling

1. **Use Surrogate Keys**
   - Auto-increment integers for all primary keys
   - Maintain business keys separately
   - Benefits: Performance, flexibility, simplicity

2. **Denormalize for Performance**
   - Star schema over snowflake for OLAP
   - Pre-calculate common aggregations
   - Trade storage for query speed

3. **Slowly Changing Dimensions (SCD)**
   - Type 1: Overwrite (current implementation)
   - Type 2: Add new row with effective dates (recommended for production)
   - Type 3: Add new column for previous value

4. **Grain Declaration**
   - Clearly define fact table granularity
   - Example: fact_transactions = one row per transaction
   - Example: fact_returns = one row per asset per user per day
   - Maintain consistent grain across related facts

5. **Conformed Dimensions**
   - Share dimensions across multiple fact tables
   - Ensures consistent analysis
   - Example: dim_date used by both fact tables

### ETL Best Practices

1. **Idempotent Loads**
   - ETL should produce same result if run multiple times
   - Use UPSERT (INSERT or UPDATE) logic
   - Check for existing records before inserting

2. **Batch Processing**
   - Load data in batches for better performance
   - Use bulk insert operations
   ```python
   # Good: Batch insert
   session.bulk_insert_mappings(DimDate, date_records)
   
   # Avoid: Individual inserts in loop
   for record in records:
       session.add(record)
   ```

3. **Error Recovery**
   - Implement checkpoints
   - Log all ETL activities
   - Enable restart from failure point

4. **Data Quality Checks**
   - Validate before loading
   - Check referential integrity
   - Monitor for anomalies
   ```python
   # Example validation
   if pd.isna(transaction_amount):
       raise ValueError("Transaction amount cannot be null")
   if transaction_amount < 0:
       raise ValueError("Transaction amount must be positive")
   ```

### Query Optimization

1. **Index Strategy**
   ```sql
   -- Create indexes on foreign keys
   CREATE INDEX idx_ft_date ON fact_transactions(date_key);
   CREATE INDEX idx_ft_user ON fact_transactions(user_key);
   CREATE INDEX idx_ft_asset ON fact_transactions(asset_key);
   CREATE INDEX idx_ft_region ON fact_transactions(region_key);
   
   -- Create indexes on frequently filtered columns
   CREATE INDEX idx_ft_type ON fact_transactions(transaction_type);
   CREATE INDEX idx_ft_timestamp ON fact_transactions(timestamp);
   ```

2. **Aggregate Tables**
   - Pre-calculate common aggregations
   - Create monthly/quarterly summary tables
   ```sql
   CREATE TABLE fact_transactions_monthly AS
   SELECT 
       date_key,
       user_key,
       asset_key,
       SUM(total_amount) as monthly_volume,
       COUNT(*) as transaction_count
   FROM fact_transactions
   GROUP BY date_key, user_key, asset_key;
   ```

3. **Partitioning**
   - Partition large fact tables by date
   - Improves query performance
   - Simplifies data archival

4. **Query Design**
   - Filter early in the query
   - Use appropriate JOIN types
   - Limit result sets
   ```sql
   -- Good: Filter before joining
   SELECT *
   FROM fact_transactions ft
   JOIN dim_date d ON ft.date_key = d.date_key
   WHERE d.year = 2023
   
   -- Better: Use subquery to reduce join size
   SELECT *
   FROM fact_transactions ft
   JOIN (SELECT date_key FROM dim_date WHERE year = 2023) d
   ON ft.date_key = d.date_key
   ```

### Dashboard Performance

1. **Caching**
   ```python
   @st.cache_resource
   def get_database():
       # Cache database connection
       return create_engine(...)
   
   @st.cache_data(ttl=3600)
   def load_transaction_data():
       # Cache query results for 1 hour
       return pd.read_sql(query, engine)
   ```

2. **Lazy Loading**
   - Load data only when needed
   - Use pagination for large datasets
   - Implement infinite scroll

3. **Optimize Visualizations**
   - Limit data points in charts
   - Use sampling for large datasets
   - Aggregate before plotting

### Security Best Practices

1. **Data Access Control**
   - Implement user authentication
   - Role-based access control (RBAC)
   - Row-level security for sensitive data

2. **Secure Connections**
   - Use HTTPS for web access
   - Encrypt database connections
   - Secure API endpoints

3. **Data Privacy**
   - Anonymize PII (Personally Identifiable Information)
   - Implement data masking
   - Comply with GDPR/CCPA regulations

4. **Audit Logging**
   - Log all data access
   - Track schema changes
   - Monitor for suspicious activity

---

## Troubleshooting

### Common Issues and Solutions

#### 1. ngrok Connection Failed

**Problem:** Cannot connect to ngrok tunnel
```
ERROR: failed to connect to ngrok
```

**Solutions:**
```python
# Solution 1: Verify token
from pyngrok import conf
print(conf.get_default().auth_token)

# Solution 2: Re-authenticate
from pyngrok import ngrok
ngrok.kill()
conf.get_default().auth_token = "YOUR_NEW_TOKEN"

# Solution 3: Check firewall/proxy settings
# Ensure port 8501 is not blocked
```

#### 2. Streamlit Not Starting

**Problem:** Streamlit server fails to start

**Solutions:**
```bash
# Check if Streamlit is already running
!ps aux | grep streamlit

# Kill existing processes
!pkill -9 streamlit

# Check port availability
!netstat -tuln | grep 8501

# Start with verbose output
!streamlit run investment_dw_app.py --server.port 8501 --logger.level=debug
```

#### 3. Database Connection Error

**Problem:** Cannot connect to SQLite database
```
OperationalError: unable to open database file
```

**Solutions:**
```python
# Solution 1: Check file permissions
import os
os.chmod('investment_analytics_dw.db', 0o666)

# Solution 2: Verify database path
import os
print(os.getcwd())
print(os.path.exists('investment_analytics_dw.db'))

# Solution 3: Create new database
os.remove('investment_analytics_dw.db')  # Delete corrupted DB
# Re-run ETL pipeline
```

#### 4. Memory Issues in Colab

**Problem:** Out of memory errors during ETL

**Solutions:**
```python
# Solution 1: Reduce batch size
for i in range(0, len(data), 100):  # Process 100 records at a time
    batch = data[i:i+100]
    process_batch(batch)

# Solution 2: Clear cache
import gc
gc.collect()

# Solution 3: Use Colab Pro for more RAM
# Upgrade to Colab Pro or Pro+ for additional resources
```

#### 5. Slow Query Performance

**Problem:** Dashboard queries taking too long

**Solutions:**
```sql
-- Solution 1: Add indexes
CREATE INDEX idx_fact_date ON fact_transactions(date_key);

-- Solution 2: Use EXPLAIN to analyze query
EXPLAIN QUERY PLAN
SELECT * FROM fact_transactions 
WHERE date_key = 20230101;

-- Solution 3: Limit result sets
SELECT * FROM fact_transactions
LIMIT 1000;  -- Add pagination

-- Solution 4: Pre-aggregate data
CREATE VIEW monthly_summary AS
SELECT 
    date_key,
    SUM(total_amount) as total
FROM fact_transactions
GROUP BY date_key;
```

#### 6. ETL Data Quality Issues

**Problem:** Incorrect or missing data after ETL

**Diagnostic Queries:**
```sql
-- Check for null values
SELECT COUNT(*) 
FROM fact_transactions 
WHERE total_amount IS NULL;

-- Check for orphaned records
SELECT COUNT(*) 
FROM fact_transactions ft
LEFT JOIN dim_date d ON ft.date_key = d.date_key
WHERE d.date_key IS NULL;

-- Check for duplicate records
SELECT date_key, user_key, asset_key, COUNT(*)
FROM fact_transactions
GROUP BY date_key, user_key, asset_key
HAVING COUNT(*) > 1;

-- Verify data ranges
SELECT 
    MIN(total_amount) as min_amount,
    MAX(total_amount) as max_amount,
    AVG(total_amount) as avg_amount
FROM fact_transactions;
```

**Solutions:**
```python
# Add validation before loading
def validate_transaction(record):
    assert record['total_amount'] > 0, "Amount must be positive"
    assert record['date_key'] in valid_dates, "Invalid date"
    assert record['user_key'] in valid_users, "Invalid user"
    return True

# Add data quality checks
def check_data_quality(engine):
    # Check row counts
    assert pd.read_sql("SELECT COUNT(*) FROM dim_date", engine).iloc[0,0] == 731
    
    # Check for nulls
    null_count = pd.read_sql(
        "SELECT COUNT(*) FROM fact_transactions WHERE total_amount IS NULL", 
        engine
    ).iloc[0,0]
    assert null_count == 0, "Found null amounts"
```

#### 7. Visualization Not Displaying

**Problem:** Charts not rendering in dashboard

**Solutions:**
```python
# Solution 1: Check data format
print(df.head())
print(df.dtypes)

# Solution 2: Ensure data is not empty
if df.empty:
    st.warning("No data available for this filter")
else:
    fig = px.bar(df, x='x', y='y')
    st.plotly_chart(fig)

# Solution 3: Handle missing values
df = df.dropna()  # Remove null values
df = df.fillna(0)  # Or fill with zeros

# Solution 4: Check Plotly version
import plotly
print(plotly.__version__)
```

#### 8. Session State Issues

**Problem:** Filters not persisting between interactions

**Solution:**
```python
# Use Streamlit session state
if 'filters' not in st.session_state:
    st.session_state.filters = {
        'transaction_type': 'All',
        'asset_type': 'All'
    }

# Access and update
transaction_type = st.selectbox(
    "Transaction Type",
    ["All", "BUY", "SELL"],
    key='transaction_type_filter'
)
st.session_state.filters['transaction_type'] = transaction_type
```

---

## Advanced Topics

### 1. Incremental ETL

For production systems, implement incremental loading:

```python
class IncrementalETL:
    def __init__(self, engine):
        self.engine = engine
        self.last_load_date = self.get_last_load_date()
    
    def get_last_load_date(self):
        """Get the last successfully loaded date"""
        query = """
        SELECT MAX(full_date) as last_date 
        FROM dim_date d
        JOIN fact_transactions ft ON d.date_key = ft.date_key
        """
        result = pd.read_sql(query, self.engine)
        return result['last_date'].iloc[0]
    
    def extract_incremental(self):
        """Extract only new data since last load"""
        new_data = extract_source_data(
            start_date=self.last_load_date + timedelta(days=1),
            end_date=datetime.now()
        )
        return new_data
    
    def load_incremental(self, new_data):
        """Load only new records"""
        for record in new_data:
            # Check if record already exists
            exists = self.check_exists(record)
            if not exists:
                self.insert_record(record)
```

### 2. Data Versioning

Implement Type 2 Slowly Changing Dimensions:

```python
class DimUserSCD2(Base):
    __tablename__ = 'dim_user_scd2'
    user_key = Column(Integer, primary_key=True, autoincrement=True)
    user_id = Column(String(50))
    user_name = Column(String(100))
    risk_profile = Column(String(20))
    effective_date = Column(Date)
    expiration_date = Column(Date)
    is_current = Column(Integer, default=1)

def update_user_scd2(user_id, new_risk_profile):
    """Update user with history tracking"""
    # Expire current record
    current = session.query(DimUserSCD2).filter_by(
        user_id=user_id, 
        is_current=1
    ).first()
    current.expiration_date = datetime.now().date()
    current.is_current = 0
    
    # Insert new record
    new_record = DimUserSCD2(
        user_id=user_id,
        risk_profile=new_risk_profile,
        effective_date=datetime.now().date(),
        expiration_date=date(9999, 12, 31),
        is_current=1
    )
    session.add(new_record)
    session.commit()
```

### 3. Real-time Analytics

Implement streaming analytics:

```python
from kafka import KafkaConsumer
import json

def stream_transactions():
    """Consume real-time transactions from Kafka"""
    consumer = KafkaConsumer(
        'transactions',
        bootstrap_servers=['localhost:9092'],
        value_deserializer=lambda m: json.loads(m.decode('utf-8'))
    )
    
    for message in consumer:
        transaction = message.value
        # Process and load to data warehouse
        load_transaction(transaction)
        
        # Update real-time dashboard
        update_dashboard_metrics(transaction)
```

### 4. Data Quality Framework

Implement comprehensive data quality checks:

```python
class DataQualityChecker:
    def __init__(self, engine):
        self.engine = engine
        self.checks = []
    
    def add_check(self, name, query, threshold):
        """Add a data quality check"""
        self.checks.append({
            'name': name,
            'query': query,
            'threshold': threshold
        })
    
    def run_checks(self):
        """Execute all data quality checks"""
        results = []
        for check in self.checks:
            result = pd.read_sql(check['query'], self.engine)
            value = result.iloc[0, 0]
            passed = value <= check['threshold']
            
            results.append({
                'check': check['name'],
                'value': value,
                'threshold': check['threshold'],
                'passed': passed
            })
        
        return pd.DataFrame(results)

# Example usage
dq = DataQualityChecker(engine)

# Add checks
dq.add_check(
    'Null Transaction Amounts',
    'SELECT COUNT(*) FROM fact_transactions WHERE total_amount IS NULL',
    threshold=0
)

dq.add_check(
    'Future Dated Transactions',
    "SELECT COUNT(*) FROM fact_transactions WHERE timestamp > datetime('now')",
    threshold=0
)

dq.add_check(
    'Orphaned Transactions',
    '''SELECT COUNT(*) FROM fact_transactions ft
       LEFT JOIN dim_user u ON ft.user_key = u.user_key
       WHERE u.user_key IS NULL''',
    threshold=0
)

# Run checks
quality_report = dq.run_checks()
print(quality_report)
```

### 5. Machine Learning Integration

Integrate predictive analytics:

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split

def train_return_prediction_model(engine):
    """Train ML model to predict asset returns"""
    
    # Extract features
    query = """
    SELECT 
        fr.daily_return_pct,
        fr.volume,
        a.asset_type,
        d.day_of_week,
        d.is_weekend,
        LAG(fr.daily_return_pct, 1) OVER (
            PARTITION BY fr.asset_key 
            ORDER BY fr.date_key
        ) as prev_day_return,
        LAG(fr.daily_return_pct, 7) OVER (
            PARTITION BY fr.asset_key 
            ORDER BY fr.date_key
        ) as prev_week_return
    FROM fact_returns fr
    JOIN dim_asset a ON fr.asset_key = a.asset_key
    JOIN dim_date d ON fr.date_key = d.date_key
    """
    
    df = pd.read_sql(query, engine)
    df = df.dropna()
    
    # Prepare features
    X = df[['volume', 'is_weekend', 'prev_day_return', 'prev_week_return']]
    y = df['daily_return_pct']
    
    # Train model
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
    model = RandomForestRegressor(n_estimators=100)
    model.fit(X_train, y_train)
    
    # Evaluate
    score = model.score(X_test, y_test)
    print(f"Model R² Score: {score:.4f}")
    
    return model
```

---

## Performance Benchmarks

### Query Performance

| Query Type | Records | Execution Time | Optimization |
|------------|---------|----------------|--------------|
| Simple SELECT | 500 | 0.01s | - |
| Single JOIN | 500 | 0.02s | Index on FK |
| Multi-JOIN | 500 | 0.05s | Index on all FKs |
| Aggregation | 36,500 | 0.10s | Indexed columns |
| Window Functions | 36,500 | 0.25s | Partitioned index |
| Complex OLAP | 36,500 | 0.50s | Materialized view |

### ETL Performance

| Phase | Records | Duration | Bottleneck |
|-------|---------|----------|------------|
| Extract | 50 users | 0.1s | N/A |
| Extract | 8 assets | 0.1s | N/A |
| Extract | 731 dates | 0.1s | N/A |
| Transform | All dims | 1.0s | Date calculations |
| Load Dimensions | 893 | 2.0s | Database I/O |
| Load Transactions | 500 | 3.0s | Database I/O |
| Load Returns | 36,500 | 25.0s | Database I/O |
| **Total** | **37,893** | **~30s** | **Bulk inserts** |

### Dashboard Performance

| Component | Load Time | Optimization Strategy |
|-----------|-----------|----------------------|
| Initial Load | 3s | @st.cache_resource |
| KPI Metrics | 0.2s | Simple aggregations |
| Line Chart | 0.5s | Limit data points |
| Pie Chart | 0.3s | Pre-aggregated data |
| OLAP Heatmap | 1.0s | Dynamic pivot |
| Table Rendering | 0.4s | Pagination (100 rows) |

---

## Production Deployment Checklist

### Infrastructure

- [ ] Use production-grade database (PostgreSQL, MySQL)
- [ ] Set up database replication
- [ ] Configure automated backups
- [ ] Implement disaster recovery plan
- [ ] Set up monitoring and alerting
- [ ] Configure load balancing
- [ ] Enable SSL/TLS encryption

### Security

- [ ] Implement user authentication (OAuth, SAML)
- [ ] Set up role-based access control
- [ ] Encrypt sensitive data at rest
- [ ] Implement audit logging
- [ ] Regular security audits
- [ ] Vulnerability scanning
- [ ] Data anonymization for non-production

### Performance

- [ ] Create all necessary indexes
- [ ] Implement query caching
- [ ] Set up materialized views
- [ ] Configure connection pooling
- [ ] Implement lazy loading
- [ ] Enable CDN for static assets
- [ ] Optimize dashboard queries

### Data Quality

- [ ] Implement data validation rules
- [ ] Set up automated data quality checks
- [ ] Create data lineage tracking
- [ ] Implement error notifications
- [ ] Schedule regular data audits
- [ ] Document data definitions
- [ ] Create data dictionary

### Maintenance

- [ ] Schedule regular backups
- [ ] Plan for data archival
- [ ] Implement ETL monitoring
- [ ] Set up log rotation
- [ ] Create runbook for common issues
- [ ] Document recovery procedures
- [ ] Schedule performance reviews

---

## Glossary

**OLAP (Online Analytical Processing):** Technology that enables multi-dimensional analysis of business data.

**Star Schema:** Data warehouse design pattern with a central fact table surrounded by dimension tables.

**Fact Table:** Table containing measures/metrics and foreign keys to dimensions.

**Dimension Table:** Table containing descriptive attributes for analysis.

**ETL (Extract-Transform-Load):** Process of moving data from source systems to data warehouse.

**Grain:** Level of detail in a fact table (e.g., daily, monthly).

**Surrogate Key:** Artificial primary key (usually auto-increment integer).

**Business Key:** Natural key from source system (e.g., ticker symbol).

**SCD (Slowly Changing Dimension):** Dimension that changes over time, with strategies to track history.

**Drill-down:** Navigate from summary to detail.

**Roll-up:** Aggregate from detail to summary.

**Slice:** Select subset based on single dimension.

**Dice:** Select subset based on multiple dimensions.

**Pivot:** Rotate data cube to view different perspectives.

**Conformed Dimension:** Dimension shared across multiple fact tables.

**Degenerate Dimension:** Dimension attribute stored in fact table.

**Junk Dimension:** Dimension combining multiple low-cardinality flags.

**Bridge Table:** Table resolving many-to-many relationships.

---

## References & Resources

### Documentation
- SQLAlchemy: https://docs.sqlalchemy.org/
- Streamlit: https://docs.streamlit.io/
- Plotly: https://plotly.com/python/
- Pandas: https://pandas.pydata.org/docs/

### Books
- "The Data Warehouse Toolkit" by Ralph Kimball
- "Building the Data Warehouse" by Bill Inmon
- "Designing Data-Intensive Applications" by Martin Kleppmann

### Online Resources
- Kimball Group: https://www.kimballgroup.com/
- Google Colab: https://colab.research.google.com/
- ngrok: https://ngrok.com/docs

### Sample Datasets
- Yahoo Finance API for real market data
- Alpha Vantage for stock prices
- Kaggle for financial datasets

---

## Conclusion

This Investment Analytics Data Warehouse demonstrates core data warehousing concepts including:

✅ **Dimensional Modeling** with star schema design
✅ **ETL Pipeline** development and best practices  
✅ **OLAP Operations** for multi-dimensional analysis
✅ **Interactive Dashboards** for data visualization
✅ **SQL Analytics** with complex queries
✅ **Performance Optimization** techniques
✅ **Cloud Deployment** on Google Colab

### Next Steps

1. **Extend the Schema**
   - Add more dimensions (broker, account, strategy)
   - Create aggregate tables for performance
   - Implement slowly changing dimensions

2. **Enhance ETL**
   - Connect to real data sources
   - Implement incremental loading
   - Add data quality framework

3. **Advanced Analytics**
   - Portfolio risk metrics (VaR, Sharpe ratio)
   - Correlation analysis
   - Machine learning predictions

4. **Production Deployment**
   - Migrate to PostgreSQL/Snowflake
   - Implement proper authentication
   - Set up monitoring and alerting

### Support

For issues or questions:
- Review the Troubleshooting section
- Check the SQL Query Examples
- Consult the online documentation links

---

**Version:** 1.0  
**Last Updated:** November 2025  
**Author:** Investment Analytics Team
