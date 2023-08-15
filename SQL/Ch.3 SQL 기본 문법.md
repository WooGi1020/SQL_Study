### 03-1. 기본 of 기본 SELECT ~ FROM ~ WHERE
---
- SELECT
	SELECT 는 구축 완료된 테이블에서 데이터를 추출, 아무리 남발해도 기존 데이터가 변경되지는 않음. 
	기본 형식은 SELECT (열 이름) FROM (테이블 이름) WHERE (조건식)

- 데이터베이스 만들기
	```python
	DROP DATABASE IF EXISTS market_db;
	# market_db를 삭제하는 문장, 처음 실행 시 필요X, 재실행 시 필요
	CREATE DATABASE marker_db;
	# 데이터 베이스를 새로 만듦
	USE market_db;
	# USE 문은 market_db 데이터베이스를 선택하는 문장, 데이터베이스를 수동으로 
	# 더블클릭하던 것과 동일한 효과
	CREATE TABLE member -- 회원 테이블
	( mem_id  		CHAR(8) NOT NULL PRIMARY KEY, -- 사용자 아이디(PK)
	  mem_name    	VARCHAR(10) NOT NULL, -- 이름
	  mem_number    INT NOT NULL,  -- 인원수
	  addr	  		CHAR(2) NOT NULL, -- 지역(경기,서울,경남 식으로 2글자만입       력)
	  phone1		CHAR(3), -- 연락처의 국번(02, 031, 055 등)
	  phone2		CHAR(8), -- 연락처의 나머지 전화번호(하이픈제외)
	  height    	SMALLINT,  -- 평균 키
	  debut_date	DATE  -- 데뷔 일자
	);
	# member 테이블을 만드는 과정
	CREATE TABLE buy -- 구매 테이블
	(  num 		INT AUTO_INCREMENT NOT NULL PRIMARY KEY, -- 순번(PK)
	   mem_id  	CHAR(8) NOT NULL, -- 아이디(FK)
	   prod_name 	CHAR(6) NOT NULL, --  제품이름
	   group_name 	CHAR(4)  , -- 분류
	   price     	INT  NOT NULL, -- 가격
	   amount    	SMALLINT  NOT NULL, -- 수량
	   FOREIGN KEY (mem_id) REFERENCES member(mem_id)
	);
	# buy 테이블을 만드는 과정
	# AUTO_INCERMENAT의 경우 자동으로 숫자를 입력해줌
```
---
- 데이터 입력하기
```python
INSERT INTO member VALUES('TWC', '트와이스', 9, '서울', '02', '11111111', 167, '2015.10.19');
# 회원 테이블에 값을 입력.
INSERT INTO buy VALUES(NULL, 'BLK', '지갑', NULL, 30, 2);
# 첫번째 열은 AUTO_INCREMENT를 통해 숫자가 자동 기입되므로 NULL로 채움
```
---
- 데이터 조회하기
```python
SELECT * FROM member;
SELECT * FROM buy;
```
---

- USE 문
	SELECT 문 사용 전에 사용할 데이터베이스를 지정하는 문장
	'USE 데이터베이스_이름' 과 같이 사용
---

- 기본 형식
```python
SELECT 열_이름
	FROM 테이블_이름
	WHERE 조건식
	GROUP BY 열_이름
	HAVING 조건식
	ORDER BY 열_이름
	LIMIT 숫자
```

- SELECT와 FROM
```python
USE market_db; # market_db 데이터베이스를 선택

SELECT * FROM market_db.member;
SELECT * FROM member;
# 형식적으로 첫번째와 같이 사용하나 USE문으로 DB를 선택했으므로 동일
SELECT addr 주소 from member;
# 이런 식으로 열의 별칭을 지정해줄 수 있음.
```

- SELECT와 FROM과 WHERE
```python
SELECT 열_이름 FROM 테이블_이름 WHERE 조건식 #기본적인 문법
SELECT * FROM member WHERE mem_name = '블랙핑크';
# 열의 값에 해당하는 데이터 출력
```

```python
SELECT mem_name, height, mem_number FROM member WHERE BETWEEN 163 AND 165;
# 관계, 논리 연산자의 사용이 가능
# 범위값을 지정하는 경우 BETWEEN을 쓰는 것이 편리함
SELECT mem_name, addr FROM member WHERE addr IN('경기', '전남', '경남');
# 문자열의 경우 IN을 사용하여 간결하게 작성가능
SELECT * FROM member WHERE mem_name LIKE '우%';
# 문자열의 일부 글자를 검색하는 경우 LIKE 사용 %의 경우 뒷글자는 상관없다는 의미
SELECT * FROM member WHERE mem_name '__핑크';
# 글자와 매치하기 위 해 _(언더바) 사용, 글자 수에 맞춰 _도 사용해야함
```

- 서브 쿼리
```python
SELECT mem_name, height FROM member WHERE height > (SELECT height FROM member WHERE mem_name = '에이핑크');
# 에이핑크의 회원 키보다 더 큰 멤버의 이름과 키를 알고 싶은 경우 SELECT문을 중복하여 사용이 가능하다
```
---
### 03-2. DEEP하게 알아보는 SELECT문
ORDER BY, LIMIT, DISTINCT, GROUP BY, HAVING 등의 사용 

---

- ORDER BY 절
	결과의 값이나 개수에 대해서는 영향을 끼치지 않으나, 출력되는 순서를 조절
```python
SELECT mem_id, mem_name, debut_date FROM member ORDER BY debut_date;
# 데뷔 일자가 빠른 순서대로 출력
SELECT mem_id, mem_name, debut_date FROM member ORDER BY debut_date DESC;
# 내림차순 출력 시 Descending의 약자인 DESC를 맨 뒤 에 붙여줌
SEELCT mem_id, mem_name, debut_date, height FROM member WHERE height >= 164 ORDER BY height desc, debut_date asc;
# 이런식으로 WHERE 문과 함께 사용 가능, 정렬 기준은 먼저 작성한 문장 기준
```

- LIMIT 절
	출력하는 개수를 제한하는 문장
```python
SELECT * FROM member LIMIT 3;
# 회원 테이블을 0번째부터 3건까지만 조회
LIMIT 시작, 개수
# 위 문장은 0이 생략됨, 시작하는 행과 개수를 지정하여 사용
```

- DISTINCT 절
	조회된 결과에서 중복된 데이터를 1개만 남김
```python
SELECT DISTINCT addr FROM member;
# 중복되는 데이터들을 합쳐서 하나로 출력해줌
```

- GROUP BY 절
	그룹으로 묶어주는 역할을 하는 문장 (데이터를 그룹화)
```python
# GROUP BY와 함께 쓰이는 집계함수표
SUM()
# 합계를 구함
AVG()
# 평균을 구함
MIN()
# 최솟값을 구함
MAX()
# 최댓값을 구함
COUNT()
# 행의 개수를 구함
COUNT(DISTINCT)
# 행의 개수를 구함 (중복은 1개만 인정)

SELECT mem_id, SUM(amount) FROM buy GROUP BY mem_id;
# 멤버 별로 구매한 물품의 양 출력
SELECT mem_id, SUM(price * amount) FROM buy GROUP BY mem_id;
# 구매한 물품의 금액 총합 출력
SELECT AVG(amount) FROM buy;
# 평균 구매 개수
SELECT COUNT(*) FROM member;
# 이런 식으로 작성할 경우 NULL값까지 모두 있는것으로 계산하므로 따로 열을 지정해줘야함.
SELECT COUNT(phone1) FROM member;
# 이런 식.
```

- HAVING 절
	GROUP BY와 함께 사용하는 WHERE의 대체 절, GROUP BY 뒤에 나와야함.
```python
SELECT mem_id "회원 아이디", SUM(price * amount) "총 구매금액" FROM buy GROUP BY mem_id HAVING SUM(price * amount) > 1000;
# 총 구매금액을 집계하여 1000 이상인 회원만 구하고자 할때
```
---

### 03-3. 데이터 변경을 위한 SQL문
데이터를 입력/수정/삭제하는 기능을 가진 문법

- 데이터 입력 : INSERT
```python
USE market_db;
CREATE TABLE hongong1 (toy_id INT, toy_name CHAR(4), age INT);
INSERT INTO hongong1 VALUEs (1, '우디', 25);
# 테이블의 열의 개수에 따라 입력하는 데이터 개수도 같아야함
# 입력하고 싶지않은 열에는 NULL을 넣어줘야함
```

- 자동으로 증가하는 AUTO_INCREMENT
	열을 자동으로 1부터 차례대로 정의해줌 (꼭 기본키로 지정해야함!)
```python
CREATE TABLE hongong2 (toy_id INT AUTO_INCREMENT PRIMARY KEY, toy_name CHAR(4), age INT);

INSERT INTO hongong2 VALUES (NULL, '우디', 25);
INSERT INTO hongong2 VALUES (NULL, '제시', 21);
INSERT INTO hongong2 VALUES (NULL, '웬디', 27);
# AUTO_INCREMENT로 지정된 열은 NULL값을 넣어서 자동으로 숫자가 채워지게 함
SELECT LAST_INSERT_ID();
# 가장 마지막에 추가한 toy_id를 출력
ALTER TABLE hongong2 AUTO_INCREMENT = 1000;
# 시작값을 1000으로 지정
```

```python
# 시스템 변수
# AUTO_INCREMENT 등의 설정값이 주어진 함수를 의미하며, 이를 변경 가능함
SET @@auto_increment_increment = 3; # 증가값을 3으로 지정
```

```python
INSERT INTO hongong2 VALUES (NULL, '우디', 25);
INSERT INTO hongong2 VALUES (NULL, '제시', 21);
INSERT INTO hongong2 VALUES (NULL, '웬디', 27);

INSERT INTO hongong2 VALUES (NULL, '우디', 25), (NULL, '제시', 21);
# 이런 식으로 써도 동일함
```

- 다른 테이블의 데이터를 가져와 한번에 입력하는 INSERT INTO ~ SELECT
```python
CREATE TABLE city_popul (city_name CHAR(35), population INT);
INSERT INTO city_popul SELECT Name, Population FROM world.city;
```

- 데이터 수정 : UPDATE
	행 데이터를 수정하는 경우 사용
```python
UPDATE 테이블_이름
	SET 열1 = 값1, 열2 = 값2, etc..
	WHERE 조건;

USE market_db;
UPDATE city_popul
	SET city_name = '서울'
    WHERE city_name = 'Seoul';
SELECT * FROM city_popul WHERE city_name = '서울';
# WHERE을 사용하지 않으면 지정한 열의 모든 이름이 서울로 변경되므로 주의!
```

- 데이터 삭제 : DELETE
	테이블의 행 데이터를 삭제하는 경우
```python
DELETE FROM city_popul
	WHERE city_name LIKE 'New%'
# New가 들어가는 모든 city_name 삭제
DELETE FROM city_popul
	WHERE city_name LIKE 'New%'
	LIMIT 5;
# New가 들어가는 상위 5개 city_name 만 삭제
```