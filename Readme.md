# SQL Data Cleaning – Global Layoffs Dataset

## Project Overview
This project focuses on cleaning and preparing a real-world layoffs dataset using SQL in MySQL.  
The goal was to transform raw data containing duplicates, inconsistent values, formatting issues, and missing information into a structured dataset ready for analysis.

To follow best practices, a staging table was created and all cleaning operations were performed on this table rather than modifying the original dataset.

---

## Dataset
The dataset contains information about company layoffs across industries and countries between 2020 and 2023.

Key fields include:

- Company  
- Location  
- Industry  
- Total Laid Off  
- Percentage Laid Off  
- Date  
- Country  
- Stage  
- Funds Raised  

---

## Data Cleaning Steps

### 1. Creating a Staging Table
A staging table was created to preserve the raw dataset.  
All cleaning processes were performed on this table so the original dataset remained unchanged.

---

### 2. Removing Duplicate Records
Duplicate records were identified using the `ROW_NUMBER()` window function with `PARTITION BY`.

```sql
ROW_NUMBER() OVER(PARTITION BY company, location, industry, total_laid_off) AS row_num
```

Rows with a row number greater than 1 were removed to eliminate duplicate entries.

---

### 3. Standardizing Data

#### Removing Whitespace
Leading and trailing whitespace in the `company` column was removed using:

```sql
TRIM(company)
```

#### Fixing Industry Inconsistencies
Some records contained inconsistent industry values such as:

- Crypto  
- Cryptocurrency  

These were standardized to maintain consistent categories.

#### Cleaning Country Values
Some country values contained trailing punctuation such as:

- United States.

These were cleaned using:

```sql
TRIM(TRAILING '.' FROM country)
```

---

### 4. Standardizing Date Format
The `date` column was originally stored as text.  
The `STR_TO_DATE()` function was used to convert the values into proper date format.

```sql
STR_TO_DATE(date, '%m/%d/%Y')
```

After conversion, the column type was modified using:

```sql
ALTER TABLE layoffs_staging
MODIFY COLUMN date DATE;
```

---

### 5. Handling Null and Blank Values
Some records contained missing values.  
A self join was used to populate matching rows and fill missing values where possible.

---

### 6. Removing Irrelevant Records
Rows where both `total_laid_off` and `percentage_laid_off` were null were removed since they contained no meaningful information for analysis.

---

### 7. Removing Helper Columns
During duplicate detection, a helper column (`row_num`) was created using the `ROW_NUMBER()` function.

After duplicates were removed, the column was dropped using:

```sql
ALTER TABLE layoffs_staging
DROP COLUMN row_num;
```

---

## Skills Demonstrated

- SQL data cleaning techniques  
- Window functions (`ROW_NUMBER`)  
- Data standardization using `TRIM`  
- Date conversion using `STR_TO_DATE`  
- Table modification with `ALTER TABLE`  
- Handling missing values using self joins  
- Removing duplicate records using partitioning  

---

## Tools Used

- MySQL  
- SQL  

---

## Outcome
The final dataset is a clean and structured version of the layoffs dataset, ready for exploratory data analysis and further data visualization.