# Competitor Price Watchdog - Documentation

## 1. Overview

Competitor Price Watchdog is an assignment-level AI automation system designed to automate competitor price monitoring, comparison, storage, AI interpretation, and notification.

The implementation separates operational monitoring from scheduled reporting through two independent n8n Cloud workflows:

1. **Monitor & Alert** - daily price checks and change-triggered alerts.
2. **Daily Summary** - scheduled management-style summary.

The system uses n8n as the orchestration layer, Apify for e-commerce product data collection, Google Sheets as the lightweight state store, Gemini for natural-language interpretation and summarization, and Telegram for notifications.

## 2. System Flow

```text
                    COMPETITOR PRICE WATCHDOG

     Google Sheets
     Product URLs +
        Baseline
             |
             v
        Apify Scraper
             |
             v
      n8n + JavaScript
      Compare + Update
             |
        +----+----+
        |         |
        v         v
   Price Changed?  Persist State
        |
       YES
        |
        v
      Gemini
  AI Interpretation
        |
        v
     Telegram
   Instant Alert


     Separate Daily Reporting Flow
             |
             v
      Google Sheets
             |
             v
      Summary Logic
             |
             v
          Gemini
             |
             v
        Telegram
```

## 3. Workflow 1 - Monitor & Alert

### Purpose

The first workflow performs the core watchdog process: collect current prices, compare them with the stored value, persist the new state, and send an alert only when a genuine price change is detected.

### Flow

```text
Daily 08:00 Price Check
        ->
Read Products
        ->
Apify - Scrape Product
        ->
Compare Price
        ->-----------------> Update Google Sheet
        |
        v
Price Changed?
        |
       YES
        v
Gemini - Analyze Change
        ->
Build Telegram Alert
        ->
Telegram Alert
```

### Node responsibilities

**Daily 08:00 Price Check**  
Starts the monitoring workflow at 08:00 Asia/Dhaka.

**Read Products**  
Loads tracked product rows from the Google Sheets `Products` sheet.

**Apify - Scrape Product**  
Receives each product URL and retrieves structured product information, including price and currency.

**Compare Price**  
Extracts a numeric current price, reads the stored price, calculates the absolute and percentage difference, and determines whether the price changed.

**Update Google Sheet**  
Writes the latest state back to the matching `Product_ID` row. `Product_ID` is used as the matching key to update existing records rather than create duplicates.

**Price Changed?**  
Routes only changed items to the AI alert branch.

**Gemini - Analyze Change**  
Produces a concise, business-friendly interpretation using the verified numeric price movement.

**Build Telegram Alert**  
Formats the product, competitor, previous/current price, change, direction, AI insight, and product URL into a Telegram-ready message.

**Telegram Alert**  
Delivers the instant alert.

## 4. Deterministic Price Comparison

The numeric comparison is handled by workflow logic rather than by the LLM.

```text
new_price = scraped price
old_price = stored Current_Price

price_change = new_price - old_price

percentage_change =
((new_price - old_price) / old_price) * 100
```

The change flag is based on the numeric values. Gemini is used after the change is already verified.

This separation keeps the price calculation reproducible and auditable.

## 5. Workflow 2 - Daily Summary

### Purpose

The second workflow is independent from instant alerts and provides a scheduled daily digest.

### Flow

```text
Daily 10:00 Summary
        ->
Read Products For Summary
        ->
Build Summary Prompt
        ->
Gemini - Daily Summary
        ->
Build Daily Telegram Message
        ->
Telegram Daily Summary
```

### Node responsibilities

**Daily 10:00 Summary**  
Starts the reporting workflow at 10:00 Asia/Dhaka.

**Read Products For Summary**  
Retrieves the latest tracked product records from Google Sheets.

**Build Summary Prompt**  
Counts tracked products and products with price changes for the current date, then builds the input prompt for Gemini.

**Gemini - Daily Summary**  
Generates a concise plain-text competitor price-watch summary.

**Build Daily Telegram Message**  
Formats the generated summary and provides a fallback message when no model text is returned.

**Telegram Daily Summary**  
Sends the daily digest.

## 6. AI Component

Gemini is intentionally used as an interpretation and summarization layer.

It is not responsible for:

- Scraping product pages
- Guessing prices
- Performing the core numeric comparison

Instead, Gemini receives verified structured values and turns them into readable business-oriented language.

This design keeps the critical numeric logic deterministic while using AI where language generation adds value.

## 7. Google Sheets Data Model

The Google Sheets state store uses `Product_ID` as the unique row key.

| Field | Purpose |
|---|---|
| `Product_ID` | Unique row identifier |
| `Product_Name` | Human-readable product name |
| `Competitor` | Competitor site or brand |
| `Product_URL` | Product page to monitor |
| `Current_Price` | Latest confirmed price |
| `Previous_Price` | Price from the prior comparison cycle |
| `Price_Change` | Current minus previous price |
| `Last_Checked` | Timestamp of the latest monitoring run |

## 8. Testing & Validation

The supplied implementation report documents successful validation of the main dependencies and paths.

Documented checks include:

- Google Sheets returned 2 product rows.
- Apify returned Walmart and Best Buy prices.
- First-run baseline handling avoided a false alert when no previous price existed.
- A controlled price-change test set `price_changed = true`.
- The changed item reached Gemini and returned text.
- Telegram returned a successful alert delivery.
- The daily summary workflow produced a summary from the tracked rows.
- 08:00 monitoring and 10:00 summary schedules were configured for Asia/Dhaka.

A controlled end-to-end scenario temporarily changed a stored price from USD 60 to USD 69.95. The system calculated a +USD 9.95 change (+16.58%), routed the item through the alert path, invoked Gemini, and delivered the Telegram alert.

## 9. Key Design Decisions

- **Two workflows instead of one large workflow** - separates instant event handling from scheduled reporting.
- **Google Sheets as the lightweight state store** - suitable for the assignment-level MVP.
- **Product-specific URLs** - keeps scraping targets unambiguous.
- **Product_ID matching** - updates existing rows in place.
- **AI after deterministic detection** - avoids using an LLM as the numeric source of truth.
- **Controlled testing** - proves the alert branch without waiting for a natural market movement.

## 10. Repository Structure

```text
competitor-price-watchdog/
├── README.md
├── documentation.md
└── workflows/
    ├── monitor-and-alert.json
    └── daily-summary.json
```

## 11. Setup

1. Import the required workflow JSON into n8n.
2. Connect your own Google Sheets credential.
3. Configure your own Google Sheets document and `Products` sheet.
4. Connect your own Apify account.
5. Configure your own Gemini API authentication.
6. Connect your own Telegram credential and chat ID.
7. Replace public-repository placeholders such as `YOUR_GOOGLE_SHEET_URL` and `YOUR_TELEGRAM_CHAT_ID`.
8. Test each workflow before activating scheduled execution.

## 12. Security

The workflow JSON files in this repository are sanitized portfolio copies.

Do not commit:

- API keys
- OAuth credentials
- Telegram bot tokens
- Private spreadsheet URLs or identifiers
- Private webhook secrets
- Passwords
- n8n instance-specific credentials or metadata

## 13. Limitations

The implementation report identifies several practical limitations:

- Scraping depends on third-party website layouts and anti-bot behavior.
- Price can vary with coupons, membership pricing, shipping, or product variants.
- Cross-currency comparison requires a currency-conversion layer.
- The MVP stores the latest and previous values rather than a full historical price table.
- AI output is an explanation layer and should not replace the deterministic numeric price.
- External API quotas and credentials require secure management.

## 14. Future Improvements

Potential extensions documented in the implementation report include:

- A dedicated price-history table or database.
- Configurable change thresholds such as alerting only above a percentage threshold.
- Stock and variant tracking.
- Currency normalization and shipping-inclusive comparisons.
- Retry and error handling for scraper/API failures.
- Dashboarding after a historical data layer is introduced.
- Migration from Google Sheets to a dedicated database for larger SKU volumes.
- Competitor-level trend and comparison summaries.

## 15. Implementation Status

**Working MVP validated end-to-end.**

The supplied report documents successful operation with Google Sheets, Apify, Gemini, and Telegram, with both workflows configured for scheduled execution.

