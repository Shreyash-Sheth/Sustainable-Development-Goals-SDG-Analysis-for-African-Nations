# Sustainable Development Goals (SDG) Analysis for African Nations

### Dashboard Link : 

## Problem Statement
Ubuntu (fictitious), an NGO operating in Africa, needed to assess the progress of the 10 most populous African countries (Congo, Algeria, Sudan, Egypt, Ethiopia, Nigeria, Kenya, South Africa, Tanzania, Uganda) in achieving key Sustainable Development Goals (SDGs) related to:

- SDG 2: Zero Hunger (Cereal Yield)
- SDG 3: Good Health and Well-being (Maternal Mortality, Under-5 Mortality, Neonatal Mortality, HIV Infections, Tuberculosis Incidence, Chronic Disease Mortality)
- SDG 11: Sustainable Cities and Communities (Access to Improved Water)
- SDG 17: Partnerships for the Goals (Government Spending on Health and Education)

This comprehensive analysis will serve as a foundation for Ubuntu to devise a strategic plan to address any deficiencies and shortcomings in nations that are falling behind in their pursuit of SDG targets.

## Challenges:

- Data scattered across multiple sources (WHO, World Bank, UNICEF, UNAIDS)
- Missing values, inconsistent formats
- Need for automated ETL pipeline for future updates
- Interactive dashboards for policy recommendations

## Methodology

### Data Extraction
- Collected data from World Bank, WHO, UNICEF, UNAIDS, and UN agencies in CSV, Excel, and API formats.
- Used Python (Pandas & Requests) to fetch and merge datasets.

       import pandas as pd
       import requests
       # Example: Fetching World Bank Data via API
       def fetch_world_bank_data(indicator, countries):
       url = f"http://api.worldbank.org/v2/country/{countries}/indicator/{indicator}?format=json" 
       response = requests.get(url).json()
       data = pd.DataFrame(response[1])
       return data[['country', 'date', 'value']]

       # Example: Loading CSV/Excel files
       def load_local_data(file_path):
       if file_path.endswith('.csv'):
       return pd.read_csv(file_path)
       elif file_path.endswith('.xlsx'):
       return pd.read_excel(file_path)
       
       # Fetching Cereal Yield Data (SDG 2)
       cereal_yield_data = fetch_world_bank_data("AG.YLD.CREL.KG", "DZA;COD;EGY;ETH;KEN;NGA;ZAF;SDN;TZA;UGA")


### Data Transformation
- Handled missing values (dropped years with incomplete data).
- Standardized metrics (e.g., converting maternal mortality rate).
- Applied SQL queries for filtering and aggregation.

Python code snippet for data cleaning:

    def clean_data(df):
    # Drop rows with missing values
    df = df.dropna()
    
    # Standardize maternal mortality rate (per 100k → per 1k)
    if 'maternal_mortality' in df.columns:
        df['maternal_mortality'] = df['maternal_mortality'] / 100
    
    # Convert data types
    df['year'] = pd.to_datetime(df['year'], format='%Y')
    
    return df

SQL Queries (snippte) for Aggregation:

    -- Example: Calculating average mortality rates per country
    SELECT 
       country,
       AVG(maternal_mortality) AS avg_maternal_mortality,
       AVG(under5_mortality) AS avg_under5_mortality,
       AVG(neonatal_mortality) AS avg_neonatal_mortality
    FROM health_indicators
    WHERE year BETWEEN 2000 AND 2019
    GROUP BY country
    ORDER BY avg_maternal_mortality ASC;


### Data Loading
- Stored cleaned data in a SQLite database for efficient querying.
- The final datasets were exported to CSV for Tableau.
   
       import sqlite3
       def save_to_sqlite(df, db_name, table_name):
              conn = sqlite3.connect(db_name)
              df.to_sql(table_name, conn, if_exists='replace', index=False)
              conn.close()
       # Example usage
       save_to_sqlite(cleaned_data, "africa_sdg.db", "health_indicators")


### Data Analysis
- Calculated measures of central tendency (mean, median) and dispersion (variance, standard deviation) for each indicator
- Compare country performance against UN SDG targets
- Identified trends, outliers, and patterns in the data
- Conducted correlation analysis between related indicators (e.g., HIV infections and tuberculosis incidence)

## Visualisation Development in Tableau
Created an interactive dashboard with multiple visualization components:
- Treemap: Shows top and bottom 3 performing countries for selected indicators.
- Line charts: Display trends over time for selected indicators and countries.
- Box-and-whisker plots: Visualize data distribution and outliers across countries
- Map: Ranks countries by average performance using color gradients.
- Scatter plot: Shows correlation between HIV infections and tuberculosis incidence.
- Stacked bar chart: Compares different mortality rates by country for a selected year.

<img width="762" alt="image" src="https://github.com/user-attachments/assets/5fd957f0-4237-4eb7-93c5-cf9b37e897e4" />


Dashboard Features:
- Dynamic filters (SDG indicator, country, year range)
- Tooltips with detailed metrics
- Mobile-responsive design

## Insights and Recommendations
- Identified countries with negative or stagnating trends for each indicator
- Highlighted best performing countries that could serve as models
- Provided specific recommendations for Ubuntu to address shortcomings in underperforming countries
- Suggested policy interventions based on successful approaches from top performers

### This project demonstrates:
- End to-end ETL pipeline (Python + SQL)
- Automated data cleaning & analysis
- Interactive Tableau dashboards for actionable insights
