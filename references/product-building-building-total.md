# 건물 상세 정보

## 1. 언제 사용

주소, `pnu`, `ppk`, `jpk`로 건물·호 상세 속성과 키 브리지를 조회할 때 사용한다.

### 사용하면 안 되는 상황

- 지도 표시용 좌표가 필요한 경우
- 건물 polygon이 필요한 경우
- 단지 내부 마커나 단지 polygon이 필요한 경우

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `d0000001-0000-0000-0000-000000000001` |
| source_type | `external_postgres` |
| source | `이동욱.tb_building_total` |
| row 단위 | 건물/호/전유부 성격 혼재 row 1건 |
| 기본 정렬 | `ppk` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `pnu` | `pnu` | `=` | `string` | `1111010100100010000` |
| `ppk` | `ppk` | `=` | `string` | `1002116640` |
| `jpk` | `jpk` | `=` | `string` | `none` |
| `address` | `land_number_address` OR `road_name_address` | `LIKE` | `string` | `테헤란로` |

## 4. 필수 조건

- required: `pnu/ppk/jpk/address`
- bbox 미지원

## 5. 응답 필드

```text
pnu, ppk, jpk, land_number_address, road_name_address, building_name, private_area, public_area, pyeong_number, ground_floor_count, underground_floor_count, building_purpose_name, house_purpose_name, structure_name, dong_name, ho_name, floor_name, standard_ym, building_division_name, title_purpose_area_name, agg_land_area, agg_building_area, agg_total_floor_area, agg_buildingcoverage_rate, agg_floorarea_rate, total_parking_count, building_height, passenger_purpose_elevator_count, emergency_purpose_elevator_count, agg_household_count, title_use_approval_date, agg_energy_efficiency_grade, quakeproof_design_is, living_private_area, living_public_area, total_total_floor_area, agg_family_count, title_family_count, agg_indoor_mechanical_parking_count, title_indoor_mechanical_parking_count, agg_outdoor_mechanical_parking_count, title_outdoor_mechanical_parking_count, agg_indoor_direct_parking_count, title_indoor_direct_parking_count, agg_outdoor_direct_parking_count, title_outdoor_direct_parking_count
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  pnu: "1111010100100010000",
  fields: "pnu,ppk,jpk,road_name_address,building_name,building_purpose_name",
  limit: "10",
});
await fetch(`${API_BASE}/api/v1/data-products/d0000001-0000-0000-0000-000000000001/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 원천 161컬럼 중 API는 46컬럼만 노출한다.
- 좌표 컬럼이 없다. 지도 표시는 `pnu`로 필지 중심 좌표 또는 마커 상품을 추가 조회한다.
- `address`는 지번주소와 도로명주소 2개 컬럼을 OR 부분검색한다.
- `jpk='none'`은 NULL이 아니라 문자열 값이다.
