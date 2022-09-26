이 네가지를 설명하려고 할때 가장 짜증나는건 이것이 REST API에만 국한된다고 설명하는 것이다. 아니 말이 되냐고. 원래 이 웹 리퀘스트가 먼저 있고 그 다음이 REST API인데 사람들은 자기가 많이 쓰는걸 기준으로 생각하나보다.

# GET
주로 조회를 할때 사용되며 캐싱을 한다고 할때 GET콜을 캐싱하는 것을 의미한다. 여러번 조회해도 같은 응답을 보장한다. 그러니까 캐싱을 한다. 

데이터를 조회하는 것이기 때문에 요청시에 Body 값과 Content-Type 가 비워져있다. 조회할 데이터에 대한 정보는 URL을 통해서 파라메터를 받고 있는 모습을 볼 수 있다. - 하지만 Body 값을 쓸 수도 있다. -> 다만 이 경우에 몇가지 문제가 있을 수 있다.  맨 아래에 이를 쓰고 있다.

데이터 조회에 성공한다면 Body 값에 데이터 값을 저장하여 성공 응답을 보낸다.
GET은 캐싱이 가능하여 같은 데이터를 한번 더 조회할 경우에 저장한 값을 사용하여 조회 속도가 빨라진다.

 캐싱이나 프록시 설계 중 일부틑 GET의 body값을 참조하지 않는 경우가 있다. 그렇기에 body를 사용하지 않는 경우 캐싱으로 인한 성능향상을 기대하긴 어렵다.

통용되는 디자인 철학을 벗어나는 문제또한 있다.

결정적인 문제는 지원하지 않는 클라이언트에 있다 Spring RestTemplate의 경우 GET매서드를 사용하느 ㄴ경우 body값을 보내지 않고 있다. 특정 클라이언트 혹은 서버에서는 이러한 입력이 완전히 무시된다. 
# POST

post call은 넷들 중 유일하게 멱등성을 갖지 않는 요청의 형태다. 여러번 콜을 했을 때 결과값이 같음을 보장하지 않는다. 아니 그보다 보장할 수 없다. 

데이터를 생성하는 것이기 때문에 요청시에 Body 값과 Content-Type 값을 작성해야한다. 해당 예시는 JSON을 통해서 작성된 예시이다.

URL을 통해서 데이터를 받지 않고, Body 값을 통해서 받는다.

데이터 조회에 성공한다면 Body 값에 저장한 데이터 값을 저장하여 성공 응답을 보낸다.

# PUT
PUT는 리소스를 생성 / 업데이트하기 위해 서버로 데이터를 보내는 데 사용됩니다.

-   PUT 요청은 idempotent 합니다.
-   동일한 PUT 요청을 여러 번 호출하면 항상 동일한 결과가 생성됩니다.


# DELETE

데이터를 삭제하는 것이기 때문에 요청시에 Body 값과 Content-Type 값이 비워져있다.

URL을 통해서 어떠한 데이터를 삭제할지 파라메터를 받는다.

데이터 삭제에 성공한다면 Body 값 없이 성공 응답만 보내게 된다.

# 이와 별개로 REST API, RESTful API가 뭔데?
*Representational State Transfer* 라는용어의 약자이다.  
자원을 `URI`로 표시하고 해당 자원의 상태를 주고 받는 것을 의미한다.

REST의 구성 요소는
-  자원(Resource): URI
-  행위(Verb): HTTP METHOD`
-  표현(Representations) 
으로 이루어져 있다.

## 2. REST의 특징

1.  Uniform Interface (유니폼 인터페이스)  
    HTTP 표준만 따른다면 어떤 언어 혹은 어떤 플랫폼에서 사용하여도 사용이 가능한 인터페이스 스타일이다.  
    안드로이드 플랫폼, IOS 플랫폼 등 특정 언어나 플랫폼에 종속되지 않고 사용이 가능하다.
2.  Stateless (상태 정보 유지 안함)  
    `Rest`는 상태 정보를 유지하지 않는다.  
    서버는 각각의 요청을 완전히 다른 것으로 인식하고 처리를 한다.  
    이전 요청이 다음 요청 처리에 연관이 되면 안된다.  
    
3.  Cacheable (캐시 가능)  
    `HTTP`의 기존 웹 표준을 그대로 사용하기 때문에 `HTTP`가 가진 캐싱 기능 적용이 가능하다.  
    
4.  Self-descriptiveness (자체 표현 구조)  
    `Rest API` 메시지만 보고도 쉽게 이해할 수 있는 자체 표현 구조로 되어있다.
5.  Client-Server  
    `Rest` 서버는 `API` 제공을 하고 클라이언트는 사용자 인증에 관련된 일들을 직접 관리한다.  
    자원이 있는 쪽을 Server라고 하고 자원을 요청하는 쪽이 Client가 된다.  
    서로간의 의존성이 줄어들기 때문에 역할이 확실하게 구분되어 개발해야할 내용들이 명확해진다.  
    
6.  Layerd System (계층화)  
    클라이언트는 `Rest API` 서버만 호출한다.  
    `Rest` 서버는 다중 계층으로 구성될수 있으면 로드 밸런싱, 암호화, 사용자 인증 등을 추가하여 구조상의  
    유연성을 둘 수 있다.

## 3. REST API란?

Rest 기반의 규칙들을 지켜서 설계된 API를 Rest API 혹은 Restful API이라고 한다.

## 4. REST API 설계 규칙

1.  URI는 정보의 자원을 표현해야한다.  
    자원의 이름은 동사보다는 명사를 사용한다.  
    URI는 자원을 표현하는데 중점을 두어야 하기 때문에 행위에 대한 표현이 들어가면 안된다.  
    (URI에 HTTP METHOD와 행위에대한 동사 표현이 들어가면 안된다.)
    
    ```null
    GET /users/321
    ```
    
2.  자원에 대한 행위는 HTTP METHOD로 표현한다. (GET, POST, PUT DELETE)  
    URI에 자원의 행위에 대한 표현이 들어가지 않는 대신 HTTP METHOD를 통해 대신한다.
    
    ```null
    GET /users/321 321 ID를 가진 유저 정보를 가져오기
    DELETE /users/321 321 ID를 가진 유저 정보를 삭제하기
    POST /users 유저를 생성하기
    ```
    
3.  슬래시 (/)는 계층 관계를 나타내는데 사용한다.
    
    ```null
    http://restapi.test.com/users/rooms
    http://restapi.test.com/users/board
    ```
    
4.  URI 마지막은 슬래시(/)를 사용하면 안된다.
    
    ```null
    http://restapi.test.com/users/rooms/ [X]
    http://restapi.test.com/users/rooms  [O]
    ```
    
5.  하이픈 (-)은 URI의 가독성을 높이는데 사용한다.  
    불가피하게 긴 URI를 사용하게 될 경우 하이픈을 이용하여 가독성을 높인다.
6.  언더바(_) 혹은 밑줄은 URI에 사용하지 않는다.  
    밑줄은 보기 어렵거나 밑줄 때문에 문자가 가려지기도 한다.  
    그래서 대신 언더바를 사용하지 않고 하이픈을 이용한다.
7.  URI는 경로에는 소문자가 적합하다.  
    URI 경로에는 대문자 사용을 피해야한다.  
    대소문자에 따라 각자 다른 리소스로 인식하기 때문이다.  
    RFC3986(URI 문법 형식)은 URI 스키마와 호스트를 제외하고는 대소문자를 구별하도록 규정하기 때문이다.
8.  파일 확장자는 URI에 포함하지 않는다.  
    REST API에서는 메시지 바디 내용의 포맷을 나타내기 위한 파일 확장자를 URI 안에 포함시키지 않는다.  
    Accept header를 사용한다.
9.  리소스 간의 관계를 표현하는 방법
    
    ```null
    GET : /users/{userid}/devices
    ```
    

## 5. RESTFUL API란?

위에서 설명했던 REST를 REST답게 쓰기 위한 방법이지만 누군가가 공식적으로 발표한 것이 아니고  
그저 개발자들이 비공식적으로 의견을 제시한 것이라 명확한 정의는 없다고 한다.  
하지만 RESTFUL의 목적은 이해하기 쉽고 사용하기 쉬운 REST API를 만드는 것이다.

CRUD                                       HTTP METHOD                                 URI

user들을 표시                             GET                                                  /users

user 하나만 표시                         GET                                               /users/:id

user를 생성                                POST                                            /users

user를 수정                                 PUT                                             /users:id

user를 삭제                               DELETE                                        /users:id

-   Restful 하지 못한 경우
-   CRUD 기능을 전부 POST METHOD로만 처리하는 API
-   URI에 자원과 id외 정보가 들어가는 경우
    
    ```null
     PUT /users/update-nickname [X]
     PUT /users/:id/nickname [O]
    ```