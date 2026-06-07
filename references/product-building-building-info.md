# 주거형 동 정보 통합

## 1. 언제 사용

단지 내부 동/건물 단위의 좌표, 용도, 층수, 주차, 엘리베이터, 동호 요약을 함께 조회할 때 사용한다.

### 사용하면 안 되는 상황

- 동호별 개별 row가 필요한 경우
- `pnu`만으로 모든 건물 상세를 찾으려는 경우
- `units_summary`를 원본 동호 전체 목록으로 가정해야 하는 경우

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `f5b3f1ea-d744-5a57-b6f9-180293cbe179` |
| source_type | `external_postgres` |
| source | `박수진.dp_building_info` |
| row 단위 | `(complex_key, ppk)` 단지 내 동/건물 1행 |
| 기본 정렬 | `complex_key` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_key` | `complex_key` | `=` | `string` | `00584362` |
| `ppk` | `ppk` | `=` | `string` | `1002116640` |
| `dong_name` | `dong_name` | `LIKE` | `string` | `101동` |
| `building_name` | `building_name` | `LIKE` | `string` | `101` |
| `building_purpose_name` | `building_purpose_name` | `=` | `string` | `공동주택` |
| `min_lat/max_lat/min_lng/max_lng` | `latitude`, `longitude` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `complex_key/ppk/bbox`
- bbox: `latitude/longitude`, 최대 `0.1/0.1`

## 5. 응답 필드

```text
complex_key, ppk, marker_standard_ym, dong_name, building_name, latitude, longitude, building_purpose_name, house_purpose_name, structure_name, ground_floor_count, underground_floor_count, building_height, agg_household_count, total_parking_count, passenger_purpose_elevator_count, emergency_purpose_elevator_count, title_use_approval_date, agg_total_floor_area, agg_buildingcoverage_rate, agg_floorarea_rate, agg_energy_efficiency_grade, quakeproof_design_is, building_division_name, road_name_address, land_number_address, building_standard_ym, dongho_dong_id, units_summary, total_unit_count
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  complex_key: "00584362",
  ppk: "1002116640",
  fields: "complex_key,ppk,dong_name,latitude,longitude,units_summary,total_unit_count",
  limit: "1",
});
await fetch(`${API_BASE}/api/v1/data-products/f5b3f1ea-d744-5a57-b6f9-180293cbe179/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- `units_summary`는 JSON 배열 성격이며 빈 배열일 수 있다.
- `quakeproof_design_is`는 boolean이 아니라 문자열 코드로 취급한다.
- 동호 상세와 연결할 때는 `dongho_dong_id`와 `dong_id` 의미를 확인한다.
