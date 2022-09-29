Long 타입으로도 연산이 안 되는 아주 큰 수를 다루고 저장할때, String 타입이 근본이 되는 빅-인티저 타입을 이용해 연산이 가능하다.

## 빅-인티저 선언 

```Java
BigInteger bigNumber = new BigInteger("10000");

```
BigInteger은 java.math안에 있으며 위와 같이 선언하시면 됩니다. 특이한 점은 BigInteger을 초기화하기 위해서는 문자열을 인자 값으로 넘겨주어야 한다는 점입니다. BigInteger가 문자열로 되어 있기 때문입니다.

### BigInteger 계산

```java
BigInteger bigNumber1 = new BigInteger("100000");
BigInteger bigNumber2 = new BigInteger("10000");
		
System.out.println("덧셈(+) :" +bigNumber1.add(bigNumber2));
System.out.println("뺄셈(-) :" +bigNumber1.subtract(bigNumber2));
System.out.println("곱셈(*) :" +bigNumber1.multiply(bigNumber2));
System.out.println("나눗셈(/) :" +bigNumber1.divide(bigNumber2));
System.out.println("나머지(%) :" +bigNumber1.remainder(bigNumber2));
```

BigInteger은 문자열이기에 사칙연산이 안됩니다. 그렇기에 BigIntger 내부의 숫자를 계산하기 위해서는 BigIntger 클래스 내부에 있는 메서드를 사용해야 합니다.

### BigInteger 형 변환

```java
BigInteger bigNumber = BigInteger.valueOf(100000); //int -> BigIntger

int int_bigNum = bigNumber.intValue(); //BigIntger -> int
long long_bigNum = bigNumber.longValue(); //BigIntger -> long
float float_bigNum = bigNumber.floatValue(); //BigIntger -> float
double double_bigNum = bigNumber.doubleValue(); //BigIntger -> double
String String_bigNum = bigNumber.toString(); //BigIntger -> String
```

BigIntger 클래스를 기본 타입으로 형 변환을 해야 할 경우에는 위와 같이 하시면 됩니다.

### BigIntger 두 수 비교

```java
BigInteger bigNumber1 = new BigInteger("100000");
BigInteger bigNumber2 = new BigInteger("1000000");
		
//두 수 비교 compareTo 맞으면 0   틀리면 -1
int compare = bigNumber1.compareTo(bigNumber2);
System.out.println(compare);
```

BigInteger의 값을 비교할 때는 compareTo라는 메서드를 사용합니다.