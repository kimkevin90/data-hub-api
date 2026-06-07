# 주거형 단지 영역

## 1. 언제 사용

단지의 중심점, polygon, 학군·인근 지하철 요약을 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `b99e3aa5-6bcc-57ac-8463-f443c4b9921c` |
| source_type | `external_postgres` |
| source | `이동욱.dp_apt_polygon` |
| row 단위 | `complex_key` 1개당 단지 polygon 1행 |
| 기본 정렬 | `complex_key` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_key` | `complex_key` | `=` | `string` | `00584362` |
| `name` | `name` | `LIKE` | `string` | `헬리오` |
| `total_household_min` | `total_household` | `>=` | `int` | `500` |
| `total_household_max` | `total_household` | `<=` | `int` | `3000` |
| `total_dong_min` | `total_dong` | `>=` | `int` | `5` |
| `total_dong_max` | `total_dong` | `<=` | `int` | `30` |
| `highest_floor_min` | `highest_floor` | `>=` | `int` | `10` |
| `highest_floor_max` | `highest_floor` | `<=` | `int` | `35` |
| `min_lat/max_lat/min_lng/max_lng` | `center_lat`, `center_lng` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `complex_key/name/bbox`
- bbox: `center_lat/center_lng`, 최대 `0.1/0.1`

## 5. 응답 필드

```text
complex_key, name, center_lng, center_lat, polygon_geojson, assignment_elementary_school_name, assignment_elementary_school_points, assignment_middle_school_name, assignment_middle_school_points, assignment_high_school_name, assignment_high_school_points, total_household, total_dong, highest_floor, nearby_subway_station_name, nearby_subway_station_distance
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  complex_key: "00584362",
  fields: "complex_key,name,center_lng,center_lat,polygon_geojson",
  limit: "1",
});
await fetch(`${API_BASE}/api/v1/data-products/b99e3aa5-6bcc-57ac-8463-f443c4b9921c/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- bbox 검색은 단지 중심점 기준이다. 실제 경계 표시는 `polygon_geojson`을 사용한다.
- `name`은 부분 검색이고 `complex_key`는 정확 일치다.
- `assignment_*_points`는 점수값이 아니라 좌표 목록 문자열 성격이다.
