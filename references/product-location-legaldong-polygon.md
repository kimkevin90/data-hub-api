# 법정동 영역

## 1. 언제 사용

법정동 코드나 지역명으로 행정구역 polygon과 중심점을 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `12308309-128f-5118-810c-2575a1832edc` |
| source_type | `external_postgres` |
| source | `이동욱.dp_legaldong_polygon` |
| row 단위 | `legaldong_code` 1개당 polygon 1행 |
| 기본 정렬 | `legaldong_code` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `legaldong_code` | `legaldong_code` | `=` | `string` | `1111010100` |
| `full_name` | `full_name` | `LIKE` | `string` | `서울 종로` |
| `sido_name` | `sido_name` | `=` | `string` | `서울특별시` |
| `sigungu_name` | `sigungu_name` | `=` | `string` | `종로구` |
| `eupmyeondong_name` | `eupmyeondong_name` | `LIKE` | `string` | `청운` |
| `min_lat/max_lat/min_lng/max_lng` | `center_lat`, `center_lng` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `bbox/legaldong_code/full_name/sido_name/sigungu_name/eupmyeondong_name`
- bbox: `center_lat/center_lng`

## 5. 응답 필드

```text
standard_ym, legaldong_code, full_name, sido_name, sigungu_name, eupmyeondong_name, li_name, center_lng, center_lat, polygon_geojson
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({ legaldong_code: "1111010100", limit: "1" });
await fetch(`${API_BASE}/api/v1/data-products/12308309-128f-5118-810c-2575a1832edc/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- bbox는 중심점 기준 검색이다. 경계 표시는 `polygon_geojson`을 사용한다.
- 지역 집계는 bbox보다 `legaldong_code` 기준이 안전하다.
- `legaldong_code`는 문자열로 유지한다.
