# 📌 JOIN이란?
2개 이상의 테이블을 연결하는 방법입니다. 여러개의 테이블을 마치 하나의 테이블인 것처럼 활용하는 방법입
연결을 위해서 적어도 하나의 컬럼을 공유하고 있어야하고 보통 primary key나 foreign key로 두 테이블을 연결합니다.

## 📌 JOIN의 종류는?
1. INNER JOIN
교집합
1-1. CROSS JOIN
조인되는 두 테이블에서 곱집합을 반환한다.
기준 테이블이 A일 경우 A 데이터의 ROW를 B 테이블 전체와 JOIN 하는 방식
A레코드 수 * B레코드 수 행을 생성한다.
2. LEFT OUTER JOIN
JOIN문 기준 왼쪽 테이블의 전체데이터와 A테이블과 B테이블의 중복 데이터를 조회
3. RIGHT OUTER JOIN
JOIN문 기준 오른쪽 테이블의 전체데이터와 A테이블과 B테이블의 중복 데이터를 조회
4. FULL OUTER JOIN
합집합
5. SELF JOIN
한 테이블에서 자기자신에 조인 시키는 것
계층형 구조에서 사용된다.
'''
SELECT * 
FROM EMP e JOIN EMP m;
ON e.manager_id = m.id
'''

1. 명시적 join 표현
'''
SELECT *
FROM employee INNER JOIN department
ON employee.DepartmentID = department.DepartmentID;
'''
3. 암시적 join 표현
'''
SELECT *
FROM employee, department
WHERE employee.DepartmentID = department.DepartmentID;
'''

![image](https://github.com/amazinguss/cs_study/assets/57309311/10423d92-dadf-40ef-9afb-892695b6a734)

오라클은 OUTER JOIN아 있지만, MYSQL은 없어서 LEFT + RIGHT JOIN

## 📌 JOIN시 고려사항
- 조인할 대상의 집합을 최소화
조건을 먼저 적용하여 관계를 맺고 조인을 맞는 것이 효과적이다.
- 인덱스를 활용하면, 조인의 연산 비용을 극적으로 낮출 수 있다.

## 📌 JOIN의 방식은?
1. Nested Loop Join (중첩 반복 조인)
2. Merge Join / Sort Merge Join (정렬 병합)
3. HASH Join

### 🔍 Nested Loop Join
(1) 정의
2개 이상 테이블에서 하나의 테이블을 기준으로 순차적으로 상대방 Row를 결합하여 원하는 결과를 추출하는 방식

(2) 처리 방식
Driving Table로 테이블 하나를 선정하고, 이 테이블로부터 where절에 정의된 검색 조건을 만족하는 데이터를 걸러낸다. 
이 값을 가지고 Driven Table에 액세스 하면서 조인 조건을 만족하는 최종 결과값을 구한다. 

2중 for문과 작동원리가 비슷하다.
'''
 for(i=0; i<dept.length; i++) { -- driving table 
    for(j=0; j<emp.length; j++) { -- driven table
       // Search
    } 
}
'''


(3)Driving Table이란/Driven Table이란?
- Driving Table (선행 테이블):조인 시 **먼저 액세스** 되는 테이블로 where 절로 최대한 데이터를 거를 수 있는 테이블이다. 보통 데이터 양이 적은 테이블로 선정한다.(옵티마이저가 결정)
- Driven Table (후행 테이블):조인 시 **나중에 액세스** 되는 테이블로 조인을 위한 인덱스가 생성되어 있는 것이 좋다.(인덱스가 없다면 Driving Table에서 도출된 결과가 맞는지 매번 full scan으로 비교해야함)

(4) 해당 조인을 사용하는 이유
- 한쪽 입력이 작고 (Driving Table), 다른 한쪽 입력이 크면서 join 열에 인덱스가 있는 경우 (Driven Table)
- I/O 연산과 비교 연산이 가장 적게 필요하기 때문
- 조인해야 할 데이터가 많지 않은 경우 사용

(5) 특징
- 선행 테이블의 결과를 통해 후행 테이블에 액세스 할 때 랜덤 I/O가 발생한다(두 테이블의 랜덤 I/O가 높게 나옴)
- 인덱스에 의한 랜덤 액세스에 기반하므로, 대량 데이터 처리 시 적합하지 않다.
- 메모리 사용량이 가장 적다.
- 순차적으로 처리한다.
- 후행 테이블에 인덱스가 필요하다.

(6) Driving Table (선행 테이블) 유도 방법
적절한 드라이빙 테이블을 선정하므로써 성능 개선 가능 : 드라이빙 테이블의 row가 더 적어야함
- 힌트 사용
from 절에 먼저 기술
leading (table명)
- 뷰 사용
뷰를 통해 데이터를 먼저 읽어내고, 이 결과로 다음 데이터를 연결하는 방식으로 시도

### 🔍 Sort Merge Join (정렬 병합)
(1) 정의
양 테이블에 각각 접근하여 결과를 정렬하고, 정렬한 결과를 scan해가면서 연결 조건으로 merge하는 방식

(2) 동작 방식
1. 각 테이블에 대해 동시에 독립적으로 데이터를 먼저 읽어 들인다.
2. 읽혀진 각 테이블의 데이터를 조인을 위한 연결고리에 대하여 정렬을 수행한다.
3. 정렬이 모두 끝난 후 조인 작업을 수행한다.

(3) 해당 조인을 사용하는 이유
- 조회의 범위가 많을 때 사용
- 두 테이블을 join한 열을 미리 정렬된 상태로 가져올 수 있는 경우
- 연결 고리에 인덱스가 전혀 없는 경우
- 대용량의 자료를 조인할 때 유리한 경우
- 조인 조건으로 범위 비교 연산자가 사용된 경우
- 인덱스 사용에 따른 랜덤 액세스의 오버헤드가 많은 경우
- 두 입력 크기가 서로 비슷한 경우 merge join과 hash join 성능이 비슷하지만, 두 입력 크기가 다른 경우 hash join 성능이 더 좋다.

(4) 성능 개선 방법
- 양쪽 테이블을 access 하는 과정에서 적절한 scan을 사용해서 access 속도를 빠르게 해준다.
- join 열이 이미 정렬되었다면 속도 향상 됨
- 두 테이블의 access 속도와 정렬 속도를 최대한 비슷하게 맞춘다. (두 테이블의 작업이 모두 끝나야 join이 시작되기 때문)
- sort_area_size를 적당한 크기로 최적화 시킨다.
sort_area_size: 두 테이블이 정렬 작업을 위해 사용되는 별도의 정렬 공간. 할당받을 수 있는 메모리 사이즈. 만약 사이즈가 부족하면 temporary table space를 사용하게 되어 딜레이 발생

### 🔍 HASH Join
(1) 정의
- 두 테이블 중 하나를 Hash Table로 선정하여, 조인될 테이블의 key 값을 Hash 알고리즘으로 비교하여 조인을 수행하는 방식
- Sort Merge 조인은 테이블의 크기가 커서 Sort시 부하가 많이 발생하여 이를 보완하기 위한 방법으로 Sort대신 해쉬값을 이용하는 join이 생김

(2) 동작 방식
- 둘 중 작은 집합(Build Input)을 읽어 Hash area에 Hash table을 생성 (해시 함수에서 리턴 받은 버킷 주소로 찾아가 해시 체인에 엔트리 연결)
- 반대쪽 큰 집합(Probe Input)을 읽어 Hash table을 탐색하면서 Join
- 해시 함수에서 리턴 받은 버킷 주소로 찾아가 해시 체인을 스캔하면서 데이터를 찾는다.
<img width="672" alt="image" src="https://github.com/amazinguss/cs_study/assets/57309311/b24d1453-dd7a-4518-934e-1a418f24d8af">

(3) 해당 조인을 사용하는 이유
1. join 컬럼에 적당한 인덱스가 없어서 Nested Loop Join이 비효율적일 때
2. join access량이 많아 random access 부하가 심하여 Nested Loop Join이 비효율적일 때
3. 대용량 데이터를 조인할 때(join 입력 크기가 크고, 정렬되지 않았을 때)
4. 수행빈도가 낮고 쿼리 수행 시간이 오래 걸리는 대용량 테이블을 join할 때
5. '=' 연산을 수행할 때
6. 집합 일치 연산(inner/outer/semi join/intersecion/union/difference 등), 중복제거, 그룹핑을 할 때
7. 비용 기반 옵티마이저를 사용할 때

(4) 성능 개선 포인트
- **해시 테이블을 만드는 과정을 효율화한다.** 해시 테이블 생성 비용 최적화, 해시 테이블로 만들 Build Input이 hash area에 담길 정도로 충분히 작아야하고 해시 키 칼럼에 중복 값이 거의 없어야 됨
- **CPU의 성능을 향상한다.** 해시 버킷이 조인 집합에 구성되어 해시 함수 결과를 저장해야 하는데 기본적으로 HASH_AREA_SIZE에 지정된 크기만큼의 메모리가 할당되어 사용됩니다. 이러한 처리에는 많은 메모리와 CPU 자원을 소모하게 된다. 그렇기에 CPU의 자원이 넉넉하다면 다른 조인에 비해 보다 좋은 효율을 내지만 부족한 상황에서는 다른 조인 방법보다 느려질 수도 있다. 그러므로 CPU의 성능을 향상한다면 HASH JOIN의 성능을 향상할 수 있다.
- **충분한 PGA 메모리 확보** Hash Area는 PGA 메모리에 할당되는데 Build Input이 HASH_AREA_SIZE를 초과하게 되면 가장 큰 순서대로 Hash Bucket이 Temporary Table Space로 내려가서 구성된다. 디스크로 내려간 Hash Bucket에 변경이 일어날 때마다 디스크 I/O가 발생하게 되어 성능이 현저하게 저하된다.

HASH_AREA_SIZE
HASH JOIN에 사용되는 최대 메모리 SIZE를 지정하는 설정값. Hash Join에서 사용되는 해쉬 메모리 크기(HASH_AREA_SIZE)의 기본 값은 SORT_AREA_SIZE의 2배이다. 9i 이상에서 값을 지정하는 것을 권장하지 않고, PGA_AGGREGATE_TARGET parameter 사용을 권장한다.
