# SQL 파일 설명서 (v3.0) 🆕

## 📂 사용 중인 SQL 파일 (3개)

### 1. `create_tables_sqlite.sql` ⭐ 실제 사용
**목적**: SQLite 데이터베이스 테이블 생성

**사용 시점**: `python main.py --mode load` 또는 `--mode all` 실행 시

**내용**:
- **10개 테이블 생성** (DDL)

  **차원 테이블 (4개)**:
  - `dim_region` - 지역 차원 테이블 (17개)
  - `dim_industry` - 산업 차원 테이블 (26개: 표준산업분류 21개 + 집계분류 5개)
  - `dim_education` 🆕 - 교육수준 차원 테이블 (4개)
  - `dim_age_group` 🆕 - 연령대 차원 테이블 (9개)

  **사실 테이블 (6개)**:
  - `fact_unemployment_monthly` - 월별 실업률 (2017-2025)
  - `fact_employment_by_industry_monthly` - 산업별 고용 (2017-2025)
  - `fact_population_monthly` - 월별 인구 (2017-2025)
  - `fact_employment_insurance` 🆕 - 고용보험 피보험자 (2017-2025)
  - `fact_employment_by_education` 🆕 - 교육수준별 취업자 (2017-2025)
  - `fact_employment_by_age` 🆕 - 연령대별 취업자 (2017-2025)

**자동 실행**: `src/db_loader.py`에서 자동으로 실행됩니다.

---

### 2. `insights_sqlite.sql` ⭐ 실제 사용
**목적**: SQL 기반 데이터 분석 (15가지 인사이트 도출)

**사용 시점**: `python main.py --mode analyze` 또는 `--mode all` 실행 시

**내용**:

**주요 고용 지표 분석 (8가지)**:
1. **[인사이트 1]** 교육수준별 고용 트렌드
2. **[인사이트 2]** 청년 고용 변화 (코로나 전후 비교)
3. **[인사이트 3]** 연령대별 고용 분포
4. **[인사이트 4]** 고용보험 가입률 (고용의 질)
5. **[인사이트 5]** 학력-고용 미스매치
6. **[인사이트 6]** 고령화 인력 구조
7. **[인사이트 7]** 고용 품질 종합지표
8. **[인사이트 8]** 고용보험 이직률

**신규 데이터 기반 심층 분석 (7가지)**:
9. **[인사이트 9]** 지역별 산업 집중도 (HHI 지수)
10. **[인사이트 10]** 지역별 실업률 변동성
11. **[인사이트 11]** 주력 산업 분석
12. **[인사이트 12]** 코로나19 회복력 분석
13. **[인사이트 13]** 경제활동참가율 추세
14. **[인사이트 14]** 산업별 고용 성장률
15. **[인사이트 15]** 실업률 상위/하위 지역 비교

**자동 실행**: `src/analyzer.py`에서 자동으로 실행됩니다.

---

### 3. `create_tables_mysql_erd.sql` 📊 ERD 생성용
**목적**: MySQL Workbench에서 ERD(다이어그램) 생성

**사용 시점**: MySQL Workbench에서 수동 실행

**내용**:
- SQLite와 동일한 11개 테이블 구조
- MySQL 문법으로 작성
- 한글 COMMENT 추가 (ERD에 표시됨)
- `year_month` 백틱 처리 (MySQL 호환)
- 🆕 신규 5개 테이블 포함

**실행 방법**:
1. MySQL Workbench 실행
2. 이 파일 열기
3. 전체 선택 → Execute
4. Database → Reverse Engineer → ERD 자동 생성

**결과**: 테이블 관계도 PNG/PDF로 저장 가능

---

## 🗄️ Archive 폴더 (사용 안 함)

`sql/archive/` 폴더에 이동된 파일들:
- `create_tables.sql` - MySQL 원본 (SQLite로 전환하면서 불필요)
- `insights.sql` - MySQL용 인사이트 쿼리 (SQLite 버전 사용)
- `analysis_queries.sql` - 기본 분석 쿼리 (insights로 통합)
- `sample_queries.sql` - 샘플 쿼리 (테스트용)

**삭제해도 됨**: 프로젝트 실행에 영향 없음

---

## 🔄 SQL 파일 실행 흐름

### 전체 파이프라인 (`python main.py --mode all`)

```
1. ETL (데이터 정제)
   └─> src/etl.py

2. DB 적재
   └─> src/db_loader.py
       └─> create_tables_sqlite.sql 실행 ✅
       └─> DataFrame.to_sql() (데이터 삽입)

3. SQL 분석
   └─> src/analyzer.py
       └─> insights_sqlite.sql 실행 ✅
       └─> 15개 쿼리 순차 실행 (8개 + 7개)
       └─> 결과를 DataFrame으로 반환

4. AI/ML 분석
   └─> src/ml_models.py
       └─> SQL로 데이터 로드
       └─> ML 모델 실행
       └─> PNG 시각화 저장
```

---

## 💡 요약

| 파일 | 용도 | 자동 실행? | 필수? |
|------|------|-----------|-------|
| `create_tables_sqlite.sql` | DB 테이블 생성 | ✅ 자동 | ✅ 필수 |
| `insights_sqlite.sql` | SQL 분석 (15개) | ✅ 자동 | ✅ 필수 |
| `create_tables_mysql_erd.sql` | ERD 생성 | ❌ 수동 | ⭐ 선택 (보고서용) |
| `archive/*` | 사용 안 함 | - | ❌ 불필요 |

**핵심**: 실제로는 **2개 파일만** 자동으로 사용됩니다!
- `create_tables_sqlite.sql` - 테이블 생성 (10개 테이블)
- `insights_sqlite.sql` - 분석 (15개 인사이트)

**ERD 파일**은 보고서/발표용 다이어그램이 필요할 때만 사용.
