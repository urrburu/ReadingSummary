이런 기능이 있을 줄은 몰랐다. 근데 이거 써도 되나 하는 생각이 먼저 앞선다. 출처는 한국 데이터 산업 진흥원의 데이터 온에어에서 봤다. 

#### [문제]

마농군은 5000원을 가지고 과일을 사려고 합니다. 5000원 예산 금액으로 살 수 있는 모든 과일들의 조합을 만들어 낸 뒤, 가장 마음에 드는 조합을 선택해 장바구니에 담습니다. 과일의 가격정보 테이블은 과일코드(COD), 과일가격(AMT) 항목으로 구성돼 있습니다. <표 1>의 원본테이블로부터 <표 2>의 가능한 모든 과일의 조합을 구하는 문제입니다.
```SQL

리스트 1> 과일 가격 테이블WITH t AS  
(  
SELECT 1 cod, '배' nam, 5000 amt FROM dual  
UNION ALL SELECT 2, '사과', 3000 FROM dual  
UNION ALL SELECT 3, '딸기', 2000 FROM dual  
UNION ALL SELECT 4, '참외', 2000 FROM dual  
UNION ALL SELECT 5, '자두', 1000 FROM dual  
)  
SELECT * FROM t  
;
```

![tech_img1676.jpg](https://dataonair.or.kr/publishing/img/knowledge/tech_img1676.jpg)


#### [문제설명]

5000원의 금액 예산을 모두 소진하는 과일 장바구니 조합을 모두 구하는 문제입니다. 5000원짜리 배 1개를 담을 수도 있고, 1000원짜리 자두 5개를 담을 수도 있습니다. 과일의 개수가 1개일 경우엔 그 개수를 별도로 표기하지 않고 과일명만 표기하며, 여러개인 경우에는 과일명 옆에 개수를 곱하기(*) 기호와 함께 표시합니다. 한가지 과일뿐만 아니라, 여러 과일을 섞어 담을 수도 있습니다. 여러 과일이 섞여 담겨질 경우에는 더하기(+)기호로 연결해서 보여줍니다. 과일의 종류와 개수를 <표 2>의 NAMS 항목처럼 표시하고, AMTS 항목에는 과일의 금액과 개수를 이용해 총액을 구하는 계산식을 표시합니다. 이렇게 해서 총액이 5000 원이 되는 모든 과일 장바구니 조합을 만들어 내는 문제입니다.



















그런데 정답을 보고 해설을 봐도 처음부터 짜라고 하면 못 할거 같다......
여러번 이걸 다시 볼 필요가 있을거 같다.
#### [정답]

문제를 스스로 해결해봤나요 이제 정답을 알아보겠습니다.

  
```SQL

WITH t AS  
( /* 원본 테이블 */ )  
, t1(cod, nam, amt, cnt, nams, amts, tot) AS  
(  
SELECT cod, nam, amt  
, 1 cnt  
, CAST(nam AS VARCHAR2(4000)) nams  
, CAST(amt AS VARCHAR2(4000)) amts  
, amt tot  
FROM t  
WHERE amt < = 5000  
UNION ALL  
SELECT c.cod, c.nam, c.amt  
, DECODE(p.cod, c.cod, p.cnt+1, 1) cnt  
, DECODE(p.cod, c.cod,  
REGEXP_REPLACE(p.nams,'[*][0-9]+$')||'*'||(p.cnt+1)  
, p.nams||'+'||c.nam) nams  
, DECODE(p.cod, c.cod,  
REGEXP_REPLACE(p.amts,'[*][0-9]+$')||'*'||(p.cnt+1)  
, p.amts||'+'||c.amt) amts  
, p.tot + c.amt tot  
FROM t c  
, t1 p  
WHERE p.tot + c.amt < = 5000  
AND p.cod < = c.cod  
)  


SELECT nams, amts  
FROM t1  
WHERE tot = 5000  
;

```

정답코드는 크게 T 테이블과 T1테이블의 정의로 이루어진다. 

여기서 주의할 점은
WITH절은 복잡한 SQL에서 동일 블록에 대해 반복적으로 SQL문을 사용하는 경우 그 블록에 이름을 부여하여 재사용 할 수 있게 함으로서 쿼리 성능을 높일 수 있는데 WITH절을 이용하여 미리 이름을 부여해서 Query Block을 만들 수 있습니다. 자주 실행되는 경우 한번만 Parsing되고 Plan 계획이 수립되므로 쿼리의 성능향상에 도움이 됩니다.


#### [해설]

이번 문제는 가능한 모든 경우의 조합을 구하는 문제입니다. 경우의 수 조합은 어떻게 구현할 수 있을까요 계층구조 쿼리를 이용해 경우의 수를 구하는 간단한 예제를 먼저 살펴보겠습니다.

  

<리스트 3> 경우의 수 구하기WITH t AS  
(  
SELECT 'A' cd FROM dual  
UNION ALL SELECT 'B' FROM dual  
UNION ALL SELECT 'C' FROM dual  
)  
SELECT SUBSTR(SYS_CONNECT_BY_PATH(cd, '-'), 2) path  
FROM t  
CONNECT BY PRIOR cd < cd  
;  

  
  
![tech_img1677.jpg](https://dataonair.or.kr/publishing/img/knowledge/tech_img1677.jpg)  
  

<리스트 3>의 쿼리를 수행하여 <표 3>의 결과를 얻었습니다. <표 3> A, B, C 3개의 코드로 만들 수 있는 가능한 모든 조합을 나타냅니다. CONNECT BY PRIOR cd < cd 조건으로 상위의 코드보다 큰 코드를 찾아나가면서 SYS_CONNECT_BY_PATH를 이용해 계층구조를 만드는 형태입니다. 정말 간결한 구문으로 가능한 모든 경우의 조합을 매우 쉽게 구할 수 있습니다. 자 이 방법을 이번 문제에 적용을 해볼까요

  

<리스트 4> ERROR 발생SELECT SUBSTR(SYS_CONNECT_BY_PATH(cod, '-'), 2) path  
FROM t  
CONNECT BY PRIOR cod < = cod  
;  

  
  
![tech_img1678.jpg](https://dataonair.or.kr/publishing/img/knowledge/tech_img1678.jpg)  
  

<리스트 4>의 쿼리를 수행하여 <표 4>의 ERROR가 발생됐습니다. <리스트 3>에서는 크다(< ) 조건으로 계층구조를 전개했습니다. 그러나 <표 2> 의 자두 5개 처럼 같은 코드가 연결되는 경우도 포함해야 합니다. 그래서 이퀄(=) 조건을 추가해서 크거나 같다(<=) 조건을 사용한 것이구요. 이 조건 때문에 <표 4>의 ERROR가 발생한 것입니다. 같은 코드가 반복되는 것을 허용하지 않네요. 이는 사전에 에러를 발생시켜 무한반복 될 가능성을 제거하기 위함입니다. 그렇다면 이 문제를 어떻게 해결하면 될까요. 금액 조건을 추가하여 무한반복을 중간에 멈출 수 있도록 한다면 가능할까요 하지만 이 마저도 금액 합계를 구하는 부분이 쉽게 해결될 것 같지 않습니다.

  
  

#### 무한반복 가능성 문제와 금액을 계산하는 문제

이 두가지 문제를 해결할 다른 방법을 모색해야 합니다. 오라클 11G부터는 계층쿼리의 또 다른 구현방법이 제공됩니다. 바로 Recursive SQL(재귀 쿼리)입니다.

  

<리스트 5> Recursive SQLWITH t AS  
( /* 원본 테이블 */ )  
, t1(cod, nam, amt, nams, tot) AS  
(  
SELECT cod, nam, amt  
, CAST(nam AS VARCHAR2(4000)) nams  
, amt tot  
FROM t  
WHERE amt < = 5000 -- 시작조건  
UNION ALL  
SELECT c.cod, c.nam, c.amt  
, p.nams||'+'||c.nam nams -- 과일 연결  
, p.tot + c.amt tot -- 금액 합계  
FROM t c -- 원본집합(자식)  
, t1 p -- 재귀호출(부모)  
WHERE p.tot + c.amt < = 5000 -- 계층전개 조건  
AND p.cod < = c.cod -- 계층전개 조건  
)  
SELECT *  
FROM t1  
WHERE tot = 5000 -- 최종조건  
;  

  
  
![tech_img1679.jpg](https://dataonair.or.kr/publishing/img/knowledge/tech_img1679.jpg)  
  

<리스트 5>의 쿼리를 수행하여 <표 5>의 결과를 얻었습니다. WITH문으로 정의된 집합 T1이 WITH 구문 안의 FROM절에서 참조되고 있습니다. 즉, 재귀적으로 자신을 참조하는 형태의 SQL 구문인 것이죠. 구문을 살펴보면 두 개의 SQL이 UNION으로 연결되어 있습니다. UNION 위쪽 SQL은 계층쿼리의 시작지점을 의미합니다. UNION 아래쪽 SQL은 자기 자신(T1)과 원본(T)를 조인합니다. T1에는 계층쿼리의 시작자료가 있고, 이 시작지점 자료를 기준으로 다음 레벨의 자식을 연결합니다. 이렇게 연결된 자식은 다시 또 부모가 되어 다음 자식에게 연결되는 구조입니다.  
  
Recursive SQL의 개괄적인 구문에 대해 소개했습니다. 이제 <리스트 5>의 구문을 부분별로 나눠 상세히 설명하겠습니다.  
  
WITH t1(cod, nam, amt, nams, tot) AS  
  
Recursive SQL은 WITH문으로 구성되며 컬럼명을 반드시 기술해 줘야만 합니다. Recursive SQL은 UNION으로 구성되며 첫 번째 SQL은 계층전개의 시작지점의 집합을 조회합니다.  
  
SELECT cod, nam, amt  
, CAST(nam AS VARCHAR2(4000)) nams  
, amt tot  
FROM t  
WHERE amt < = 5000  
  
금액이 5000원 이하인 과일을 시작조건으로 조회합니다. nams항목은 nam항목을 연결하기 위한 항목으로 문자열 연결을 위해 크기를 크게 지정합니다. tot항목은 금액을 합산해 나가기 위한 항목입니다. UNION 하단의 두 번째 SQL은 계층전개의 전개조건입니다.  
  
SELECT c.cod, c.nam, c.amt  
, p.nams||'+'||c.nam nams  
, p.tot + c.amt tot  
FROM t c  
, t1 p  
WHERE p.tot + c.amt < = 5000  
AND p.cod < = c.cod  
  
T1 을 재귀적으로 참조하여 부모(Parent)역할을 하게 하고 원본 집합인 T를 자식(Child)역할을 하게 해서 부모코드보다 크거나 같은 자식코드 조건을 만족하면서 부모의 금액 합계에 자식 금액을 합산한 값이 5000보다 작은 조건을 만족하는 자료를 조회합니다.  
  
이렇게 조회된 자료는 2레벨이 되고 2레벨은 다시 재귀적으로 참조되어 3레벨을 만들어 내는 구조입니다. 이때 5000보다 작다 조건처럼 무한반복을 멈추게끔 하는 조건이 반드시 필요합니다. 마지막 WITH문을 이용해 최종 결과를 조회합니다.  
  
SELECT *  
FROM t1  
WHERE tot = 5000  
;  
  
이 때 계층 구조 전개 되었던 금액 합계 5000원보다 작았던 모든 조합들 중에서 금액합계가 5000원 인것만 조회합니다. <표 5>의 결과 10건이 도출되었습니다.  
  
<표 5>의 결과 중 참외+자두+자두+자두는 참외+자두*3 형태로 바꿔 표현해야 하는데요. 같은 과일의 건수를 구하기 위한 항목 CNT를 추가하고 정규식(Regular Expression)을 이용해 텍스트를 변형하겠습니다.

  

<리스트 5> Recursive SQL<리스트 6> 표현식 변경WITH t AS  
( /* 원본 테이블 */ )  
, t1(cod, nam, amt, cnt, nams, tot) AS  
(  
SELECT cod, nam, amt  
, 1 cnt  
, CAST(nam AS VARCHAR2(4000)) nams  
, amt tot  
FROM t  
WHERE amt < = 5000  
UNION ALL  
SELECT c.cod, c.nam, c.amt  
, DECODE(p.cod, c.cod, p.cnt+1, 1) cnt  
, DECODE(p.cod, c.cod,  
REGEXP_REPLACE(p.nams,'[*][0-9]+$')||'*'||(p.cnt+1)  
, p.nams||'+'||c.nam) nams  
, p.tot + c.amt tot  
FROM t c  
, t1 p  
WHERE p.tot + c.amt < = 5000  
AND p.cod < = c.cod  
)  
SELECT nams  
FROM t1  
WHERE tot = 5000  
;  

  
  

최초 연속 cnt 는 1이며,  
, 1 cnt  
  
자식의 카운트는 부모와 코드가 같으면 기존건수+1 다르면 다시 1로 초기화 됩니다.  
, DECODE(p.cod, c.cod, p.cnt+1, 1) cnt  
  
과일 연결(nams)는 부모와 코드가 같으면 이전 nams 의 마지막 “*수량” 부분을 없애고 기존수량에 1을 더하여 “*”와 함께 붙여줍니다.  
REGEXP_REPLACE(p.nams,'[*][0-9]+$')||'*'||(p.cnt+1)  
  
부모와 코드가 다르면 이전 nams 에 +새과일명을 붙여줍니다.  
, p.nams||'+'||c.nam) nams  
  
amts 항목도 nams 와 마찬가지 방법으로 만들어 주면 <리스트 2>의 정답쿼리가 완성됩니다.  
이번 퀴즈의 풀이에 사용된 TIP 을 정리해볼까요.  
  
1. 계층구조 전개를 이용한 모든 가능한 경우의 수 조합 구하기  
2. Recursive SQL 을 이용한 계층 구조 전개 방법 이해하기  
3. 정규식을 이용한 문자열 변형


정규식, 계층구조 전개를 이용하는 복잡한 방법이었다고 생각한다. 이에 익숙해지도록 노력해야겠다.