
### 04-1. MySQL의 데이터 형식
데이터의 내부적 구성을 이해하는 데이터의 형식

---
- 데이터 형식
	- 정수형
	1) TINYINT : 1Byte, -128 ~ 127
	2) SMALLINT : 2Byte, -32768 ~ 32767
	3) INT : 4Byte, -21억 ~ 21억
	4) BIGINT : 8Byte, 약 -900경 ~ 900경

		UNSIGNED : 바이트 단위를 반반으로 나눠 -,+로 표현하는 기존의 법칙을 +로만 표현하는 예약어

	- 문자형
	1) CHAR(개수) : 1~255Byte, 고정길이 문자형 (개수를 지정할 경우 몇글자를 써도 지정한 개수만큼 공간을 사용함.)
	2) VARCHAR(개수) : 1~16383Byte, 가변길이 문자형 (개수를 지정해도 글자를 쓴만큼 공간을 사용함)

		-> 공간효율성을 따지면 VARCHAR, 내부적 성능(속도)을 따지면 CHAR

	- 대량의 데이터 형식
		문자형인 CHAR와 VARCHAR는 각각 255, 16383자까지 지정 가능.
		더 큰 데이터를 저장하기 위해 사용 (사진이나 동영상 등)

	1) TEXT형식
		1) TEXT : 1 ~ 65535Byte
		2) LONGTEXT : 1 ~ 4294967295Byte
	2)  BLOB형식
		1) BLOB : 1 ~ 65535Byte
		2) LONGBLOB : 1 ~ 4294967295Byte
	
	```python
	# 사용 예시
	CREATE DATABASE netflix_db;
	USE netflix_db;

	CREATE TABLE movie (
	movie_id INT,
    movie_title VARCHAR(30),
    movie_director VARCHAR(20),
    movie_star VARCHAR(20),
    movie_script LONGTEXT,
    movie_film LONGBLOB
    );
```

	- 실수형
	1) FLOAT : 4Byte , 소수점 아래 7자리까지
	2) DOUBLE : 8Byte, 소수점 아래 15자리까지

	- 날짜형
	1) DATE : 3Byte, 날짜만 저장, YYYY-MM-DD 형식으로 사용
	2) TIME : 3Byte, 시간만 저장, HH:MM:SS 형식으로 사용
	3) DATETIME : 8Byte, 날짜 및 시간 저장, YYYY-MM-DD HH:MM:SS 형식으로 사용

- 변수의 사용
```python
# 일반적인 변수의 선언 및 입력 방식
SET @변수이름 = 변수의 값;
SELECT @변수이름;
```
	-> 종료 시에는 없어짐 (임시로 사용한다고 생각)
```python
# 사용 예
USE market_db;
SET @myVar1 = 5;
SET @myVar2 = 4.25;

SELECT @myVar1;
SELECT @myVar1 + @myVar2;

SET @txt = '가수 이름 ==> ';
SET @height = 166;
SELECT @txt '이름' , mem_name '그룹'FROM member WHERE height > @height;
```

 -> LIMIT 에는 문법상 오류로 사용할 수 없음
	 -> 이를 해결하기 위해 PREPARE과 EXECUTE 사용
```python
SET @count = 3;
PREPARE mySQL FROM 'SELECT mem_name, height FROM member ORDER BY height LIMIT ?';
EXECUTE mySQL USING @count;
```
---
- 데이터 형 변환
	직접 함수를 사용하는 '명시적 변환', 자연스럽게 변환하는 '임시적 변환'으로 나뉨
```python
CAST ( 값 AS 데이터_형식 [ (길이) ] )
CONVERT ( 값, 데이터_형식 [ (길이) ] )
# 형식만 다를 뿐, 동일한 기능
```

```python
# 사용 예
USE market_db;
SELECT AVG(price) AS '평균 가격' FROM buy;
SELECT CAST(AVG(price) AS SIGNED ) '평균 가격' FROM buy;
SELECT CAST('2022$12#12' AS DATE); # 날짜형으로 변
```