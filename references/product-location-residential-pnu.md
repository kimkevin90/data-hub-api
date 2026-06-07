# 거주형 건물 좌표

## 1. 언제 사용

주거용 PNU의 좌표와 건물 용도를 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `d0000001-0000-0000-0000-000000000008` |
| source_type | `external_postgres` |
| source | `이동욱.tb_pnu_residential_master` |
| row 단위 | 주거용 PNU 좌표 스냅샷 1건 |
| 기본 정렬 | `pnu` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `pnu` | `pnu` | `=` | `string` | `1111010100100010000` |
| `building_purpose_name` | `building_purpose_name` | `=` | `string` | `공동주택` |
| `min_lat/max_lat/min_lng/max_lng` | `latitude`, `longitude` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `pnu/bbox`
- bbox: `latitude/longitude`, 최대 `0.01/0.01`

## 5. 응답 필드

```text
pnu, standard_ym, longitude, latitude, building_purpose_name
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({ pnu: "1111010100100010000", limit: "1" });
await fetch(`${API_BASE}/api/v1/data-products/d0000001-0000-0000-0000-000000000008/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 주거용 PNU 좌표 상품이므로 전체 건물 좌표 커버리지로 가정하지 않는다.
- `building_purpose_name`은 정확 일치 필터다.
- 일반 필지 중심 좌표만 필요하면 `필지 중심 좌표` 상품을 우선 고려한다.
