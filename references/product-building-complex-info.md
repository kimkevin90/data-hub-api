# 주거형 단지 통합 정보

## 1. 언제 사용

단지 기본정보, polygon 요약, 마커 요약을 한 행으로 함께 조회할 때 사용한다.

### 사용하면 안 되는 상황

- 원천별 값을 따로 비교해야 하는 경우
- 단지 polygon만 필요한 경우
- 단지 내부 마커 전체를 원본 row 단위로 다뤄야 하는 경우

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `881422c4-a568-5c56-9f97-9b19967f837b` |
| source_type | `external_postgres` |
| source | `박수진.dp_complex_info` |
| row 단위 | `complex_key` 1개당 통합 단지 1행 |
| 기본 정렬 | `complex_key` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_key` | `complex_key` | `=` | `string` | `00584362` |
| `complex_name` | `complex_name` | `LIKE` | `string` | `헬리오` |
| `region_code` | `region_code` | `STARTS_WITH` | `string` | `11110` |
| `complex_type` | `complex_type` | `=` | `int` | `1` |
| `min_lat/max_lat/min_lng/max_lng` | `latitude`, `longitude` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `complex_key/complex_name/region_code/bbox`
- bbox: `latitude/longitude`, 최대 `0.1/0.1`

## 5. 응답 필드

```text
complex_key, complex_name, complex_type, latitude, longitude, region_code, complex_address, total_household_count, use_approval_date, highest_floor, passing_year, constructor_name, developer_name, heat_division_name, nearby_subway_distance, nearby_hospital_distance, nearby_elementary_school_distance, nearby_middle_school_distance, nearby_high_school_distance, nearby_park_distance, nearby_park_area, nearby_large_store_count, polygon_center_latitude, polygon_center_longitude, polygon_geojson, assignment_elementary_school_name, assignment_elementary_school_points, assignment_middle_school_name, assignment_middle_school_points, assignment_high_school_name, assignment_high_school_points, polygon_total_household_count, total_dong_count, polygon_highest_floor, nearby_subway_station_name, nearby_subway_station_distance, markers, marker_count
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  complex_key: "00584362",
  fields: "complex_key,complex_name,latitude,longitude,polygon_geojson,marker_count",
  limit: "1",
});
await fetch(`${API_BASE}/api/v1/data-products/881422c4-a568-5c56-9f97-9b19967f837b/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 여러 원천을 묶은 통합 제공용 테이블이다.
- `total_household_count`와 `polygon_total_household_count`를 자동 합산하지 않는다.
- `latitude/longitude`와 `polygon_center_latitude/polygon_center_longitude`도 출처가 다른 좌표로 보고 택일한다.
- `markers`는 배열 성격의 통합 필드이며 비어 있을 수 있다.
