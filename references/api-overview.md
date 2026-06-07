# Data Market API Overview

## 1. 핵심 구조

데이터 상품 조회는 아래 단일 호출 구조를 사용한다.

```text
POST /api/v1/data-products/{product_id}/call
Header: X-API-KEY: <발급 키>
Query: 상품별 허용 파라미터 + fields + limit + offset
```

`product_id`는 실제 부동산 데이터 ID가 아니라 내부 `data_products.id`와 매칭되는 상품 설정 키다. API는 이 설정을 읽고 외부 DB source, 허용 필터, 응답 필드, 기본 정렬을 결정한다.

## 2. 요청 규칙

| 항목 | 규칙 |
|---|---|
| method | 항상 `POST` |
| 인증 헤더 | `X-API-KEY` 필수 |
| path | `{product_id}`는 상품 UUID |
| filter | JSON body가 아니라 URL query parameter |
| 유효 filter | 해당 상품의 `filter_config.filters`에 정의된 param만 |
| fields | `filter_config.allowed_fields`의 부분집합, comma-separated |
| limit | `1..1000`, 기본 `100` |
| offset | `0..10000`, 기본 `0` |
| bbox | `min_lat`, `max_lat`, `min_lng`, `max_lng` 4개 모두 필요 |

`QUERY_PARAM_KEYS`는 전체 후보 어휘일 뿐이다. 상품별 leaf의 "유효 요청 파라미터" 표를 기준으로 코드를 생성한다.

## 3. 처리 흐름

```text
X-API-KEY 인증
-> product_id로 data_products 조회
-> status=published 확인
-> query parameter 수집
-> filter_config로 필수 조건·bbox·fields 검증
-> data_source_type에 맞는 handler 선택
-> 외부 PostgreSQL table/function 조회
-> 사용 기록과 credit 결과 포함 응답 반환
```

## 4. 응답 구조

| 필드 | 의미 |
|---|---|
| `success` | 호출 성공 여부 |
| `data` | 외부 DB 조회 결과 row 배열 |
| `row_count` | 이번 응답 row 수 |
| `total_available` | 전체 가능 row 수. handler에 따라 `null` 가능 |
| `limit` | 적용된 limit |
| `offset` | 적용된 offset |
| `has_next` | 다음 페이지 존재 여부 |
| `credit_used_milli` | 차감된 milli-credit 정수값 |
| `credit_used` | 표시용 credit 값 |
| `credit_balance_milli` | 차감 후 잔액 milli-credit 정수값 |
| `credit_balance` | 표시용 잔액 credit 값 |

정산·비교 판단은 `*_milli` 정수 필드를 기준으로 한다.

## 5. 에러 코드

| 상태코드 | 대표 상황 |
|---:|---|
| `400` | 미공개 상품, 잘못된 `fields`, bbox 오류, 필수 필터 누락, 날짜 형식 오류 |
| `401` | API Key 무효, 비활성, 만료 |
| `402` | 크레딧 부족 |
| `404` | `product_id`에 해당하는 상품 없음 |
| `429` | 분/일 호출 제한 초과 |
| `422` | `X-API-KEY` 헤더 누락, query 타입·범위 검증 실패 |

## 6. 호출 예시

```ts
const params = new URLSearchParams({
  complex_key: "00584362",
  fields: "complex_key,name,center_lng,center_lat",
  limit: "10",
});

const res = await fetch(`${API_BASE}/api/v1/data-products/b99e3aa5-6bcc-57ac-8463-f443c4b9921c/call?${params}`, {
  method: "POST",
  headers: { "X-API-KEY": apiKey },
});
```

응답 필드를 지정할 때는 상품 leaf의 `allowed_fields` 목록 안에서만 선택한다.
