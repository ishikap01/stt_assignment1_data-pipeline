# Box Office Bomb Data Pipeline

An end-to-end data engineering pipeline that extracts, cleans, validates, and enriches data on major box-office failures.

---

## Overview

This project was developed as part of a course assignment at IIT Gandhinagar. The objective is to analyze financial failures in the film industry by building a robust data pipeline that processes messy real-world data.

The pipeline extracts data from Wikipedia, enforces a strict schema using Pydantic, enriches it with external API data, and generates a clean dataset for analysis.

---

## Features

- Web scraping of Wikipedia data
- Data cleaning and preprocessing
- Schema validation using Pydantic
- API integration with OMDb
- Data consistency checks
- Categorization of financial losses
- Export to CSV for analysis

---

## Tech Stack

- Python  
- BeautifulSoup (Web Scraping)  
- Pydantic (Data Validation)  
- Pandas (Data Processing)  
- Requests (API Calls)  

---

## Pipeline Workflow

### 1. Data Scraping
- Extracts data from:  
  https://en.wikipedia.org/wiki/List_of_biggest_box-office_bombs  
- Parses raw fields:
  - Title  
  - Year  
  - Budget  
  - Estimated Loss  

---

### 2. Data Cleaning & Validation
- Removes:
  - Footnotes (e.g., `[1]`, `[nb 2]`)
  - Special symbols (`§`, `†`)
- Converts:
  - Budget and loss ranges → average values  
- Ensures:
  - Correct data types using Pydantic  

---

### 3. Data Enrichment (OMDb API)
For each movie:
- Fetches:
  - Plot  
  - IMDb Rating  
  - Metascore  
  - Director  
  - Language  
- Handles missing data gracefully (`N/A → None`)

---

### 4. Data Consistency Check
- Compares Wikipedia year with OMDb year
- Assigns:
  - `Verified` (±1 year match)  
  - `Mismatch`  
  - `Not Found`  

---

### 5. Final Dataset & Categorization
- Creates a structured DataFrame
- Categorizes losses:
  - **Catastrophic**: ≥ $100M  
  - **Severe**: $50M – $100M  
  - **Moderate**: < $50M  

---

