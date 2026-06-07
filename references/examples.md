# Examples

## fetch-complex-by-name.ts

```ts
type MarketplaceResponse<T> = {
  success: boolean;
  data: T[];
  row_count: number;
};

type ComplexSearchRow = {
  pnu: string;
  complex_name: string | null;
  search_complex_name: string | null;
  match_score: number | null;
};

const API_BASE = process.env.DATA_MARKET_API_BASE ?? "http://localhost:8000";
const API_KEY = process.env.DATA_MARKET_API_KEY ?? "";
const PRODUCT_ID = "d0000001-0000-0000-0000-000000000016";

async function callDataProduct<T>(productId: string, query: Record<string, string>): Promise<MarketplaceResponse<T>> {
  const params = new URLSearchParams(query);
  const response = await fetch(`${API_BASE}/api/v1/data-products/${productId}/call?${params}`, {
    method: "POST",
    headers: { "X-API-KEY": API_KEY },
  });

  if (!response.ok) {
    throw new Error(`Data product call failed: ${response.status} ${await response.text()}`);
  }

  return response.json() as Promise<MarketplaceResponse<T>>;
}

export async function fetchComplexByName(complexName: string): Promise<ComplexSearchRow[]> {
  const result = await callDataProduct<ComplexSearchRow>(PRODUCT_ID, {
    complex_name: complexName,
    limit: "5",
  });

  return result.data;
}

// 단지명 검색은 complex_key를 직접 반환하지 않는다.
// 응답의 pnu, complex_name, match_score를 후보 확인과 후속 조회에 사용한다.
```

## fetch-complex-detail.ts

```ts
type MarketplaceResponse<T> = {
  success: boolean;
  data: T[];
  row_count: number;
};

type AptPolygonRow = {
  complex_key: string;
  name: string | null;
  center_lng: number | null;
  center_lat: number | null;
  polygon_geojson: string | null;
};

const API_BASE = process.env.DATA_MARKET_API_BASE ?? "http://localhost:8000";
const API_KEY = process.env.DATA_MARKET_API_KEY ?? "";
const APT_POLYGON_PRODUCT_ID = "b99e3aa5-6bcc-57ac-8463-f443c4b9921c";

async function callDataProduct<T>(productId: string, query: Record<string, string>): Promise<MarketplaceResponse<T>> {
  const params = new URLSearchParams(query);
  const response = await fetch(`${API_BASE}/api/v1/data-products/${productId}/call?${params}`, {
    method: "POST",
    headers: { "X-API-KEY": API_KEY },
  });

  if (!response.ok) {
    throw new Error(`Data product call failed: ${response.status} ${await response.text()}`);
  }

  return response.json() as Promise<MarketplaceResponse<T>>;
}

export async function fetchComplexDetail(complexKey: string): Promise<AptPolygonRow | null> {
  const result = await callDataProduct<AptPolygonRow>(APT_POLYGON_PRODUCT_ID, {
    complex_key: complexKey,
    fields: "complex_key,name,center_lng,center_lat,polygon_geojson",
    limit: "1",
  });

  return result.data[0] ?? null;
}

// center_lng/center_lat은 대표점이다.
// 실제 단지 경계 표시는 polygon_geojson을 사용한다.
```

## fetch-building-by-pnu.ts

```ts
type MarketplaceResponse<T> = {
  success: boolean;
  data: T[];
  row_count: number;
};

type BuildingRow = {
  pnu: string;
  ppk: string | null;
  jpk: string | null;
  road_name_address: string | null;
  land_number_address: string | null;
  building_name: string | null;
};

type PnuCentroidRow = {
  pnu: string;
  longitude: number | null;
  latitude: number | null;
};

const API_BASE = process.env.DATA_MARKET_API_BASE ?? "http://localhost:8000";
const API_KEY = process.env.DATA_MARKET_API_KEY ?? "";
const BUILDING_TOTAL_PRODUCT_ID = "d0000001-0000-0000-0000-000000000001";
const PNU_CENTROID_PRODUCT_ID = "d0000001-0000-0000-0000-000000000007";

async function callDataProduct<T>(productId: string, query: Record<string, string>): Promise<MarketplaceResponse<T>> {
  const params = new URLSearchParams(query);
  const response = await fetch(`${API_BASE}/api/v1/data-products/${productId}/call?${params}`, {
    method: "POST",
    headers: { "X-API-KEY": API_KEY },
  });

  if (!response.ok) {
    throw new Error(`Data product call failed: ${response.status} ${await response.text()}`);
  }

  return response.json() as Promise<MarketplaceResponse<T>>;
}

export async function fetchBuildingByPnu(pnu: string) {
  const [building, centroid] = await Promise.all([
    callDataProduct<BuildingRow>(BUILDING_TOTAL_PRODUCT_ID, {
      pnu,
      fields: "pnu,ppk,jpk,road_name_address,land_number_address,building_name",
      limit: "10",
    }),
    callDataProduct<PnuCentroidRow>(PNU_CENTROID_PRODUCT_ID, {
      pnu,
      fields: "pnu,longitude,latitude",
      limit: "1",
    }),
  ]);

  return {
    buildings: building.data,
    centroid: centroid.data[0] ?? null,
  };
}

// 건물 상세 정보에는 좌표가 없으므로 pnu-centroid를 별도 호출한다.
// pnu, ppk, jpk는 문자열로 유지한다.
```

## fetch-realdeal-by-complex-key.ts

```ts
type MarketplaceResponse<T> = {
  success: boolean;
  data: T[];
  row_count: number;
};

type RealdealRow = {
  complex_key: string | null;
  deal_type: string | null;
  price: number | null;
  deposit_price: number | null;
  rent_price: number | null;
  contract_date: string | null;
  is_cancelled: boolean | null;
};

const API_BASE = process.env.DATA_MARKET_API_BASE ?? "http://localhost:8000";
const API_KEY = process.env.DATA_MARKET_API_KEY ?? "";
const REALDEAL_PRODUCT_ID = "0f3aa719-4cdc-512b-beac-4f8c02369bd1";

async function callDataProduct<T>(productId: string, query: Record<string, string>): Promise<MarketplaceResponse<T>> {
  const params = new URLSearchParams(query);
  const response = await fetch(`${API_BASE}/api/v1/data-products/${productId}/call?${params}`, {
    method: "POST",
    headers: { "X-API-KEY": API_KEY },
  });

  if (!response.ok) {
    throw new Error(`Data product call failed: ${response.status} ${await response.text()}`);
  }

  return response.json() as Promise<MarketplaceResponse<T>>;
}

export async function fetchRealdealByComplexKey(complexKey: string): Promise<RealdealRow[]> {
  const result = await callDataProduct<RealdealRow>(REALDEAL_PRODUCT_ID, {
    complex_key: complexKey,
    date_from: "2025-01-01",
    date_to: "2025-12-31",
    fields: "complex_key,deal_type,price,deposit_price,rent_price,contract_date,is_cancelled",
    limit: "20",
  });

  return result.data;
}

// 이 상품의 price, deposit_price, rent_price는 만원 단위다.
// is_cancelled는 응답에서 boolean 계열로 해석한다.
```

## codegen-validation-prompts.md

### 코드제너레이터 검증 프롬프트

#### 1. 단지 상세

```text
SKILL.md와 references/product-location-apt-polygon.md를 참고해서
complex_key로 단지 polygon과 중심점을 조회하는 TypeScript 함수를 작성해줘.
fields는 allowed_fields 안에서만 쓰고, POST와 X-API-KEY를 사용해줘.
```

합격 기준

- `POST /api/v1/data-products/{product_id}/call`
- `X-API-KEY`
- `complex_key`
- `fields`가 `complex_key,name,center_lng,center_lat,polygon_geojson` 같은 허용 필드

#### 2. PNU 건물 상세 + 좌표

```text
references/product-building-building-total.md와 references/product-location-pnu-centroid.md를 참고해서
PNU로 건물 상세와 필지 중심 좌표를 함께 가져오는 TypeScript 코드를 작성해줘.
```

합격 기준

- 건물 상세 상품에서 좌표를 기대하지 않음
- `pnu-centroid`를 별도 호출
- `pnu`, `ppk`, `jpk` 문자열 유지

#### 3. 단지 실거래

```text
references/product-realdeal-realdeal-unified.md를 참고해서
complex_key 기준 최근 실거래 20건을 조회하는 TypeScript 코드를 작성해줘.
금액 단위와 취소 거래 처리 주의점도 주석으로 써줘.
```

합격 기준

- `date_from/date_to`를 `contract_date` 조건으로 쓰는 상품 param 사용
- 금액 단위 만원 명시
- `is_cancelled`를 boolean 계열로 해석

#### 4. 좌표 축 예외

```text
references/product-location-ppk-centroid.md를 참고해서
ppk 중심 좌표를 지도 marker로 변환하는 코드를 작성해줘.
```

합격 기준

- `x_coordinate=위도`
- `y_coordinate=경도`
- 일반 x=경도/y=위도 가정 금지
