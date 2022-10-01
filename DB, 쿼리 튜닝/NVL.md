# [NULL 처리 함수 이해하기](http://wiki.gurubee.net/pages/viewpage.action?pageId=27427916)

Syntax : NVL ( expr1, expr2 )

NVL 은 NULL 값을 다른 값으로 대체하기 위한 함수이다.
expr1 이 NULL 이면 expr2 를 리턴하고, expr1 이 NOT NULL 이면 expr1 을 리턴한다.
expr1 과 expr2 는 여러 데이터 타입을 가질 수 있는데, 서로 데이터 타입이 다르면 expr1 의 데이터 타입으로 리턴한다.

```SQL
-- DATE 데이터 타입
SQL> SELECT NVL(c2, '01/JAN/2011') as "Date" FROM NULL_T
  2  WHERE c2 is null AND rownum <= 1 ;

Date
---------
01-JAN-11

SQL> ALTER SESSION SET nls_date_format = 'DD/MON/YYYY' ;

Session altered.

-- Charater 데이터 타입
SQL> SELECT NVL(c2, '01/JAN/2011') as "Date" FROM NULL_T
  2  WHERE c2 is null AND rownum <= 1 ;

Date
-----------
01/JAN/2011

SQL> SELECT NVL(c3, 'Null Data') AS "Character" FROM NULL_T
  2  WHERE c3 IS NULL AND ROWNUM <= 1 ;

Character
----------
Null Data

-- Number 데이터 타입
SQL> SELECT NVL(c3, 100) AS "Character" FROM NULL_T
  2  WHERE c3 IS NULL AND ROWNUM <= 1 ;

Character
----------
100

SQL> SELECT NVL(c4, 'Null Data') AS "Number" FROM null_t
  2  WHERE c4 IS NULL AND ROWNUM <= 1 ;
SELECT NVL(c4, 'Null Data') AS "Number" FROM null_t
               *
ERROR at line 1:
ORA-01722: invalid number


SQL> SELECT NVL(c4, '10') AS "Number" FROM NULL_T
  2  WHERE C4 IS NULL AND ROWNUM <= 1 ;

    Number
----------
        10

SQL> SELECT NVL(c4, 10) AS "Number" FROM NULL_T WHERE C4 IS NULL AND ROWNUM <= 1 ;

    Number
----------
        10
```
Syntax : NVL2 (expr1, expr2, expr3)

 - NVL2 는 expr1 컬럼의 데이터가 NULL or NOT NULL 여부에 따라 서로 다른 값을 추출 하고자 할 때 사용되는 함수이다. NVL2 를 통해 추출되는 데이터는 아래와 같다.
 	- expr1 -> NOT NULL -> Return: expr2
 	- expr1 -> NULL -> Return: expr3
 - NVL 은 expr1 컬럼의 NULL 인 데이터만 expr2 로 대체하고, NOT NULL 인 경우에는 expr1 컬럼의 데이터 자체를 조회하나, NVL2 는 expr1 컬럼이 NOT NULL 인 경우에도 컬럼의 데이터가 아닌 특정 값으로 대체를 할 수 있으므로 다방면으로 활용이 가능하다.
 - expr2 와 expr3 의 데이터 타입은 LONG 데이터 타입을 제외한 모든 데이터 타입은 가능하다.
	- expr2 (CHAR) vs. expr3 (CHAR) -> CHAR
	- expr2 (CHAR) vs. expr3 (NUMBER) -> CHAR
	- expr2 (NUMBER) vs. expr3 (NUMBER) -> NUMBER
	- expr2 (NUMBER) vs. expr3 (CHAR) -> ERROR (expr3 가 '100' 형태 제외)

```SQL

SQL> WITH null_t_temp AS ( SELECT c3 FROM NULL_T WHERE c3 IS NULL AND ROWNUM <= 1
    UNION ALL
    SELECT c3 FROM NULL_T WHERE c3 IS NOT NULL AND ROWNUM <= 1 )
    SELECT c3, NVL2(c3, 'Not Null', 'Null') AS Return_Data FROM null_t_temp ;


C3         RETURN_D
---------- --------
           Null
01-APR-00  Not Null


SQL> WITH null_t_temp AS ( SELECT c3
  2  FROM NULL_T
  3  WHERE c3 IS NULL AND ROWNUM <= 1
  4  UNION ALL
  5  SELECT c3
  6  FROM NULL_T
  7  WHERE c3 IS NOT NULL AND ROWNUM <= 1 )
  8  SELECT c3, NVL2(c3, 100, 100) AS Return_Data
  9  FROM null_t_temp ;

C3         RETURN_DATA
---------- -----------
                   100
01-APR-00          100

SQL> WITH null_t_temp AS ( SELECT c3
  2  FROM NULL_T
  3  WHERE c3 IS NULL AND ROWNUM <= 1
  4  UNION ALL
  5  SELECT c3
  6  FROM NULL_T
  7  WHERE c3 IS NOT NULL AND ROWNUM <= 1 )
  8  SELECT c3, NVL2(c3, 100, 'Null') AS Return_Data
  9  FROM null_t_temp ;
SELECT c3, NVL2(c3, 100, 'Null') AS Return_Data
                         *
ERROR at line 8:
ORA-01722: invalid number


SQL> WITH null_t_temp AS ( SELECT c3
  2  FROM NULL_T
  3  WHERE c3 IS NULL AND ROWNUM <= 1
  4  UNION ALL
  5  SELECT c3
  6  FROM NULL_T
  7  WHERE c3 IS NOT NULL AND ROWNUM <= 1 )
  8  SELECT c3, NVL2(c3, 100, '95') AS Return_Data
  9  FROM null_t_temp ;

C3         RETURN_DATA
---------- -----------
                    95
01-APR-00          100

SQL> WITH null_t_temp AS ( SELECT c3
  2  FROM NULL_T
  3  WHERE c3 IS NULL AND ROWNUM <= 1
  4  UNION ALL
  5  SELECT c3
  6  FROM NULL_T
  7  WHERE c3 IS NOT NULL AND ROWNUM <= 1 )
  8  SELECT c3, NVL2(c3, 'Not Null', 100) AS Return_Data
  9  FROM null_t_temp ;

C3         RETURN_D
---------- --------
           100
01-APR-00  Not Null

```

