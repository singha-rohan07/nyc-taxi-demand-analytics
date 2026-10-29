🚕 NYC Taxi Demand Analytics

When and where do people take yellow cabs in New York City? Which trips generate the most revenue, and how do tipping patterns vary?

An end-to-end data analytics and business intelligence project analyzing 11.8 million NYC yellow taxi trips from June to August 2025, with 10.1 million records retained after data cleaning.

The project combines Python, DuckDB, SQL, Excel, and Power BI to explore taxi demand, trip economics, pickup zones, airport activity, payment methods, tipping behavior, and temporal trends.

Project highlights

11.8M raw trip records processed.
10.1M cleaned trips retained.
$289.2M in recorded trip charges.
14 SQL business queries.
DuckDB star-schema data warehouse.
Excel KPI workbook with pivots and charts.
Four-page Power BI dashboard project.
25 DAX measures.

Demo: Watch the 32-second charts walkthrough

!NYC Taxi Analytics — Executive Overview

📑 Table of Contents
Project Overview
Business Questions
Dataset
Data Cleaning
Technology Stack
Project Architecture
Python Analytics Pipeline
DuckDB Data Warehouse
SQL Analysis
Excel KPI Workbook
Power BI Dashboard
Key Performance Indicators
Key Business Findings
Business Value
Installation and Setup
Repository Structure
Limitations
Skills Demonstrated
Future Improvements
License
🎯 Project Overview

New York City's yellow taxi data provides insights into urban mobility, travel patterns, transportation revenue, and customer behavior.

This project analyzes three months of NYC Taxi and Limousine Commission (TLC) yellow taxi trip records to understand when demand peaks, which pickup zones generate the most activity, how trip distance relates to revenue, and how payment methods influence recorded tipping behavior.

The project follows an end-to-end analytics workflow, from downloading and cleaning raw data to building a data warehouse, writing analytical SQL queries, preparing an Excel reporting workbook, and developing a Power BI semantic model and dashboard.

Project Objectives
Analyze taxi demand by hour, weekday, weekend, and month.
Identify high-volume pickup zones and airport corridors.
Evaluate trip revenue across different distance bands.
Compare trip duration, distance, and average speed.
Understand payment methods and recorded tipping patterns.
Examine congestion relief zone fees.
Build reusable SQL queries and business KPIs.
Develop interactive dashboards for operational and business analysis.
Reporting Period

June 1 – August 31, 2025

The analysis uses the June, July, and August 2025 monthly TLC yellow taxi trip files, supplemented by the TLC taxi zone lookup dataset.

❓ Business Questions

The analysis is designed to answer five primary business questions.

Business Question	Analytical Focus
When is taxi demand highest?	Hourly, daily, weekday, weekend, and monthly trends
Where does demand originate?	Pickup zones, boroughs, Manhattan, JFK, and LaGuardia
Which trips generate the most revenue?	Trip distance, duration, total charges, and revenue efficiency
How do customers tip?	Payment type, recorded tip amount, tip rate, and time of day
How is demand changing?	Daily and monthly trip volume and revenue trends

Additional analysis explores congestion fees, data quality, passenger counts, and missing payment information.

📊 Dataset
Data Sources

1. NYC TLC Yellow Taxi Trip Records

Monthly Parquet files for June, July, and August 2025.

Source: NYC Taxi and Limousine Commission — Trip Record Data

2. Taxi Zone Lookup

The taxi_zone_lookup.csv file provides zone identifiers, boroughs, and service-zone classifications.

Dataset Scale
Metric	Value
Reporting period	June–August 2025
Raw trip records	11,796,014
Cleaned trip records	10,101,536
Records retained	85.6%
Approximate raw download size	200 MB
Taxi zone lookup entries	265

The raw source files are not included in the repository. They are downloaded locally by the data ingestion script.

bash
python scripts/download_data.py

Important Data Fields

The TLC trip records include information such as:

Pickup and drop-off timestamps.
Pickup and drop-off location IDs.
Trip distance.
Passenger count.
Payment type.
Fare amount.
Total trip amount.
Tip amount.
Tolls and additional charges.
Rate code.
Congestion-related fees, where available in the source schema.

These fields support the project's demand analysis, trip economics, zone analysis, and payment behavior investigation.

🧹 Data Cleaning

The raw dataset contains invalid timestamps, unusual distances, negative fares, missing passenger counts, and invalid or missing rate codes.

A sequential cleaning pipeline applies validation rules in order. Each rejected record is attributed to the first rule it fails, allowing the impact of each step to be measured.

The resulting audit log is saved to:

data/processed/cleaning_log.csv

Cleaning Results
Cleaning Step	Rows Remaining	Rows Dropped
Raw rows from all three files	11,796,014	—
Pickup timestamp within June–August 2025	11,795,991	23
Valid pickup and drop-off zone IDs (1–263)	11,698,973	97,018
Trip duration between 1 and 180 minutes	11,389,892	309,081
Trip distance between 0.1 and 100 miles	11,061,518	328,374
Fare between $3 and $500	10,330,580	730,938
Valid nonnegative tips, tolls, and extras; total at least fare	10,330,390	190
Average speed below 70 mph	10,329,605	785
Missing passenger count or passenger count between 1 and 6	10,272,514	57,091
Missing or valid rate code; invalid code 99 excluded	10,101,536	170,978
Data Retention

<box gap={1}>
<row align="center" justify="between">
<text color="secondary" size="sm">Records retained after cleaning</text>
<title size="lg" color="success">85.6%</title>
</row>
<box background="surface-tertiary" radius="md" height="10px" clip>
<box background="#16A34A" width="85.6%" height="10px" radius="md"/>
</box>
<text color="secondary" size="sm">10,101,536 retained from 11,796,014 raw records.</text>
</box>

Key Data Quality Observation

Negative fares account for a substantial share of rejected records. The raw dataset contains 783,844 rows with fares below zero, which may include refunds, reversals, or voided transactions.

These records are excluded by the project's fare-validation rule. This improves consistency for the selected analysis but means the resulting revenue figures represent the cleaned dataset rather than every financial adjustment present in the raw files.

🛠️ Technology Stack
Category	Technologies
Programming	Python
Data Processing	Pandas, PyArrow
Data Storage	Parquet
Analytical Database	DuckDB
Query Language	SQL
Spreadsheet Analytics	Microsoft Excel
Business Intelligence	Microsoft Power BI
Semantic Model	Power BI Project (PBIP), TMDL
BI Calculations	DAX
Visualization	Matplotlib
Source Control	Git, GitHub
🏗️ Project Architecture

The project separates data ingestion, transformation, analytical storage, querying, and reporting.

text
NYC TLC Yellow Taxi Parquet Files
                |
                v
       Python Data Ingestion
                |
                v
      Cleaning and Validation
                |
                v
      Cleaned Parquet Dataset
                |
                v
       DuckDB Star Schema
                |
                v
        14 SQL Business Queries
                |
       +--------+---------+
       |                  |
       v                  v
   Excel KPI         Power BI Data
    Workbook          Preparation
                          |
                          v
                  Power BI Semantic Model
                          |
                          v
                   Four Report Pages

Architecture by Layer
Layer	Responsibility
Data ingestion	Downloads monthly TLC trip files and zone lookup data
Data processing	Cleans records and documents row-level exclusions
Data storage	Stores cleaned trips in Parquet format
Data warehouse	Organizes trips and descriptive dimensions in DuckDB
SQL analytics	Produces reusable business queries and CSV outputs
Excel reporting	Presents KPIs, pivots, summaries, and charts
Power BI	Provides a semantic model, DAX measures, and report pages
Visualization	Generates static chart pages from analytical query results
🐍 Python Analytics Pipeline

Python automates the core data preparation and reporting workflow.

Main Scripts
Script	Purpose
scripts/download_data.py	Downloads source files from TLC
scripts/clean_trips.py	Validates and cleans trip records
scripts/build_warehouse.py	Builds the DuckDB analytical database
scripts/run_queries.py	Executes SQL business queries and saves results
scripts/export_powerbi_data.py	Prepares data files for Power BI
scripts/make_charts.py	Generates chart images from analytical results
scripts/build_excel.py	Creates the Excel KPI workbook
Exploratory Data Analysis

The notebook notebooks/01_cleaning_and_eda.ipynb documents the data exploration process.

EDA covers:

Raw data structure and quality.
Cleaning rules and record retention.
Trip distance and duration distributions.
Hourly demand patterns.
Payment method distribution.
Monthly trip volume and revenue.
Weekday versus weekend behavior.
Pickup zone and airport performance.

The notebook provides the analytical foundation for the SQL queries and dashboard metrics.

🗄️ DuckDB Data Warehouse

DuckDB serves as the analytical database for querying millions of cleaned taxi records efficiently.

The project uses a star-schema design to organize the main trip fact table and its descriptive dimensions.

Data Model

Fact table

fact_trips

Contains the cleaned trip-level records, including timestamps, zone IDs, trip metrics, payment information, and monetary amounts.

Dimension tables

dim_zone — zone, borough, and service-zone information.
dim_date — calendar attributes used for date-based analysis.
dim_time — time-of-day attributes used for hourly analysis.
dim_payment — payment type descriptions.
Star Schema
text
                 dim_zone
                /        \
               /          \
       dim_date ---------- fact_trips ---------- dim_payment
               \          /
                \        /
                  dim_time


The model uses five relationships in the Power BI semantic model. The drop-off zone relationship is inactive and can be activated in DAX when analyzing drop-off locations.

Why DuckDB?
Efficient analytical SQL execution.
Direct querying of Parquet data.
Local, file-based database storage.
Convenient integration with Python.
Well suited to exploratory and batch analytics.
Database Location

data/warehouse/nyc_taxi.duckdb

The database is created by:

bash
python scripts/build_warehouse.py

📈 SQL Analysis

The project includes 14 business-focused SQL queries that investigate demand, trip economics, location performance, and customer payment behavior.

Query files are stored in:

sql/queries/

Results are exported as CSV files to:

sql/results/

Analytical Areas
Daily and monthly demand.
Hourly demand patterns.
Weekday and weekend comparisons.
Pickup zone rankings.
Airport pickup activity.
Trip distance and duration bands.
Revenue per mile and per minute.
Payment method distribution.
Recorded tipping behavior.
Congestion relief zone fees.
Cleaning and data quality analysis.
Example Business Questions
Question	Analysis
Which month has the highest trip volume?	Monthly trip aggregation
What is the busiest hour?	Trips grouped by hour
Which zones generate the most pickups?	Pickup zone ranking
How important are airports to revenue?	Airport pickup and revenue comparison
Which trip lengths generate the most revenue per mile?	Distance-band analysis
How do tips differ by payment type?	Payment and tip aggregation
What is the weekday versus weekend difference?	Day-type comparisons
How much was collected in congestion fees?	Fee aggregation

The exported results provide a consistent source for the Excel workbook and static charts.

📗 Excel KPI Workbook

The Excel reporting layer provides an accessible summary of the analysis for users who prefer spreadsheet-based reporting.

Workbook: excel/nyc_taxi_kpis.xlsx

Workbook Contents
KPI summary.
Monthly trend analysis.
Weekday-by-hour demand pivot table.
Conditional formatting with color scales.
Weekday versus weekend comparison.
Top pickup zones.
Airport analysis.
Payment methods and tipping.
Trip distance bands.
Daily trip volumes.
Data cleaning log.
Native Excel charts.

The workbook is generated using:

bash
python scripts/build_excel.py


It provides a supplementary reporting format alongside the Power BI dashboards.

📊 Power BI Dashboard

The Power BI project is organized into four report pages, supported by a TMDL semantic model and custom DAX measures.

Project: dashboard/NycTaxiDemand.pbip

1. Executive Overview

<text color="secondary" size="sm">Purpose: Summarize overall taxi demand and financial performance.</text>

Key metrics and analyses:

Total trips.
Total trip charges.
Average trip amount.
Average distance and duration.
Monthly demand trends.
High-level trip performance indicators.
2. Demand by Time

<text color="secondary" size="sm">Purpose: Understand when taxi demand is highest.</text>

Key analyses:

Trips by hour.
Weekday versus weekend demand.
Daily trip volume.
Monthly trends.
Average trip speed by time of day.
3. Zones and Airports

<text color="secondary" size="sm">Purpose: Identify geographic demand patterns.</text>

Key analyses:

Pickup zone rankings.
Borough and service-zone comparisons.
Manhattan demand.
JFK and LaGuardia pickup activity.
Airport revenue contribution.
Pickup and drop-off zone analysis.
4. Revenue and Tips

<text color="secondary" size="sm">Purpose: Explore trip economics and payment behavior.</text>

Key analyses:

Total trip charges.
Revenue per mile.
Revenue per minute.
Trip distance bands.
Payment type distribution.
Recorded tip rates.
Congestion relief zone fees.
Semantic Model and DAX

The Power BI project contains:

A TMDL semantic model.
A star schema aligned with the DuckDB warehouse.
Five relationships, including an inactive drop-off zone relationship.
25 DAX measures.
Four report pages.

The measure definitions are documented in:

dashboard/DAX_MEASURES.md

Data Loading Modes

The project supports two fact-table loading modes:

Mode	Dataset	Intended Use
Full data	fact_trips.parquet	Complete analysis using approximately 10.1 million cleaned trips
Sample data	fact_trips_sample.parquet	Lightweight development and demonstration using a 200,000-row random sample

The export script prepares the Power BI data files.

bash
python scripts/export_powerbi_data.py


When the full fact file is present, the model loads the full dataset; otherwise, it falls back to the committed sample.

Important: Totals displayed from the sample represent only a fraction of the full dataset. Rates and averages may be similar, but they are not guaranteed to match exactly.

Power BI Setup
Open dashboard/NycTaxiDemand.pbip in Power BI Desktop.
Ensure PBIP project support is enabled in your installation.
Set the DataFolder parameter to your local data/powerbi directory.
Refresh the model.
Validate the loaded row count and measures.
Navigate through all four report pages.

The PBIP project has not yet been verified by opening it in Power BI Desktop, so the repository's chart screenshots should not be presented as actual Power BI screenshots.

📌 Key Performance Indicators

The project uses KPIs to describe taxi demand, trip economics, and recorded payment behavior.

KPI	Definition
Total Trips	Number of cleaned trip records
Total Trip Charges	Sum of total_amount
Average Trip Amount	Average total_amount per trip
Average Trip Distance	Average trip distance in miles
Average Trip Duration	Average trip duration in minutes
Trips per Day	Number of trips divided by the number of calendar days
Revenue per Mile	Trip charges divided by total distance
Revenue per Minute	Trip charges divided by total duration
Average Trip Speed	Total distance divided by total trip duration
Recorded Tip Rate	Recorded tip amount relative to the defined fare or trip-charge denominator
Payment Mix	Share of trips by payment type
Airport Revenue Share	Airport pickup revenue divided by total revenue
Congestion Fee Total	Sum of recorded congestion relief zone fees
Metric Interpretation

Financial and tipping metrics require careful interpretation.

total_amount represents recorded trip charges, not necessarily the taxi operator's net profit.
Revenue per mile and revenue per minute are trip-charge efficiency measures, not driver earnings.
Recorded tips do not capture unreported cash gratuities.
Average trip speed should be calculated from distance and duration at a clearly defined aggregation level.
Payment type 0 is treated as missing or unspecified for some analyses.
💡 Key Business Findings

The following findings are based on the cleaned June–August 2025 dataset.

1. Overall Demand and Revenue

<grid columns={2} gap={3}>
<grid-item>
<box border radius="lg" padding={3} gap={1}>
<text color="secondary" size="sm">Cleaned Trips</text>
<title size="2xl" color="default">10.10M</title>
</box>
</grid-item>
<grid-item>
<box border radius="lg" padding={3} gap={1}>
<text color="secondary" size="sm">Total Trip Charges</text>
<title size="2xl" color="default">$289.2M</title>
</box>
</grid-item>
<grid-item>
<box border radius="lg" padding={3} gap={1}>
<text color="secondary" size="sm">Average per Trip</text>
<title size="xl" color="default">$28.63</title>
</box>
</grid-item>
<grid-item>
<box border radius="lg" padding={3} gap={1}>
<text color="secondary" size="sm">Average Distance</text>
<title size="xl" color="default">3.55 mi</title>
</box>
</grid-item>
</grid>

The median trip distance is just 1.98 miles, while the average duration is 16.6 minutes. This suggests that short urban journeys make up a substantial part of the cleaned dataset.

2. Monthly Demand Declined Throughout Summer
Month	Trips per Day	Monthly Revenue
June	124,580	$108.1M
July	108,420	$95.5M
August	96,875	$85.6M
Trips per day declined by 10.1% from June to July.
Trips per day declined by a further 10.6% from July to August.
The five busiest days in the dataset were all in June.
Thursday, June 5, was the busiest day with 150,914 trips.
Monday, August 18, was the quietest day with 73,984 trips.

This pattern suggests a summer-period decline in recorded yellow taxi activity. Additional years of data would be needed to distinguish seasonality from longer-term changes.

3. Evening Is the Busiest Period
18:00 is the busiest hour for both weekdays and weekends.
Weekday demand peaks at approximately 7,345 trips per day during that hour.
Weekend demand peaks at approximately 6,918 trips per day.
Weekday speeds fall to approximately 10–11 mph between 09:00 and 13:00.
Overnight speeds rise to approximately 19–23 mph.

These patterns indicate that trip demand and traffic conditions vary significantly throughout the day.

4. Weekday and Weekend Patterns Differ
Metric	Weekdays	Weekends
Trips per day	109,815	109,761
Average trip distance	3.43 mi	3.85 mi
Average speed	12.2 mph	14.2 mph

Daily volume is almost identical across the two groups, but weekend trips are longer and faster on average.

Wednesday is the busiest weekday, averaging 117,966 trips per day, while Monday is the quietest, averaging 97,420.

5. Manhattan and Airport Zones Drive Activity
Approximately 80.0% of trips both start and end in Manhattan.
JFK is the largest individual pickup zone, with 473,813 trips.
Midtown Center follows with 450,607 pickups.
JFK and LaGuardia together account for 7.6% of trips but 19.6% of revenue.
A JFK pickup averages $78.41 in trip charges, covers 15.2 miles, and lasts 43.6 minutes.

Airport trips contribute a disproportionately large share of revenue relative to their share of total trip volume.

6. Short Trips Generate More Charges per Mile
Trip Distance	Charges per Mile	Charges per Minute
Under 1 mile	$21.64	$2.32
20+ miles	$4.54	$1.88
2–5 miles	—	$1.50

Trips under one mile generate substantially more recorded charges per mile than trips of 20 miles or more.

However, revenue per mile is not the same as profitability. Waiting time, deadheading, operating costs, traffic, and driver compensation would need to be included to estimate net earnings.

7. Credit Cards Dominate Recorded Tipping Analysis
Credit cards account for 65.6% of trips.
Credit card trips have a recorded tip rate of 22.9% of fare.
94.7% of credit card trips carry a recorded tip.
Recorded card tip rates peak at 24.1% around 18:00.
Overnight recorded tip rates fall to approximately 21%.
Cash tips are not recorded in the same way and appear as 0.0% in the source data.

Consequently, the observed tipping results describe recorded tips rather than all gratuities actually paid by passengers.

8. Payment Type 0 Is a Significant Data Gap

Approximately 22.7% of cleaned trips have payment type 0, which is treated as flex fare or unspecified in this project.

These records frequently lack rate-code and passenger-count information. They remain useful for demand and recorded trip-charge analysis, but are less informative for payment-specific tipping comparisons.

9. Congestion Relief Zone Fees
Approximately 82.6% of Manhattan pickups incurred the recorded CBD congestion relief zone fee.
The dataset contains approximately $5.73M in recorded congestion-related fees across all trips.

These figures describe the cleaned trip records and their recorded fee fields. They should not be interpreted as a complete accounting of all congestion-pricing collections.

💼 Business Value

The analysis can support several practical use cases.

Fleet Operations
Identify peak demand hours.
Understand weekday and weekend operating patterns.
Explore trip lengths and duration distributions.
Investigate demand changes across the summer period.
Geographic Planning
Identify high-volume pickup zones.
Compare Manhattan and airport demand.
Understand the contribution of airport trips to recorded charges.
Explore pickup and drop-off patterns.
Revenue Analysis
Compare recorded charges across trip distance bands.
Analyze revenue per mile and per minute.
Investigate airport-related trip economics.
Understand the contribution of recorded additional fees.
Customer and Payment Analysis
Compare recorded tips by payment type.
Examine how tipping varies by time of day.
Identify limitations in payment-method data.
Avoid misleading comparisons caused by missing cash tips.
Business Intelligence
Standardize KPIs across SQL, Excel, and Power BI.
Provide repeatable analytical outputs.
Deliver dashboard-ready datasets.
Communicate findings through charts and reports.
▶️ Installation and Setup
Prerequisites
Python 3.10 or a version compatible with the project's dependencies.
Git.
Microsoft Excel or a compatible spreadsheet application.
Microsoft Power BI Desktop for the dashboard project.

DuckDB is used as the analytical database and can be installed through the Python dependency file.

1. Clone the Repository
bash
git clone <YOUR_REPOSITORY_URL>
cd <YOUR_REPOSITORY_DIRECTORY>


Replace the placeholders with the actual GitHub repository URL and directory name.

2. Install Dependencies
bash
pip install -r requirements.txt


For a reproducible setup, use a virtual environment and the dependency versions specified by the project.

3. Download the Raw Data
bash
python scripts/download_data.py


This downloads the monthly trip files and zone lookup data into data/raw/.

The source files are approximately 200 MB in total.

4. Clean the Trip Data
bash
python scripts/clean_trips.py


Expected outputs include:

data/processed/trips_clean.parquet
data/processed/cleaning_log.csv
5. Build the DuckDB Warehouse
bash
python scripts/build_warehouse.py


Expected database:

data/warehouse/nyc_taxi.duckdb

6. Execute the SQL Queries
bash
python scripts/run_queries.py


Query outputs are saved in:

sql/results/

7. Export Power BI Data
bash
python scripts/export_powerbi_data.py


This prepares the dimension files and trip fact data used by the Power BI model.

8. Generate Static Charts
bash
python scripts/make_charts.py


Expected outputs:

screenshots/charts/

These are Python-generated charts rather than screenshots captured from Power BI Desktop.

9. Build the Excel Workbook
bash
python scripts/build_excel.py


Expected output:

excel/nyc_taxi_kpis.xlsx

10. Open Power BI

Open dashboard/NycTaxiDemand.pbip in Power BI Desktop.

Configure the DataFolder parameter to point to your local data/powerbi directory and refresh the model.

If the full fact_trips.parquet file exists, the model loads the full cleaned dataset. Otherwise, it falls back to the committed 200,000-row random sample.

Important: Verify the final refresh, table relationships, DAX measures, and report visuals in Power BI Desktop before publishing the dashboard as a validated deliverable.

📂 Repository Structure
text
NYC-Taxi-Demand-Analytics/
│
├── data/
│   ├── raw/
│   ├── processed/
│   │   ├── cleaning_log.csv
│   │   └── trips_clean.parquet
│   ├── warehouse/
│   │   └── nyc_taxi.duckdb
│   └── powerbi/
│       ├── fact_trips_sample.parquet
│       ├── dimension files
│       └── full fact file when exported
│
├── dashboard/
│   ├── NycTaxiDemand.pbip
│   └── DAX_MEASURES.md
│
├── excel/
│   └── nyc_taxi_kpis.xlsx
│
├── notebooks/
│   └── 01_cleaning_and_eda.ipynb
│
├── scripts/
│   ├── download_data.py
│   ├── clean_trips.py
│   ├── build_warehouse.py
│   ├── run_queries.py
│   ├── export_powerbi_data.py
│   ├── make_charts.py
│   └── build_excel.py
│
├── sql/
│   ├── schema.sql
│   ├── queries/
│   └── results/
│
├── screenshots/
│   └── charts/
│       └── 01-executive-overview.png
│
├── artifacts/
│   └── charts-walkthrough.mp4
│
├── requirements.txt
├── README.md
└── .gitignore


This is a representative structure. Keep only the paths and filenames that exist in the actual repository.

⚠️ Limitations and Considerations
1. Limited Reporting Period

The analysis covers only June through August 2025. Monthly declines may reflect seasonal effects, holidays, weather, tourism patterns, or other factors. Comparisons with previous years are required to establish whether the pattern is recurring.

2. Cleaning Rules Affect the Results

The cleaning pipeline excludes invalid and unusual records. In particular, excluding negative fares and other nonstandard transactions can affect totals compared with the original TLC data.

3. Recorded Tips Are Incomplete

Cash tips are not captured consistently in the trip records. Tip comparisons therefore focus on recorded gratuities, particularly those associated with credit card payments.

4. Missing Payment Information

Payment type 0 represents a substantial portion of cleaned records. This limits the reliability of payment-specific tipping analysis and some customer behavior comparisons.

5. Trip Charges Are Not Profit

The total_amount field measures recorded trip charges. It does not represent net income after fuel, maintenance, labor, insurance, idle time, or other operating costs.

6. Power BI Validation

The PBIP/TMDL project has not yet been opened and validated in Power BI Desktop. The static chart pages are generated using Python and should not be described as Power BI screenshots.

7. Sample Versus Full Dataset

When the dashboard uses the 200,000-row sample, absolute totals will differ substantially from full-dataset results. Always confirm the loaded data source before comparing dashboard totals with the full analysis.

🧠 Skills Demonstrated
Python and Data Engineering
Data ingestion and automation.
Parquet data processing.
Data cleaning and validation.
Exploratory data analysis.
Reproducible analytics workflows.
SQL and Data Warehousing
DuckDB.
Star-schema design.
Fact and dimension tables.
Aggregations and analytical queries.
Time-based analysis.
Query result exports.
Excel Analytics
KPI reporting.
Pivot tables.
Conditional formatting.
Native charts.
Business reporting workbooks.
Power BI and DAX
PBIP project structure.
TMDL semantic models.
Table relationships.
DAX measure development.
Interactive report design.
Dashboard-ready data preparation.
Business Analytics
Demand analysis.
Revenue efficiency analysis.
Geographic performance analysis.
Payment and tipping behavior.
Data quality assessment.
Business insight communication.
Version Control and Documentation
Git and GitHub.
Repository organization.
Technical documentation.
Reproducible project setup.
🚀 Future Improvements

Potential enhancements include:

Compare summer demand across multiple years.
Add weather, holiday, and major-event data.
Analyze pickup-to-drop-off flows using geographic maps.
Estimate net profitability using operating cost assumptions.
Investigate airport demand by hour and weekday.
Develop demand forecasting models.
Analyze congestion pricing before and after implementation.
Add automated data-quality monitoring.
Validate and publish Power BI screenshots.
Automate dashboard data refreshes.
Improve documentation of payment types and fee fields.
📄 License and Attribution

The underlying taxi trip records are provided by the New York City Taxi and Limousine Commission.

Source: NYC TLC Trip Record Data

Review the source terms and any applicable data-use requirements before redistributing source files or derived datasets. Add a repository LICENSE file specifying the license for your own code and project materials.

👨‍💻 Author

Project: NYC Taxi Demand Analytics

Tools: Python · DuckDB · SQL · Excel · Power BI · DAX

Reporting period: June–August 2025

An end-to-end analytics project transforming millions of NYC taxi trip records into insights on urban demand, geographic activity, trip economics, and recorded customer tipping behavior.