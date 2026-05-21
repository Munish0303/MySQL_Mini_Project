# 🌍 World Layoffs — SQL Data Analysis Project

A end-to-end SQL project covering data cleaning and exploratory analysis on a real-world dataset of global tech layoffs (2361 records).

---

## 📁 Project Structure

```
├── layoffs.csv                    # Raw dataset
├── DATA_CLEANING_PROJECT.sql      # Data cleaning pipeline
└── DATA_EXPLORATORY_ANALYSIS.sql  # Exploratory analysis queries
```

---

## 🗄️ Dataset

| Column | Description |
|---|---|
| `company` | Company name |
| `location` | City |
| `industry` | Sector (e.g. Crypto, Retail, Finance) |
| `total_laid_off` | Number of employees laid off |
| `percentage_laid_off` | Fraction of workforce laid off |
| `date` | Layoff date |
| `stage` | Funding stage (Seed, Series A–E, Post-IPO, etc.) |
| `country` | Country |
| `funds_raised_millions` | Total funding raised (USD millions) |

---

## 🧹 Data Cleaning (`DATA_CLEANING_PROJECT.sql`)

1. **Staging tables** — raw data copied into `layoffs_staging` → `layoffs_staging2` to preserve the original
2. **Duplicate removal** — `ROW_NUMBER()` with full-row `PARTITION BY` to identify and delete exact duplicates
3. **Standardization**
   - Trimmed whitespace from company names
   - Normalized `Crypto%` variants → `'Crypto'`
   - Stripped trailing periods from country names (`United States.` → `United States`)
   - Converted date strings to `DATE` type via `STR_TO_DATE()`
4. **Null handling** — backfilled missing `industry` values using self-join on `company + location`; dropped rows where both `total_laid_off` and `percentage_laid_off` are NULL
5. **Schema cleanup** — dropped the `row_num` helper column after deduplication

---

## 🔍 Exploratory Analysis (`DATA_EXPLORATORY_ANALYSIS.sql`)

- **Max layoffs & complete shutdowns** — companies where `percentage_laid_off = 1`, sorted by size and funding
- **By company** — total layoffs aggregated per company (ranked)
- **By industry** — which sectors were hit hardest
- **By country** — geographic distribution of layoffs
- **By year** — annual totals
- **By funding stage** — which stages saw the most cuts
- **Monthly trend** — month-by-month totals using `SUBSTRING(date, 1, 7)`
- **Rolling total** — cumulative layoffs over time using `SUM() OVER (ORDER BY month)`
- **Top 5 companies per year** — `DENSE_RANK()` inside a CTE to rank companies within each year

---

## 🛠️ Tools

- **MySQL** (InnoDB, utf8mb4)
- Window functions: `ROW_NUMBER()`, `DENSE_RANK()`, `SUM() OVER()`
- CTEs for multi-step logic

---

## 🚀 How to Run

```sql
-- 1. Create and select the database
CREATE DATABASE world_layoffs;
USE world_layoffs;

-- 2. Import layoffs.csv into the `layoffs` table

-- 3. Run DATA_CLEANING_PROJECT.sql

-- 4. Run DATA_EXPLORATORY_ANALYSIS.sql
```

> The cleaning script creates `layoffs_staging2` as the clean working table. All analysis queries run against it.
