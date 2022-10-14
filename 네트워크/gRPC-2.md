
> _4._ **_Protocol Buffer Encoding_**

기본적인 proto file 작성법 및 message 구조를 알았으니, 이제 실제로 Serialize 되는 방식을 살펴보겠습니다.

_1) ​Message (Key-Value) Encoding_

앞서 정의한 message 들은일련의 Key-Value 쌍으로 이루어진 binary data로 인코딩되는데요. Key는 Field Number 뿐만 아니라, 해당 Field의 data type을 지시하는 Wire Type을 표현합니다.

Key는 field_number << 3) | wire_type의 형태로, 일반적인 1byte인 경우 Key는 Filed Number(5bit) + Wire Type(3bit)로 이루어집니다. Field Number는 proto file에 명시된대로 들어가지만, Wire Type은 선언한 data type 별로 지정됩니다.

![](https://miro.medium.com/max/656/0*tC507UO9Qv1Sj3kk)

Protocol Buffer Wire Type

앞선 proto3 message 예제를 다시 살펴보겠습니다.

query의 Field number는 1이며, string이므로 wire type은 2가 되겠죠. 때문에 Key는 00001 | 010 = 0x0a 가 될 것입니다. result_per_page 필드의 경우 Field number = 3이며, int32이므로 wire type이 0 즉, 00011 | 000 = 0x18를 Key로 가지게 되겠네요.

![](https://miro.medium.com/max/244/0*EsogsUM73Pm-ZO5E)

message 예제 (proto3)

_2) ​Varients_

**▶ wire type 0**

wire type에 따른 자료형은 여러가지가 있지만 정수를 Serializing 하는 Varints Encoding부터 알아보겠습니다. Varints에 포함되는 정수형 타입들은 첫 byte의 1bit를 무조건 뒷 byte에 대한 지시 역할을 하는 msb(most significant bit)로 가지게 됩니다. 이 값이 1이면 뒷 데이터가 더 있다는 것이고, 0이라면 이어지는 byte stream과는 분리된다는 의미죠. “least significant group first” 룰을 따르기 때문에 하위 byte부터 저장됩니다.

앞선​ 2.3.1에서 1byte로는 Field number를 1~15까지만 표현 가능한 이유가 여기에 있습니다.

Field number는 정수형이므로 Varints Encoding 법칙을 따르게 됩니다. 따라서 1byte key에서 wire type 부분을 제외한 5bit 중 1bit가 msb로 사용되겠죠. 즉 key가 1byte라도 실제 필드 번호 값을 담는 크기는 4bit이기 때문에 1~15까지만 표현 가능한 것입니다. 그 이상의 값이라도 Varints Encoding 법칙을 벗어나지는 않습니다. (아래 정수 300 encoding 예시 참조)

**▶ Encoding**

300이라는 정수를 Encoding 해봅시다.

① 300은 이진수로 256 + 32 + 8 + 4 = 100101100 로 표기됩니다.

② Varints 직렬화를 위해선 byte 당 msb가 포함돼야 하므로 7bit 단위로 구분해줍니다.

→□000 0010□010 1100

③ least significant group first 룰에 맞게 byte를 역순으로 나열합니다.

→□010 1100□0000010

④ msb를 설정해줍니다.

→1010 11000000 0010

**▶ Deconding**

만약1010 11000000 0010라는 직렬화 데이터를 받았고 이를 Decoding 한다면,위 절차를 거꾸로 따라가면 됩니다.

① msb를 제외합니다.

→ □010 1100□000 0010

② byte는 역순으로 나열되어 있으므로 다시 역순으로 정렬합니다.

→ □000 0010□010 1100

③ msb 제외 후 data를 연접합니다.

→ 10010 1100= 300

**▶ Signed Integers**

​만약 protocol buffer에서 음의 정수를 표기한다면 signed int(sint) 형을 사용하는게 효율적입니다. 일반 int형에서는 음수로 사용 시 절댓값에 관계없이 항상 고정된 byte 크기를 잡는데, signed int형은 ZigZag Encoding으로 이미 부호있는 정수를 부호없는 정수로 매핑시켜 두었기 때문입니다.

​_3) Non-Varint number_

**▶ wire type 1,5**

정수형이 아닌 실수형 타입은 예외 없이 간단합니다. wire type 1인 64bit double 등은 고정된 64bit 데이터를 지시하며, wire type 5인 32bit형 float 등은 고정된 32bit 데이터를 지시합니다.

**▶ wire type 2**

string 같이 wire type이 2인 경우, varints 형태에서 길이를 지시하는 byte가 추가됩니다. 즉 key를 읽어서 wire type이 2라면 그 다음 바이트는 길이를 지정하는거죠. 실제 설정된 value는 UTF-8로 인코딩됩니다.

예를 들어 2.3.1에서 query 필드의 type은 string이었고 key는 0x12 라고 하였죠. 이 query 필드에 “testing”이라는 데이터가 저장되었다면 Encoding시_12 07 74 65 73 74 69 6e 67_로 표기될 것입니다.

- 0x12 : 0001 0010  field num = 2 & wire type = 2 (string)

- 0x07 : 7 byte

- UTF-8 encoding : “testing”

길이 byte는 1 byte로 지정된 것은 아니며, 만약 wire type2 데이터의 길이가 0xFF = 255 bytes 이상이라면 아래와 같이2byte 이상으로도 표기됩니다. 참고로 default message limit는 4*1024*1024=4Mbyte입니다.

![](https://miro.medium.com/max/500/0*HKWL4QOdAY4ljeXI)

wire type2의 길이 byte는 1보다 클 수 있음

​4_) Embedded Message_

package 설명에서 잠깐 예를 들었지만 message 정의 시 field type을 다른 메시지로도 지정할 수 있습니다. 아래와 같은 Test1이라는 message에 a라는 field가 있고 여기에 150이 설정됐다고 가정하면 a 필드는_08 96 01_으로 encoding 됩니다. 이 Test1이라는 message가 Test3 message의 field type으로 사용된다면, wire type = 2 이므로 길이 byte가 추가되고 결과적으로 c 필드는_1a 03 08 96 01_로 encoding 되겠죠.

![](https://miro.medium.com/max/149/0*u9dxMymh2f6YqVup)

Embedded Message

**▶ a=150**

08 96 01

0x08 : 0000 1000 → field num = 1 & wire type = 0 (int32)

0x96 0x01 : 1001 0110 0000 0001

→ □001 0110 □000 0001

→ 0000001 0010110 → 10010110

**▶ c**

0x1a : 0001 1010 → filed num = 3 & wire type = 2 (embedded Mesgage)

0x03 : 3byte

08 96 01: a=150

> _5. Value Type_

​1_) Sclar Type_

이 표는 일반적으로 사용되는 Scalar 자료형들이 각 언어로 generate 되었을 때 어떤 type으로 변환되는지 정리한 표입니다. 유의 사항이 언어별로 좀 상이한데 특히 python의 경우 예외가 많습니다.

![](https://miro.medium.com/max/700/1*eLY22c5aR1GQKsPqkcBc-A.png)

Data Type 별 호환 자료형

* [1] Java : unsigned int32/64는 signed로 표시→최상위 비트가 부호 비트로 사용

* [2–4] Python

* [2] Python 변환 시 type check 필수

* [3] unsigned int32/64는 long으로 변환되므로 해당 변수 사용 시 int 형변환 후 사용

![](https://miro.medium.com/max/343/0*2MlqzYYa6M50ZqQQ)

* [4] string은 unicode로 변환 (단, ASCII code면 str)

* [5] PHP : 64bit 자료형은 64bit 환경에서는 정수, 32bit 시스템에서는 string으로 변환됨

​_2) Default Value_

protocol buffer에서 지정하는 디폴트 값들입니다. 특별히 유의해야 할 점은 enum이라는 열거형 데이터는 첫 번째 값을 default value로 지정한다는 점입니다. message field에 사용되는 Type에 경우 언어에 따라 조금씩 다르기 때문에 API reference 문서를 참조하면 되겠습니다.

![](https://miro.medium.com/max/700/0*m5gdnMd9cXmoa7_w)

Protobuf Default Value

​3_) Enumeration_

**▶ 기본 문법**

이번엔 첫번째 값을 default value로 삼는 enum형을 살펴보겠습니다. enum은 상수를 저장하는 열거형 데이터이며, encoding 하면 const 상수 형태로 저장되고, 각 name / value 별 map이 별도로 지정됩니다.

예를 들어 메시지 유형을 정의할 때, enum에 원하는 상수 값을 넣어두면 이걸 값으로도 참조할 수 있는 거죠. enum형을 쓸 때 유의할 것은 default value에 정의됐다시피 첫 번째 값이 default 값으로 지정되므로 첫 상수는 0으로 지정해줘야 한다는 점입니다. 그 밖에 값들은 세미콜론(;)으로 구분합니다.

![](https://miro.medium.com/max/412/0*yEXVHqzWHwokSm71)

enum 사용 시 첫 상수 0 지정 및 세미콜론(;)으로 구분하지 않았을 때 error

**▶ 옵션**

enum에서 사용할 수 있는 옵션이 2가지가 있습니다. enum 전체에 적용되는 allow_alias 옵션을 주면 다른 name에 같은 값을 줄 수 있습니다. 향후 value를 참조할 때는 복수 개의 값이 있을 테니 oneof로 하나만 선정하여 사용합니다.

![](https://miro.medium.com/max/417/0*DT6A5h2BHLPASbfQ)

ecnumoption : allow_alias

enum 값을 삭제하거나 주석처리하여 update 시켰다면 다른 사용자가 이 값을 재 사용하게될 수도 있는데, 이는 proto file의 버전이 안맞을 경우 데이터 손상 등의 문제를 일으킬 수 있습니다. 이를 방지하고 싶다면, value / name을 더이상 접근할 수 없게 reserve 합니다. 단, 아래 예제와 같이 reserved 만 선언한다면 error가 리턴되므로 default 값을 고려하여 enum 첫 줄엔 하나 이상의 상수를 선언해야합니다.

![](https://miro.medium.com/max/431/0*dsY8dT9u7vuzovIV)

enum option : reserved

​4_) Maps_

다음은 map 입니다. 흔히 아는 일련의 key-value 쌍이죠. 이 맵의 필드들은 reqeated 될 수 없으며, 순서가 있는 데이터도 아니고 언어별로 구현 방식이 상당히 다릅니다. key type은 string과 scalar type의 자료형만 지정 가능하며 value type은 map 외의 모든 자료형 지정이 가능합니다.

때문에 개인적인 필요에 의해 테스트 해본 점은 map을 중첩해서 구현하는 방법인데요,

![](https://miro.medium.com/max/520/0*0I2bQ7WqGSgMtJKI)

map의 value type으로는 map이 지원되지 않음

이런 경우엔 map field를 갖는 message를 다시 사용하는 방식으로 구현합니다. 하지만 이렇게 하면 쓸데없이 stub code가 복잡해지는 터라 권장하긴 어렵습니다. 다만 특별한 대안은 없어 꼭 필요하다면 message name을 줄이면서 용량을 축소시킬 수 있겠습니다.

![](https://miro.medium.com/max/644/0*Kdy7EqWjLGRZskF2)

개별 message로 선언하여 사용 가능

> **_6. 인증 (Authentication)_**

암호화 인증이 필요한 경우 ssl/tls 기반 그리고 token 기반, 두 가지 메커니즘을 지원합니다. 대체적으로 전자를 사용하여 인증 후 암호화 통신을 진행하는데요. 아래 그림과 같이 client의 경우 dial 옵션으로, Server에선 신규 grpc 서버 생성 시 적용됩니다. token 기반 인증은 gRPC로 Google API 접근 시 OAuth2 token과 같은 google access token이 지원됩니다.

![](https://miro.medium.com/max/542/0*cN4tzWghw6eBFQFl)

SSL/TLS 구현 방법

> **_7. Failover Test_**

gRPC 운영 환경에서 일어날 수 있을 만한 case를 가정하여 Test 한 결과를 공유합니다. 테스트 환경은 아래와 같습니다.

- gRPC : v1.26.0-dev

- Protocol Buffer : v3.10.0

-사용 언어 : Golang

_1) Server down일 때 Client가 요청하는 경우_

Server가 준비되지 않은 상태에서 Client가 요청하는 경우, retansmission은 8번씩 새로운 session이 맺어질 때까지 무한정 시도됩니다.

![](https://miro.medium.com/max/642/0*k37ejfoKapGlAfsM)

_2) Server down 후 다시 up 되었을 때_

일반적으로 server가 down 된다면 timeout에 걸려 통신이 종료되는데요(default timeout= 1s). timeout을 일정치 이상 준 경우엔 sever down 감지 후 recovery 되어 올라왔을 때 retransmission에 응답하여 새로운 세션으로 연결됩니다.

![](https://miro.medium.com/max/668/0*SBmYCOgI4T2bz9I8)

_3) Client timeout이 충분히 커도, 8회 초과시 close_

단 timeout이 충분히 큰 경우라도 8회 Retransmission까지 응답이 없으면 해당 통신은 종료됩니다.

![](https://miro.medium.com/max/404/0*VO7UxPlrXdvjVrAs)

_4) L4환경에서 failover_

VIP로 gRPC 요청할 때, session이 할당된 active server down 시엔 기존 session은 FIN 종료 후 즉시 새로운 session으로 연결됩니다. 참고로 L4 구동 모드는 Proxy이며 Round Robin 알고리즘으로 LB 설정하였습니다.

![](https://miro.medium.com/max/682/0*83C-wZNJrauImG2c)

# **gRPC summary**

> **_1. 특징 및 장점_**

gRPC에서는 아직 브라우저 관련 API가 제공되지 않기 때문에 브라우저에서 직접 gRPC 서비스를 호출하는 것은 불가능합니다. 또한 기존 데이터 통신과 다르게 텍스트 기반이 아니라 Encoding 된 Binary Stream이기 때문에 사람이 읽기는 어렵죠. 하지만 아래와 같이 장점이 훨씬 큰 기술이므로 서비스 개발 시 높은 생산성, 다양한 언어, 빠른 속도 등의 좋은 퍼포먼스를 보여줄 것입니다.

**1) 높은 생산성과 효율적인 유지 보수**

- ProtoBuf의 IDL만 정의하면 높은 성능을 보장하는 서비스와 메시지에 대한 소스코드가 자동으로 생성

**2) 다양한 언어와 플랫폼 지원**

- IDL을 활용한 서비스 정의 한 개로 다양한 언어와 플랫폼에서 동작하는 서버와 클라이언트 코드가 생성

**3) HTTP/2 기반의 양방향 스트리밍**

- 서버와 클라이언트가 서로 동시에 데이터를 스트리밍으로 주고받음

**4) 높은 메시지 압축률과 성능**

- HTTP/2에 의한 압축뿐만 아니라 protoBuf에 의한 메시지 정의에 의해서 메시지 크기를 획기적으로 줄임

**5) 다양한 gRPC 생태계**

- 필요에 따라 Authentication, Tracing, Load Balancing, Health Checking, API Gateway 등의 다양한 도구 지원

​

> **_2. When is it used?_**

_그렇다면 어떤 서비스에 gRPC를 활용하면 좋을까요?_

거의 모든 서버 시스템 개발에 효율적으로 적용될 수 있지만, 특히 Microservice Architecture 서비스에 적합합니다. 마이크로서비스는 작은 서비스들을 유기적으로 결합해 하나의 응용프로그램을 개발하는 방법론인데요. 구성 서비스가 독립적이기에 개발 및 배포 운영이 용이하여, 확장을 유연하게 할 수 있죠. 때문에 새로운 기술 도입 및 변경에도 용이한 면을 보입니다.

하지만 분산 시스템 특성상 공통 기능의 중복이 발생하여 메모리를 비효율적으로 사용할 수도 있고, 프로그램 규모가 커질 수록 구성원들의 철학이나 기술 스택이 제각기 다르니 운영도 어려워지는데요. 이에 gRPC는 앞서 언급한 특징 덕에 이러한 단점을 보완하며 장점을 극대화 시킬 수 있습니다.

브라우저를 사용하지 않는 백엔드간 서버 통신이나, 자원 한정적인 환경에서도 유용합니다. byte/호출/cpu 수 등으로 과금되는 클라우드 환경에서는 비용 절감의 효과도 생각할 수 있겠네요. 최근엔 시스코, 주니퍼 등 주요 네트워크 장비에서도 grpc를 모두 지원하고 있어 모니터링이나 자동화 등 인프라 운영에도 활용 방안이 많을 것으로 기대합니다.

![](https://miro.medium.com/max/700/0*6KBQjT_GZUaiZYLZ)

cisco / juniper gRPC 제공