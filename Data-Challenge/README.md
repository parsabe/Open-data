
<h1>Bitcoin Daily Prices (FAIR) – Dataset and Workflow Notes</h1>

<p>
  This page documents a cleaned, FAIR-leaning tabular dataset of daily Bitcoin (BTC-USD) prices
  along with minimal workflow and reproducibility notes. It is intended for reuse in research and teaching.
</p>

<hr />

<h2>Quick Facts</h2>
<ul>
  <li><strong>File:</strong> <code>btc_fair2.csv</code></li>
  <li><strong>Rows:</strong> 2,530</li>
  <li><strong>Columns:</strong> 10</li>
  <li><strong>Date coverage:</strong> 2014–2021 (inclusive)</li>
  <li><strong>Missing values:</strong> none (4 rows removed during cleaning)</li>
  <li><strong>Duplicates:</strong> none</li>
  <li><strong>Redundant columns:</strong> <code>Adj Close</code> dropped (identical to <code>Close</code>)</li>
  <li><strong>Source:</strong> Yahoo Finance (BTC-USD)</li>
  <li><strong>License (data):</strong> CC BY 4.0</li>
  <li><strong>License (code):</strong> MIT</li>
</ul>

<hr />

<h2>Download</h2>
<ul>
  <li>Dataset (CSV): <code>btc_fair2.csv</code></li>
  <li>Metadata (JSON): <code>metadata.json</code></li>
  <li>README (this page) : <code>index.html</code></li>
</ul>

<hr />

<h2>FAIR Compliance (Summary)</h2>
<ul>
  <li><strong>Findable:</strong> Public repo with clear filename, metadata, and searchable terms. Consider releasing a DOI via Zenodo.</li>
  <li><strong>Accessible:</strong> Open CSV and JSON files downloadable without authentication.</li>
  <li><strong>Interoperable:</strong> Standard, machine-readable formats (CSV, JSON) with ISO-8601 dates and documented schema.</li>
  <li><strong>Reusable:</strong> Explicit licenses, full cleaning steps, provenance, and versioning notes for transparent reuse.</li>
</ul>

<hr />

<h2>Data Dictionary</h2>
<table border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr>
      <th>Column</th>
      <th>Type</th>
      <th>Units / Domain</th>
      <th>Description</th>
      <th>Notes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Date</td>
      <td>string (ISO 8601)</td>
      <td>YYYY-MM-DD (UTC)</td>
      <td>Trading day timestamp.</td>
      <td>Parse to datetime in clients if needed.</td>
    </tr>
    <tr>
      <td>Open</td>
      <td>float</td>
      <td>USD</td>
      <td>Opening price.</td>
      <td>Daily frequency.</td>
    </tr>
    <tr>
      <td>High</td>
      <td>float</td>
      <td>USD</td>
      <td>Highest price of the day.</td>
      <td>Daily frequency.</td>
    </tr>
    <tr>
      <td>Low</td>
      <td>float</td>
      <td>USD</td>
      <td>Lowest price of the day.</td>
      <td>Daily frequency.</td>
    </tr>
    <tr>
      <td>Close</td>
      <td>float</td>
      <td>USD</td>
      <td>Closing price.</td>
      <td>Daily frequency.</td>
    </tr>
    <tr>
      <td>Volume</td>
      <td>float</td>
      <td>BTC</td>
      <td>Trading volume (units of BTC).</td>
      <td>Provider-specific calculation may apply.</td>
    </tr>
    <tr>
      <td>year</td>
      <td>int</td>
      <td>2014–2021</td>
      <td>Calendar year extracted from <code>Date</code>.</td>
      <td>Derived feature.</td>
    </tr>
    <tr>
      <td>month</td>
      <td>int</td>
      <td>1–12</td>
      <td>Calendar month extracted from <code>Date</code>.</td>
      <td>Derived feature.</td>
    </tr>
    <tr>
      <td>day</td>
      <td>int</td>
      <td>1–31</td>
      <td>Calendar day extracted from <code>Date</code>.</td>
      <td>Derived feature.</td>
    </tr>
    <tr>
      <td>new_mon</td>
      <td>string</td>
      <td>Month name</td>
      <td>Month name extracted from <code>Date</code>.</td>
      <td>Derived feature (e.g., January, February, ...).</td>
    </tr>
  </tbody>
</table>

<hr />

<h2>Cleaning and Standardization Steps</h2>
<ol>
  <li>Loaded raw CSV and trimmed/normalized column names.</li>
  <li>Parsed <code>Date</code> into ISO-8601 (string in final CSV; can be parsed to datetime by consumers).</li>
  <li>Removed 4 rows containing missing values across price/volume fields.</li>
  <li>Dropped <code>Adj Close</code> because it was identical to <code>Close</code> for all rows.</li>
  <li>Verified there are 0 duplicate rows after cleaning.</li>
  <li>Added derived time features: <code>year</code>, <code>month</code>, <code>day</code>, and <code>new_mon</code>.</li>
  <li>Saved finalized dataset as <code>btc_fair2.csv</code> (CSV) and prepared <code>metadata.json</code>.</li>
</ol>

<h3>Reproducible Pseudocode</h3>
<pre><code># read
df = read_csv("BTC-USD.csv")
df.columns = df.columns.str.strip()

# parse dates
df["Date"] = to_datetime(df["Date"], errors="coerce")

# remove rows with missing values (4 rows)
df = df.dropna()

# drop Adj Close if identical to Close
if "Adj Close" in df.columns and (df["Close"] == df["Adj Close"]).all():
    df = df.drop(columns=["Adj Close"])

# add time features
df["year"]  = df["Date"].dt.year
df["month"] = df["Date"].dt.month
df["day"]   = df["Date"].dt.day
df["new_mon"] = df["Date"].dt.month_name()

# save
df.to_csv("btc_fair2.csv", index=False)
</code></pre>

<hr />

<h2>Example Usage</h2>

<h3>Python (pandas)</h3>
<pre><code>import pandas as pd
df = pd.read_csv("btc_fair2.csv")
df["Date"] = pd.to_datetime(df["Date"])
monthly = df.groupby(["year", "new_mon"])["Close"].mean().reset_index()
print(monthly.head())
</code></pre>

<h3>R</h3>
<pre><code>library(readr)
library(dplyr)
library(lubridate)

df &lt;- read_csv("btc_fair2.csv")
df &lt;- df %&gt;% mutate(Date = ymd(Date))
monthly &lt;- df %&gt;% group_by(year, new_mon) %&gt;% summarise(avg_close = mean(Close), .groups = "drop")
print(head(monthly))
</code></pre>

<h3>JavaScript (D3 fetch)</h3>
<pre><code>fetch("btc_fair2.csv")
  .then(r =&gt; r.text())
  .then(text =&gt; {
    // parse CSV (use your preferred CSV parser)
    console.log(text.slice(0, 200));
  });
</code></pre>

<hr />

<h2>Metadata (JSON Example)</h2>
<p>The repository also ships a machine-readable metadata file. Example:</p>
<pre><code>{
  "title": "Bitcoin Daily Price Dataset (FAIR)",
  "description": "Cleaned and FAIR-structured daily BTC-USD prices with derived time features.",
  "file": "btc_fair2.csv",
  "rows": 2530,
  "columns": [
    "Date","Open","High","Low","Close","Volume","year","month","day","new_mon"
  ],
  "coverage": {
    "time_range": "2014-01-01 to 2021-12-31",
    "currency": "USD",
    "timezone": "UTC"
  },
  "column_descriptions": {
    "Date": "Trading day in ISO-8601 format (YYYY-MM-DD, UTC).",
    "Open": "Opening price (USD).",
    "High": "Daily high (USD).",
    "Low": "Daily low (USD).",
    "Close": "Closing price (USD).",
    "Volume": "Trading volume (units of BTC).",
    "year": "Year extracted from Date.",
    "month": "Month number (1-12).",
    "day": "Day of month (1-31).",
    "new_mon": "Month name (January..December)."
  },
  "provenance": {
    "source": "Yahoo Finance (BTC-USD)",
    "cleaning": [
      "Trimmed headers; parsed Date.",
      "Removed 4 rows with NaNs.",
      "Dropped Adj Close (identical to Close).",
      "Added time features (year, month, day, new_mon)."
    ]
  },
  "licenses": {
    "data": "CC-BY-4.0",
    "code": "MIT"
  },
  "version": "1.0.0",
  "created_utc": "2025-08-15T00:00:00Z",
  "maintainer": {
    "name": "Parsa Besharat",
    "contact": "your.email@example.com"
  }
}
</code></pre>

<hr />

<h2>Known Limitations</h2>
<ul>
  <li>Coverage currently ends in 2021; future releases may extend this period.</li>
  <li>Provider-calculated <code>Volume</code> may vary by data source methodology.</li>
  <li>No holidays/market-closure annotations included.</li>
</ul>

<hr />

<h2>Versioning</h2>
<ul>
  <li><strong>v1.0.0</strong>: Initial FAIR release; 2014–2021, 2,530 rows, 10 columns.</li>
</ul>

<hr />

<h2>Citation</h2>
<p>Please cite this dataset as:</p>
<pre><code>Besharat, P. (2025). Bitcoin Daily Prices (FAIR) [Data set].
Available at: &lt;your repository or DOI link&gt;. CC BY 4.0.
</code></pre>

</body>
</html>
