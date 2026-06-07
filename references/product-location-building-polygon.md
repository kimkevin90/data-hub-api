# 건물 영역

## 1. 언제 사용

건물관리번호나 지도 bbox로 건물 polygon을 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `da47a1db-2908-5528-86b4-30a938155c5c` |
| source_type | `external_postgres` |
| source | `이동욱.dp_building_polygon` |
| row 단위 | `building_management_number` 1개당 건물 polygon 1행 |
| 기본 정렬 | `building_management_number` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `building_management_number` | `building_management_number` | `=` | `string` | 건물관리번호 |
| `road_name_address_management_number` | `road_name_address_management_number` | `=` | `string` | 도로명주소관리번호 |
| `sido_sigungu_code` | `sido_sigungu_code` | `=` | `string` | `11110` |
| `road_name_code` | `road_name_code` | `=` | `string` | 도로명코드 |
| `underground_division_code` | `underground_division_code` | `=` | `string` | `0` |
| `min_lat/max_lat/min_lng/max_lng` | `center_lat`, `center_lng` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `bbox/building_management_number/road_name_address_management_number/sido_sigungu_code/road_name_code`
- bbox: `center_lat/center_lng`, 최대 `0.02/0.02`

## 5. 응답 필드

```text
building_management_number, standard_ym, road_name_address_management_number, sido_sigungu_code, road_name_code, underground_division_code, main_building_number, sub_building_number, center_lng, center_lat, polygon_geojson
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  min_lat: "37.50",
  max_lat: "37.51",
  min_lng: "127.02",
  max_lng: "127.03",
  limit: "100",
});
await fetch(`${API_BASE}/api/v1/data-products/da47a1db-2908-5528-86b4-30a938155c5c/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- `building_management_number`는 `pnu`/`ppk`와 다른 키 체계다.
- 이 상품은 건물명·주소 문자열·용도 설명을 제공하지 않는다.
- 상세 속성은 건물 상세 정보와 별도 결합한다.
