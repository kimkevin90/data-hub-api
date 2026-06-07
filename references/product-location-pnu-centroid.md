# 필지 중심 좌표

## 1. 언제 사용

`pnu` 기준 대표 중심 좌표를 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `d0000001-0000-0000-0000-000000000007` |
| source_type | `external_postgres` |
| source | `이동욱.tb_pnu_centroid` |
| row 단위 | PNU 중심점 1건 |
| 기본 정렬 | `pnu` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `pnu` | `pnu` | `=` | `string` | `1111010100100010000` |
| `min_lat/max_lat/min_lng/max_lng` | `latitude`, `longitude` | `range` | `float` | bbox 4개 모두 |

## 4. 필수 조건

- required: `pnu/bbox`
- bbox: `latitude/longitude`, 최대 `0.01/0.01`

## 5. 응답 필드

```text
pnu, standard_ym, longitude, latitude
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({ pnu: "1111010100100010000", limit: "1" });
await fetch(`${API_BASE}/api/v1/data-products/d0000001-0000-0000-0000-000000000007/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 좌표는 필지 중심점이며 건물 중심이나 출입구 위치가 아니다.
- 건물 상세 정보에 좌표가 필요하면 `pnu`로 이 상품을 추가 조회한다.
- `pnu`는 문자열로 유지한다.
