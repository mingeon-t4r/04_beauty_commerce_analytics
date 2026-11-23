# Beauty Commerce Analytics

> 공개 뷰티 이커머스 행동 데이터를 활용한 실무형 데이터 분석 프로젝트

## 1. Project Overview

### 프로젝트 소개

온라인 뷰티 커머스 플랫폼의 사용자 행동 이벤트를 분석하여 상품 탐색, 장바구니 이용, 구매 행동을 이해하고 데이터 기반 의사결정에 활용할 수 있는 분석 체계를 구축하는 프로젝트입니다.

SQL, Python, 통계 분석을 활용하여 데이터 품질 검증부터 KPI 정의, 데이터 마트 설계, 고객 행동 분석 및 결과 보고까지 단계적으로 수행합니다.

본 프로젝트는 특정 기업의 내부 데이터를 활용한 업무가 아닌 **공개 데이터 기반의 독립적인 포트폴리오 프로젝트**입니다.

### 프로젝트 목표

1. 원천 데이터의 구조와 품질을 검증한다.
2. 비즈니스 문제를 정의하고 분석 목적에 맞는 KPI를 설계한다.
3. 재사용 가능한 분석용 데이터 마트를 구축한다.
4. 고객의 조회·장바구니·구매 행동을 분석한다.
5. 분석 결과를 바탕으로 실행 가능한 개선 방향을 제안한다.
6. 분석 과정과 결과를 재현 가능한 코드 및 문서로 관리한다.

---

## 2. Dataset

### 데이터 출처

**eCommerce Events History in Cosmetics Shop**

- 제공 플랫폼: Kaggle
- 데이터 링크: https://www.kaggle.com/datasets/mkechinov/ecommerce-events-history-in-cosmetics-shop
- 데이터 유형: 온라인 화장품 쇼핑몰 사용자 행동 이벤트
- 분석 기간: 2019년 10월 ~ 2020년 2월
- 파일 형식: CSV
- 원본 파일: 5개
- 전체 이벤트: **20,692,840건**

### 데이터 구성

| Column | Description |
|---|---|
| event_time | 이벤트 발생 시각 |
| event_type | 이벤트 종류 |
| product_id | 상품 식별자 |
| category_id | 카테고리 식별자 |
| category_code | 카테고리 코드 |
| brand | 브랜드 |
| price | 이벤트에 기록된 상품 가격 |
| user_id | 사용자 식별자 |
| user_session | 사용자 세션 식별자 |

### Event Types

| Event | Description |
|---|---|
| view | 상품 조회 |
| cart | 장바구니 추가 |
| remove_from_cart | 장바구니 제거 |
| purchase | 구매 이벤트 |

**주의:** 본 데이터는 행동 이벤트 로그이며 실제 주문·정산 원장과 다릅니다. `purchase` 이벤트 수를 실제 주문 수와 동일하게 해석하지 않습니다.

---

## 3. Project Progress

각 업무는 Task 단위로 수행하며, 코드 리뷰와 데이터 검증 후 완료 여부를 판단합니다.

| Task | Description | Status |
|---|---|---|
| TASK-001 | 원천 데이터 확보 및 품질 검증 | Completed |
| TASK-002 | 비즈니스 문제 정의 및 KPI 설계 | Planned |
| TASK-003 | 데이터 마트 설계 | Planned |
| TASK-004 | 데이터 마트 구축 및 정합성 검증 | Planned |
| TASK-005 이후 | 데이터 운영 자동화 및 행동 분석 | Planned |

이후 Task는 앞선 업무 결과를 기준으로 구체화할 예정입니다.

### TASK-001 — Data Source Review

**업무 목적**

원천 데이터의 구조, 규모, 품질을 검증하여 향후 분석에 활용할 수 있는지 평가합니다.

**수행 내용**

- 5개월 원천 CSV의 구조 및 자료형 확인
- 월별 이벤트 수 및 날짜 범위 검증
- 주요 컬럼의 결측치 검사
- 이벤트 유형별 분포 분석
- 중복 후보의 발생 비율 및 유형별 분포 검증
- 0원·음수 가격 이벤트 분석
- 사용자·세션 식별자 검증
- 데이터 분석 가능 범위 및 제약 문서화

**검토 결과: 조건부 사용 가능**

데이터 품질 이슈를 고려한 분석 기준을 적용하는 조건으로 고객 행동 이벤트 분석에 활용할 수 있다고 판단했습니다.

**산출물**

- [원천 데이터 검토 보고서](docs/data_source_review.md)
- [원천 데이터 탐색 Notebook](notebooks/01_raw_data_exploration.ipynb)

---

## 4. Key Findings

TASK-001에서 확인한 주요 결과입니다.

### 4.1 이벤트 분포

| Event Type | Event Count | Share |
|---|---:|---:|
| view | 9,657,821 | 46.67% |
| cart | 5,768,333 | 27.88% |
| remove_from_cart | 3,979,679 | 19.23% |
| purchase | 1,287,007 | 6.22% |
| **Total** | **20,692,840** | **100.00%** |

※ 위 비율은 이벤트 구성 비율이며 구매 전환율이 아닙니다.

### 4.2 주요 데이터 품질 이슈

| Issue | Result | Implication |
|---|---|---|
| category_code 결측 | 98.29% | 카테고리 분석 제약 |
| brand 결측 | 42.32% | 브랜드 분석의 대표성 검토 필요 |
| user_session 결측 | 4,598건 | 세션 기반 분석 시 처리 기준 필요 |
| 동일 행 중복 후보 | 1,109,098건 | 이벤트 건수 과대 집계 가능성 |
| 0원 가격 이벤트 | 104,157건 | 가격 관련 KPI 검증 필요 |
| 음수 가격 이벤트 | 131건 | 가격 기록 의미 확인 필요 |
| 복수 사용자 연결 세션 | 월별 22~86개 | 세션 식별자 연결 규칙 필요 |

### 4.3 주요 발견 및 해석

**장바구니 제거 이벤트의 중복 후보 집중**

전체 중복 후보 중 약 89.43%가 `remove_from_cart` 이벤트에서 발견되었습니다.

해당 이벤트의 중복 후보 비율은 약 24.92%로, 장바구니 행동을 분석할 때 중복 처리 방식이 결과에 큰 영향을 줄 수 있습니다.

단, 동일 행이 실제 잘못 수집된 중복이라는 사실은 확인되지 않았으므로 원본 데이터를 임의로 삭제하지 않았습니다.

**음수 가격의 특정 이벤트 집중**

음수 가격 131건 중 126건은 구매 이벤트에서 발생했습니다.

월별로 음수 가격이 기록된 고유 상품은 각각 5개였으며, 가격이 음수로 기록된 원인은 아직 확인되지 않았습니다.

**2020년 2월의 0원 가격 집중**

전체 0원 가격 이벤트 104,157건 중 71,254건이 2020년 2월에 발생했습니다.

이에 따라 해당 기간의 가격 기록 방식이나 상품 구성에 차이가 존재했는지 추가 검토가 필요합니다.

---

## 5. Tech Stack

| Category | Tools |
|---|---|
| Language | Python |
| Data Processing | pandas |
| Development | VS Code, Jupyter Notebook |
| Version Control | Git, GitHub |
| Database / SQL | 향후 데이터 마트 단계에서 확정 |
| Visualization | 향후 분석 요구사항에 따라 선정 |

SQL 처리 엔진과 대시보드 도구는 현재 확정하지 않았습니다.

---

## 6. Repository Structure

```text
04_beauty_commerce_analytics/
│
├── data/
│   └── raw/                          # 원천 CSV (Git 제외)
│
├── docs/
│   └── data_source_review.md         # TASK-001 원천 데이터 검토
│
├── notebooks/
│   └── 01_raw_data_exploration.ipynb # TASK-001 탐색 및 품질 검증
│
├── .gitignore
└── README.md
```

향후 프로젝트 단계에 따라 `sql/`, `src/`, `tests/`, `reports/` 등의 폴더를 추가할 예정입니다.

---

## 7. How to Run

### 7.1 Requirements

실행 환경:

- Windows 11
- Python
- pandas
- Jupyter Notebook 또는 VS Code Jupyter 확장

현재 사용한 정확한 Python 및 pandas 버전은 추후 환경 재현 정보에 추가할 예정입니다.

### 7.2 데이터 다운로드

1. Kaggle 데이터셋 페이지에서 원본 CSV를 다운로드합니다.
2. 프로젝트 최상위 디렉터리에 `data/raw/` 폴더를 생성합니다.
3. 압축 해제한 CSV 파일을 해당 폴더에 배치합니다.

필요한 파일:

```text
data/raw/
├── 2019-oct.csv
├── 2019-nov.csv
├── 2019-dec.csv
├── 2020-jan.csv
└── 2020-feb.csv
```

파일명은 Notebook에서 참조하는 이름과 일치해야 합니다.

원본 CSV 파일은 용량 및 데이터 재배포 조건을 고려하여 GitHub에 등록하지 않습니다.

### 7.3 Notebook 실행

1. 프로젝트 폴더를 VS Code에서 엽니다.
2. `notebooks/01_raw_data_exploration.ipynb` 파일을 실행합니다.
3. 프로젝트에 맞는 Python 환경 및 Jupyter 커널을 선택합니다.
4. Notebook의 작업 디렉터리가 `notebooks/`인지 확인합니다.
5. 전체 셀을 위에서 아래 순서대로 실행합니다.
6. 데이터 품질 검사와 검산 결과를 확인합니다.

Notebook은 원본 CSV를 읽고 월별 요약, 결측, 중복 후보, 가격 이상값, 사용자·세션 검증을 수행합니다.

원본 파일을 수정하지 않도록 구성되어 있습니다.

---

## 8. Data Limitations

본 프로젝트에서 사용하는 데이터에는 다음 제약이 존재합니다.

- 주문 ID, 수량, 정산·환불 정보가 부족하여 실제 주문 수와 순매출을 정확히 계산할 수 없습니다.
- `category_code`와 `brand`에 상당한 결측이 존재합니다.
- 동일 행 중복 후보가 존재하지만 실제 수집 오류 여부는 확인되지 않았습니다.
- 세션 결측 및 여러 사용자와 연결된 세션이 존재합니다.
- 이벤트 가격이 0 또는 음수인 사례가 존재합니다.
- 월별 고유 사용자 수의 합계는 전체 기간 고유 사용자 수와 다릅니다.
- 관측 이벤트만으로 구매 전환율 변화의 인과적 원인을 확정할 수 없습니다.

분석 지표를 개발할 때 위 제약을 반영하고, 필요한 처리 기준을 문서화할 예정입니다.

---

## 9. Next Steps

### TASK-002 — Business Problem Definition & KPI Design

다음 단계에서는 원천 데이터의 특성과 제약을 바탕으로 분석할 비즈니스 문제를 정의하고 KPI를 설계합니다.

**예정 업무**

- 주요 비즈니스 문제 선정
- 분석 목표 및 가설 정의
- 사용자 기반 KPI 설계
- 세션 기반 KPI 설계
- 지표의 분모·분자·관찰 기간 정의
- 데이터 품질 이슈의 KPI 반영 기준 결정

**예정 산출물**

- `docs/problem_statement.md`
- `docs/kpi_definitions.md`

---

## 10. Project Management

본 프로젝트는 일차별 학습이 아니라 **Task 기반 실무 수행 방식**으로 관리합니다.

1. 업무 요구사항 확인
2. 데이터 탐색 및 분석 수행
3. Notebook·SQL·문서 산출물 작성
4. 결과 검증 및 코드 리뷰
5. 수정사항 반영
6. 완료 기준 충족 후 다음 Task 진행

새로운 기술이나 분석 방법을 적용할 때는 사용 목적과 기존 방법과의 차이를 검토합니다.

단순한 코드 실행을 넘어 문제 정의, 데이터 검증, 분석 결과 해석, 의사결정 지원 역량을 강화하는 것을 목표로 합니다.

---

## 11. Data Source

eCommerce Events History in Cosmetics Shop

https://www.kaggle.com/datasets/mkechinov/ecommerce-events-history-in-cosmetics-shop

데이터의 이용 및 재배포는 원본 데이터셋의 라이선스와 제공 조건을 따릅니다.