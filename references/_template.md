# Product Reference Template

Use this structure when adding or regenerating a product reference.

## When To Use

State the user intent and source row unit.

## Do Not Use When (Optional)

Use this section when the product has confusing adjacent products or impossible outputs.

## Product

| 항목 | 값 |
|---|---|
| product_id | `` |
| source_type | `` |
| source | `` |
| row 단위 |  |
| 기본 정렬 | `` |

## Valid Query Parameters

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|

## Required Filters

- required:
- bbox:

## Response Fields

```text
allowed_field_1, allowed_field_2
```

## Example

```ts
const params = new URLSearchParams({});
await fetch(`${API_BASE}/api/v1/data-products/<product_id>/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## Guardrails

- Keep IDs as strings.
- Use only allowed fields.
- Specify money and coordinate units when relevant.
