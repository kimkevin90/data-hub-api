# 주거형 동호 상세

## 1. 언제 사용

단지 내부 동, 호, 층, 면적 상세를 조회할 때 사용한다.

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `73476dc4-70b2-53e8-a1d5-adf61bf92308` |
| source_type | `external_postgres` |
| source | `이동욱.dp_apt_dong_ho` |
| row 단위 | 단지 안의 동호 구성 1건 |
| 기본 정렬 | `complex_key` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_key` | `complex_key` | `=` | `string` | `00584362` |
| `dong_name` | `dong_name` | `LIKE` | `string` | `101동` |
| `ho_name` | `ho_name` | `LIKE` | `string` | `1001` |
| `area_type` | `area_type` | `=` | `string` | `84` |
| `floor_min` | `floor` | `>=` | `int` | `10` |
| `floor_max` | `floor` | `<=` | `int` | `20` |
| `private_area_min` | `private_area` | `>=` | `float` | `80` |
| `private_area_max` | `private_area` | `<=` | `float` | `90` |

## 4. 필수 조건

- required: `complex_key`
- bbox 미지원
- 권장: `limit`과 동/호/면적 보조 조건 사용

## 5. 응답 필드

```text
complex_key, dong_id, dong_name, ho_name, floor, area_type, private_area, public_area
```

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  complex_key: "00584362",
  dong_name: "101",
  limit: "100",
});
await fetch(`${API_BASE}/api/v1/data-products/73476dc4-70b2-53e8-a1d5-adf61bf92308/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 약 880만 row 규모의 대용량 상품이다. `complex_key`와 `limit` 없이 호출하지 않는다.
- `dong_id`는 원천 동 코드이며 `ppk/jpk/pnu` 직접 연결키가 아니다.
- `dong_name`과 `ho_name`은 부분 검색이다.
