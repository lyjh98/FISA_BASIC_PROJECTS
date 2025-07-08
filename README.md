# FISA_BASIC_PROJECTS
우리FISA 클라우드엔지니어링 과정 기본적인 프로젝트 요소 기록입니다.

# 📘 RDBMS SQL 학습 프로젝트

## ✨ 왜 공부했나요?

현대의 대부분의 기업 데이터는 관계형 데이터베이스(RDBMS)에 저장됩니다.
SQL은 이 데이터를 탐색, 가공, 분석하기 위해 **가장 기본적이고 중요한 언어**입니다.
특히 실무에서는 단순 조회뿐 아니라 조건 검색, 정렬, 집계, 패턴 매칭 등을 종합적으로 다루게 됩니다.

이번 학습은 **Oracle과 MySQL** 두 DBMS를 함께 사용하여, 각각의 차이점과 공통점을 이해하고 **데이터 분석·비즈니스 로직에 필요한 SQL 작성 능력**을 기르기 위해 진행했습니다.

---

## 🛠 어디에 쓰이나요?

* **백엔드 개발**: API에서 데이터 조회·저장·가공 로직 작성
* **데이터 분석**: 비즈니스 리포트 생성, KPI 측정
* **ETL 파이프라인**: 데이터 추출·변환·적재 작업
* **운영 관리**: 관리자 페이지, 보고서, 통계 화면 구축
* **데이터 감사 및 보안 감사**

---

## ⚙️ 실습 테이블

* `emp`: 사원 정보 테이블

  * 사번, 이름, 직업, 상사번호, 급여, 커미션, 입사일, 부서번호 등
* `dept`: 부서 정보 테이블

  * 부서번호, 부서명, 위치

---

## 📄 학습 문제 & SQL 예시

아래 9가지 문제를 Oracle, MySQL로 각각 작성해보며 실습했습니다.

---

### 1️⃣ 돈 많이 버는 사람 이름 TOP 5 출력

**Oracle**

```sql
SELECT ename
FROM (
  SELECT ename
  FROM emp
  ORDER BY sal DESC
)
WHERE ROWNUM <= 5;
```

**MySQL**

```sql
SELECT ename
FROM emp
ORDER BY sal DESC
LIMIT 5;
```

---

### 2️⃣ 직업에 A가 2번 들어가는 사람 이름

**Oracle/MySQL (동일)**

```sql
SELECT ename
FROM emp
WHERE job LIKE '%A%A%';
```

---

### 3️⃣ 복합 조건 검색

> 사원번호 7900 이상이며 두번째 글자가 A
> 또는 부서번호 30이면서 1981-09-01 이후 입사자

**Oracle**

```sql
SELECT empno, ename, deptno, hiredate
FROM emp
WHERE (empno >= 7900 AND ename LIKE '_A%')
   OR (deptno = 30 AND hiredate >= TO_DATE('1981-09-01', 'YYYY-MM-DD'));
```

**MySQL**

```sql
SELECT empno, ename, deptno, hiredate
FROM emp
WHERE (empno >= 7900 AND ename LIKE '_A%')
   OR (deptno = 30 AND hiredate >= '1981-09-01');
```

---

### 4️⃣ 1981년대 입사자 이름 내림차순 - Made By Me

**Oracle**

```sql
SELECT ename
FROM emp
WHERE hiredate BETWEEN TO_DATE('1981-01-01', 'YYYY-MM-DD')
                   AND TO_DATE('1981-12-31', 'YYYY-MM-DD')
ORDER BY ename DESC;
```

**MySQL**

```sql
SELECT ename
FROM emp
WHERE hiredate BETWEEN '1981-01-01' AND '1981-12-31'
ORDER BY ename DESC;
```

---

### 5️⃣ 1981년 입사자 급여순 상세 조회

**Oracle**

```sql
SELECT ename, job, sal, hiredate
FROM emp
WHERE hiredate BETWEEN TO_DATE('1981-01-01', 'YYYY-MM-DD')
                   AND TO_DATE('1981-12-31', 'YYYY-MM-DD')
ORDER BY sal DESC;
```

**MySQL**

```sql
SELECT ename, job, sal, hiredate
FROM emp
WHERE hiredate BETWEEN '1981-01-01' AND '1981-12-31'
ORDER BY sal DESC;
```

---

### 6️⃣ 월급 1000 이상인 사원 이름, 직업, 월급 - Made By Me

**Oracle/MySQL (동일)**

```sql
SELECT ename, job, sal
FROM emp
WHERE sal >= 1000;
```

---

### 7️⃣ 직업이 SALESMAN인 사원 이름, 직업, 연봉 내림차순

**Oracle**

```sql
SELECT ename, job, sal*12 AS 연봉
FROM emp
WHERE job = 'SALESMAN'
ORDER BY sal*12 DESC;
```

**MySQL**

```sql
SELECT ename, job, sal*12 AS 연봉
FROM emp
WHERE job = 'SALESMAN'
ORDER BY 연봉 DESC;
```

---

### 8️⃣ 사원 이름, 번호, 직업, 연봉을 부서번호로 정렬

**Oracle/MySQL (동일)**

```sql
SELECT ename, empno, job, sal*12 AS 연봉
FROM emp
ORDER BY deptno;
```

---

### 9️⃣ 이름, 직업, 고용일, 연봉 (고용일 오름차순, 연봉 내림차순)

**Oracle**

```sql
SELECT ename AS 이름, job AS 직업, hiredate AS 고용일, sal*12 AS 연봉
FROM emp
ORDER BY 고용일 ASC, 연봉 DESC;
```

**MySQL**

```sql
SELECT ename AS 이름, job AS 직업, hiredate AS 고용일, sal*12 AS 연봉
FROM emp
ORDER BY 고용일 ASC, 연봉 DESC;
```

---

## 🔍 MySQL vs Oracle 비교

| 구분             | Oracle          | MySQL                    |
| -------------- | --------------- | ------------------------ |
| **TOP N 조회**   | `ROWNUM` 사용     | `LIMIT` 사용               |
| **날짜 포맷**      | `TO_DATE()`로 변환 | `'YYYY-MM-DD'` 문자열 직접 사용 |
| **NULL 처리 함수** | `NVL()`         | `IFNULL()`               |
| **문자 대소문자**    | 대소문자 구분 엄격      | 기본은 구분 안함 (BINARY 필요)    |
| **서브쿼리/정렬**    | ANSI SQL 호환     | ANSI SQL 호환              |

---

## ✅ 학습 포인트

* 표준 SQL과 DB별 문법 차이 이해
* 실무에 자주 쓰는 `ORDER BY`, `LIKE`, `BETWEEN`, `IN`, `NOT IN`
* 날짜/숫자 조건 및 문자열 패턴 매칭
* 데이터 조회 로직의 **가독성**, **재사용성** 고려
