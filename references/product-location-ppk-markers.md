# 주거형 PPK 마커

## 1. 언제 사용

단지 내부 동/건물 마커 좌표를 `complex_key` 또는 `ppk` 기준으로 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `8783b32e-b010-589a-bb54-ba91c8e75f59` |
| source_type | `external_postgres` |
| source | `이동욱.dp_apt_ppk_markers` |
| row 단위 | `(complex_key, ppk)` 단지 내부 마커 1행 |
| 기본 정렬 | `complex_key` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_key` | `complex_key` | `=` | `string` | `00584362` |
| `ppk` | `ppk` | `=` | `string` | `1002116640` |
| `dong_name` | `dong_name` | `LIKE` | `string` | `101동` |
| `building_name` | `building_name` | `LIKE` | `string` | `101` |

## 4. 필수 조건

- required: `complex_key/ppk`
- bbox 미지원

## 5. 응답 필드

```text
complex_key, ppk, standard_ym, dong_name, building_name, latitude, longitude
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({ complex_key: "00584362", limit: "100" });
await fetch(`${API_BASE}/api/v1/data-products/8783b32e-b010-589a-bb54-ba91c8e75f59/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- `ppk` 하나만 전역 유일하다고 가정하지 않는다.
- 단지 내부 마커로 볼 때는 `complex_key`와 함께 사용하는 편이 안전하다.
- bbox를 지원하지 않으므로 지도 범위 검색에는 단지 영역 또는 좌표 상품을 사용한다.
