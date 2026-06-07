# PPK 중심 좌표

## 1. 언제 사용

`ppk` 기준 중심 좌표를 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `d0000001-0000-0000-0000-000000000015` |
| source_type | `external_postgres` |
| source | `이동욱.tb_ppk_centroid` |
| row 단위 | PPK 중심점 1건 |
| 기본 정렬 | `ppk` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `ppk` | `ppk` | `=` | `string` | `1002116640` |

## 4. 필수 조건

- required: `ppk`
- bbox 미지원

## 5. 응답 필드

```text
ppk, x_coordinate, y_coordinate
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({ ppk: "1002116640", limit: "1" });
await fetch(`${API_BASE}/api/v1/data-products/d0000001-0000-0000-0000-000000000015/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 이 상품은 예외적으로 `x_coordinate=위도`, `y_coordinate=경도`로 해석한다.
- 일반 좌표 컬럼의 x=경도/y=위도 가정을 적용하지 않는다.
- bbox를 지원하지 않는다.
