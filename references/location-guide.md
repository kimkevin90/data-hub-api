# 위치정보 가이드

## 1. 언제 이 카테고리로 시작하나

위치정보는 키 확보와 지도 표시의 진입점이다.

| 입력/목적 | 시작 상품 | 확보되는 값 |
|---|---|---|
| 단지명 검색 | 단지명 검색 | `pnu`, 표준 단지명 후보 |
| 지도 bbox로 단지 찾기 | 주거형 단지 영역 | `complex_key`, 단지 중심점, polygon |
| 단지 내부 동/건물 마커 | 주거형 PPK 마커 | `complex_key`, `ppk`, 좌표 |
| 법정동 경계 | 법정동 영역 | `legaldong_code`, polygon |
| 건물 경계 | 건물 영역 | `building_management_number`, polygon |
| PNU 좌표 | 필지 중심 좌표 | `pnu`, `longitude`, `latitude` |
| PPK 좌표 | PPK 중심 좌표 | `ppk`, 중심 좌표 |
| 주거용 PNU 좌표 | 거주형 건물 좌표 | `pnu`, 좌표, 건물 용도 |
| 실거래 건물 좌표 | 실거래 건물 마커 | 실거래가 있는 `pnu` 좌표 |

## 2. 조합 흐름

```text
단지명 -> 단지명 검색 -> pnu/표준 단지명 후보
후보 확인 -> 건물 상세 또는 단지 상품으로 보강
complex_key -> 단지 영역 -> polygon/학군/중심점
complex_key -> PPK 마커 -> 단지 내부 동 좌표
complex_key -> 건물정보/실거래정보
```

```text
pnu -> 필지 중심 좌표
pnu -> 건물 상세 정보
pnu -> 실거래 건물 마커 또는 실거래 이력
```

## 3. 주의점

- polygon 상품의 bbox는 보통 중심점 기준 검색이다. 실제 면 정보는 `polygon_geojson`을 사용한다.
- `tb_ppk_centroid`는 `x_coordinate=위도`, `y_coordinate=경도`로 해석한다.
- 생활인프라와 달리 위치정보 MVP 상품은 대부분 `complex_key`, `pnu`, `ppk`, `legaldong_code` 중 하나를 만든다.
- 건물 영역은 건물명·주소·용도 설명이 없으므로 상세 속성은 건물정보 상품과 결합한다.
