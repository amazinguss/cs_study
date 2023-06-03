## SQL Injection
### 📌 SQL Injection이 무엇인가? 
---
- 악의적인 사용자가 보안상의 취약점을 이용해 **임의로 SQL 문을 주입하고 실행되게 하여 데이터베이스가 비정상적으로 동작하게 조작**하는 행위를 말한다. 
- 대부분 클라이언트의 입력 값을 제대로 필터링하거나 ESCAPE 하지 않아 발생한다.
-  **신뢰할 수 없는 데이터가 입력될 때 개발자가 의도하지 않은 동적 쿼리가 생성되어 데이터베이스 정보를 열람하거나 조작이 가능해지는 것**을 말한다.
-  공격이 쉽지만 피해가 상당하기 때문에 **OWASP TOP 10**로도 선정되었다. (2022 년까지)

> #### ✍ OWASP TOP 10이란?
> - OWASP(Open Web Application Security Project) 오픈소스 웹 애플리케이션 보안 프로젝트
> - 주로 웹의 정보 노출, 악성 파일 및 스크립트, 보안 취약점을 연구하며 10 대 웹 애플리케이션 취약점을 발표한다.
> - 이때 OWASP TOP 10이란 웹 애플리케이션 취약점 중 빈도가 가장 높고, 보안상 영향을 크게 줄 수 있는 열 가지를 선정한 문서이다.
> 
> ![image](https://github.com/amazinguss/cs_study/assets/39427152/e3a1f8bd-7c67-4033-aefe-9d1b9c0196d0)

<br>

### 📌 SQL Injection가 발생하는 경우는 어떤 경우일까?
---
![image](https://github.com/amazinguss/cs_study/assets/39427152/218c6314-7aa7-44d9-97dc-03161eb96b9f)
- 예를 들어 학생 기록을 기반으로 한 애플리케이션이 있다고 했을 때 모든 학생들은 본인의 고유한 학생 ID를 입력해 자신의 기록만 볼 수 있다.
- 그런데 학생 ID를 입력하는 필드에 12222345 OR 1=1를 입력해 본다고 가정하자. 
```sql
SELECT *
  FROM STUDENT 
 WHERE ID == 12222345 
   OR 1=1
```
- 1=1은 참이기 때문에 모든 레코드를 반환하게 된다. 그렇다면 학생 데이터가 노출 및 손상될 수 있다.

<br>

### 📌 SQL Injection가 미치는 영향은 무엇일까?
---
- 해커가 모든 사용자의 개인 정보, 신용 카드 정보 등과 같은 데이터베이스에 저장된 중요한 정보에 접근할 수 있고, 삭제도 가능하다.
- 또한 그를 이용해 관리자 포털과 같이 보호된 영역의 액세스 권한을 얻을 수 있다.
- 추가적으로 백엔드 데이터베이스 서버가 모두 손상되게 할 수도 있다.


<br>

### 📌 SQL Injection의 공격 종류와 방법
---
#### 1. Error based SQL Injection
- **논리적 에러**를 이용한 SQL Injection이며 **가장 많이 쓰이고 대중적인 기법**이다.
- 앞서 예시로 보여 줬던 1=1이 이에 해당된다.
- 로그인에서 사용하는 기법으로 해당 구문에 입력값에 대한 검증이 없다를 확인했다면 WHERE 절에 `OR 1=1`과 같이 모든 구문을 참으로 만들어 주거나 `'OR 1=1 --`를 넣어 이후 내용들을 모두 주석으로 만들어 주는 것이다.
```sql
# ' OR 1=1 --를 입력한 경우
SELECT *
  FROM USERS
 WHERE ID = '' OR 1 = 1 -- PASSWORD = ''
```
- 매우 간단하지만 USER 테이블에 있는 모든 정보를 조회할 수 있고, 가장 많이 만들어진 계정으로 로그인 역시 가능하다.
- 대부분은 관리자 계정이 가장 처음 생기는 계정이 되는데 만약 첫 번째 계정이 관리자 계정이라 관리자 계정으로 로그인이 되었다면 관리자 권한을 이용할 수 있게 된다.

<br>

#### 2. Union based SQL Injection
- **Union 명령어**를 이용한 SQL Injection이며 `Error based SQL Injection`과 같이 `Classic SQLi`로 가장 일반적인 공격 방법이다.
- `Classic SQLi`란 공격자가 동일한 통신 구간에서 공격을 시도하고 결과를 얻는 것을 말한다.
- `UNION Injection`을 가능하게 하는 두 가지 요인은 두 테이블의 컬럼 수가 같아야 하고, 데이터 타입이 같아야 한다는 점이다.
- `UNION` 명령문 시 오류가 나지 않는 조건과 동일하다.
```sql
SELECT * 
  FROM BOARD
 WHERE TITLE LIKE '%INPUT%'
    OR CONTENTS '%INPUT%' 
```
- 다음과 같은 조회 쿼리가 돌아가는 게시 글 조회 화면이 있다고 했을 때 여기에 임의의 UNION을 추가하게 되면 어떻게 될까?
- `' UNION SELECT NULL, ID, PASSWORD FROM USERS --`를 넣으면 다음과 같은 쿼리로 바뀌게 된다.
```sql
SELECT * 
  FROM BOARD
 WHERE TITLE LIKE '%'
 UNION
SELECT NULL
     , ID
     , PASSWORD 
  FROM USERS
    OR CONTENTS '%INPUT%' 
```
- 쿼리가 이렇게 바뀌게 되면 UNION 된 결과 값이 출력되므로 사용자 정보와 게시 글 정보가 모두 보여 지게 된다.

<br>

#### 3. Boolean based SQL
- `Blind SQLi`의 하나로 `Blind SQLi`란 데이터베이스로부터 특정한 값이나 데이터를 전달받지 않고, 단순히 참과 거짓의 정보만 알 수 있을 때 사용한다.
- 다음과 같은 쿼리를 가진 로그인 폼이 있다고 생각하자.
```sql
SELECT * 
  FROM USERS 
WHERE ID = 'INPUT1' AND PASSWORD = 'INPUT2'
```
- 예를 들어 악의적인 클라이언트가 `abc123`이라는 계정으로 가입하였을 때 `abc123’ and ASCII(SUBSTR(SELECT name From information_schema.tables WHERE table_type='base table' limit 0,1)1,1)) > 100 --` 해당 구문을 주입한다.
```sql
SELECT *
  FROM USERS
 WHERE ID = 'abc123' 
   AND ASCII(SUBSTR(SELECT NAME 
                      FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE='base table' 
                     LIMIT 0,1)1,1)) > 100 --' AND PASSWORD = 'INPUT2'
```
- 이렇게 되면 LIMIT를 통해 하나의 테이블만 조회하고 SUBSTR 함수로 첫 글자만 추출되며 ASCII를 이용해 ASCII 값으로 반환된다.
- 거짓이만 실패하지만 참이 될 때까지 100이라는 숫자를 변경하며 비교하게 된다.
- 그래서 공격자는 이 프로세스를 자동화 스크립트를 통하여 단기간에 테이블 명을 알아낼 수 있게 된다.

<br>

#### 4. Time based SQL
- `Blind SQLi`의 하나로 서버로부터 특정한 응답 대신 참 혹은 거짓의 응답을 통해 데이터베이스의 정보를 유추하는 방법이다.
- 사용되는 함수는 `SLEEP`이나 `BENCHMARK`인데 **`SLEEP(초)`는 해당 초만큼 쿼리를 잠깐 지연**하게 해 준다. **`BENCHMARK(COUNT, EXP)`는 COUNT만큼 반복적으로 EXP를 실행하는 함수이며 EXP를 얼마나 빨리 실행하는지 시간 또한 잰다.** 하지만 **결과 값은 늘 0**이다.
- 예를 들어 로그인 폼에 다음과 같이 `abc123' OR (LENGTH(DATABASE()) = 1 AND SLEEP(2)) --`를 주입해 보자.
```sql
SELECT *
  FROM USERS
 WHERE ID = 'abc123' 
   OR (LENGTH(DATABASE()) = 1 AND SLEEP(2)) --
```
- 그러면 LENGTH(DATABASE()) = 1 가 참이면 SLEEP(2) 가 동작하고, 거짓이면 동작하지 않게 된다.
- 이를 통해 숫자 1 부분을 조작해 데이터베이스 길이를 알아낼 수 있다.


<br>

#### 5. Stored Procedure SQL Injection
- 저장 프로시저(Stored Procedure)는 일련 쿼리를 모아 하나의 함수처럼 사용하는 것이다.
- 공격에 사용되는 대표적인 저장 프로시저는 `MS-SQL`에 있는 `XP_CMDSHELL`로 윈도우 명령어를 사용할 수 있다.
- 시스템 권한을 획득해야 해서 공격 난이도가 높지만 직접적 피해를 입힐 수 있다.



<br>

### 📌 SQL Injection의 대응 방법은 무엇인가?
---
- 블랙 리스트와 화이트 리스트 기반의 입력 값 검증을 사용한다.
  - SQL 쿼리 구조를 변경하는 특수문자, SQL 예약어 등을 미리 등록하고, 입력을 제한하는 방식이 블랙 리스트이다.
  - 화이트 리스트는 블랙 리스트보다 좀 더 강력하지만 이용자의 편의성이 떨어진다.
  - 화이트 리스트는 허용하는 문자열을 미리 등록해 두고, 리스트 이외의 문자열이 입력되었을 때 차단한다.
 -  오류 메시지 출력을 제한한다.
    - 오류 메시지 노출은 2 차 공격 정보를 제공한다.
    - 또한 오류 메시지 노출이 꼭 필요한다면 중요한 정보들을 노출하지 않는다.
 - DB 보안 적용 
    - 관리자 DB 계정과 웹 운영 계정을 분리한다.
    - 웹 방화벽을 사용한다.
    - 주기적으로 로그 점검한다.
    - 취약점을 점검한다.
