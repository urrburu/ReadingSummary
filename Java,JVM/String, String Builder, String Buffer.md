# String -> immutable
직관적이어서 가장 많이 사용할 듯한 위의 예제에서 "hello" 값을 가지고 있던 String 클래스의 참조변수 str이 가리키는 곳에 저장된 "hello"에 "world" 문자열을 더해 "hello world"로 변경한 것으로 착각할 수 있습니다.

하지만 기존에 "hello" 값이 들어가있던 String 클래스의 참조변수 str이 "hello world"라는 값을 가지고 있는 새로운 메모리영역을 가리키게 변경되고 **처음 선언했던 "hello"로 값이 할당되어 있던 메모리 영역은 Garbage로 남아있다가 GC(garbage collection)에 의해 사라지게 되는 것** 입니다. String 클래스는 불변하기 때문에 문자열을 수정하는 시점에 새로운 String 인스턴스가 생성된 것이지요.

위와 같이 String은 불변성을 가지기 때문에 변하지 않는 문자열을 자주 읽어들이는 경우 String을 사용해 주시면 좋은 성능을 기대할 수 있습니다. 그러나 **문자열 추가,수정,삭제 등의 연산이 빈번하게 발생**하는 알고리즘에 String 클래스를 사용하면 **힙 메모리(Heap)에 많은 임시 가비지(Garbage)가 생성**되어 힙메모리가 부족으로 어플리케이션 성능에 치명적인 영향을 끼치게 됩니다.

  

이를 해결하기 위해 Java에서는 가변(mutable)성을 가지는 StringBuffer / StringBuilder 클래스를 도입했습니다.

String 과는 반대로 StringBuffer/StringBuilder 는 가변성 가지기 때문에 .append() .delete() 등의 API를 이용하여 동일 객체내에서 문자열을 변경하는 것이 가능합니다. 따라서 문자열의 추가,수정,삭제가 빈번하게 발생할 경우라면 String 클래스가 아닌 StringBuffer/StringBuilder를 사용하셔야 합니다.

# String Builder -> 가변, 동기화 X

**StringBuilder**는 동기화를 지원하지 않기때문에 멀티쓰레드 환경에서 사용하는 것은 적합하지 않지만 동기화를 고려하지 않는 만큼 단일쓰레드에서의 성능은 StringBuffer 보다 뛰어납니다.

# String Buffer -> 가변, 동기화O
**StringBuffer**는 동기화 키워드를 지원하여 **멀티쓰레드 환경에서 안전하다는 점(thread-safe)** 입니다.  참고로 **String**도 불변성을 가지기때문에 마찬가지로  **멀티쓰레드 환경에서의 안정성(thread-safe)**을 가지고 있습니다.
