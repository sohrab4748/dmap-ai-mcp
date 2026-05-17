# DMAP-AI MCP Server

**Drought Monitoring and Analysis Platform — Model Context Protocol Server**

[![MCP](https://img.shields.io/badge/MCP-compatible-blue)](https://modelcontextprotocol.io)
[![License](https://img.shields.io/badge/license-proprietary-lightgrey)](https://droughtanalysis.com/terms)
[![Live Server](https://img.shields.io/badge/server-live-brightgreen)](https://droughtanalysis.com/mcp)

DMAP-AI is a remote MCP server by [AgriMetSoft LLC](https://agrimetsoft.com) that exposes drought monitoring and analysis tools to AI assistants — including Claude, ChatGPT, and any MCP-compatible client. It provides SPI tables, drought severity event detection, wavelet scalogram analysis, and drought periodicity diagnostics over real climate data (NASA POWER and ERA5).

---

## Quick Connect

### Claude (claude.ai)
1. Go to **Settings → Connectors → Add custom connector**
2. Name: `DMAP-AI`
3. URL: `https://droughtanalysis.com/mcp`
4. Click **Add**, then enable it in your chat via the `+` button

### ChatGPT / Gemini
Already published — search for **DMAP-AI** in the respective app stores.

### Any MCP client (mcp.json / config)
```json
{
  "mcpServers": {
    "dmap-ai": {
      "type": "streamable-http",
      "url": "https://droughtanalysis.com/mcp"
    }
  }
}
```

---

## Tools

All tools are **read-only** (`readOnlyHint: true`, `destructiveHint: false`). They fetch and compute drought diagnostics from real climate data. No user data is written or stored.

### `debug_ping`
Confirms the server is live and returns the current tool version and available free tools.

```
Input:  none
Output: server status, tool version, available tools list
```

---

### `run_spi_table`
Returns a Standardized Precipitation Index (SPI) table for a given location and time period.

```
Input:
  latitude        float    e.g. 42.03
  longitude       float    e.g. -93.62
  start_date      string   YYYY-MM-DD  (default: 1981-01-01)
  end_date        string   YYYY-MM-DD  (default: 2024-12-31)
  baseline_start  int      (default: 1981)
  baseline_end    int      (default: 2024)
  spi_scale       int      1–48 months  (default: 12)
  data_source     string   "nasa_power" | "era5"  (default: nasa_power)
  step            string   "yearly" | "monthly"   (default: yearly)
  yearly_method   string   "jan_dec_totals"       (default)
  category_scheme string   "classic_spi" | "usdm_style" | "percentile_band" | "kmeans"
  max_rows        int      (default: 1000)

Output: SPI table with date, precipitation, SPI value, and drought category per row
```

**Example prompt:**
> *"Show me the SPI-12 table for Ames, Iowa from 1981 to 2024."*

---

### `run_drought_severity_events`
Detects and returns drought events (start, end, duration, minimum SPI, magnitude) using a configurable severity threshold.

```
Input:
  latitude / longitude / start_date / end_date / baseline_start / baseline_end
  spi_scale       int      (default: 12)
  data_source     string   "nasa_power" | "era5"
  step / yearly_method / category_scheme
  severity_threshold  string  SPI threshold, e.g. "-0.99", "-1.5"  (default: -0.99)

Output: drought events table (start date, end date, duration, min SPI, magnitude)
```

**Example prompt:**
> *"Find all drought events in Ames, Iowa from 1981 to 2024 using SPI-12 and a threshold of -1.0."*

---

### `run_wavelet_scalogram`
Runs a continuous wavelet transform and returns a compact time–period power matrix (scalogram), highlighting high-power drought zones.

```
Input:
  latitude / longitude / start_date / end_date / baseline_start / baseline_end
  spi_scale       int      (default: 12)
  data_source     string   "nasa_power" | "era5"
  step / yearly_method
  max_periods     int      downsampled period axis limit  (default: 24)
  max_times       int      downsampled time axis limit    (default: 60)

Output: compact scalogram matrix, top high-power cells, period and time axes
```

**Example prompt:**
> *"Run a wavelet scalogram for SPI-12 in Ames, Iowa from 1981 to 2024 and show the high-power zones."*

---

### `run_drought_periodicity_analysis`
Computes the global wavelet spectrum and identifies dominant and secondary drought periods (cycles) for a location.

```
Input:
  latitude / longitude / start_date / end_date / baseline_start / baseline_end
  spi_scale       int      (default: 12)
  data_source     string   "nasa_power" | "era5"
  step / yearly_method

Output: dominant period, top ranked periods by global power, global wavelet spectrum
```

**Example prompt:**
> *"Use DMAP-AI to run global wavelet analysis for SPI-12 in Ames, Iowa from 1981 to 2024. Then explain the dominant period."*

---

## Data Sources

| Source | Description | Coverage |
|---|---|---|
| `nasa_power` | NASA POWER (default) | Global, ~0.5° grid |
| `era5` | ERA5 / Copernicus reanalysis | Global, ~0.25° grid |

---

## Supported Category Schemes

| Scheme | Description |
|---|---|
| `classic_spi` | WMO standard SPI thresholds (default) |
| `usdm_style` | US Drought Monitor–style D0–D4 categories |
| `percentile_band` | Empirical percentile-based bands |
| `kmeans` | AI/ML k-means cluster labels |

---

## Notes

- **Free tools:** SPI table, drought severity events, wavelet scalogram, periodicity/global wavelet spectrum
- **Not included in the free MCP version:** Copula analysis and other advanced licensed features
- All tools are **stateless and idempotent** — the same inputs always return the same outputs
- Data source is **NASA POWER by default** (no API key required)

---

## Links

- **Live server:** https://droughtanalysis.com/mcp
- **Research version:** https://droughtanalysis.com/DMAP_AI_App
- **Privacy policy:** https://droughtanalysis.com/privacy
- **Terms of service:** https://droughtanalysis.com/terms
- **Contact / support:** https://agrimetsoft.com/contact
- **Developer:** [AgriMetSoft LLC](https://agrimetsoft.com)

---

## Citation

If you use DMAP-AI outputs in research or publications, please cite DMAP-AI and the relevant data source (NASA POWER or ERA5/Copernicus).

---

© AgriMetSoft LLC. DMAP-AI is a drought monitoring and analysis platform.
