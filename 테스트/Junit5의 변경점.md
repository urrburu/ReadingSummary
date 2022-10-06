Junit 5 정식 버전이 나왔다. 테스트 코드를 작성하는 개발자 입장에서 JUnit 5를 보면 JUnit 4에 비해 중요한 차이점은 다음과 같다.

-   JUnit 4가 단일 jar였던 것에 비해, JUnit 5는 크게 JUnit Platform, JUnit Jupiter, JUnit Vintage 모듈로 구성되어 있다.
-   테스트 작성자를 위한 API 모듈과 테스트 실행을 위한 API가 분리되어 있다.

-   예를 들어, JUnit Jupiter는 테스트 코드 작성에 필요한 junit-jupiter-api 모듈과 테스트 실행을 위한 junit-jupiter-engine 모듈로 분리되어 있다.

-   자바 8 또는 그 이상 버전을 요구한다.


JUnit Platform은 테스트를 발견하고 테스트 계획을 생성하는 TestEngine 인터페이스를 정의하고 있다. Platform은 TestEngine을 통해서 테스트를 발견하고, 실행하고, 결과를 보고한다.  

TestEngine의 실제 구현체는 별도 모듈로 존재한다. 이 모듈 중 하나가 jupiter-engine이다. 이 모듈은 jupiter-api를 사용해서 작성한 테스트 코드를 발견하고 실행한다. Jupiter API는 JUnit 5에 새롭게 추가된 테스트 코드용 API로서, 개발자는 Jupiter API를 사용해서 테스트 코드를 작성할 수 있다.

기존에 JUnit 4 버전으로 작성한 테스트 코드를 실행할 때에는 vintage-engine 모듈을 사용한다.

만약 테스트 코드를 작성하기 위한 새로운 API를 창안하다면, 그 API에 알맞은 엔진 모듈을 함께 구현해서 제공하면 JUnit Platform 수정없이 새로 창안한 테스트 API를 실행하고 결과를 리포팅할 수 있게 된다.


**Jupiter API: 테스트 작성과 실행**

  

**테스트 코드 작성하기**

  

Jupiter API를 이용해서 테스트 코드를 작성하는 방법은 JUnit 4 버전과 크게 다르지 않다. 다음은 Jupiter API를 이용한 테스트 코드 작성예이다.

  
```Java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;


public class Test {

  

    void weak() {

        PasswordMeter meter = new PasswordMeter();

        **assertEquals**(PasswordLevel.WEAK, meter.meter("1234"));

    }

}
```


JUnit 4를 이용한 테스트 코드와 거의 동일한 것을 알 수 있다. @Test 애노테이션을 이용해서 테스트로 사용할 메서드를 지정하고, assertEquals() 메서드를 이용해서 기대하는 값과 실제 결과 값이 같은지 비교한다. 차이점은 @Test 애노테이션과 assertEquals() 메서드가 속한 패키지이다. Jupiter API의 패키지는 org.junit.jupiter.api로 시작한다.