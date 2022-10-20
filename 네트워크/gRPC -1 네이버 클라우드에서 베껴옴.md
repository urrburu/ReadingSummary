***gRPC(Google Remote Procedure Call**) **:** Google에서 만든 RPC

# 등장배경

> **_1. Server-Client Model_**

PC(Personal Computer)의 개념이 없던 시절, 프로그램은 하나의 메인 프레임에서 동작하는 Monolothic 구조로 설계되었습니다. 이때까지만 해도 모든 기능들이 한 공간에서 구동되다보니 지금처럼 네트워크 통신이 크게 중요하진 않았습니다.

기술 발전에 따라 소형 컴퓨터 장비들(PC, 워크스테이션 서버 등)이 등장하게 되고, 기업 입장에선 매우 고가인 메인 프레임워크를 비교적 저가의 워크스테이션 서버로 대체하고 싶어했습니다. 하지만 메인 프레임워크의 초고사양 서비스를 워크스테이션 서버에서 그대로 제공하기엔 한계가 있었습니다.

이 때문에 메인 프레임워크의 기능을 워크스테이션 서버로 분산시키고, 네트워크 연결로 서비스하는 방식을 채택하게 됩니다. 흔히 말하는 Server-Client Model입니다. 이처럼 서버 간 혹은 서버와 개인 PC 간 네트워크 연결/통신이 중요해지면서 OSI 7 layer, TCP/IP 등 네트워크 계층 구조가 정의되고 발전하기 시작합니다.

> _2._ **_IPC_**

프로세스들은 기본적으로 상호독립적입니다. 메모리를 공유하지 않기 때문에 각자 자신의 일만 하며 서로 간섭을 하지 않아요. 하지만 필요에 따라 프로세스간 정보를 교환해야하는 경우가 있겠죠? 이때 별도 수단을이용하여 프로세스 통신하는 방법론을 통칭하여 IPC(Inter Process Communication) 라고 합니다.

_1) Socket_

IPC 기법에는 공유 메모리, PIPE, 메시지 큐 등 여러가지가 있지만 이 중 소켓(socket)을 살펴보겠습니다.

socket이란, 앞서 언급한 OSI 7 layer 구조의 Application Layer(L7)에서 Transport Port(L4)의 TCP 또는 UDP를 이용하기 위한 수단입니다. 일종의 창구 역할을 하는 것이죠. 목적지와의 통신이 컴퓨터 내부가 아니라 온라인 범위에서 이루어지기 때문에 네트워크 간 통신이라고 구분하기도 하지만, 실질적으로는 로컬 컴퓨터의 프로세스와 원격지 컴퓨터의 프로세스가 IPC 통신을 하는 것입니다.

소켓은 대부분의 언어에서 API 형태로 제공하는 편리함 때문에 지금도 많이 사용되고 있지만, 일련의 통신 과정을 직접 구현하므로 통신 관련 장애를 처리하는 것은 고스란히 개발자의 몫이 됩니다. 서비스가 고도화될 수록 수백 수천가지 데이터가 돌아다니게 될텐데, 이에 따라 data formatting 을 하는 것도 점점 어려워지게 되죠.

_2) RPC (Remote Procedure Call)_

이런 소켓의 한계에서 RPC(Remote Procedure Call)라는 기술이 등장합니다. 이름 그대로 네트워크로 연결된 서버 상의 프로시저(함수, 메서드 등)를 원격으로 호출할 수 있는 기능입니다. 네트워크 통신을 위한 작업 하나하나 챙기기 귀찮으니. 통신이나 call 방식에 신경쓰지 않고 원격지의 자원을 내 것처럼 사용할 수 있죠. IDL(Interface Definication Language) 기반으로 다양한 언어를 가진 환경에서도 쉽게 확장이 가능하며, 인터페이스 협업에도 용이하다는 장점이 있습니다.

◆ 지원 언어 : C++, Java, Python, Ruby, Node.js, C#, Go, PHP, Objective-C …

RPC의 핵심 개념은 ‘Stub(스텁)’이라는 것인데요. 서버와 클라이언트는 서로 다른 주소 공간을 사용 하므로, 함수 호출에 사용된 매개 변수를 꼭 변환해줘야 합니다. 안그러면 메모리 매개 변수에 대한 포인터가 다른 데이터를 가리키게 될 테니까요. 이 변환을 담당하는게 스텁입니다.

client stub은 함수 호출에 사용된 파라미터의 변환(Marshalling, 마샬링) 및 함수 실행 후 서버에서 전달 된 결과의 변환을, server stub은 클라이언트가 전달한 매개 변수의 역변환(Unmarshalling, 언마샬링) 및 함수 실행 결과 변환을 담당하게 됩니다. 이런 Stub을 이용한 기본적인 RPC 통신 과정을 살펴보겠습니다.

![](https://miro.medium.com/max/582/0*KkT_CXgPiSLd1tF-)

RPC 구조 (출처 :[https://middlewares.files.wordpress.com/2008/04/17.jpg](https://middlewares.files.wordpress.com/2008/04/17.jpg))

① IDL(Interface Definition Language)을 사용하여 호출 규약 정의합니다.

- 함수명, 인자, 반환값에 대한 데이터형이 정의된 IDL 파일을 rpcgen으로 컴파일하면 stub code가 자동으로 생성됩니다.

② Stub Code에 명시된 함수는 원시코드의 형태로, 상세 기능은 server에서 구현됩니다.

- 만들어진 stub 코드는 클라이언트/서버에 함께 빌드합니다.

③ client에서 stub 에 정의된 함수를 사용할 때,

④ client stub은 RPC runtime을 통해 함수 호출하고

⑤ server는 수신된 procedure 호출에 대한 처리 후 결과 값을 반환합니다.

⑥ 최종적으로 Client는 Server의 결과 값을 반환받고, 함수를 Local에 있는 것 처럼 사용할 수 있습니다.

> RPC는 원격지의 것을 끌어다 로컬처럼 사용하는데다, IDL 기반이라 확장성도 좋고, 개발자도 편해지고 요즘같은 분산 프로그래밍 환경에선 큰 이점을 제공하죠. 그런데 왜 쉽게 찾아볼 수가 없었을까요?

RPC는 상당히 획기적인 방법론이었으며, 분산 환경의 등장에 따라 함께 발전해 온 오래된 기술입니다. 따라서 구현체도 CORBA, RMI 등 여러가지가 있었는데요. 이들 모두 로컬에서 제공하는 빠른 속도, 가용성 등을 분산 프로그래밍에서도 제공하고 있다고 홍보를 했지만, 정작 구현의 어려움/지원 기능의 한계 등으로 제대로 활용되지 못했습니다. 그렇게 RPC 프로젝트도 점차 뒷길로 가게되며 데이터 통신을 우리에게 익숙한 Web을 활용해보려는 시도로 이어졌고, 이 자리를 REST가 차지하게됩니다.

3) REST (REpresentational State Transfer)_

REST는 HTTP/1.1 기반으로 URI를 통해 모든 자원(Resource)을 명시하고 HTTP Method를 통해 처리하는 아키텍쳐 입니다. 자원 그 자체를 표현하기에 직관적이고, HTTP를 그대로 계승하였기에 별도 작업 없이도 쉽게 사용할 수 있다는 장점으로 현대에 매우 보편화되어있죠. 하지만 REST에도 한계는 존재합니다. REST는 일종의 스타일이지 표준이 아니기 때문에 parameter와 응답 값이 명시적이지 않아요. 또한 HTTP 메소드의 형태가 제한적이기 때문에 세부 기능 구현에는 제약이 있습니다.

덧붙여, 웹 데이터 전달 format으로 xml, json을 많이 사용하는데요.

XML은 html과 같이 tag 기반이지만 미리 정의된 태그가 없어(no pre-defined tags) 높은 확장성을 인정 받아 이기종간 데이터 전송의 표준이었으나, 다소 복잡하고 비효율적인 데이터 구조탓에 속도가 느리다는 단점이 있었습니다. 이런 효율 문제를 JSON이 간결한 Key-Value 구조 기반으로 해결하는 듯 하였으나, 제공되는 자료형의 한계로 파싱 후 추가 형변환이 필요한 경우가 많아졌습니다. 또한 두 타입 모두 string 기반이라 사람이 읽기 편하다는 장점은 있으나, 바꿔 말하면 데이터 전송 및 처리를 위해선 별도의 Serialization이 필요하다는 것을 의미합니다.

# **_gRPC (google Remote Procedure Call)_**

**gRPC**는 google 사에서 개발한 오픈소스 RPC(Remote Procedure Call) 프레임워크입니다. 이전까지는 RPC 기능은 지원하지 않고, 메세지(JSON 등)을 Serialize할 수 있는 프레임워크인 PB(Protocol Buffer, 프로토콜 버퍼)만을 제공해왔는데, PB 기반 Serizlaizer에 HTTP/2를 결합하여 RPC 프레임워크를 탄생시킨 것이죠.

REST와 비교했을 때 기반 기술이 다르기에 특징도 많이 다르지만, 가장 두드러진 차이점은 HTTP/2를 사용한다는 것과 프로토콜 버퍼로 데이터를 전달한다는 점입니다. 그렇기에 Proto File만 배포하면 환경과 프로그램 언어에 구애받지 않고 서로 간의 데이터 통신이 가능합니다.

> **_1. HTTP/2_**

http/1.1은 기본적으로 클라이언트의 요청이 올때만 서버가 응답을 하는 구조로 매 요청마다 connection을 생성해야만 합니다. cookie 등 많은 메타 정보들을 저장하는 무거운 header가 요청마다 중복 전달되어 비효율적이고 느린 속도를 보여주었습니다. 이에 http/2에서는 한 connection으로 동시에 여러 개 메시지를 주고 받으며, header를 압축하여 중복 제거 후 전달하기에 version1에 비해 훨씬 효율적입니다. 또한, 필요 시 클라이언트 요청 없이도 서버가 리소스를 전달할 수도 있기 때문에 클라이언트 요청을 최소화 할 수 있습니다.

![](https://miro.medium.com/max/271/0*RbY-0HC7iCxiAJCI)

HTTP/1.1 vs HTTP/2 connection 구조 (그림 출처 :[https://jumpic.com/hashtag.php?q=http2](https://jumpic.com/hashtag.php?q=http2))

> _2._ **_ProtoBuf (Protocol Buffer, 프로토콜 버퍼)_**

Protocol Buffer는 google 사에서 개발한 구조화된 데이터를 직렬화(Serialization)하는 기법입니다.

직렬화란, 데이터 표현을 바이트 단위로 변환하는 작업을 의미합니다. 아래 예제처럼 같은 정보를 저장해도 text 기반인 json인 경우 82 byte가 소요되는데 반해, 직렬화 된 protocol buffer는 필드 번호, 필드 유형 등을 1byte로 받아서 식별하고, 주어진 length 만큼만 읽도록 하여 단지 33 byte만 필요하게 됩니다.

![](https://miro.medium.com/max/700/0*EqWBu3VDbav3svJk)

Json vs Protocol Buffer 데이터 비교 (그림 출처 :[https://martin.kleppmann.com/2012/12/05/schema-evolution-in-avro-protocol-buffers-thrift.html](https://martin.kleppmann.com/2012/12/05/schema-evolution-in-avro-protocol-buffers-thrift.html))

> **_3. Proto File_**

자세한 Encoding/Decoding 원리는 Protocol Buffer의 기본 정보를 명세하는 Proto File의 구성 요소를 살펴본 후 다음 회차에서 다뤄보겠습니다.

(예시 대부분은 google에서 제공하는 Protocol Buffer 공식 docs를 참고하였습니다.)

_1)Message and Field_

Proto File에서는 주고 받는 data들을 message 라는 것으로 정의합니다. 이 메시지는 여러가지 타입의 필드로 구성됩니다. 아래 예시로 query, page_number, result_per_page 라는 필드를 가지는 SearchRequest 라는 메시지를 정의해보았습니다.

![](https://miro.medium.com/max/244/0*K68cleSpziBsMrZJ)

message 예제 (proto3)

**▶ Naming**

message 이름은 CamelCase 형태, field 이름은 under_bar 형태로 사용할 것을 권장하고 있습니다.(필수는 아닙니다.) 유의할 것은 field 이름은 숫자로 시작할 수 없다는 점인데요. 숫자를 표기해야 할 경우 꼭 문자 뒤에 표기해주어야합니다.

ex) query_1 (o) / 1_query (x)

**▶ Field Tag (= Field number)**

메시지에 정의된 필드들은 각각 고유한 번호를 가지게되고 이는 Enconding 이후 binary data에서 필드를 식별하는데 사용됩니다. Field Tag는 최소 1, 최대 536,870,911(=229–1) 로 지정 가능하며, 19000 ~ 19999는 프로토콜 버퍼 구현을 위해 reserved 된 값이므로 사용할 수 없습니다.

필드 번호가 1~15일 때는 1byte, 16~2047은 2byte를 Tag로 가져가게 되는데요. 때문에 자주 호출되는 필드에 대해선 1~15로 지정해두는 것이 좋습니다. 그런데 여기서 한 가지 의문이 드는데요, 1byte는 8bit라서 255까지 표현이 가능한데 필드 번호로 쓰일땐 왜 1~15 까지만 표현될까요? 이 이유는 다음 회차 포스팅의 [Protocol Buffer Encoding] 부분에서 알아보도록 하겠습니다.

**▶ proto2 VS proto3**

위 예제에서는 첫 줄에 syntax = “proto3”을 지정해줌으로써 proto version 3의 규약을 따르겠다고 선언했습니다. 이를 명시하지 않으면 default로 version2 문법을 따르게 됩니다. 아래와 같이 지원 언어도 다르지만, message 작성 시 field rule 지정 등 문법에도 차이가 나타납니다.

- Proto2 지원 언어 : C++, Java, Python, Go

- Proto3 지원 언어 : C++, Java, Python, Go, Ruby, Objectice-C, C#, JavaScript, PHP, Dart

**▶ Proto File Field Rule**

- required : 필수로 가져야 할 필드 (only use proto2)

- optional : 해당 필드를 가지지 않거나 하나만 가짐 (only use proto2)

- repeated : 임의 반복 가능한 필드 (번호 및 값의 순서는 보존)

* [packed=true] 옵션 : key-value 쌍 형태에서 value만 반복

![](https://miro.medium.com/max/317/0*hs5wKx58ZMnfnoAw)

message 예제 (proto2)

위 예시처럼 proto2의 경우 required, optional를 필드 별로 꼭 명시해주어야 합니다. proto3에선 required, optional은 사라지고, repeated 만 사용됩니다. proto2도 계속 기술지원이 되고 있으나, 지원 언어 및 새로운 기능 지원을 위해 proto3을 사용할 것을 권장합니다.

이 글에서도 proto3에 맞추어 기술하도록 하겠습니다.

![](https://miro.medium.com/max/438/0*ZOM_AmPYD4vf9zz5)

message repeated field 예제 (proto3)

위와 같이 repeated rule을 주게되면 Field를 배열의 형태로도 사용할 수 있게 됩니다. 필드는 Key-Value 구조로 저장되어 repeated field를 사용할 때도 key가 계속 붙게되는데, reqeated 뒤에 packed 옵션을 주면 value만 반복하게끔 할 수 있어요. 어차피 필드 번호는 바뀌지 않으니 되도록 이 옵션을 주면 보다 효율적인 Enconding이 되겠네요.

_2) Package_

package는 message type 이름을 중첩없이 구분할 때 사용합니다. 메시지 사용 시 package를 명시함으로써 필드와 명확히 구분해주죠. 아래 예제에서는 Open이라는 message를 타입으로 하는 field 이름을 open으로 주어 모호한 정의를 package로 구분하였습니다. 사실 foo.bar라는 package를 굳이 쓰지 않는다고 사용이 불가한 것은 아니지만, 구성 메시지가 많다면 명확하게 구분될 수 있게 명시해 주는 것이 좋겠습니다.

![](https://miro.medium.com/max/151/0*ILpG1Ac-syEIo4xZ)

package 미사용 예제

![](https://miro.medium.com/max/217/0*20LoTOYc_DvT9-eJ)

package 사용 예제

_3) Service_

Service는 RPC를 통해 서버가 클라이언트에게 제공할 함수의 형태를 정의합니다. 서비스명과 RPC 메소드명 모두 CamelCase 형태를 권장하네요. 옵션을 주지 않으면 단일 요청/응답으로 동작하지만, stream 옵션을 주면 RPC를 구현할 수 있습니다.

![](https://miro.medium.com/max/456/0*uSJ1OuoZAOOnHUfi)

**Unary RPC**

![](https://miro.medium.com/max/569/0*0lY2WHk-THxGb0G-)

**양방향 Streaming RPC**