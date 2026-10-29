🚕 NYC Taxi Demand Analytics
An End-to-End Data Analytics & Business Intelligence Project

When and where do people take yellow cabs in New York City? Which trips generate the most revenue, and how do tipping patterns vary?

An end-to-end analytics project analyzing 11.8 million NYC yellow taxi trip records from June to August 2025, with approximately 10.1 million records retained after data cleaning.

Using Python, DuckDB, SQL, Excel, and Power BI, this project explores urban taxi demand, trip economics, pickup zones, airport activity, payment methods, tipping behavior, and temporal trends.

📊 Project Highlights
Metric	Result
Raw trip records	11,796,014
Cleaned trip records	10,101,536
Records retained	85.6%
Total recorded trip charges	$289.2M
Average trip charges	$28.63
SQL business queries	14
Power BI report pages	4
DAX measures	25
Excel KPI workbook	1
Reporting period	June–August 2025

Demo: Watch the 32-second charts walkthrough

!NYC Taxi Analytics — Executive Overview

Note: The image above is a Python-generated chart, not a screenshot captured from Power BI Desktop.

📑 Table of Contents
Project Overview
Business Questions
Dataset
Data Cleaning and Validation
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
Limitations and Considerations
Skills Demonstrated
Future Improvements
License and Attribution
🎯 Project Overview

New York City's yellow taxi trip records provide valuable information about urban mobility, transportation demand, trip economics, and passenger payment behavior.

This project analyzes three months of NYC Taxi and Limousine Commission (TLC) yellow taxi records to investigate demand patterns, identify high-volume pickup zones, compare trip economics, and examine recorded tipping behavior.

The solution follows an end-to-end analytics workflow, from raw data ingestion and cleaning to analytical SQL queries, spreadsheet reporting, and interactive business intelligence.

Project Objectives
Analyze taxi demand by hour, weekday, weekend, and month.
Identify high-volume pickup zones, boroughs, and airport locations.
Evaluate recorded trip charges across distance and duration categories.
Compare trip duration, distance, and average speed.
Examine payment methods and recorded tipping patterns.
Analyze congestion relief zone fees.
Develop reusable SQL queries and business KPIs.
Build an Excel KPI workbook and Power BI dashboards.
Communicate data-driven findings while documenting data limitations.
Reporting Period

June 1 – August 31, 2025

The analysis uses the June, July, and August 2025 TLC yellow taxi trip files, supplemented by the official taxi zone lookup dataset.

❓ Business Questions
Business question	Analytical approach
When is taxi demand highest?	Hourly, daily, weekday, weekend, and monthly trends
Where does demand originate?	Pickup zones, boroughs, Manhattan, JFK, and LaGuardia
Which trips generate the most charges?	Distance bands, duration, and recorded trip amounts
How do passengers tip?	Payment type, recorded tip amount, tip rate, and time of day
How is demand changing?	Daily and monthly trip volume and recorded charges
How do trip economics vary?	Charges per mile and per minute
How significant are airport trips?	Airport pickup volume and revenue contribution
What data quality issues affect interpretation?	Missing values, invalid fares, and unspecified payment types
📊 Dataset
Data Sources

1. NYC TLC Yellow Taxi Trip Records

Monthly Parquet files for June, July, and August 2025.

Source: NYC Taxi and Limousine Commission — Trip Record Data

2. Taxi Zone Lookup Dataset

The taxi_zone_lookup.csv file provides zone identifiers, boroughs, and service-zone classifications.

Dataset Summary
Attribute	Description
Source	NYC TLC yellow taxi trip records
Period	June–August 2025
Raw records	11,796,014
Cleaned records	10,101,536
Retention rate	85.6%
Approximate download size	200 MB
Zone lookup entries	265
Source format	Parquet and CSV

The raw source files are not included in the repository. They are downloaded locally using the ingestion script.

bash
python scripts/download_data.py

Important Data Fields
Pickup and drop-off timestamps
Pickup and drop-off location IDs
Trip distance
Passenger count
Payment type
Fare amount
Total trip amount
Tip amount
Tolls and additional charges
Rate code
Congestion-related fee fields, where available

These fields support the project's demand, geographic, financial, and payment behavior analyses.

🧹 Data Cleaning and Validation

The raw dataset contains invalid timestamps, unusual trip distances, negative fares, missing passenger counts, and invalid rate codes.

A sequential Python cleaning pipeline applies validation rules in order. Each rejected record is attributed to the first rule it fails, making it possible to quantify the impact of each validation step.

Cleaning audit: data/processed/cleaning_log.csv

Cleaning Results
Cleaning step	Rows remaining	Rows dropped
Raw records from all three files	11,796,014	—
Pickup timestamp within June–August 2025	11,795,991	23
Valid pickup and drop-off zone IDs (1–263)	11,698,973	97,018
Trip duration between 1 and 180 minutes	11,389,892	309,081
Trip distance between 0.1 and 100 miles	11,061,518	328,374
Fare between $3 and $500	10,330,580	730,938
Valid nonnegative tips, tolls, and extras; total at least fare	10,330,390	190
Average speed below 70 mph	10,329,605	785
Missing passenger count or count between 1 and 6	10,272,514	57,091
Missing or valid rate code; invalid code 99 excluded	10,101,536	170,978
Data Retention

Approximately 85.6% of raw records remain after cleaning.

The raw dataset contains 783,844 records with negative fares. These may include refunds, reversals, or voided transactions, although their exact causes cannot be established from the fare field alone.

The project's fare-validation rules exclude negative fares, improving consistency for the selected analyses. Consequently, the resulting trip-charge totals describe the cleaned dataset rather than every financial adjustment present in the original data.

🛠️ Technology Stack
Category	Technologies	Purpose
Programming	Python	Data ingestion and analytics automation
Data processing	Pandas, PyArrow	Cleaning and Parquet processing
Data storage	Parquet	Efficient columnar data storage
Analytical database	DuckDB	Local analytical warehouse
Query language	SQL	Aggregations and business analysis
Spreadsheet analytics	Microsoft Excel	KPI summaries, pivots, and charts
Business intelligence	Power BI	Interactive dashboards
Semantic modeling	PBIP, TMDL	Power BI project and model structure
BI calculations	DAX	Measures and business KPIs
Visualization	Matplotlib	Static analytical charts
Version control	Git, GitHub	Project management and documentation
🏗️ Project Architecture

The project separates ingestion, transformation, analytical storage, querying, and reporting.

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
          +------+------+
          |             |
          v             v
     Excel KPI      Power BI Data
      Workbook       Preparation
                         |
                         v
                Power BI Semantic Model
                         |
                         v
                 Four Report Pages

Architecture by Layer
Layer	Responsibility
Data ingestion	Downloads monthly TLC trip files and zone lookup data
Data processing	Cleans records and documents exclusions
Data storage	Stores cleaned trips in Parquet format
Data warehouse	Organizes trips and descriptive dimensions in DuckDB
SQL analytics	Produces reusable queries and CSV results
Excel reporting	Presents KPIs, pivots, summaries, and charts
Power BI	Provides a semantic model, DAX measures, and report pages
Visualization	Generates static charts from analytical results
🐍 Python Analytics Pipeline

Python automates the main data preparation and reporting workflow.

Main Scripts
Script	Purpose
scripts/download_data.py	Downloads source files from TLC
scripts/clean_trips.py	Validates and cleans trip records
scripts/build_warehouse.py	Builds the DuckDB analytical database
scripts/run_queries.py	Executes SQL queries and exports results
scripts/export_powerbi_data.py	Prepares data files for Power BI
scripts/make_charts.py	Generates static charts
scripts/build_excel.py	Creates the Excel KPI workbook
Exploratory Data Analysis

The notebook notebooks/01_cleaning_and_eda.ipynb documents the exploratory analysis.

EDA includes:

Raw data structure and quality checks
Cleaning rules and record retention
Trip distance and duration distributions
Hourly demand patterns
Payment method distribution
Monthly trip volume and revenue trends
Weekday versus weekend behavior
Pickup zone and airport performance

The notebook establishes the analytical foundation for the SQL queries and reporting layers.

🗄️ DuckDB Data Warehouse

DuckDB serves as the analytical database for querying millions of cleaned taxi records.

The warehouse uses a star-schema design, separating the trip-level fact table from descriptive dimensions.

Fact Table

fact_trips

Contains cleaned trip records, including timestamps, zone IDs, trip metrics, payment information, and monetary amounts.

Dimension Tables
dim_zone — Zone, borough, and service-zone information
dim_date — Calendar attributes for date-based analysis
dim_time — Time-of-day attributes for hourly analysis
dim_payment — Payment type descriptions
Conceptual Star Schema
text
                     dim_zone
                    /        \
                   /          \
             dim_date ---- fact_trips ---- dim_payment
                   \          /
                    \        /
                      dim_time


The Power BI semantic model contains five relationships, including an inactive drop-off zone relationship that can be activated in DAX when analyzing drop-off locations.

Why DuckDB?
Efficient analytical SQL execution
Direct integration with Parquet files
Local, file-based database storage
Convenient integration with Python
Suitable for exploratory and batch analytics

Database location: data/warehouse/nyc_taxi.duckdb

Build the warehouse using:

bash
python scripts/build_warehouse.py

📈 SQL Analysis

The project includes 14 business-focused SQL queries covering demand, trip economics, geographic performance, and payment behavior.

Query files: sql/queries/
Exported results: sql/results/
Warehouse schema: sql/schema.sql
Analytical Areas
Daily and monthly demand
Hourly demand patterns
Weekday and weekend comparisons
Pickup zone rankings
Airport pickup activity
Trip distance and duration bands
Charges per mile and per minute
Payment method distribution
Recorded tipping behavior
Congestion relief zone fees
Cleaning and data quality analysis
Example Business Questions
Question	Analysis
Which month has the highest trip volume?	Monthly trip aggregation
What is the busiest hour?	Trips grouped by hour
Which zones generate the most pickups?	Pickup zone ranking
How important are airports to trip charges?	Airport pickup and charge comparison
Which trip lengths have the highest charges per mile?	Distance-band analysis
How do recorded tips vary by payment type?	Payment and tip aggregation
How do weekdays compare with weekends?	Day-type comparisons
How much was collected in recorded congestion fees?	Fee aggregation

Execute the queries with:

bash
python scripts/run_queries.py


The resulting CSV files provide a consistent source for the Excel workbook and static charts.

📗 Excel KPI Workbook

The Excel reporting layer provides a spreadsheet-based view of the project's findings.

Workbook: excel/nyc_taxi_kpis.xlsx

Workbook Contents
KPI summary
Monthly trend analysis
Weekday-by-hour demand pivot table
Conditional formatting and color scales
Weekday versus weekend comparison
Top pickup zones
Airport analysis
Payment methods and tipping
Trip distance bands
Daily trip volumes
Data cleaning log
Native Excel charts

Generate the workbook using:

bash
python scripts/build_excel.py


The workbook complements Power BI by providing familiar spreadsheet-based summaries and analysis.

📊 Power BI Dashboard

Project: dashboard/NycTaxiDemand.pbip

The Power BI project is organized into four report pages, supported by a TMDL semantic model and custom DAX measures.

Page 1: Executive Overview

Purpose: Summarize overall taxi demand and recorded financial performance.

Total trips
Total recorded trip charges
Average trip amount
Average distance and duration
Monthly demand trends
High-level trip performance indicators
Page 2: Demand by Time

Purpose: Understand when taxi demand is highest.

Trips by hour
Weekday versus weekend demand
Daily trip volume
Monthly trends
Average trip speed by time of day
Page 3: Zones and Airports

Purpose: Identify geographic demand patterns.

Pickup zone rankings
Borough and service-zone comparisons
Manhattan demand
JFK and LaGuardia pickup activity
Airport trip-charge contribution
Pickup and drop-off zone analysis
Page 4: Revenue and Tips

Purpose: Explore trip economics and recorded payment behavior.

Total recorded trip charges
Charges per mile
Charges per minute
Trip distance bands
Payment type distribution
Recorded tip rates
Congestion relief zone fees
Semantic Model and DAX

The project includes:

A TMDL semantic model
A star schema aligned with the DuckDB warehouse
Five relationships, including an inactive drop-off zone relationship
25 DAX measures
Four report pages

Measure definitions are documented in dashboard/DAX_MEASURES.md.

Data Loading Modes
Mode	Dataset	Intended use
Full data	fact_trips.parquet	Analysis of the complete cleaned dataset
Sample data	fact_trips_sample.parquet	Lightweight development and demonstration using 200,000 rows

The export script prepares the Power BI data files:

bash
python scripts/export_powerbi_data.py


When the full fact file is available, the model loads it. Otherwise, it falls back to the committed sample.

Important: Dashboard totals from the sample represent only a fraction of the full dataset. Rates and averages may be similar, but they are not guaranteed to match exactly.

Power BI Setup
Open dashboard/NycTaxiDemand.pbip in Power BI Desktop.
Ensure PBIP project support is enabled in your installation.
Set the DataFolder parameter to your local data/powerbi directory.
Refresh the model.
Validate the loaded row count, relationships, and measures.
Navigate through all four report pages.

The PBIP project has not yet been verified by opening it in Power BI Desktop. The static chart pages should therefore not be presented as screenshots of the Power BI report.

📌 Key Performance Indicators
KPI	Definition
Total Trips	Count of cleaned trip records
Total Trip Charges	Sum of total_amount
Average Trip Amount	Average total_amount per trip
Average Trip Distance	Average trip distance in miles
Average Trip Duration	Average trip duration in minutes
Trips per Day	Daily trip count, or total trips divided by calendar days
Charges per Mile	Total trip charges divided by total distance
Charges per Minute	Total trip charges divided by total duration
Average Trip Speed	Total distance divided by total duration
Recorded Tip Rate	Recorded tips divided by the chosen fare or charge denominator
Payment Mix	Share of trips by payment type
Airport Revenue Share	Airport pickup charges divided by total trip charges
Congestion Fee Total	Sum of recorded congestion relief zone fees
Metric Interpretation
total_amount represents recorded trip charges, not operator net profit.
Charges per mile and per minute are efficiency measures, not driver earnings.
Recorded tips do not capture unreported cash gratuities.
Average speed should be calculated from total distance and duration at a clearly defined aggregation level.
Payment type 0 is treated as missing or unspecified for selected analyses.
Airport revenue share depends on the pickup-zone definition used in the analysis.
💡 Key Business Findings

The following findings are based on the cleaned June–August 2025 dataset.

1. Overall Demand and Recorded Charges
KPI	Result
Cleaned trips	10.10 million
Total recorded trip charges	$289.2 million
Average trip amount	$28.63
Average trip distance	3.55 miles
Median trip distance	1.98 miles
Average trip duration	16.6 minutes

The difference between median and average distance indicates that trip lengths are not evenly distributed, with longer trips pulling the average upward.

2. Monthly Demand Declined Throughout Summer
Month	Trips per day	Monthly recorded charges
June	124,580	$108.1M
July	108,420	$95.5M
August	96,875	$85.6M
Trips per day declined by 10.1% from June to July.
Trips per day declined by a further 10.6% from July to August.
The five busiest days in the dataset were all in June.
Thursday, June 5, was the busiest day, with 150,914 trips.
Monday, August 18, was the quietest day, with 73,984 trips.

This pattern indicates a decline in recorded taxi activity over the reporting period. Additional years of data are needed to distinguish seasonal effects from longer-term changes.

3. Evening Is the Busiest Period
18:00 is the busiest hour on both weekdays and weekends.
Weekday demand peaks at approximately 7,345 trips per day during that hour.
Weekend demand peaks at approximately 6,918 trips per day.
Weekday speeds fall to approximately 10–11 mph between 09:00 and 13:00.
Overnight speeds rise to approximately 19–23 mph.

Demand and traffic conditions vary throughout the day, making time-of-day analysis useful for operational planning.

4. Weekday and Weekend Patterns Differ
Metric	Weekdays	Weekends
Trips per day	109,815	109,761
Average trip distance	3.43 miles	3.85 miles
Average speed	12.2 mph	14.2 mph

Daily trip volume is almost identical across weekdays and weekends, but weekend trips are longer and faster on average.

Wednesday is the busiest weekday, averaging 117,966 trips per day, while Monday is the quietest, averaging 97,420.

5. Manhattan and Airport Zones Drive Activity
Approximately 80.0% of trips both start and end in Manhattan.
JFK is the largest individual pickup zone, with 473,813 trips.
Midtown Center follows with 450,607 pickups.
JFK and LaGuardia together account for 7.6% of trips but 19.6% of recorded trip charges.
A JFK pickup averages $78.41 in recorded trip charges, covers 15.2 miles, and lasts 43.6 minutes.

Airport trips contribute a disproportionately large share of recorded trip charges relative to their share of trip volume.

6. Short Trips Generate More Charges per Mile
Trip distance	Charges per mile	Charges per minute
Under 1 mile	$21.64	$2.32
2–5 miles	—	$1.50
20+ miles	$4.54	$1.88

Trips under one mile generate substantially more recorded charges per mile than trips of 20 miles or more.

However, charges per mile are not equivalent to profitability. Operating costs, waiting time, deadheading, traffic, and driver compensation would need to be included to estimate net earnings.

7. Credit Cards Dominate Recorded Tipping Analysis
Credit cards account for 65.6% of trips.
Credit card trips have a recorded tip rate of 22.9% of fare.
94.7% of credit card trips carry a recorded tip.
Recorded card tip rates peak at 24.1% around 18:00.
Overnight recorded tip rates fall to approximately 21%.
Cash tips are not captured consistently and appear as 0.0% in the source data.

These findings describe recorded tips rather than all gratuities actually paid by passengers.

8. Payment Type 0 Is a Significant Data Gap

Approximately 22.7% of cleaned trips have payment type 0, treated as flex fare or unspecified in this project.

These records frequently lack rate-code and passenger-count information. They remain useful for demand and recorded charge analysis but are less informative for payment-specific tipping comparisons.

9. Congestion Relief Zone Fees
Approximately 82.6% of Manhattan pickups incurred the recorded CBD congestion relief zone fee.
The dataset contains approximately $5.73 million in recorded congestion-related fees across all trips.

These figures describe the cleaned records and their fee fields. They should not be interpreted as a complete accounting of all congestion-pricing collections.

💼 Business Value
Fleet Operations
Identify peak demand hours.
Compare weekday and weekend operating patterns.
Investigate trip length and duration distributions.
Monitor demand changes over time.
Geographic Planning
Identify high-volume pickup zones.
Compare Manhattan and airport activity.
Evaluate airport contributions to recorded charges.
Explore pickup and drop-off patterns.
Trip Economics
Compare charges across distance bands.
Analyze charges per mile and per minute.
Investigate airport trip characteristics.
Examine recorded additional fees.
Customer and Payment Analysis
Compare recorded tips by payment type.
Examine tipping patterns by time of day.
Identify missing payment information.
Avoid misleading conclusions from incomplete cash-tip records.
Business Intelligence
Standardize KPIs across SQL, Excel, and Power BI.
Produce repeatable analytical outputs.
Prepare dashboard-ready datasets.
Communicate findings through charts and reports.
▶️ Installation and Setup
Prerequisites
Python 3.10 or a version compatible with the project dependencies
Git
Microsoft Excel or a compatible spreadsheet application
Microsoft Power BI Desktop for the dashboard project
Step 1: Clone the Repository
bash
git clone <YOUR_REPOSITORY_URL>
cd <YOUR_REPOSITORY_DIRECTORY>


Replace the placeholders with the actual repository URL and directory name.

Step 2: Install Dependencies
bash
python -m venv .venv


Activate the virtual environment, then run:

bash
pip install -r requirements.txt

Step 3: Download the Raw Data
bash
python scripts/download_data.py


Downloads the monthly trip files and zone lookup dataset into data/raw/.

Step 4: Clean the Trip Data
bash
python scripts/clean_trips.py


Expected outputs:

text
data/processed/
├── trips_clean.parquet
└── cleaning_log.csv

Step 5: Build the DuckDB Warehouse
bash
python scripts/build_warehouse.py


Expected database:

text
data/warehouse/nyc_taxi.duckdb

Step 6: Execute the SQL Queries
bash
python scripts/run_queries.py


Query outputs are saved in sql/results/.

Step 7: Export Power BI Data
bash
python scripts/export_powerbi_data.py


Prepares the dimension files and trip fact data used by the Power BI model.

Step 8: Generate Static Charts
bash
python scripts/make_charts.py


Expected output directory:

text
screenshots/charts/


These are Python-generated charts, not Power BI screenshots.

Step 9: Build the Excel Workbook
bash
python scripts/build_excel.py


Expected output:

text
excel/nyc_taxi_kpis.xlsx

Step 10: Open Power BI

Open dashboard/NycTaxiDemand.pbip in Power BI Desktop.

Configure the DataFolder parameter.
Refresh the model.
Verify the selected full or sample fact file.
Validate table relationships and DAX measures.
Review all four report pages.

Important: Confirm that the full dataset is loaded before comparing Power BI totals with the full-data findings in this README.

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
│       ├── fact_trips.parquet
│       └── dimension files
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


This structure is representative. Keep only the paths and filenames that exist in the actual repository. Large source files and generated datasets can remain excluded from Git when they can be reproduced through the documented pipeline.

⚠️ Limitations and Considerations
Limited reporting period: The analysis covers only June through August 2025. Historical comparisons are required to determine whether the monthly decline is seasonal or part of a longer-term trend.
Cleaning rules affect results: Invalid and unusual records are excluded. Removing negative fares and nonstandard transactions affects totals relative to the original TLC data.
Recorded tips are incomplete: Cash gratuities are not captured consistently. Tipping comparisons primarily describe recorded tips, particularly those associated with credit card payments.
Missing payment information: Payment type 0 represents a substantial portion of cleaned records, limiting some payment-specific comparisons.
Trip charges are not profit: The total_amount field does not account for fuel, maintenance, labor, insurance, idle time, or other operating costs.
Power BI validation: The PBIP/TMDL project has not yet been verified in Power BI Desktop. Dashboard visuals and measures should be validated before publication.
Sample versus full dataset: The 200,000-row sample produces much smaller absolute totals than the full dataset. Rates and averages may be similar but are not guaranteed to match exactly.
Airport classification: Airport results depend on the pickup-zone definitions used in the analysis. Zone mappings and inclusion rules should be documented when reproducing the results.
Fee interpretation: Recorded congestion-related fees may not capture the complete financial picture of congestion-pricing collections.
🧠 Skills Demonstrated
Python and Data Engineering
Data ingestion and automation
Parquet data processing
Data cleaning and validation
Exploratory data analysis
Reproducible analytics workflows
SQL and Data Warehousing
DuckDB
Star-schema design
Fact and dimension tables
Aggregations and analytical queries
Time-based analysis
Query result exports
Excel Analytics
KPI reporting
Pivot tables
Conditional formatting
Native charts
Business reporting workbooks
Power BI and DAX
PBIP project structure
TMDL semantic models
Table relationships
DAX measure development
Interactive report design
Dashboard-ready data preparation
Business Analytics
Demand analysis
Trip-charge efficiency analysis
Geographic performance analysis
Payment and tipping behavior
Data quality assessment
Business insight communication
Version Control and Documentation
Git and GitHub
Repository organization
Technical documentation
Reproducible project setup
🚀 Future Improvements
Compare taxi demand across multiple years.
Integrate weather, holidays, and major-event data.
Map pickup-to-drop-off flows geographically.
Estimate net profitability using operating-cost assumptions.
Analyze airport demand by hour and weekday.
Develop demand forecasting models.
Evaluate congestion pricing before and after implementation.
Add automated data-quality monitoring.
Validate and publish Power BI screenshots.
Automate data refreshes and reporting.
Improve documentation of payment types and fee fields.
📄 License and Attribution

The underlying taxi trip records are provided by the New York City Taxi and Limousine Commission.

Source: NYC TLC Trip Record Data

Review the source terms and applicable data-use requirements before redistributing source files or derived datasets.

Add a LICENSE file specifying the license for your own code and project materials. Do not imply that the source data and your code necessarily share the same license.

👨‍💻 Project Summary

Project: NYC Taxi Demand Analytics

Tools: Python · Pandas · DuckDB · SQL · Excel · Power BI · DAX

Reporting period: June–August 2025

An end-to-end analytics project transforming millions of NYC yellow taxi trip records into insights on urban demand, geographic activity, trip economics, and recorded customer tipping behavior.

The project demonstrates a reproducible analytics workflow that connects data engineering, SQL analysis, spreadsheet reporting, and business intelligence while documenting the assumptions and limitations behind the findings.
