# AI-Powered Customer Email Routing & SLA Operations

## Project Overview
This project showcases an end-to-end data engineering and analytics pipeline designed to automate customer support email triage using Large Language Models (LLMs) and monitor Service Level Agreement (SLA) compliance. 

By leveraging **Databricks Medallion Architecture** and **Power BI**, the system ingests raw support tickets, utilizes AI for intelligent routing (Sentiment, Category, Urgency), processes business logic, and visualizes operational bottlenecks for the management team.

## Tech Stack & Tools
* **Data Engineering & ETL:** Databricks, PySpark, Databricks SQL, Delta Lake.
* **AI & Machine Learning:** Databricks `ai_query()` (LLM integration for text classification).
* **Business Intelligence (BI):** Power BI, DAX.
* **Version Control:** Git & GitHub via Databricks Repos.

## Project Structure & Medallion Architecture
The ETL pipeline strictly follows the Medallion Architecture to ensure data quality and scalability:

* `customer_support_tickets.csv`: The raw synthetic dataset simulating customer email logs.
* `1_bronze_ingestion.ipynb`: **Bronze Layer (Raw Data)** 
  * Ingests the raw CSV file into a Delta table, preserving the original state of the system logs for auditability and historical tracking.
* `2_silver_transformation.ipynb`: **Silver Layer (Cleansed & Enriched)** 
  * Handles data type casting and deduplication.
  * **AI Integration:** Implements Databricks' `ai_query()` to automatically analyze email text and predict `ai_sentiment`, `ai_category`, and `ai_urgency`.
* `3_gold_modeling.ipynb`: **Gold Layer (Business-Ready)** 
  * Computes complex SLA logic based on Urgency levels (e.g., High = 4 hours, Medium = 24 hours).
  * **Data Quality Fix:** Implemented `ABS()` functions to resolve system logging errors (negative resolution times).
  * Creates a denormalized view (`fact_tickets_sla_denorm`) specifically optimized for Power BI's direct query performance, preventing redundant relationships in the BI semantic model.

## Power BI Dashboard & Analytics
The dashboard serves as an operational control panel for Support Dispatchers and Managers, featuring:

**Core KPIs:**
1. **Total Tickets:** Overall workload volume.
2. **SLA Compliance Rate:** Percentage of tickets resolved within the AI-assigned SLA targets.
3. **Avg Resolution Time (Hrs):** Average turnaround time for support requests.
4. **AI Automation Rate:** The percentage of tickets successfully routed without human intervention.
   * *Technical Highlight:* Utilized the `KEEPFILTERS` DAX function (`KEEPFILTERS('table'[routing_status] = "Auto-Routed by AI")`) to ensure the AI Automation Rate calculates accurately through filter context intersections when dynamic Slicers are applied.

**Visual Insights:**
* **Customer Sentiment Distribution:** A Donut Chart highlighting the emotional state of inbound requests (filtered to remove blanks).
* **Workload Distribution by Team:** A Clustered Bar Chart visualizing the ticket volume across departments (IT, Finance, Logistics) for load balancing.
* **Hourly SLA Breach Trend:** A Line Chart tracking SLA violation rates across a 24-hour operational window to identify critical bottleneck hours.

## How to Run & Reproduce
1. **Databricks Setup:** Clone this repository into your Databricks Workspace using Databricks Repos.
2. **Data Upload:** Upload the `customer_support_tickets.csv` to your Databricks File System (DBFS) or Unity Catalog Volume.
3. **Run Pipeline:** Execute the notebooks sequentially (`1_bronze` -> `2_silver` -> `3_gold`) on a compatible Databricks compute cluster.
4. **Connect Power BI:** 
   * Open Power BI Desktop and select **Get Data -> Databricks**.
   * Input your Databricks SQL Warehouse Server Hostname and HTTP Path.
   * Authenticate using a Personal Access Token (PAT).
   * Load the `fact_tickets_sla_denorm` view and construct the visual layout.
