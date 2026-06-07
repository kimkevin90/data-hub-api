# Data Hub API Skill

Bigvalue Data Marketplace API code-generation skill.

## Install

```bash
npx skills add kimkevin90/data-hub-api
```

## Local Test

```bash
npx skills add ./ -l
npx skills add ./ -y
```

## Notes

- Uses `POST /api/v1/data-products/{product_id}/call`.
- Uses `X-API-KEY` authentication.
- Keep API keys out of generated source files.
