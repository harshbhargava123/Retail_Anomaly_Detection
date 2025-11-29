# Retail Anomaly Detection - Production Solution

## Executive Overview

This solution provides **automated anomaly detection** for retail operations using advanced time-series forecasting. It identifies unusual patterns in sales, discounts, and promotional activities to help leadership make data-driven decisions and quickly respond to business irregularities.

### Business Value
- **Early Detection**: Identifies anomalies in daily business metrics before they become critical issues
- **Data-Driven Insights**: Uses machine learning (Prophet) to separate normal variations from true anomalies
- **Automated Monitoring**: One-click execution enables scheduled, hands-off monitoring
- **Actionable Intelligence**: Clear visualizations and categorized anomalies (above/below expected ranges)

### Link to HTML Rendered Databricks Notebooks (For Output Visualization)

- Exploration Notebook - https://harshbhargava123.github.io/Retail_Anomaly_Detection/EDA.html
 
- Production Notebook - https://harshbhargava123.github.io/Retail_Anomaly_Detection/

## Dataset Used

- Dunnhumby - The Complete Journey dataset from Kaggle has been used to simulate the solution.
- Link to Dataset - https://www.kaggle.com/datasets/frtgnn/dunnhumby-the-complete-journey/data

### About Dataset
This dataset contains household level transactions over two years from a group of 2,500 households who are frequent shoppers at a retailer. It contains all of each household’s purchases, not just those from a limited number of categories. 
For certain households, demographic information as well as direct marketing contact history are included. Following are the details for the available tables:

1. transaction_data: Contains every item-level purchase made by households, including product, quantity, discounts, store, and day of purchase.

2. product: Contains product attributes (brand, category, subcategory, manufacturer) for every item sold.

3. household: Contains static attributes of households such as demographic segments.

4. campaign_desc: Provides the description, campaign ID, start day, and end day for each marketing campaign.

5. campaign_table: Maps each household to the campaigns they were targeted with.

6. coupon: Lists coupons issued in each campaign along with their descriptions.

7. coupon_redempt: Contains all events where households redeemed coupons (household, coupon UPC, day).

8. hh_demographic: Provides demographic details for households (income, kids, marital status, etc.).

9. causal_data: Contains causal marketing conditions like display type and mailer promotions per product per week.

Note - For the scope of this project csv has been downloaded and uploaded to Databricks to create static tables. However in real-life scenario we will pull the data on regular intervals from SnowFlake.

---

## What Does This Solution Do?

### Key Capabilities

1. **Sales Anomaly Detection**
   - Monitors daily sales patterns
   - Identifies days with unusually high or low sales
   - Accounts for weekly and yearly seasonality

2. **Retail Discount Monitoring**
   - Tracks discount patterns relative to campaign activity
   - Flags abnormal discount levels that may indicate pricing issues

3. **Coupon Discount Analysis**
   - Monitors coupon redemption and discount patterns
   - Identifies unusual coupon usage that may signal fraud or system issues

4. **Comprehensive Reporting**
   - Executive summary with key metrics and percentages
   - Visual dashboards showing trends and anomalies
   - Detailed breakdown of upper vs. lower bound violations
   - Campaign participation and redemption trend analysis

### Anomaly Classification

All anomalies are classified into two categories:
- **Above Upper Bound**: Values exceeding expected maximum (e.g., unusually high sales or discounts)
- **Below Lower Bound**: Values below expected minimum (e.g., unusually low sales)

This distinction helps leadership understand whether anomalies represent opportunities (high sales) or risks (low sales, excessive discounts).

---

## How Does It Work?

### Technical Approach

#### 1. **Data Integration**
The solution integrates multiple data sources:
- **Transaction Data**: Daily sales, quantities, and discount information
- **Campaign Data**: Marketing campaign schedules and household participation
- **Coupon Redemption**: Coupon usage patterns and redemption counts

#### 2. **Machine Learning Model**
Uses **Facebook Prophet**, an industry-standard time-series forecasting model that:
- Learns normal business patterns from historical data
- Accounts for weekly cycles (weekends vs. weekdays)
- Accounts for yearly patterns (holidays, seasonal trends)
- Incorporates business context (campaign activity as a predictor)
- Generates confidence intervals (95% by default)

#### 3. **Anomaly Detection Logic**
An anomaly is detected when:
```
Actual Value > Upper Bound (95th percentile) → Above Upper Bound
Actual Value < Lower Bound (5th percentile)  → Below Lower Bound
```

#### 4. **Quality Assurance**
Built-in data validation includes:
- Data quality checks for invalid transactions
- Campaign date validation
- Removal of data entry errors (negative quantities, positive discounts)

---

## Solution Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    DATA SOURCES                             │
│  • Transaction Data  • Campaign Data  • Coupon Redemption   │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                 DATA QUALITY & CLEANING                     │
│  • Remove invalid records  • Validate dates  • Clean data   │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│              FEATURE ENGINEERING                            │
│  • Daily aggregations  • Campaign flags  • Redemption flags │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│           PROPHET TIME-SERIES MODELS                        │
│  • Sales Model  • Retail Discount Model  • Coupon Model     │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│              ANOMALY DETECTION                              │
│  • Compare actuals to forecasts  • Classify anomalies       │
│  • Track upper/lower bound violations                       │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│         EXECUTIVE REPORTING & VISUALIZATION                 │
│  • Summary statistics  • Interactive dashboards             │
│  • Anomaly timelines  • Exportable results                  │
└─────────────────────────────────────────────────────────────┘
```

---

## Key Outputs

### 1. Executive Summary
Provides high-level metrics:
- Analysis date range and total days analyzed
- Total sales and average daily sales
- Anomaly counts and percentages for each metric
- Breakdown of upper vs. lower bound violations
- Specific dates and values of most significant anomalies

### 2. Visualizations

#### Core Visualizations:
- **Sales Forecast Charts**: Show predicted vs. actual sales with confidence bands
- **Anomaly Scatter Plots**: Highlight anomalous days in red against normal patterns
- **Trend Decomposition**: Break down yearly and weekly patterns
- **Campaign/Redemption Trends**: Track promotional activity over time
- **Anomaly Timeline Heatmap**: Visual calendar showing all anomalies across metrics
- **Upper vs. Lower Breakdown**: Bar charts comparing violation types

### 3. Detailed Anomaly Lists
Exportable tables containing:
- Date of anomaly
- Actual value
- Expected range (upper/lower bounds)
- Anomaly type (Above Upper / Below Lower)

---

## How to Use This Solution

### One-Click Execution
The production notebook (`Retail_Anomaly_Detection_Production.ipynb`) is designed for **one-click execution**:

1. **Open the Notebook**: Launch in Databricks
2. **Run All Cells**: Execute sequentially from top to bottom
3. **Review Results**: All outputs, charts, and summaries appear inline

### Scheduling Options
For automated monitoring:
- **Daily**: Detect anomalies from previous day
- **Weekly**: Review cumulative patterns and trends
- **Monthly**: Comprehensive business review

### Interpreting Results

#### When to Take Action:

**High Priority** (Investigate Immediately):
- Multiple anomalies on the same day (sales + discounts)
- Consecutive days of anomalies
- Extreme values far outside bounds
- Anomalies during non-promotional periods

**Medium Priority** (Monitor):
- Single-day anomalies
- Anomalies during known campaigns or events
- Small deviations from bounds

**Low Priority** (Context-Dependent):
- Anomalies on known holidays
- Expected seasonal variations
- One-off promotional events

---

## Business Scenarios

### Example Use Cases

#### Scenario 1: Unusual Sales Drop
```
Alert: Sales below lower bound on 2025-03-15
Actual: $45,000 | Expected: $65,000-$85,000
Action: Investigate system issues, supply chain, or competitor actions
```

#### Scenario 2: Excessive Discounting
```
Alert: Retail discount above upper bound on 2025-04-22
Actual: $12,000 | Expected: $3,000-$8,000
Action: Review pricing policies, check for unauthorized promotions
```

#### Scenario 3: Coupon Fraud Pattern
```
Alert: Coupon discount above upper bound (5 consecutive days)
Actual: $8,500/day | Expected: $2,000-$4,000/day
Action: Audit coupon redemptions, investigate potential fraud
```

---

## Technical Requirements

### Environment
- **Platform**: Databricks
- **Python**: 3.8+
- **Spark**: For distributed data processing
- **Libraries**: 
  - `pyspark` (data processing)
  - `pandas` (data manipulation)
  - `prophet` (forecasting)
  - `plotly` (interactive visualizations)

### Data Requirements
- **Minimum History**: 90+ days for reliable seasonality detection
- **Data Frequency**: Daily aggregation
- **Required Fields**:
  - Transaction dates and amounts
  - Campaign schedules
  - Coupon redemption records

---

## Customization Options

### Adjusting Sensitivity
Change the confidence interval in the model:
```python
# More sensitive (90% confidence = detects more anomalies)
Prophet(interval_width=0.90)

# Less sensitive (99% confidence = detects fewer anomalies)
Prophet(interval_width=0.99)
```

### Adding New Metrics
The framework can be extended to monitor:
- Product-specific sales
- Store-level performance
- Customer segment behavior
- Inventory anomalies

### Integration Options
- Export results to database for historical tracking
- Connect to alerting systems (email, Slack, Teams)
- Feed into BI tools (Tableau, Power BI)
- API integration for real-time monitoring

---

## Files in This Solution

| File | Description |
|------|-------------|
| `Retail_Anomaly_Detection_Production.ipynb` | Main production notebook (ready to run) |
| `Retail_Anomaly_Detection_Exploration.ipynb` | Original exploratory analysis notebook |
| `README.md` | This documentation file |

---

*This solution empowers leadership to proactively monitor business health through automated, intelligent anomaly detection.*
