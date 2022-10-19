ㅅㅂ 이걸 2019년 라이 블로그에서 처음 보고 이걸로 문제 풀 날이 올 줄은 몰랐다.

- Naive Algorithm : 가장 쉽게 생각할 수 있는, O(N²)의 전체 탐색 알고리즘입니다.
- Rabin & Karp Algorithm : 해시를 이용한 문자열 탐색 알고리즘입니다.
- Boyer & Moore Algorithm : 일반적으로 **가장 빠른 알고리즘**입니다.
- Suffix Tree / Array : 접미사 트리 / 배열이라고 불리는, 테이블을 이용한 알고리즘입니다.\

## **동작 원리**

KMP 알고리즘을 **'빠르게'** 문자열(패턴)을 찾는 알고리즘이라고 했습니다.

가장 기본적으로 생각할 수 있는 O(N²) 알고리즘이 왜 느릴까요?

문자 하나하나를 다 검색하기 때문에 효율이 저하되기 때문입니다.

이를 극복하기 위해, KMP 알고리즘에서는 **실패함수 (Fail function)** 라는 개념을 도입했습니다.

이 실패함수는, **'문자 매칭에 실패했을 때, 얼만큼 건너뛰어야 하는가?'를 알기 위해 사용됩니다. 

다른 말로는, **'문자 매칭에 실패하기 직전 상황에서, 접두사 / 접미사가 일치한 최대 길이'라고 풀이됩니다.

다음과 같은 상황을 보겠습니다.

![](https://blog.kakaocdn.net/dn/Rg3zA/btq0ECY6QVV/Xyvj6EjKr24okbhTdDVeo0/img.png)

전체 문자열 **'ababdababa'** 에서, 

패턴 문자열 **'ababa'** 를 찾으려고 합니다.

찾는 과정에서, 위의 그림과 같이 5번째 문자에서 불일치가 발생했습니다.

만약 모든 문자를 대상으로 찾는 알고리즘이라면 한 칸을 이동하겠지만,

KMP 알고리즘은 실패함수를 기반으로 아래와 같이 이동하겠다는 것입니다.

![](https://blog.kakaocdn.net/dn/FhauB/btq0BE4deKp/9DUz9yt7mPkq4Ey3KWEVl0/img.png)

어떻게 이러한 것이 가능할까요?

바로, **불일치가 발생한 부분에서 직전까지 일치했던 최대 접두사 / 접미사**가 'ab' 였기 때문입니다.

여기에서 최대 접두사 / 접미사라는 것은 본인 전체는 제외하기 때문에, 'abab'가 아닌 'ab'인 것입니다.

## **실패함수의 구현**

**'ababc'**를 기준으로 실패함수를 구해보겠습니다.

**2개의 인덱스 (i, j)** 를 활용해서 실패함수를 구할 것입니다.

인덱스를 증가시켜나가며 **두 문자가 일치하면 두 인덱스를 증가**시키고,

일치하지 않으면 **이전까지의 상황의 실패함수를 참고**해나가며, 참고할 값이 없으면 0 이 됩니다.

말로는 이해가 어려우니 아래의 예시를 보겠습니다.

![](https://blog.kakaocdn.net/dn/XuilP/btq0ApfBvr9/zOBKbmXe8D3JFKK3Js8JQ0/img.png)

실패함수의 첫번째 항목은 0 입니다.

위에서 자기 자신은 제외한다고 했으므로 항상 0 으로 생각하시면 됩니다.

현재 **i 와 j 인덱스에서 문자가 불일치**하므로, **이전까지의 실패함수를 참고**를 시도합니다.

하지만 이전 상태에서의 실패함수의 값은 0 입니다.

그러므로, i 번째 Fail 함수의 값도 다음과 같이 0 으로 갱신되고, i 가 증가합니다.

![](https://blog.kakaocdn.net/dn/S2hPv/btq0BFhIFh7/eihkLoPETDIOB4gDV5Kdm1/img.png)

이번엔 어떤가요?

i 와 j 에서 문자열이 일치하므로, 다음과 같이 j + 1의 값이 i번째 실패함수의 값으로 입력되며,

i와 j의 값이 모두 증가합니다.

![](https://blog.kakaocdn.net/dn/bhnQ9E/btq0A3QGNRk/6U8MQ2KkTON2pENcgXiLKk/img.png)

여기에서도 역시, 'b' 와 'b' 로 일치하기 때문에

i 번째 실패함수의 값은 j + 1인 2로 변경됩니다.

![](https://blog.kakaocdn.net/dn/WTSfS/btq0zZO1xmD/t7hB2ZpzGoDNk1wHp57FXk/img.png)

이제는 어떤가요?

'b'와 'c'로 불일치하기 때문에, 이전까지의 실패함수를 참고합니다.

자세하게는, 이전 인덱스의 실패함수의 값으로 j를 옮겨서, 다시 문자를 비교합니다.

지속해서 불일치한다면 j가 0이 될 때까지 반복합니다.

![](https://blog.kakaocdn.net/dn/cvHYT2/btq0BD5hIH4/hsFPOXldrEKqnB1zk9oD4K/img.png)

최종적으로 위와 같은 실패함수가 만들어졌습니다.

이 실패함수로 알수 있는 사실은 다음과 같습니다.

**- 'a'** 까지 일치하는 접두사/접미사 최대 길이 : **0 (없음)** 

**-** **'ab'** 까지 일치하는 접두사/접미사 최대 길이 : **0** (**없음)**

**- 'aba'** 까지 일치하는 접두사/접미사 최대 길이 : **1 ('a')**

**- 'abab'** 까지 일치하는 접두사/접미사 최대 길이 : **2** **('ab')**

**- 'ababc'** 까지 일치하는 접두사/접미사 최대 길이 : **0 (없음)**

이러한 사실을 이용해서, KMP 알고리즘을 구현할 수 있습니다.

## **KMP 알고리즘의 동작 원리**

아래와 같은 상황을 가정해보겠습니다.

전체 문자열은 **'ababdababc'** 이고,

찾는 문자열은 **'ababc'** 입니다.

![](https://blog.kakaocdn.net/dn/cwj6VL/btq0Ao11xOC/8ed1VFV5h0k86VctOGtVD0/img.png)

5번째 항목에서 **불일치**가 발생했습니다.

그러므로, 우리는 4번째의 실패함수 값을 참조합니다.

![](https://blog.kakaocdn.net/dn/cvHYT2/btq0BD5hIH4/hsFPOXldrEKqnB1zk9oD4K/img.png)

**'abab'** 까지는 실패함수가 **2** 라는 값임을 보여줍니다.

이 값이 무슨 값인지 기억하시나요?

**'abab'** 에서는 **'ab'**로 최대 접두사/접미사 길이가 2 라는 것입니다.

그러므로 이 값을 참고해서, 우리는 2 만큼 패턴을 이동할 수 있게 됩니다.

![](https://blog.kakaocdn.net/dn/bAtV8G/btq0Apmmbrm/LlCbaqj4GHe5XIqFj3r1w1/img.png)

이번엔 어떤가요?

6번째 항목에서 **불일치**가 발생했습니다.

위와 동일하게, 3번째의 실패함수 값을 참고하면 1 만큼 이동할 수 있겠습니다.

![](https://blog.kakaocdn.net/dn/T1TFx/btq0DrQ3W65/GJb0xD4HkOxC42fckPsst1/img.png)

역시 동일하게, 이 상황에서는 4번째 실패함수의 값을 참고합니다.

2 칸을 이동합니다.

![](https://blog.kakaocdn.net/dn/rIl2I/btq0AIeXyZg/I62BdLCUDuZEKnEowKiNF1/img.png)

드디어 원하는 패턴을 찾았습니다.