# 실거래 건물 마커

## 1. 언제 사용

실거래가 있는 건물의 마커 좌표를 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `6223a8bc-662a-5e00-91ea-1e0efc02ae96` |
| source_type | `external_postgres` |
| source | `이동욱.dp_realdeal_building_markers` |
| row 단위 | 실거래 건물 마커 1건 |
| 기본 정렬 | `pnu` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `pnu` | `pnu` | `=` | `string` | `1111010100100010000` |
| `building_name` | `building_name` | `LIKE` | `string` | `래미안` |
| `building_purpose_name` | `building_purpose_name` | `=` | `string` | `공동주택` |
| `min_lat/max_lat/min_lng/max_lng` | `latitude`, `longitude` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `pnu/building_name/building_purpose_name/bbox`
- bbox: `latitude/longitude`

## 5. 응답 필드

```text
pnu, standard_ym, building_name, building_purpose_name, latitude, longitude
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({ pnu: "1111010100100010000", limit: "20" });
await fetch(`${API_BASE}/api/v1/data-products/6223a8bc-662a-5e00-91ea-1e0efc02ae96/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 전체 건물 마커가 아니라 실거래가 있는 건물 마커다.
- 건물 상세 속성은 `pnu`로 건물 상세 정보와 결합한다.
- `building_name`은 부분 검색, `building_purpose_name`은 정확 일치다.
