# 주거형 실거래 이력

## 1. 언제 사용

단지 기준 주거형 실거래 이력을 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `300a15a6-c9aa-5b86-9e87-675ca49c4b50` |
| source_type | `external_postgres` |
| source | `이동욱.dp_trade_real` |
| row 단위 | 단지 기준 거래 이벤트 1건 |
| 기본 정렬 | `date` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_key` | `complex_key` | `=` | `string` | `00584362` |
| `date_from` | `date` | `>=` | `string` | `2025-01-01` |
| `date_to` | `date` | `<=` | `string` | `2025-12-31` |
| `registration_date_from` | `registration_date` | `>=` | `string` | `2025-01-01` |
| `registration_date_to` | `registration_date` | `<=` | `string` | `2025-12-31` |
| `category` | `category` | `=` | `int` | `1` |
| `house_type` | `house_type` | `=` | `string` | `아파트` |
| `area_type` | `area_type` | `=` | `string` | `84` |
| `dong` | `dong` | `LIKE` | `string` | `101` |
| `method` | `method` | `=` | `string` | `중개거래` |
| `is_cancelled` | `is_cancelled` | `=` | `string` | `false` |
| `floor_min` | `floor` | `>=` | `int` | `1` |
| `floor_max` | `floor` | `<=` | `int` | `30` |
| `price_min` | `price` | `>=` | `float` | `50000` |
| `price_max` | `price` | `<=` | `float` | `200000` |
| `deposit_min` | `deposit` | `>=` | `float` | `10000` |
| `deposit_max` | `deposit` | `<=` | `float` | `100000` |
| `rent_min` | `rent` | `>=` | `float` | `0` |
| `rent_max` | `rent` | `<=` | `float` | `500` |

## 4. 필수 조건

- API required: `complex_key` 또는 `date_from/date_to` 중 하나
- 운영 권장: `complex_key` + 기간 조건 + `limit`
- bbox 미지원

## 5. 응답 필드

```text
id, complex_key, category, floor, price, deposit, rent, date, registration_date, method, area_type, dong, is_cancelled, house_type
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  complex_key: "00584362",
  date_from: "2025-01-01",
  date_to: "2025-12-31",
  limit: "20",
});
await fetch(`${API_BASE}/api/v1/data-products/300a15a6-c9aa-5b86-9e87-675ca49c4b50/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 금액 컬럼(`price`, `deposit`, `rent`)은 만원 단위다.
- `method='unknown'`은 결측 삭제 대상이 아니라 거래방식 코드값이다.
- `category`는 매매/전세/월세 계열 코드다.
- 약 3,300만 row 규모의 대용량 상품이므로 키·기간·`limit`을 함께 사용한다.
