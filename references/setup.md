# Setup

## Environment Variables

```bash
export DATA_MARKET_API_BASE="http://localhost:8000"
export DATA_MARKET_API_KEY="<your-api-key>"
```

## Authentication

Use the `X-API-KEY` header for `/call`.

Do not hard-code real API keys in generated source files. Prefer environment variables, server-side secrets, or runtime configuration.

## Smoke Test

```bash
curl -X POST "$DATA_MARKET_API_BASE/api/v1/data-products/d0000001-0000-0000-0000-000000000016/call?complex_name=헬리오시티&limit=3" \
  -H "X-API-KEY: $DATA_MARKET_API_KEY"
```
