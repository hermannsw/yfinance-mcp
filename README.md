# Yahoo Finance MCP Server

A simple MCP server for Yahoo Finance using [yfinance](https://github.com/ranaroussi/yfinance). This server provides a set of tools to fetch stock data, news, and other financial information.

<a href="https://glama.ai/mcp/servers/@narumiruna/yfinance-mcp">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/@narumiruna/yfinance-mcp/badge" />
</a>

## Tools

- **yfinance_get_ticker_info**

  - Retrieve stock data including company info, financials, trading metrics and governance data.
  - Inputs:
    - `symbol` (string): The stock symbol.

- **yfinance_get_ticker_news**

  - Fetches recent news articles related to a specific stock symbol with title, content, and source details.
  - Inputs:
    - `symbol` (string): The stock symbol.

- **yfinance_search**

  - Fetches and organizes search results from Yahoo Finance, including stock quotes and news articles.
  - Inputs:
    - `query` (string): The search query (ticker symbol or company name).
    - `search_type` (string): Type of search results to retrieve (options: "all", "quotes", "news").

- **yfinance_get_top**

  - Get top entities (ETFs, mutual funds, companies, growth companies, or performing companies) in a sector.
  - Inputs:
    - `sector` (string): The sector to get.
    - `top_type` (string): Type of top companies to retrieve (options: "top_etfs", "top_mutual_funds", "top_companies", "top_growth_companies", "top_performing_companies").
    - `top_n` (number, optional): Number of top entities to retrieve (default 10).

- **yfinance_get_price_history**

  - Fetch historical price data for a given stock symbol over a specified period and interval. Can return data as a markdown table or generate professional financial charts using mplfinance, including candlestick charts with volume bars, VWAP overlays, and volume profile analysis.
  - Inputs:
    - `symbol` (string): The stock symbol.
    - `period` (string, optional): Time period to retrieve data for (e.g. '1d', '1mo', '1y'). Default is '1mo'.
    - `interval` (string, optional): Data interval frequency (e.g. '1d', '1h', '1m'). Default is '1d'.
    - `chart_type` (string, optional): Type of chart to generate. If not specified, returns price data as markdown table. Options:
      - "price_volume": Candlestick chart with volume bars
      - "vwap": Volume Weighted Average Price chart with VWAP overlay
      - "volume_profile": Candlestick chart with volume profile showing volume distribution by price level (displayed as a histogram on the right side)
  - Output:
    - If `chart_type` is not specified: Returns historical price data as a markdown table
    - If `chart_type` is specified: Returns a base64-encoded WebP image for efficient token usage

- **yfinance_get_stock_actions**

  - Fetch historical dividends and stock splits for a given stock symbol.
  - Inputs:
    - `symbol` (string): The stock symbol.

- **yfinance_get_financial_statement**

  - Fetch financial statements (income statement, balance sheet, or cash flow statement) for annual or quarterly periods.
  - Inputs:
    - `symbol` (string): The stock symbol.
    - `financial_type` (string): Type of financial statement to retrieve. Options:
      - "income_stmt": Annual income statement
      - "quarterly_income_stmt": Quarterly income statement
      - "balance_sheet": Annual balance sheet
      - "quarterly_balance_sheet": Quarterly balance sheet
      - "cashflow": Annual cash flow statement
      - "quarterly_cashflow": Quarterly cash flow statement

- **yfinance_get_holder_info**

  - Fetch shareholder and insider information for a given stock symbol.
  - Inputs:
    - `symbol` (string): The stock symbol.
    - `holder_type` (string): Type of holder information to retrieve. Options:
      - "major_holders": Top shareholders by percentage
      - "institutional_holders": Institutional ownership details
      - "mutualfund_holders": Mutual fund ownership details
      - "insider_transactions": Recent insider buy/sell transactions
      - "insider_purchases": Summary of insider purchase/sale activity
      - "insider_roster_holders": List of insiders and their holdings

- **yfinance_get_option_expiration_dates**

  - Fetch available options expiration dates for a given stock symbol. Use this before calling `yfinance_get_option_chain` to find valid expiration dates.
  - Inputs:
    - `symbol` (string): The stock symbol.

- **yfinance_get_option_chain**

  - Fetch the options chain (calls or puts) for a specific expiration date. Returns contract details including strike price, premium, implied volatility, and open interest.
  - Inputs:
    - `symbol` (string): The stock symbol.
    - `expiration_date` (string): Options expiration date in 'YYYY-MM-DD' format.
    - `option_type` (string): Option type to retrieve (options: "calls", "puts").

- **yfinance_get_recommendations**

  - Fetch analyst recommendations or rating change history for a given stock symbol.
  - Inputs:
    - `symbol` (string): The stock symbol.
    - `recommendation_type` (string): Type of recommendation data to retrieve. Options:
      - "recommendations": Analyst buy/hold/sell ratings summary by period
      - "upgrades_downgrades": Historical rating changes by analyst firm

## Usage

You can use this MCP server via uv (Python package installer), Docker, or local development.

### Via uv

1. [Install uv](https://docs.astral.sh/uv/getting-started/installation/)
2. Add the following configuration to your MCP server configuration file:

```json
{
  "mcpServers": {
    "yfmcp": {
      "command": "uvx",
      "args": ["yfmcp@latest"]
    }
  }
}
```

### Via Docker

Add the following configuration to your MCP server configuration file:

```json
{
  "mcpServers": {
    "yfmcp": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "narumi/yfinance-mcp"]
    }
  }
}
```

### Via Docker Compose

Add the following configuration to your MCP server configuration file:

```docker-compose.yml
services:
  yfinance-mcp:
    image: yfinance-mcp:latest
    container_name: yfinance-mcp
    stdin_open: true
    tty: true
    environment:
      - MCP_TRANSPORT=sse  # or streamable-http
      - MCP_HOST=0.0.0.0
      - MCP_PORT=8000
```

### Local Development

For local development, add the following configuration to your MCP server configuration file:

```json
{
  "mcpServers": {
    "yfmcp": {
      "command": "uv",
      "args": [
        "run",
        "--directory",
        "/path/to/yfinance-mcp",
        "yfmcp"
      ]
    }
  }
}
```

Replace `/path/to/yfinance-mcp` with the actual path to your local repository.

## Demo Chatbot

This repository includes a demo chatbot built with [Chainlit](https://github.com/Chainlit/chainlit) that provides a conversational interface to the Yahoo Finance MCP server.

### Features

- Interactive chat interface for querying stock information
- Automatic display of financial charts and data visualizations
- Support for both OpenAI and LiteLLM backends
- Tool calling integration with the MCP server

### Setup

1. Install demo dependencies:

```bash
uv sync --extra dev
```

2. Configure environment variables:

```bash
# Recommended: start from the template
cp .env.example .env

# For OpenAI
OPENAI_API_KEY=your_openai_api_key
DEFAULT_MODEL=gpt-4.1

# For LiteLLM (alternative)
LITELLM_API_KEY=your_litellm_api_key
LITELLM_BASE_URL=your_litellm_base_url
DEFAULT_MODEL=gpt-4.1
```

3. Run the demo:

```bash
uv run chainlit run demo.py
```

The chatbot will be available at `http://localhost:8000`.

### Example Queries

- "Get AAPL stock information"
- "Show me recent TSLA news"
- "Display NVDA price history for the past month"
- "Show me a candlestick chart for MSFT over the last 3 months"
