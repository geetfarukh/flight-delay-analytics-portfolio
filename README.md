# flight-delay-analytics-portfolio
An end-to-end data engineering and analytics project that processes millions of U.S. domestic flight records to surface on-time performance (OTP) insights across carriers, routes, and airports — using a production-style medallion architecture.

#**Project Overview**
Airlines and airport operators rely on OTP data to benchmark performance, identify delay hotspots, and make scheduling decisions. This project replicates that commercial analytics workflow using publicly available BTS (Bureau of Transportation Statistics) On-Time Performance data.
The pipeline ingests raw flight data, transforms it through Bronze → Silver → Gold layers, loads the Gold tables into a relational database, and visualises key KPIs in an interactive Power BI dashboard.

**#Architecture**
BTS Raw CSV (Jan 2023)
        │
        ▼
┌───────────────┐
│  BRONZE Layer │  Raw ingestion → Delta table (no transformation)
└───────────────┘
        │
        ▼
┌───────────────┐
│  SILVER Layer │  Cleaned, typed, null-handled, derived columns added
└───────────────┘
        │
        ▼
┌───────────────┐
│   GOLD Layer  │  Aggregated KPI tables: route_kpis, carrier_kpis, dow_heatmap
└───────────────┘
        │
        ▼
┌───────────────┐
│     MySQL     │  Gold tables loaded to Railway.com hosted MySQL instance
└───────────────┘
        │
        ▼
┌───────────────┐
│   Power BI    │  3-page dashboard with DAX measures and slicers
└───────────────┘

🛠️ Tech Stack
Layer              Tool
--------------------------------------------------------------------------
Data Processing    Apache Spark (PySpark)
Platform           Databricks Free Edition 
Storage            Delta Lake (Unity Catalog Volumnes)
Database           MySQL via Railway.com 
Orchestration      Databricks Jobs (sequential task pipeline)
Visualisation      Power BI Desktop + DAX
Data Source        BTS On-Time Performance(January 2023)

#**Repository Structure**
flight-delay-analytics/
│
├── notebooks/
│   ├── 01_bronze_ingestion.ipynb       # Raw CSV → Bronze Delta table
│   ├── 02_silver_transform.ipynb  		# Cleaning, typing, feature engineering
│   ├── 03_gold_kpis.ipynb       		# KPI table generation
│   └── 04_load_mysql.ipynb             # Gold → MySQL export
│
├── data/
│   └── README.md                       # Data source info + download link
│
├── dashboard/
│   └── flight_delay_dashboard.pbix     # Power BI report file
│


📊 **Gold Layer KPI Tables**
route_kpis
Aggregated per origin–destination route pair:
	•	OTP % (On-Time Performance)
	•	Average Arrival Delay (minutes)
	•	Cancellation Rate %
	•	Best / Worst performing routes
carrier_kpis
Aggregated per carrier:
	•	OTP %
	•	Average Departure Delay
	•	Total Flights
	•	Carrier ranking
dow_heatmap
Delay patterns by day of week and carrier — useful for identifying systemic delay trends.

📈 **Power BI Dashboard**
The report has three pages:
	1.	Overview — High-level OTP%, cancellation rate, and delay summary with slicers by carrier and airport
	2.	Route Analysis — Best and worst routes by OTP and average delay
	3.	Carrier & Day Trends — Carrier performance comparison and day-of-week delay heatmap
Key DAX Measures:
	•	OTP % = DIVIDE(COUNTROWS(FILTER(..., ArrDelayMinutes <= 15)), COUNTROWS(...))
	•	Avg Arrival Delay = AVERAGE(route_kpis[avg_arrival_delay])
	•	Cancellation Rate % = DIVIDE(SUM(...[cancelled]), COUNTROWS(...))
  
🗃️ **Data Source**
	•	Source: BTS On-Time Performance Data
	•	Period: January 2023
	•	Records: ~500,000+ domestic U.S. flights
	•	Key Fields: FlightDate, Reporting_Airline, Origin, Dest, DepDelay, ArrDelay, Cancelled, CancellationCode
Raw data is not included in this repo due to file size. Download directly from BTS using the link above and place in the /data/ folder before running the notebooks.

**How to Run**
	1.	Upload the BTS CSV to your Databricks Unity Catalog Volume
	2.	Run notebooks in order: 01 → 02 → 03 → 04
	3.	Update the MySQL connection string in 04_mysql_load.ipynb with your Railway.com credentials
	4.	Open flight_delay_dashboard.pbix in Power BI Desktop and refresh the MySQL connection
Note: This project was built on Databricks Free Edition. A Databricks workspace with Unity Catalog enabled is required.

💡 **Key Insights**
	•	Carrier OTP varied significantly — the gap between the best and worst carrier exceeded 15 percentage points in January 2023
	•	Friday and Sunday showed the highest average arrival delays across most carriers
	•	Short-haul routes in the Northeast had disproportionately high cancellation rates

  👤 **Author**
Geet — Data AnalystTargeting commercial analytics roles in the airline industry

📄 **License**
This project is for portfolio and educational purposes. Data sourced from BTS is publicly available under U.S. government open data terms
