# 실거래가 통합

## 1. 언제 사용

`complex_key`, `pnu`, `ppk`, `legaldong_code` 기준으로 통합 실거래 이벤트를 조회할 때 사용한다.

### 사용하면 안 되는 상황

- 원 단위 실거래 원천 금액이 필요한 경우
- 국토부 원천 신고 테이블 기준으로 검증해야 하는 경우
- 취소일(`cancel_date`) 자체가 필요한 경우

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `0f3aa719-4cdc-512b-beac-4f8c02369bd1` |
| source_type | `external_postgres` |
| source | `박수진.dp_realdeal` |
| row 단위 | `id` 기준 거래 이벤트 1건 |
| 기본 정렬 | `contract_date` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_key` | `complex_key` | `=` | `string` | `00584362` |
| `pnu` | `pnu` | `=` | `string` | `1111010100100010000` |
| `ppk` | `ppk` | `=` | `string` | `1002116640` |
| `legaldong_code` | `legaldong_code` | `=` | `string` | `1111010100` |
| `date_from` | `contract_date` | `>=` | `string` | `2025-01-01` |
| `date_to` | `contract_date` | `<=` | `string` | `2025-12-31` |
| `registration_date_from` | `registration_date` | `>=` | `string` | `2025-01-01` |
| `registration_date_to` | `registration_date` | `<=` | `string` | `2025-12-31` |
| `deal_type` | `deal_type` | `=` | `string` | `매매` |
| `house_type` | `house_type` | `=` | `string` | `아파트` |
| `area_type` | `area_type` | `=` | `string` | `84` |
| `dong_name` | `dong_name` | `LIKE` | `string` | `101` |
| `deal_method_name` | `deal_method_name` | `=` | `string` | `중개거래` |
| `is_cancelled` | `is_cancelled` | `=` | `string` | `false` |
| `floor_min` | `floor` | `>=` | `int` | `1` |
| `floor_max` | `floor` | `<=` | `int` | `30` |
| `area_min` | `private_area` | `>=` | `float` | `80` |
| `area_max` | `private_area` | `<=` | `float` | `90` |
| `price_min` | `price` | `>=` | `float` | `50000` |
| `price_max` | `price` | `<=` | `float` | `200000` |
| `deposit_min` | `deposit_price` | `>=` | `float` | `10000` |
| `deposit_max` | `deposit_price` | `<=` | `float` | `100000` |
| `rent_min` | `rent_price` | `>=` | `float` | `0` |
| `rent_max` | `rent_price` | `<=` | `float` | `500` |

## 4. 필수 조건

- required: `complex_key/ppk/pnu/legaldong_code`
- bbox 미지원
- 권장: 키 조건 + 기간 조건 + `limit`

## 5. 응답 필드

```text
id, complex_key, ppk, pnu, legaldong_code, deal_type, category, floor, price, deposit_price, rent_price, private_area, contract_date, registration_date, deal_method_name, area_type, dong_name, is_cancelled, house_type
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  complex_key: "00584362",
  date_from: "2025-01-01",
  date_to: "2025-12-31",
  fields: "contract_date,deal_type,price,deposit_price,rent_price,is_cancelled",
  limit: "20",
});
await fetch(`${API_BASE}/api/v1/data-products/0f3aa719-4cdc-512b-beac-4f8c02369bd1/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 금액 컬럼(`price`, `deposit_price`, `rent_price`)은 만원 단위다.
- `is_cancelled`는 요청 param으로 string 값을 보내지만 응답은 boolean 계열로 해석한다.
- 취소 거래가 포함될 수 있으므로 응답의 `is_cancelled`를 확인한다.
- 약 3,300만 row 규모의 대용량 상품이므로 필수 키와 `limit`을 함께 사용한다.
