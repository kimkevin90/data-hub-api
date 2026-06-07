# 실거래정보 가이드

## 1. 언제 이 카테고리를 쓰나

실거래정보는 거래 이벤트, 가격, 보증금, 월세, 취소 여부를 조회할 때 사용한다.

| 목적 | 추천 상품 | 금액 단위 | 기준 키 |
|---|---|---|---|
| 통합 실거래 조회 | 실거래가 통합 | 만원 | `complex_key`, `pnu`, `ppk`, `legaldong_code` |
| 단지 기준 주거형 거래 | 주거형 실거래 이력 | 만원 | `complex_key` + 기간 |
| 원천 신고 이력 | 실거래 이력 | 원 | `pnu`, `complex_name` |

## 2. 선택 기준

```text
단지/건물 서비스용 최신 통합 형태 -> 실거래가 통합
단지 기준 주거형 이력 중심 -> 주거형 실거래 이력
원천 신고 테이블 기준 확인 -> 실거래 이력
```

## 3. 금액 단위

- `dp_realdeal.price`, `deposit_price`, `rent_price`는 만원 단위다.
- `dp_trade_real.price`, `deposit`, `rent`는 만원 단위다.
- `tb_realdeal.price`, `deposit_price`는 원 단위다.
- 서로 다른 단위 상품을 섞어 비교할 때는 반드시 단위를 변환한다.

## 4. 주의점

- `is_cancelled`는 요청 param으로는 string 값을 보내지만, 응답은 boolean 계열로 해석한다.
- 실거래 상품은 대용량이므로 키 필터와 기간 조건, `limit`을 함께 사용한다.
- `date_from/date_to`는 상품에 따라 실제 컬럼 `contract_date` 또는 `date`로 매핑된다.
- 취소 거래가 포함될 수 있으므로 응답의 `is_cancelled` 또는 `cancel_date`를 확인한다.
