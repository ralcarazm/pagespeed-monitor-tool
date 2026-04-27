# pagespeed-monitor-tool

![pagespeed-monitor-tool workflow](https://github.com/ralcarazm/pagespeed-monitor-tool/blob/main/pagespeed-monitor-tool.png)

**pagespeed-monitor-tool** is a Python command-line tool for auditing one or more URLs with the Google PageSpeed Insights API. It retrieves Lighthouse lab data, PageSpeed category scores, and, when available, field data from the Chrome User Experience Report (CrUX).

The tool is designed for batch analysis, monitoring tasks, and research workflows. It can export results to CSV and XLSX, write execution logs, handle temporary API or network errors with retries, and preserve partial results while the process is running.

## Author

**Rubén Alcaraz Martínez**

## Licence

This project is licensed under the **GNU General Public License v3.0**.

---

## Repository structure

```text
pagespeed-monitor-tool/
├── pagespeed_audit.py
├── config.toml
├── requirements.txt
├── README.md
├── LEEME.md
├── INICIO_RAPIDO.md
├── LICENSE
├── results/
└── logs/
```

The folders `results/` and `logs/` can be created manually or automatically by the script when needed.

---

## Main features

- Analyse one or more URLs using the Google PageSpeed Insights API.
- Run audits for `mobile`, `desktop`, or both strategies.
- Retrieve Lighthouse category scores:
  - Performance
  - Accessibility
  - Best Practices
  - SEO
- Retrieve Lighthouse lab metrics:
  - First Contentful Paint
  - Largest Contentful Paint
  - Speed Index
  - Total Blocking Time
  - Cumulative Layout Shift
  - Time to Interactive
- Retrieve CrUX field metrics when available:
  - Largest Contentful Paint
  - Cumulative Layout Shift
  - Interaction to Next Paint
  - First Contentful Paint
  - Time to First Byte
- Export results to:
  - CSV
  - XLSX
- Generate Excel-friendly CSV files using:
  - UTF-8 with BOM
  - semicolon separator
  - comma decimal separator
- Save CSV rows progressively after each URL/strategy analysis.
- Generate XLSX output at the end of the process.
- Add controlled status values for missing data and errors.
- Generate execution logs in a `logs/` folder.
- Retry temporary network or API errors.

---

## Requirements

Recommended:

- Python 3.10 or later
- A Google PageSpeed Insights API key
- Internet connection
- Python packages listed in `requirements.txt`

The project uses:

```txt
requests>=2.31.0
openpyxl>=3.1.2
tomli>=2.0.1; python_version < "3.11"
```

`tomli` is only needed in Python versions older than 3.11. Python 3.11 and later include `tomllib` in the standard library.

---

## Getting a PageSpeed Insights API key

You need an API key from Google Cloud if you plan to perform repeated or automated requests.

General process:

1. Open Google Cloud Console.
2. Create or select a project.
3. Enable the PageSpeed Insights API.
4. Create an API key.
5. Restrict the key if appropriate.

Keep your API key private and only place it in your local `config.toml` file.

---

## Installation

### 1. Download the project

Download or copy the project files to your computer and open a terminal inside the project folder.

The project folder should contain at least:

```text
pagespeed_audit.py
config.toml
requirements.txt
```

---

## Setting up the virtual environment

### Linux

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

If `python3` is not available, check your Python installation:

```bash
python --version
python3 --version
```

### Windows PowerShell

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

If PowerShell blocks script execution, run:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Then activate the environment again:

```powershell
.\.venv\Scripts\Activate.ps1
```

### Windows Command Prompt

```cmd
py -m venv .venv
.venv\Scripts\activate.bat
python -m pip install --upgrade pip
pip install -r requirements.txt
```

---

## `requirements.txt`

Create a file called `requirements.txt` with this content:

```txt
requests>=2.31.0
openpyxl>=3.1.2
tomli>=2.0.1; python_version < "3.11"
```

Install it after activating the virtual environment:

```bash
pip install -r requirements.txt
```

---

## Configuration file

The tool is configured with a TOML file. By default, the script looks for:

```text
config.toml
```

The file must be in the same folder as `pagespeed_audit.py`, unless you explicitly pass another path with the `--config` option.

---

## Example `config.toml`

```toml
# ------------------------------------------------------------
# GOOGLE PAGESPEED INSIGHTS API
# ------------------------------------------------------------

# PageSpeed Insights API key.
api_key = "YOUR_API_KEY_HERE"


# ------------------------------------------------------------
# OUTPUT FILES
# ------------------------------------------------------------

# CSV output file.
output = "results/pagespeed_results.csv"

# Excel output file.
xlsx_output = "results/pagespeed_results.xlsx"

# Generate CSV output.
write_csv = true

# Generate XLSX output.
write_xlsx = true


# ------------------------------------------------------------
# CSV CONFIGURATION
# ------------------------------------------------------------

# CSV encoding.
# "utf-8-sig" is recommended for opening the file directly in Excel.
csv_encoding = "utf-8-sig"

# CSV column separator.
# Semicolon is recommended for Spanish/Catalan Excel configurations.
csv_delimiter = ";"

# Convert decimal points to decimal commas in the CSV.
csv_decimal_comma = true


# ------------------------------------------------------------
# DISPLAY VALUES AND CONTROL VALUES
# ------------------------------------------------------------

# Export Lighthouse display values such as "5.1 s" or "120 ms".
# For quantitative analysis, false is recommended.
include_display_values = false

# Fill textual control fields with values such as "sin_error",
# "not_available", or "not_evaluated".
fill_control_values = true

# Remove columns that are completely empty in the final output.
drop_empty_columns = false


# ------------------------------------------------------------
# LOGGING
# ------------------------------------------------------------

# Enable log file generation.
log_enabled = true

# Folder where log files will be stored.
log_dir = "logs"

# Log detail level.
# Common values: DEBUG, INFO, WARNING, ERROR.
log_level = "INFO"


# ------------------------------------------------------------
# LIGHTHOUSE STRATEGIES AND CATEGORIES
# ------------------------------------------------------------

# Analysis strategies.
# Allowed values: "mobile", "desktop".
strategies = ["mobile", "desktop"]

# Lighthouse categories.
# Allowed values:
# "performance", "accessibility", "best-practices", "seo".
categories = ["performance", "accessibility", "best-practices", "seo"]


# ------------------------------------------------------------
# LANGUAGE AND REQUEST PACING
# ------------------------------------------------------------

# Result language.
locale = "es"

# Pause between API requests, in seconds.
sleep = 1.0

# Request timeout, in seconds.
timeout = 120


# ------------------------------------------------------------
# RETRIES
# ------------------------------------------------------------

# Number of retries after temporary errors.
# Total maximum attempts = 1 initial attempt + retries.
retries = 3

# Exponential backoff factor between retries.
retry_backoff = 2.0

# HTTP status codes that trigger retries.
retry_status_codes = [408, 429, 500, 502, 503, 504]


# ------------------------------------------------------------
# URLS TO ANALYSE
# ------------------------------------------------------------

# List of URLs to analyse.
urls = [
  "https://www.example1.com",
  "https://www.example2.com",
  "https://www.example3.com"
]
```

---

## Configuration options

| Option | Type | Example | Description |
|---|---:|---|---|
| `api_key` | string | `"YOUR_API_KEY_HERE"` | Google PageSpeed Insights API key. |
| `output` | string | `"results/pagespeed_results.csv"` | Path of the CSV output file. |
| `xlsx_output` | string | `"results/pagespeed_results.xlsx"` | Path of the Excel XLSX output file. |
| `write_csv` | boolean | `true` | Enables or disables CSV generation. |
| `write_xlsx` | boolean | `true` | Enables or disables XLSX generation. |
| `csv_encoding` | string | `"utf-8-sig"` | Encoding used for the CSV file. `utf-8-sig` is recommended for Excel compatibility. |
| `csv_delimiter` | string | `";"` | CSV delimiter. Semicolon is recommended for Spanish/Catalan regional settings. |
| `csv_decimal_comma` | boolean | `true` | Converts decimal points to decimal commas in CSV numeric values. |
| `include_display_values` | boolean | `false` | Adds human-readable Lighthouse values with units, such as `"5.1 s"` or `"120 ms"`. For analysis, keep this disabled. |
| `fill_control_values` | boolean | `true` | Adds controlled text values to non-numeric status/message columns when data is missing. |
| `drop_empty_columns` | boolean | `false` | Removes columns that are completely empty from the final CSV/XLSX output. |
| `log_enabled` | boolean | `true` | Enables log file creation. |
| `log_dir` | string | `"logs"` | Folder where log files are stored. |
| `log_level` | string | `"INFO"` | Logging level. Common values: `DEBUG`, `INFO`, `WARNING`, `ERROR`. |
| `strategies` | list | `["mobile", "desktop"]` | Device strategies to analyse. Each strategy creates one API request per URL. |
| `categories` | list | `["performance", "accessibility", "best-practices", "seo"]` | Lighthouse categories requested from PageSpeed Insights. |
| `locale` | string | `"es"` | Language used for API response messages where available. |
| `sleep` | number | `1.0` | Pause between API requests, in seconds. |
| `timeout` | integer | `120` | Maximum time to wait for each API request, in seconds. |
| `retries` | integer | `3` | Number of retries after temporary errors. |
| `retry_backoff` | number | `2.0` | Exponential waiting factor between retries. |
| `retry_status_codes` | list | `[408, 429, 500, 502, 503, 504]` | HTTP status codes considered temporary and retriable. |
| `urls` | list | `["https://example.com/"]` | URLs to analyse. Each URL must start with `http://` or `https://`. |

---

## How to run the tool

### Run with the default configuration file

If the file is called `config.toml` and is in the same folder as the script:

```bash
python pagespeed_audit.py
```

This is equivalent to:

```bash
python pagespeed_audit.py --config config.toml
```

### Run with a specific configuration file

```bash
python pagespeed_audit.py --config config.toml
```

or:

```bash
python pagespeed_audit.py -c config.toml
```

You can use different configuration files for different batches:

```bash
python pagespeed_audit.py --config config_universities.toml
python pagespeed_audit.py --config config_libraries.toml
python pagespeed_audit.py --config config_repositories.toml
```

---

## How many API requests are generated?

The number of normal requests is:

```text
number of URLs × number of strategies
```

Example:

```toml
urls = [
  "https://example.com/",
  "https://example.org/"
]

strategies = ["mobile", "desktop"]
```

This generates:

```text
2 URLs × 2 strategies = 4 API requests
```

Retries can increase the total number of requests. With:

```toml
retries = 3
```

each URL/strategy analysis can be attempted up to four times:

```text
1 initial attempt + 3 retries = 4 attempts
```

---

## Recommended batch size

For stable operation, a practical recommendation is to process URLs in batches.

Suggested ranges:

| Scenario | Recommended number of URLs |
|---|---:|
| Initial test | 10-50 |
| Small controlled analysis | 100-300 |
| Regular batch | 500-1,000 |
| Large project | Several batches of 1,000 |

The CSV is written progressively after each URL/strategy combination. The XLSX file is generated at the end of the execution.

---

## Output files

The tool can generate:

```text
results/pagespeed_results.csv
results/pagespeed_results.xlsx
logs/pagespeed_audit_YYYYMMDD_HHMMSS.log
```

### CSV output

The CSV is designed to be compatible with Excel in Spanish/Catalan regional settings:

- UTF-8 with BOM
- semicolon separator
- comma decimal separator, if `csv_decimal_comma = true`

The CSV is updated progressively. This means that if the process is interrupted, the rows already analysed should remain available.

At the end of the process, the final CSV is regenerated to apply final column handling such as `drop_empty_columns`.

### XLSX output

The XLSX file is generated at the end of the process. It keeps numeric values as real numbers, which is the safest format for analysis in Excel.

The XLSX output includes:

- bold header row;
- frozen first row;
- autofilter;
- basic column width adjustment;
- numeric formats for milliseconds, seconds, percentages, scores, and CLS values.

---

## Logs

When `log_enabled = true`, the script creates a log file in the configured log folder.

Example:

```text
logs/pagespeed_audit_20260427_153022.log
```

The log records:

- start of execution;
- configuration file used;
- CSV initialisation;
- each URL and strategy analysed;
- each API attempt;
- temporary HTTP errors;
- retry attempts;
- network timeouts or connection errors;
- final API errors;
- unavailable CrUX field data;
- rows added to the progressive CSV;
- final CSV and XLSX generation;
- interruptions by the user;
- unexpected exceptions with traceback.

Example log messages:

```text
2026-04-27 15:30:22 | INFO | Inicio de ejecución.
2026-04-27 15:30:22 | INFO | Fichero de configuración: config.toml
2026-04-27 15:30:23 | INFO | [1/10] Analizando URL=https://www.ub.edu/ | strategy=mobile
2026-04-27 15:30:23 | INFO | Petición API intento 1/4 | URL=https://www.ub.edu/ | strategy=mobile
2026-04-27 15:31:10 | INFO | Análisis correcto | URL=https://www.ub.edu/ | strategy=mobile
2026-04-27 15:31:10 | INFO | Fila añadida al CSV progresivo.
```

---

## Data sources and metric types

The tool collects two main types of data.

### 1. Lab data

Lab data comes from the Lighthouse test executed by PageSpeed Insights at analysis time.

These values are useful for controlled testing, but they may vary between executions depending on network conditions, server state, device simulation, and Lighthouse environment.

Examples:

- `lab_fcp_ms`
- `lab_lcp_ms`
- `lab_speed_index_ms`
- `lab_tbt_ms`
- `lab_cls`
- `lab_tti_ms`

### 2. Field data

Field data comes from CrUX, when available. It represents aggregated real-user experience data.

Field data may be missing for some URLs or origins. This usually means that there is not enough real-user data available for Google to report reliable metrics.

Field data appears in two groups:

- `field_url_*`: data for the specific analysed URL;
- `field_origin_*`: data for the whole origin/domain.

---

## Column reference

The final CSV/XLSX may include many columns. Some columns only appear if the corresponding data is available or if specific configuration options are enabled.

### General execution columns

| Column | Definition |
|---|---|
| `checked_at_utc` | Date and time when the script processed the URL/strategy row, in UTC. |
| `status` | Row status. Usually `ok` or `error`. |
| `requested_input_url` | URL originally provided in the configuration file. |
| `strategy` | Analysis strategy used by PageSpeed Insights: `mobile` or `desktop`. |
| `error_type` | Type of error if the row failed. Uses `sin_error` when there is no error. |
| `error_message` | Error description if the row failed. Uses `sin_error` when there is no error. |
| `psi_id` | Identifier returned by PageSpeed Insights for the analysed page. Usually corresponds to the tested URL. |
| `analysis_utc_timestamp` | Timestamp returned by PageSpeed Insights for the analysis. |
| `final_url` | Final URL audited by Lighthouse after redirects. |
| `lighthouse_version` | Lighthouse version used by PageSpeed Insights. |
| `runtime_error_code` | Runtime error code returned by Lighthouse, if any. Uses `sin_error` when there is no runtime error. |
| `runtime_error_message` | Runtime error message returned by Lighthouse, if any. Uses `sin_error` when there is no runtime error. |

---

### Lighthouse category score columns

Scores are exported on a 0-100 scale.

| Column | Definition |
|---|---|
| `performance_score` | Lighthouse Performance score. |
| `accessibility_score` | Lighthouse Accessibility score. |
| `best_practices_score` | Lighthouse Best Practices score. |
| `seo_score` | Lighthouse SEO score. |

If a category was not requested in `categories`, its score may be empty.

---

### Lighthouse lab metric columns

These columns are extracted from the `lighthouseResult.audits` block.

| Column | Definition |
|---|---|
| `lab_fcp_ms` | First Contentful Paint in milliseconds. |
| `lab_fcp_s` | First Contentful Paint in seconds. |
| `lab_fcp_ms_score` | Lighthouse audit score for First Contentful Paint. |
| `lab_lcp_ms` | Largest Contentful Paint in milliseconds. |
| `lab_lcp_s` | Largest Contentful Paint in seconds. |
| `lab_lcp_ms_score` | Lighthouse audit score for Largest Contentful Paint. |
| `lab_speed_index_ms` | Speed Index in milliseconds. |
| `lab_speed_index_s` | Speed Index in seconds. |
| `lab_speed_index_ms_score` | Lighthouse audit score for Speed Index. |
| `lab_tbt_ms` | Total Blocking Time in milliseconds. |
| `lab_tbt_s` | Total Blocking Time in seconds. |
| `lab_tbt_ms_score` | Lighthouse audit score for Total Blocking Time. |
| `lab_cls` | Cumulative Layout Shift measured in the Lighthouse lab test. |
| `lab_cls_score` | Lighthouse audit score for Cumulative Layout Shift. |
| `lab_tti_ms` | Time to Interactive in milliseconds. |
| `lab_tti_s` | Time to Interactive in seconds. |
| `lab_tti_ms_score` | Lighthouse audit score for Time to Interactive. |

---

### Optional Lighthouse display columns

These columns only appear when:

```toml
include_display_values = true
```

They are human-readable values returned by Lighthouse. They may contain units such as `s` or `ms`, so they are not recommended for quantitative analysis.

| Column | Definition |
|---|---|
| `lab_fcp_ms_display` | Human-readable First Contentful Paint value. |
| `lab_lcp_ms_display` | Human-readable Largest Contentful Paint value. |
| `lab_speed_index_ms_display` | Human-readable Speed Index value. |
| `lab_tbt_ms_display` | Human-readable Total Blocking Time value. |
| `lab_cls_display` | Human-readable Cumulative Layout Shift value. |
| `lab_tti_ms_display` | Human-readable Time to Interactive value. |

---

### Field data availability columns

These columns indicate whether CrUX field data was returned.

| Column | Definition |
|---|---|
| `field_url_id` | CrUX identifier for the specific URL, when available. |
| `field_url_overall_category` | Overall CrUX category for the URL. |
| `field_url_data_available` | `true` if URL-level field metrics are available; otherwise `false`. |
| `field_url_data_status` | Status for URL-level field data, usually `available` or `not_available`. |
| `field_url_data_message` | Human-readable explanation of URL-level field data availability. |
| `field_origin_id` | CrUX identifier for the origin/domain, when available. |
| `field_origin_overall_category` | Overall CrUX category for the origin/domain. |
| `field_origin_data_available` | `true` if origin-level field metrics are available; otherwise `false`. |
| `field_origin_data_status` | Status for origin-level field data, usually `available` or `not_available`. |
| `field_origin_data_message` | Human-readable explanation of origin-level field data availability. |

Possible controlled values include:

| Value | Meaning |
|---|---|
| `available` | Data is available. |
| `not_available` | Data was not returned by PageSpeed Insights or CrUX. |
| `not_evaluated` | Data could not be evaluated because the API request failed. |
| `sin_error` | No error occurred. |
| `no_ejecutado` | The analysis did not complete successfully. |

---

## URL-level field metric columns

The prefix `field_url_` refers to real-user CrUX data for the exact analysed URL, when available.

### URL-level LCP

| Column | Definition |
|---|---|
| `field_url_lcp_ms` | URL-level Largest Contentful Paint percentile value in milliseconds. |
| `field_url_lcp_ms_status` | Availability status for URL-level LCP in milliseconds. |
| `field_url_lcp_s` | URL-level Largest Contentful Paint percentile value in seconds. |
| `field_url_lcp_s_status` | Availability status for URL-level LCP in seconds. |
| `field_url_lcp_ms_category` | CrUX quality category for URL-level LCP. |
| `field_url_lcp_ms_good_pct` | Percentage of page loads classified as good for URL-level LCP. |
| `field_url_lcp_ms_needs_improvement_pct` | Percentage of page loads classified as needing improvement for URL-level LCP. |
| `field_url_lcp_ms_poor_pct` | Percentage of page loads classified as poor for URL-level LCP. |

### URL-level CLS

| Column | Definition |
|---|---|
| `field_url_cls` | URL-level Cumulative Layout Shift percentile value. |
| `field_url_cls_status` | Availability status for URL-level CLS. |
| `field_url_cls_category` | CrUX quality category for URL-level CLS. |
| `field_url_cls_good_pct` | Percentage of page loads classified as good for URL-level CLS. |
| `field_url_cls_needs_improvement_pct` | Percentage of page loads classified as needing improvement for URL-level CLS. |
| `field_url_cls_poor_pct` | Percentage of page loads classified as poor for URL-level CLS. |

### URL-level INP

| Column | Definition |
|---|---|
| `field_url_inp_ms` | URL-level Interaction to Next Paint percentile value in milliseconds. |
| `field_url_inp_ms_status` | Availability status for URL-level INP in milliseconds. |
| `field_url_inp_s` | URL-level Interaction to Next Paint percentile value in seconds. |
| `field_url_inp_s_status` | Availability status for URL-level INP in seconds. |
| `field_url_inp_ms_category` | CrUX quality category for URL-level INP. |
| `field_url_inp_ms_good_pct` | Percentage of page loads classified as good for URL-level INP. |
| `field_url_inp_ms_needs_improvement_pct` | Percentage of page loads classified as needing improvement for URL-level INP. |
| `field_url_inp_ms_poor_pct` | Percentage of page loads classified as poor for URL-level INP. |

### URL-level FCP

| Column | Definition |
|---|---|
| `field_url_fcp_ms` | URL-level First Contentful Paint percentile value in milliseconds. |
| `field_url_fcp_ms_status` | Availability status for URL-level FCP in milliseconds. |
| `field_url_fcp_s` | URL-level First Contentful Paint percentile value in seconds. |
| `field_url_fcp_s_status` | Availability status for URL-level FCP in seconds. |
| `field_url_fcp_ms_category` | CrUX quality category for URL-level FCP. |
| `field_url_fcp_ms_good_pct` | Percentage of page loads classified as good for URL-level FCP. |
| `field_url_fcp_ms_needs_improvement_pct` | Percentage of page loads classified as needing improvement for URL-level FCP. |
| `field_url_fcp_ms_poor_pct` | Percentage of page loads classified as poor for URL-level FCP. |

### URL-level TTFB

| Column | Definition |
|---|---|
| `field_url_ttfb_ms` | URL-level Time to First Byte percentile value in milliseconds. |
| `field_url_ttfb_ms_status` | Availability status for URL-level TTFB in milliseconds. |
| `field_url_ttfb_s` | URL-level Time to First Byte percentile value in seconds. |
| `field_url_ttfb_s_status` | Availability status for URL-level TTFB in seconds. |
| `field_url_ttfb_ms_category` | CrUX quality category for URL-level TTFB. |
| `field_url_ttfb_ms_good_pct` | Percentage of page loads classified as good for URL-level TTFB. |
| `field_url_ttfb_ms_needs_improvement_pct` | Percentage of page loads classified as needing improvement for URL-level TTFB. |
| `field_url_ttfb_ms_poor_pct` | Percentage of page loads classified as poor for URL-level TTFB. |

---

## Origin-level field metric columns

The prefix `field_origin_` refers to real-user CrUX data for the full origin/domain, when available.

The origin-level columns follow the same structure as URL-level columns.

### Origin-level LCP

| Column | Definition |
|---|---|
| `field_origin_lcp_ms` | Origin-level Largest Contentful Paint percentile value in milliseconds. |
| `field_origin_lcp_ms_status` | Availability status for origin-level LCP in milliseconds. |
| `field_origin_lcp_s` | Origin-level Largest Contentful Paint percentile value in seconds. |
| `field_origin_lcp_s_status` | Availability status for origin-level LCP in seconds. |
| `field_origin_lcp_ms_category` | CrUX quality category for origin-level LCP. |
| `field_origin_lcp_ms_good_pct` | Percentage of page loads classified as good for origin-level LCP. |
| `field_origin_lcp_ms_needs_improvement_pct` | Percentage of page loads classified as needing improvement for origin-level LCP. |
| `field_origin_lcp_ms_poor_pct` | Percentage of page loads classified as poor for origin-level LCP. |

### Origin-level CLS

| Column | Definition |
|---|---|
| `field_origin_cls` | Origin-level Cumulative Layout Shift percentile value. |
| `field_origin_cls_status` | Availability status for origin-level CLS. |
| `field_origin_cls_category` | CrUX quality category for origin-level CLS. |
| `field_origin_cls_good_pct` | Percentage of page loads classified as good for origin-level CLS. |
| `field_origin_cls_needs_improvement_pct` | Percentage of page loads classified as needing improvement for origin-level CLS. |
| `field_origin_cls_poor_pct` | Percentage of page loads classified as poor for origin-level CLS. |

### Origin-level INP

| Column | Definition |
|---|---|
| `field_origin_inp_ms` | Origin-level Interaction to Next Paint percentile value in milliseconds. |
| `field_origin_inp_ms_status` | Availability status for origin-level INP in milliseconds. |
| `field_origin_inp_s` | Origin-level Interaction to Next Paint percentile value in seconds. |
| `field_origin_inp_s_status` | Availability status for origin-level INP in seconds. |
| `field_origin_inp_ms_category` | CrUX quality category for origin-level INP. |
| `field_origin_inp_ms_good_pct` | Percentage of page loads classified as good for origin-level INP. |
| `field_origin_inp_ms_needs_improvement_pct` | Percentage of page loads classified as needing improvement for origin-level INP. |
| `field_origin_inp_ms_poor_pct` | Percentage of page loads classified as poor for origin-level INP. |

### Origin-level FCP

| Column | Definition |
|---|---|
| `field_origin_fcp_ms` | Origin-level First Contentful Paint percentile value in milliseconds. |
| `field_origin_fcp_ms_status` | Availability status for origin-level FCP in milliseconds. |
| `field_origin_fcp_s` | Origin-level First Contentful Paint percentile value in seconds. |
| `field_origin_fcp_s_status` | Availability status for origin-level FCP in seconds. |
| `field_origin_fcp_ms_category` | CrUX quality category for origin-level FCP. |
| `field_origin_fcp_ms_good_pct` | Percentage of page loads classified as good for origin-level FCP. |
| `field_origin_fcp_ms_needs_improvement_pct` | Percentage of page loads classified as needing improvement for origin-level FCP. |
| `field_origin_fcp_ms_poor_pct` | Percentage of page loads classified as poor for origin-level FCP. |

### Origin-level TTFB

| Column | Definition |
|---|---|
| `field_origin_ttfb_ms` | Origin-level Time to First Byte percentile value in milliseconds. |
| `field_origin_ttfb_ms_status` | Availability status for origin-level TTFB in milliseconds. |
| `field_origin_ttfb_s` | Origin-level Time to First Byte percentile value in seconds. |
| `field_origin_ttfb_s_status` | Availability status for origin-level TTFB in seconds. |
| `field_origin_ttfb_ms_category` | CrUX quality category for origin-level TTFB. |
| `field_origin_ttfb_ms_good_pct` | Percentage of page loads classified as good for origin-level TTFB. |
| `field_origin_ttfb_ms_needs_improvement_pct` | Percentage of page loads classified as needing improvement for origin-level TTFB. |
| `field_origin_ttfb_ms_poor_pct` | Percentage of page loads classified as poor for origin-level TTFB. |

---

## Why some cells may be blank

Blank numeric cells usually mean that the metric was not available.

This is expected in several cases:

- CrUX does not have enough field data for the specific URL.
- CrUX does not have enough field data for the origin.
- The API request failed and the row has `status = "error"`.
- A Lighthouse category was not requested.
- A specific metric was not returned by PageSpeed Insights.

The tool does not fill numeric metric cells with text such as `not_available`, because that would make the column harder to analyse in Excel. Instead, it adds companion status/message columns.

Example:

```text
field_url_data_available = false
field_url_data_status = not_available
field_url_data_message = Datos de campo no disponibles para la URL analizada.
```

---

## Error handling

If a URL fails, the script does not stop the whole batch. It creates a row with:

```text
status = error
```

and stores details in:

```text
error_type
error_message
runtime_error_code
runtime_error_message
```

For failed API requests, field-data columns are marked with:

```text
field_url_data_status = not_evaluated
field_origin_data_status = not_evaluated
```

The error is also written to the log.

---

## Retry behaviour

The script retries temporary errors according to:

```toml
retries = 3
retry_backoff = 2.0
retry_status_codes = [408, 429, 500, 502, 503, 504]
```

With this configuration:

```text
Attempt 1: initial request
Attempt 2: after 2 seconds
Attempt 3: after 4 seconds
Attempt 4: after 8 seconds
```

Only temporary network errors or configured HTTP status codes are retried.

---

## Working with Excel

For Excel analysis, the recommended file is:

```text
results/pagespeed_results.xlsx
```

The XLSX file preserves numeric values correctly.

The CSV is also adapted for Excel, but depending on regional settings, Excel may still interpret some values differently if opened directly. If this happens, import the CSV using Excel's data import wizard and select:

- encoding: UTF-8;
- delimiter: semicolon;
- decimal separator: comma.

---

## Suggested workflow

1. Create and activate a virtual environment.
2. Install dependencies with `pip install -r requirements.txt`.
3. Edit `config.toml`.
4. Add your API key to `api_key`.
5. Add the URLs to the `urls` list.
6. Run a small test with 2-5 URLs.
7. Check `results/pagespeed_results.xlsx`.
8. Check the generated log file.
9. Run the full batch.

Example:

```bash
python pagespeed_audit.py --config config.toml
```

---

## Troubleshooting

### `No se ha encontrado el fichero de configuración`

The script cannot find `config.toml`.

Solution: check that `config.toml` exists in the same folder as `pagespeed_audit.py`, or run:

```bash
python pagespeed_audit.py --config path/to/your_config.toml
```

### Accented characters look wrong in Excel

Use:

```toml
csv_encoding = "utf-8-sig"
```

Also prefer the XLSX file for analysis.

### Decimal values are incorrectly interpreted in Excel

Use:

```toml
csv_delimiter = ";"
csv_decimal_comma = true
```

Also prefer the XLSX file, which stores numeric values directly.

### Many field-data cells are blank

This usually means that CrUX field data is not available for that URL or origin. Check:

```text
field_url_data_available
field_url_data_status
field_url_data_message
field_origin_data_available
field_origin_data_status
field_origin_data_message
```

### The API returns HTTP 429

HTTP 429 usually indicates too many requests in a short period or a quota issue.

Possible actions:

```toml
sleep = 2.0
retries = 3
retry_backoff = 3.0
```

Also review the quota settings in your Google Cloud project.

### The XLSX file is not generated if the process is interrupted early

The script writes the CSV progressively. The XLSX is generated at the end or when the script handles a keyboard interruption. If the process is forcibly killed, the progressive CSV is the safest partial output.

---

## Development notes

The main script is:

```text
pagespeed_audit.py
```

The script is intentionally based on a simple command-line workflow:

```bash
python pagespeed_audit.py --config config.toml
```

For larger projects, possible improvements include:

- splitting very large URL lists into batches;
- adding resume mode;
- adding parallel execution with strict rate limiting;
- exporting JSON responses for archival purposes;
- adding unit tests.
