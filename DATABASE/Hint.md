## Hint(힌트)

### 📌 Hint이 무엇인가? 
---
![image](https://github.com/amazinguss/cs_study/assets/39427152/e8a5931c-47c5-4a64-b82b-47fdef0dbecc)
- **튜닝의 핵심 부분**으로 일종의 **지시 구문**이다.
- **Optimizer의 실행 계획을 원하는 대로 바꿀 수 있도록 해 준다.**
- DB의 Optimizer가 항상 최선의 실행 계획을 수립할 수는 없다. 원하지 않은 테이블을 먼저 방문하기도 하고, 인덱스를 타기도 한다. 
- 이를 개발자가 직접 바꿀 수 있도록 DB의 Optimizer에게 일종의 가이드를 주는 것이다. 

<br>


### 📌 그런데 Optimizer가 무엇일까
---
![image](https://github.com/amazinguss/cs_study/assets/39427152/0cde2df9-b532-49a0-a65d-7502a15258dd)
- Optimizer는 **가장 효율적인 방법으로 SQL을 수행할 최적의 경로를 제안**해 주는 DBMS의 핵심 엔진
- SQL의 실행 계획을 정해 준다.
- 그림과 같이 어떤 기준으로 효율을 나눌 것이냐에 따라 두 가지로 나누어진다.
- **규칙 기반 옵티마이저 (RBO)**
  - 규칙 기반 옵티마이저는 규칙을 미리 세워 두고, 이 규칙에 따라 우선 순위가 앞서는 방식을 채택하는 형태의 옵티마이저이다.
  - 우선 순위가 있기 때문에 옵티마이저 실행 계획을 미리 예측하여 SQL을 작성할 수 있고, 원하는 대로 실행 계획을 수정할 수 있다.
  - **규칙 순위**
    1. ROWID를 사용한 단일 행
    2. 클러스터 조인에 의한 단일 행 
    3. 해시 클러스터 키에 의한 단일 행
    4. UNIQUE 옵션이나 PK를 가진 단일 행
    5. 클러스터 조인
    6. 해시 클러스터 조인
    7. 복합 컬럼 인덱스
    8. 단일 컬럼 인덱스
    9. 인덱스가 구성되어 있는 컬럼에서 제한된 범위 검색
    10. 인덱스가 구성되어 있는 컬럼에서 모든 범위 검색
    11. 정렬-병합 조인 (Sort-Merge JOIN)
    12. 인덱스가 구성되어 있는 컬럼에서 MIN, MAX
    13. 인덱스가 구성되어 있는 컬럼에서 ORDER BY
    14. 전체 테이블 스캔 (Full Table Scan)
- **비용 기반 옵티마이저 (CBO)**
  - 최근 사용되는 방식의 옵티마이저이다.
  - 오라클 10 이상부터 Default이다.
  - 기본적으로 여러 개의 실행 계획을 세운 후 비용이 가장 적을 것으로 예측되는 실행 계획을 실행한다.
  - 어떤 실행 계획이 실행될지 예측하기 힘들지만 성능, 안정성이 비용 기반보다 우수하다.
  - **최적의 비용을 예측하는 방법을 정하는 옵션**
    1. CHOOSE: 통계 정보가 있을 경우 비용 기반 실행 이외에는 규칙 기반으로 실행
    2. FIRST_ROWS: 옵티마이저 실행 계획을 통해 첫 건만 출력해 보고 이를 통해 가장 빠른 실행 계획으로 실행
    3. FIRST_ROWS_N: N 개의 출력 결과를 예측해 가장 빠른 실행 계획으로 실행
    4. ALL_ROWS: 모든 출력 결과를 예측해 가장 빠른 실행 계획으로 실행

<br>

### 📌 힌트의 특징은 뭘까?
---
- 실행 계획을 제어한다.
- 에러가 발생하지 않는다. (힌트의 시작은 /*, */)
- 선택 또는 취소될 수 있다. (힌트 문법이 올바르더라도 옵티마이저에 의해 버려질 수도 있고 선택되어질 수도 있다.)
- 다양한 종류의 힌트가 있으며 버전 업이 될 때 계속 추가된다.

<br>

### 📌 힌트는 어떻게 사용할까?
---
```
SELECT /*+ (힌트)*/ 
       컬럼명
  FROM TABLE명
 WHERE 조건;
  -- 힌트는 쿼리 서두에 명시하며, 힌트는 하나만 사용 가능한 것이 아니라 여러 개의 힌트가 필요할 경우,
  -- 여러 개의 힌트를 사용할 수 있다. /*+ leading (M) USE_NL(M V)*/와 같은 방식으로. 
  
ex) /*+ INDEX(A IDX_SUBJECT) USE_NL(M A V.M) LEADING(M) */
-- A의 테이블을 IDX_SUBJECT라는 인덱스를 타게 하고 
-- M, A, V.M(V라는 View의 M 테이블)를 Nested Loop를 돌게 하며
-- M 테이블을 먼저 조회 및 조인한다. (Driving Table에 주로 사용)
```

<br>

### 📌 힌트는 어떤 게 있을까?
---
**1) 최적화 목표**
-  /*+ ALL_ROWS */
    - 전체 처리 속도를 최소화시키기 위한 힌트이다.
    - Cost-Based 접근 방식으로 Full Scan을 선호한다. 
    - default 설정이다. 
    - 목적: Best Throughput

- /*+ FIRST_ROWS */
    - 조건에 맞는 첫 번째 ROW를 리턴하기 위한 Resource 소비를 최소화 시키기 위한 힌트이다.
    - Full Scan보다는 Index Scan을 선호한다. 
    - 또한 Sort Merge Join보다는 Nested Loop Join을 선호한다. 
    - 목적: Best Response Time
    - DELETE, UPDATE와 GROUP BY, 집합 연산자(UNION, INTERSECT, MINUS, UNION ALL), FOR UPDATE, DISTINCT를 포함한 SELECT 구문에서는 제외된다.
 
- /*+ CHOOSE */
      - ACCESS 되는 테이블에 통계 존재 여부에 따라 Optimizer에게 Rule-Based 접근과 Cost-Based 접근 중 하나를 선택할 수 있게 한다.
      - 통계 정보를 가지고 있다면 CBO, 그렇지 않다면 RBO 접근 방식을 사용한다.

**2)  접근 방법**
- FULL(table명)
    - 해당 table을 Full Scan 하도록 지정할 때 사용한다.

- HASH(table명)
    - 해당 table을 Hash Scan 하도록 지정할 때 사용한다. 
    - 해시 키로 만들어진 Cluster 내에 저장된 Table에만 적용된다.

- CLUSTER(table명)
    - 해당 table을 Cluster Scan 하도록 지정할 때 사용하며 클러스터링된 객체에만 적용된다.

- INDEX(table명 index명)
    - 플랜에서 ORACLE이 타고 있는 경로보다 더 속도가 개선될 수 있는 index가 있을 경우 index를 강제적으로 지정해 줘 해당 index를 타도록 지정할 때 사용한다.

- INDEX_ASC(table명 index명)
    - 지정된 index를 오름차순으로 타게 지정해 준다. (INDEX 힌트의 경우 default가 ASC)  

- INDEX_DESC(table명 index명)
    - 지정된 index를 내림차순으로 타게 지정해 준다. 주로 MAX 값을 구할 때 유용하다.

- INDEX_FFS(table명 index명)
    - Full Index Scan 하도록 지정할 때 사용한다.

- INDEX_SS(table명 index명)
    - Index Skip Scan 하도록 지정할 때 사용한다. 
    
> **Index Skip Scan** <br>
> ![image](https://github.com/amazinguss/cs_study/assets/39427152/f53c8177-ddea-489f-860b-b550369cee4f)
> - 루트 또는 브랜치 블록에서 읽은 컬럼 값의 정보를 이용해 조건에 부합하는 record를 포함할 가능성이 있는 블록만 골라서 접근하는 방식.

- ROWID(table 명)
    - ROWID로 테이블을 스캔하도록 유도한다.
 
**3) 조인 순서**
- LEADING(table명)
    - table명은 하나 이상이 와도 무관하며 나열한 순서대로 조인하도록 지시하는 힌트이다. 
    - 드라이빙의 경우 데이터 조회(스캔)의 범위를 줄여 주는 것이 목표이며 그러한 조건이 있는 테이블을 선두로 다음과 같은 힌트를 작성한다. 
    - LEADING의 경우 테이블 명시 순서에 따라 속도 차이가 날 수 있으므로 어떤 테이블이 우선으로 가야 스캔 범위가 줄어들지를 파악한 후 사용해야 한다.
ex) 외래환자리스트 조회 쿼리라면, PT_NO (환자번호)가 첫 번째 드라이빙 조건이 되고, 두 번째 드라이빙 조건은 MED_DT (진료일자)가 되게 된다.

- ORDERED
    - FROM절에 나열된 테이블 순서대로 조인하도록 지시하는 힌트이다.

**4) 조인 방식**
- USE_NL(table명)
    - 테이블 조인 시 Nested Loop (중첩루프) 형식으로 조인하도록 지시하는 힌트이다. 
    - 지정된 테이블은 Inner Table이 된다.

- USE_HASH(table명)
    - 지정된 테이블들이 Hash Join을 하도록 지시하는 힌트이다.

- USE_MERGE(table명)
    - 지정된 테이블들이 Sort Merge 형식으로 조인하도록 지시하는 힌트이다.

- DRIVING_SITE(table명)
    - 쿼리의 실행이 ORACLE에 의해 지정된 SITE가 아닌 다른 SITE에서 일어나도록 실행 주체를 지정한다. 

**5) 병렬 처리**
- PARALLEL(table명, degree)
    - 쿼리에 포함된 table의 degree를 설정해 준다.
    - 테이블 스캔을 DML 병렬 방식으로 처리하도록 할 때 사용하는 힌트이다.
    
> degree란 하나의 작업 수행에 대한 server process의 개수를 의미하며 시스템의 CPU 수, 시스템의 최대 Process 수, 테이블이 striping 되어 있을 시 테이블들이 걸쳐 있는 disk의 수, data의 위치, query의 형태 등으로 결정이 된다. 
 
- PARALLEL_INDEX(table명, index명, degree)
    - 인덱스 스캔을 병렬 방식으로 처리하도록 유도하는 힌트이다.

- NOPARALLEL(table명)
    - 쿼리가 병렬 처리 하지 않도록 지정해 준다.
