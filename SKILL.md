---
name: data-marketplace-api
description: >
  Use when generating code or explanations for the Bigvalue Data Marketplace API, especially Korean real-estate data products, product_id, POST /api/v1/data-products/{product_id}/call, X-API-KEY authentication, data_products, filter_config, allowed_fields, complex_key, pnu, ppk, jpk, apartment complexes, building details, polygons, coordinates, and real-deal transaction data. 한국 부동산 데이터마켓 API, 단지, 건물, 실거래, 좌표, PNU, PPK, complex_key, product_id, /call 연동 코드를 만들 때 사용.
metadata:
  version: v0.1.0
  author: Bigvalue
  organization: Bigvalue
  date: 2026-06-05
license: Internal-Use-Only
---

# Data Marketplace API Skill

## Language Behavior

- Korean user: respond in Korean and keep real-estate identifiers as code literals.
- English user: respond in English, but preserve Korean table/product names when they are source names.
- Always explain `product_id`, `complex_key`, `pnu`, `ppk`, `jpk`, and `legaldong_code` as strings.

## When To Use This Skill

Use this skill when the user asks for code, API calls, data flow, or field guidance for Bigvalue Data Marketplace products:

- Korean apartment complex, building, polygon, marker, coordinate, or real-deal transaction data
- `POST /api/v1/data-products/{product_id}/call`
- selecting a `product_id`
- deciding valid query parameters or `fields`
- avoiding money-unit, coordinate-axis, or identifier mistakes

## Setup

If base URL or API key setup is needed, load `references/setup.md`.

Core environment variables:

```bash
export DATA_MARKET_API_BASE="http://localhost:8000"
export DATA_MARKET_API_KEY="<your-api-key>"
```

Never hard-code real API keys in generated source files.

## Core Mental Model

- `product_id` is not a real-estate data ID. It selects a `data_products` execution setting row.
- Actual rows are found by query parameters such as `complex_key`, `pnu`, `ppk`, `jpk`, `legaldong_code`, and bbox.
- Query parameters are not raw SQL. Only product-specific `filter_config.filters` become WHERE conditions.
- Response fields are governed by `filter_config.allowed_fields`, not by every physical source column.
- `schema_columns` is documentation metadata, not the final SELECT allowlist.

## /call Structure

```text
POST /api/v1/data-products/{product_id}/call
Header: X-API-KEY: <api key>
Query: product-specific named parameters + fields + limit + offset
```

Rules:

- Use `POST`, not `GET`.
- Use header `X-API-KEY`; do not use mixed-case header variants.
- Send filters as URL query parameters, not a JSON body.
- `fields` must be a comma-separated subset of product `allowed_fields`.
- `limit` range: `1..1000`, default `100`.
- `offset` range: `0..10000`, default `0`.
- bbox requires all four values: `min_lat`, `max_lat`, `min_lng`, `max_lng`.
- Compare and reconcile credits with `*_milli` integer fields when credit data is used.

## Product Selection Flow

```text
User input
-> choose product_id
-> call /call with product-specific query params
-> receive allowed_fields response
-> use returned keys for next product when needed
```

Common routes:

| Input or task | Start with | Then |
|---|---|---|
| Complex name | `references/product-location-complex-search.md` | Confirm candidate, then use building or complex products |
| Address or PNU | `references/product-building-building-total.md` | Use `ppk -> references/product-building-building-info.md` to obtain `complex_key` when applicable |
| Complex polygon | `references/product-location-apt-polygon.md` | Use `complex_key` |
| Building coordinate | `references/product-location-pnu-centroid.md` | Join by `pnu` |
| Realdeal by complex | `references/product-realdeal-realdeal-unified.md` | Use `complex_key`, date filters, and `limit` |

## Critical Guardrails

1. Money units differ by product. `dp_realdeal`, `dp_trade_real`, and `dp_monthly_report` are in 만원. `tb_realdeal` is in 원.
2. `jpk='none'` is a string sentinel, not SQL NULL.
3. `tb_ppk_centroid` is the coordinate-axis exception: `x_coordinate=위도`, `y_coordinate=경도`.
4. Do not expect `complex_key` from building-total. Use `ppk -> building-info` or complex search/bbox products.
5. Keep all real-estate identifiers as strings. Never numeric-cast `pnu`, `ppk`, `jpk`, or `complex_key`.
6. Large products require narrow filters and `limit`.
7. `is_cancelled` is sent as a string query parameter but interpreted as a boolean-like response/source value.
8. The pg_function complex-search product does not apply normal `fields` projection; treat its four returned fields as the response basis.

## Code Generation Checklist

- Use the correct `product_id` from the product reference.
- Use only query parameters listed in the product reference.
- Use only response fields listed in that product's allowed fields.
- Preserve IDs as strings in TypeScript types.
- Put money units in type names, comments, or UI labels.
- Add error handling for `400`, `401`, `402`, `404`, `429`, and `422`.
- Avoid `SELECT *` assumptions and arbitrary field names.

## Reference Routing

| User task | Load these references |
|---|---|
| Setup, auth, response, errors | `references/setup.md`, `references/api-overview.md` |
| PNU, PPK, JPK, complex_key confusion | `references/domain-keys.md` |
| Location products overview | `references/location-guide.md`, `references/_sections.md` |
| Building products overview | `references/building-guide.md`, `references/_sections.md` |
| Realdeal products overview | `references/realdeal-guide.md`, `references/_sections.md` |
| Complex name search | `references/product-location-complex-search.md` |
| Complex polygon or apartment area | `references/product-location-apt-polygon.md` |
| PPK marker | `references/product-location-ppk-markers.md` |
| Legal-dong polygon | `references/product-location-legaldong-polygon.md` |
| Building polygon | `references/product-location-building-polygon.md` |
| PNU centroid | `references/product-location-pnu-centroid.md` |
| PPK centroid | `references/product-location-ppk-centroid.md` |
| Residential PNU coordinate | `references/product-location-residential-pnu.md` |
| Realdeal building marker | `references/product-location-realdeal-markers.md` |
| Apartment complex basic info | `references/product-building-apt-complex.md` |
| Integrated complex info | `references/product-building-complex-info.md` |
| Apartment dong-ho detail | `references/product-building-apt-dong-ho.md` |
| Get complex_key from PPK | `references/product-building-building-info.md` |
| Building detail by PNU/address | `references/product-building-building-total.md` |
| Unified realdeal | `references/product-realdeal-realdeal-unified.md` |
| Residential realdeal history | `references/product-realdeal-trade-real.md` |
| Raw realdeal source | `references/product-realdeal-realdeal-source.md` |
| TypeScript examples and validation prompts | `references/examples.md` |
| Add a new product reference | `references/_template.md` |
