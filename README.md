# Google Ads MCP Server

> **Fork Notice:** This repository is forked from [bertramdev/GoogleAdsMCP](https://github.com/bertramdev/GoogleAdsMCP). It provides ~47 Model Context Protocol (MCP) tools for read/write access to Google Ads accounts, extended and adapted for ChatGPT / OpenAI agents, Claude Desktop/Code, and containerized microservice architectures.



## Features

* **Accounts (3 tools):** List accessible accounts, inspect account details, and traverse MCC hierarchy trees[cite: 1].
* **Campaigns (6 tools):** Full CRUD for Search, Display, Video, and Demand Gen campaigns[cite: 1].
* **Ad Groups (5 tools):** Create, update, list, and modify ad group status and default bids[cite: 1].
* **Ads (7 tools):** Responsive Search Ads (RSA), Responsive Display, Video, and Demand Gen ad creation and status management[cite: 1].
* **Keywords (5 tools):** Add keywords with match types, manage bids, remove keywords, and fetch keyword performance[cite: 1].
* **Performance Max (8 tools):** Complete PMax lifecycle: asset groups, asset linking, audience signals, and placement metrics[cite: 1].
* **Budgets (4 tools):** Create, update, list shared budgets, and track budget utilization vs. spend[cite: 1].
* **Reporting (6 tools):** Execute custom Google Ads Query Language (GAQL) queries and pull aggregated performance reports[cite: 1].
* **Utilities (3 tools):** GAQL resource discovery, field metadata inspection, and micros-to-currency conversions[cite: 1].



## Architecture & Integration

This server exposes an MCP interface over `stdio`[cite: 1]. It can be consumed in two primary ways:

1. **Direct MCP Client:** Connected to Claude Desktop, Claude Code, or cursor via command execution[cite: 1].
2. **Submodule / Microservice Dependency:** Included as a Git submodule and installed in editable mode (`pip install -e ./GoogleAdsMCP`) inside parent services (such as `google-ads-agent-api`) to serve OpenAI or local LLM agents over REST.



## Setup & Installation

### Prerequisites

* **Python 3.12+**[cite: 1]
* **[`uv`](https://docs.astral.sh/uv/)** package manager[cite: 1]
* Google Ads API Developer Token and OAuth credentials (or GCP Service Account)[cite: 1]

### 1. Standalone Installation

```bash
git clone [https://github.com/MesutMG/GoogleAdsMCP_forChatGPT.git](https://github.com/MesutMG/GoogleAdsMCP_forChatGPT.git) GoogleAdsMCP
cd GoogleAdsMCP
uv sync
```

### 2. Submodule Installation (Within a Parent Project)

If tracking as a submodule inside a parent repository:

```bash
git submodule add [https://github.com/MesutMG/GoogleAdsMCP_forChatGPT.git](https://github.com/MesutMG/GoogleAdsMCP_forChatGPT.git) GoogleAdsMCP
uv pip install -e ./GoogleAdsMCP
```

---

## Configuration

Copy the example environment file:

```bash
cp .env.example .env
```

Set your credentials in `.env` (or supply them via parent service config):

```env
# Required credentials
GOOGLE_ADS_DEVELOPER_TOKEN="YOUR_DEVELOPER_TOKEN"
GOOGLE_ADS_LOGIN_CUSTOMER_ID="YOUR_MCC_ID"

# OAuth2 Authentication
GOOGLE_ADS_CLIENT_ID="YOUR_OAUTH_CLIENT_ID"
GOOGLE_ADS_CLIENT_SECRET="YOUR_OAUTH_CLIENT_SECRET"
GOOGLE_ADS_REFRESH_TOKEN="YOUR_OAUTH_REFRESH_TOKEN"

# Or Service Account Key Authentication
GOOGLE_ADS_SERVICE_ACCOUNT_PATH="/path/to/service-account.json"
GOOGLE_ADS_IMPERSONATED_EMAIL="user@yourdomain.com"

# Target operational customer ID
GOOGLE_ADS_CUSTOMER_ID="YOUR_TARGET_CUSTOMER_ID"
```



## Usage

### Run Standalone Server (stdio)

```bash
uv run google-ads-mcp
```

### Claude Desktop Configuration

Add the server to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "google-ads": {
      "command": "uv",
      "args": ["run", "--directory", "/absolute/path/to/GoogleAdsMCP", "google-ads-mcp"],
      "env": {
        "GOOGLE_ADS_DEVELOPER_TOKEN": "YOUR_DEVELOPER_TOKEN",
        "GOOGLE_ADS_LOGIN_CUSTOMER_ID": "YOUR_MCC_ID",
        "GOOGLE_ADS_REFRESH_TOKEN": "YOUR_REFRESH_TOKEN",
        "GOOGLE_ADS_CLIENT_ID": "YOUR_CLIENT_ID",
        "GOOGLE_ADS_CLIENT_SECRET": "YOUR_CLIENT_SECRET",
        "GOOGLE_ADS_CUSTOMER_ID": "YOUR_TARGET_CUSTOMER_ID"
      }
    }
  }
}
```

### Claude Code Configuration

Add the following to `.mcp.json` in your project root:

```json
{
  "mcpServers": {
    "google-ads": {
      "command": "uv",
      "args": ["run", "--directory", "/absolute/path/to/GoogleAdsMCP", "google-ads-mcp"]
    }
  }
}
```



## Permissions & Safety Guards

* **Tool Annotations:** Tools specify MCP annotations (`readOnlyHint`, `destructiveHint`) for granular client approval policies.


* **Destructive Protections:** Removal actions (`remove_campaign`, `remove_keyword`, `remove_asset_from_group`) require an explicit `confirm_removal=True` parameter before mutations are committed.


* **Administrative Scope:** The server contains no operations for modifying account billing, credit cards, or user access permissions.





## Tool Reference (47 Tools)

### Accounts (3)

* `list_accessible_accounts` — List accounts accessible via credentials.

* `get_account_info` — Retrieve account metadata (currency, timezone, descriptive name).

* `get_account_hierarchy` — Map parent/child MCC hierarchies.



### Campaigns (6)

* `list_campaigns` — Query campaigns with optional status filtering.

* `get_campaign` — Fetch specific campaign details.

* `create_campaign` — Deploy Search or Display campaigns.

* `update_campaign` — Modify campaign bidding or settings.

* `set_campaign_status` — Change status (`ENABLED`, `PAUSED`, `REMOVED`).

* `remove_campaign` — Soft-delete campaign (requires `confirm_removal=True`).



### Ad Groups (5)

* `list_ad_groups` — List ad groups within a campaign.

* `get_ad_group` — Inspect ad group details.

* `create_ad_group` — Create ad groups with target CPC/CPM.

* `update_ad_group` — Update ad group attributes.

* `set_ad_group_status` — Toggle ad group status.



### Ads (7)

* `list_ads` — List creative assets in an ad group.

* `create_responsive_search_ad` — Create RSAs with headlines and descriptions.

* `create_responsive_display_ad` — Create display ads with marketing assets.

* `create_video_ad` — Deploy YouTube video ads.

* `create_demand_gen_ad` — Create Demand Gen format ads.

* `set_ad_status` — Enable, pause, or remove an ad.

* `get_ad_details` — Fetch granular ad metrics and asset mappings.



### Keywords (5)

* `list_keywords` — List active/paused keywords in an ad group.

* `add_keywords` — Batch insert keywords (`EXACT`, `PHRASE`, `BROAD`).

* `remove_keyword` — Remove targeted keywords.

* `update_keyword_bid` — Adjust keyword-level CPC bids.

* `get_keyword_performance` — Inspect quality scores and click rates.



### Performance Max (8)

* `create_performance_max_campaign` — Create full PMax campaigns.

* `list_asset_groups` — List PMax asset groups.

* `get_asset_group_details` — Inspect linked image, headline, and video assets.

* `add_assets_to_group` — Link creative assets to an asset group.

* `remove_asset_from_group` — Unlink assets from a group.

* `add_audience_signal` — Attach custom audience and search theme signals.

* `get_asset_performance` — Query asset-level performance ratings.

* `get_pmax_placement_performance` — Review cross-network placement distribution.



### Budgets (4)

* `list_budgets` — Query all account budgets.

* `create_budget` — Create shared or campaign-specific budgets.

* `update_budget` — Adjust delivery methods and daily amounts.

* `get_budget_utilization` — Inspect spend against target delivery limits.



### Reporting (6)

* `execute_gaql` — Run arbitrary read-only GAQL SELECT queries.

* `get_campaign_performance` — Fetch metrics at campaign level.

* `get_ad_group_performance` — Fetch metrics at ad group level.

* `get_search_terms_report` — Inspect search term queries and match types.

* `get_keyword_performance_report` — Query keyword-level delivery reports.

* `get_account_performance_summary` — Account-level aggregate summary.



### Utilities (3)

* `list_gaql_resources` — Discover queryable GAQL resources and views.

* `get_field_metadata` — Inspect field datatypes, filters, and compatibility.

* `convert_micros` — Helper for standard currency / micro-amount conversions.



---

## Testing

Run test suites using `pytest`:

```bash
uv run pytest
```



## Attribution & License

* Originally created by [bertramdev](https://github.com/bertramdev).


* Licensed under the **Apache License 2.0**. See the [LICENSE](https://www.google.com/search?q=LICENSE) file for details.
