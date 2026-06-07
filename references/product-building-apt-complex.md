# 주거형 단지 기본정보

## 1. 언제 사용

`complex_key`, 단지명, 지역 코드로 단지 기본 속성과 주변 인프라 요약을 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `82d7a8f2-3837-59bf-9fbf-991c44b31038` |
| source_type | `external_postgres` |
| source | `이동욱.dp_apt_complex` |
| row 단위 | `complex_key` 1개당 단지 1행 |
| 기본 정렬 | `complex_key` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_key` | `complex_key` | `=` | `string` | `00584362` |
| `name` | `name` | `LIKE` | `string` | `헬리오` |
| `region_code` | `region_code` | `STARTS_WITH` | `string` | `11110` |
| `type` | `type` | `=` | `int` | `1` |
| `min_lat/max_lat/min_lng/max_lng` | `lat`, `lng` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `complex_key/name/region_code/bbox`
- bbox: `lat/lng`, 최대 `0.1/0.1`

## 5. 응답 필드

```text
complex_key, name, type, lat, lng, region_code, address, total_household, use_approval_date, highest_floor, passing_year, constructor_name, developer_name, heat_division_name, nearby_subway_distance, nearby_hospital_distance, nearby_elementary_school_distance, nearby_middle_school_distance, nearby_high_school_distance, nearby_park_distance, nearby_park_area, nearby_large_store_count
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  complex_key: "00584362",
  fields: "complex_key,name,address,total_household,lat,lng",
  limit: "1",
});
await fetch(`${API_BASE}/api/v1/data-products/82d7a8f2-3837-59bf-9fbf-991c44b31038/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- `region_code`는 prefix 검색이다.
- `lat/lng`는 대표점이며 경계는 단지 영역 상품의 `polygon_geojson`을 사용한다.
- 주변 거리 값은 요약 정보이며 개별 인프라 레이어와 직접 동일하지 않을 수 있다.
