# Precious Metals Intelligence Dashboard

A fully automated, live data pipeline that collects daily precious metals prices from a REST API, processes and stores them in AWS, and powers a custom-built interactive web dashboard. No manual intervention required. New data arrives every morning and the dashboard reflects it automatically.

**Live Dashboard:** [fehintiti.github.io/gold-pipeline-dashboard](https://fehintiti.github.io/gold-pipeline-dashboard/)

---

## What This Project Does

Every day at 08:00 UTC, an AWS Lambda function calls the Metals.Dev API, collects the latest spot prices for gold, silver, platinum, and palladium (plus exchange rates for 8 currencies), and saves the raw JSON response to an S3 data lake. Five minutes later, a second Lambda function reads all collected data, flattens it into a clean CSV, and writes it to a public S3 location. The web dashboard fetches that CSV on page load, calculates all analytics in real time using JavaScript, and renders the results. No database, no server, no manual refresh.

The dashboard includes dark and light themes, an interactive price chart with hover tooltips, a correlation matrix, multi-currency return analysis, and auto-generated market commentary that updates as the data grows.

---

## Architecture

```
Metals.Dev API
      |
      v
AWS Lambda (Collector)          Runs daily at 08:00 UTC via EventBridge
      |
      v
Amazon S3 (Raw/Daily/)          One JSON file per day, untouched
      |
      v
AWS Lambda (Transformer)        Runs daily at 08:05 UTC via EventBridge
      |
      v
Amazon S3 (Processed/)          Single clean CSV, rebuilt daily
      |
      v
GitHub Pages (index.html)       Fetches CSV on load, calculates everything client-side
```

---

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Data Source | Metals.Dev REST API | Live and historical precious metals spot prices |
| Collection | AWS Lambda (Python 3.12) | Calls API daily, saves raw JSON to S3 |
| Storage | Amazon S3 | Data lake with raw and processed layers |
| Scheduling | Amazon EventBridge | Cron triggers at 08:00 and 08:05 UTC |
| Transformation | AWS Lambda (Python 3.12) | Flattens JSON into structured CSV |
| Query Layer | Amazon Athena | SQL queries on S3 data for validation |
| Dashboard | HTML / CSS / JavaScript | Custom-built, no frameworks, hosted on GitHub Pages |
| Hosting | GitHub Pages | Free, instant load, publicly accessible |

---

## Data Collected

Each daily record contains:

**Metals (USD per troy ounce):**
Gold, Silver, Platinum, Palladium

**Currency Exchange Rates (vs USD):**
GBP, EUR, JPY, CAD, AUD, CNY, INR, CHF

Data collection began on January 1, 2026 with a historical backfill, and the pipeline has been running daily since March 22, 2026.

---

## Dashboard Features

**KPI Cards:** Current price, YTD return, YTD high/low, peak date, and average price for all four metals.

**Interactive Price Chart:** Gold and silver (scaled) price history drawn on HTML Canvas. Hover anywhere on the chart to see the exact date, gold price, and silver price via a floating tooltip with crosshair tracking.

**Correlation Matrix:** Pairwise Pearson correlation coefficients between all four metals, calculated dynamically from the full dataset. Colour intensity maps to correlation strength.

**Market Commentary:** Three auto-generated insight blocks that rewrite themselves as the data changes:
- Price Action: identifies the peak month, peak price, decline from peak, and largest single-day drop
- Market Structure: identifies the strongest and weakest correlated metal pairs
- Currency Lens: compares gold's YTD return in USD versus the best-performing currency

**Gold Returns by Currency:** Horizontal bar chart showing how the same commodity performed differently depending on the investor's home currency. Highlights how dollar strength or weakness masks or amplifies real returns.

**Key Statistics:** Most volatile month (auto-detected), price range for that month, average daily percentage move, and biggest single-day drop.

**Dark / Light Toggle:** Full theme switch with smooth 0.5s CSS transitions on every element. Gold-accented toggle knob.

**Fully Dynamic:** Every number, chart, insight, and label recalculates from the CSV on each page load. Nothing is hardcoded. As the dataset grows from weeks to months to a full year, the dashboard automatically reflects the expanded time range.

---

## S3 Bucket Structure

```
gold-price-pipeline-fehintiti/
    Raw/
        Daily/          Lambda collector output (one JSON per day)
        Backfill/       Historical data (Jan 1 - Mar 20, 2026)
    Processed/
        gold_prices_clean.csv   Transformer output (public read)
    athena-results/     Athena query output
```

---

## Key Analytical Findings (Q1 2026)

- Gold opened the year at $4,323 and peaked at $5,419 on January 28, a 25% surge in under a month
- The largest single-day crash was -8.84% on January 30, the biggest daily move of the year
- Platinum and Palladium are 0.92 correlated, moving almost in lockstep as industrial metals
- Gold is weakly correlated with the other metals (0.24 with Platinum), driven by investment demand rather than industrial use
- Gold returned +3.9% in USD but +6.0% in EUR year to date, with dollar strength absorbing nearly half the real rally
- January was the most volatile month with a $1,096 price range

---


## Built By

**Fehintiti (Tomisin Okunlola)**

This is Project 3 in an ongoing 12-month data portfolio series. Each project tackles a different real-world data problem using different tools and techniques.

- Project 1: Rock Classification (Deep Learning, PyTorch, ConvNeXt-Tiny)
- Project 2: Southern Water Consumption Analysis (SQL, Power BI)
- Project 3: Precious Metals Intelligence Pipeline (AWS, JavaScript) - this project

---

## License

This project is open source and available for learning purposes. The dashboard design and code are original work.
