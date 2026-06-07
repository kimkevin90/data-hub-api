# 단지명 검색

## 1. 언제 사용

단지명 문자열로 유사 단지 후보, 후보 `pnu`, 표준 단지명, 유사도 점수를 찾을 때 사용한다.

### 사용하면 안 되는 상황

- `complex_key`를 바로 확보해야 하는 경우
- 응답 컬럼을 `fields`로 줄여야 하는 경우
- 지도 좌표나 polygon이 필요한 경우

## 2. 상품 호출 정보

| 항목 | 값 |
|---|---|
| product_id | `d0000001-0000-0000-0000-000000000016` |
| source_type | `pg_function` |
| source | `public.fn_search_similar_complex()` |
| row 단위 | 함수 반환 검색 후보 1건 |
| 기본 정렬 | `match_score` |

## 3. 유효 요청 파라미터

| query param | 매핑 컬럼 | 연산자 | 타입 | 예시 |
|---|---|---|---|---|
| `complex_name` | `complex_name` | `LIKE` | `string` | `헬리오시티` |
| `limit` | `limit` | `=` | `int` | `10` |

## 4. 필수 조건

- required: `complex_name`
- bbox 미지원

## 5. 응답 필드

```text
pnu, complex_name, search_complex_name, match_score
```

함수형 상품이므로 `fields` projection은 실질적으로 적용되지 않는다. 응답은 위 4개 필드 전체 기준으로 다룬다.

## 6. 호출 예시

```ts
const params = new URLSearchParams({ complex_name: "헬리오시티", limit: "5" });
await fetch(`${API_BASE}/api/v1/data-products/d0000001-0000-0000-0000-000000000016/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

## 7. 주의점

- 테이블 조회가 아니라 DB 함수 호출 상품이다.
- `complex_key`를 직접 반환하지 않는다. 응답의 `pnu`와 단지명 후보를 후속 조회에 사용한다.
- `fields`로 응답 컬럼을 줄이는 일반 `external_postgres` 상품과 다르게 동작한다.
- `match_score`는 검색 유사도 정렬용 값이다.
