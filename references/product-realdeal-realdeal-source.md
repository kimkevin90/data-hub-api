# 실거래 이력

## 1. 언제 사용

원천 신고 실거래 이력을 `pnu` 또는 단지명 기준으로 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `d0000001-0000-0000-0000-000000000005` |
| source_type | `external_postgres` |
| source | `이동욱.tb_realdeal` |
| row 단위 | 실거래 신고 이벤트 1건 |
| 기본 정렬 | `pnu` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `pnu` | `pnu` | `=` | `string` | `1111010100100010000` |
| `complex_name` | `complex_name` | `LIKE` | `string` | `헬리오` |
| `deal_division_name` | `deal_division_name` | `=` | `string` | `매매` |
| `house_division_name` | `house_division_name` | `=` | `string` | `아파트` |

## 4. 필수 조건

- required: `pnu/complex_name`
- bbox 미지원
- 권장: `pnu` 또는 단지명 + `limit`

## 5. 응답 필드

```text
pnu, legaldong_name, land_number, complex_name, deal_division_name, private_area, contract_date, deposit_price, price, dong_name, floor_name, construction_year, road_name_address, cancel_date, deal_type_name, contract_start_ym, contract_end_ym, contract_division_name, house_division_name
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  pnu: "1111010100100010000",
  fields: "pnu,complex_name,contract_date,price,deposit_price,cancel_date",
  limit: "20",
});
await fetch(`${API_BASE}/api/v1/data-products/d0000001-0000-0000-0000-000000000005/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 금액 컬럼(`price`, `deposit_price`)은 원 단위다.
- `dp_realdeal`, `dp_trade_real`의 만원 단위와 섞어 비교하지 않는다.
- 취소 거래 확인은 `cancel_date`를 사용한다.
- 약 3,300만 row 규모의 원천 테이블이므로 필터와 `limit` 없이 호출하지 않는다.
