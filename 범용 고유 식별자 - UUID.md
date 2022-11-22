**범용 고유 식별자**(汎用固有識別子, [영어](https://ko.wikipedia.org/wiki/%EC%98%81%EC%96%B4 "영어"): universally unique identifier, UUID)는 [소프트웨어](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4 "소프트웨어") 구축에 쓰이는 식별자 표준으로, [개방 소프트웨어 재단](https://ko.wikipedia.org/wiki/%EA%B0%9C%EB%B0%A9_%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EC%9E%AC%EB%8B%A8 "개방 소프트웨어 재단")(OSF)이 [분산 컴퓨팅 환경](https://ko.wikipedia.org/w/index.php?title=%EB%B6%84%EC%82%B0_%EC%BB%B4%ED%93%A8%ED%8C%85_%ED%99%98%EA%B2%BD&action=edit&redlink=1 "분산 컴퓨팅 환경 (없는 문서)")(DCE)의 일부로 표준화하였다.

## 역사

UUID는 원래 [아폴로 네트워크 컴퓨팅 시스템](https://ko.wikipedia.org/w/index.php?title=%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC_%EC%BB%B4%ED%93%A8%ED%8C%85_%EC%8B%9C%EC%8A%A4%ED%85%9C&action=edit&redlink=1 "네트워크 컴퓨팅 시스템 (없는 문서)")(NCS)에서 사용되었다가 나중에 [개방 소프트웨어 재단](https://ko.wikipedia.org/wiki/%EA%B0%9C%EB%B0%A9_%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EC%9E%AC%EB%8B%A8 "개방 소프트웨어 재단")(OSF)의 [분산 컴퓨팅 환경](https://ko.wikipedia.org/w/index.php?title=%EB%B6%84%EC%82%B0_%EC%BB%B4%ED%93%A8%ED%8C%85_%ED%99%98%EA%B2%BD&action=edit&redlink=1 "분산 컴퓨팅 환경 (없는 문서)")(DCE)에서 사용되었다. DCE UUID의 초기 설계는 NCS UUID에 기반을 두었으며[[1]](https://ko.wikipedia.org/wiki/%EB%B2%94%EC%9A%A9_%EA%B3%A0%EC%9C%A0_%EC%8B%9D%EB%B3%84%EC%9E%90#cite_note-1), 여기서 디자인은 아폴로 컴퓨터가 설계한 [운영 체제](https://ko.wikipedia.org/wiki/%EC%9A%B4%EC%98%81_%EC%B2%B4%EC%A0%9C "운영 체제")인 [도메인/OS](https://ko.wikipedia.org/w/index.php?title=%EB%8F%84%EB%A9%94%EC%9D%B8/OS&action=edit&redlink=1 "도메인/OS (없는 문서)")에 정의되고 사용된 [64비트](https://ko.wikipedia.org/w/index.php?title=64%EB%B9%84%ED%8A%B8_%EC%BB%B4%ED%93%A8%ED%8C%85&action=edit&redlink=1 "64비트 컴퓨팅 (없는 문서)") 고유 식별자의 영향을 받았다.

## 개요

네트워크 상에서 서로 모르는 개체들을 식별하고 구별하기 위해서는 각각의 고유한 이름이 필요하다. 이 이름은 고유성(유일성)이 매우 중요하다. 같은 이름을 갖는 개체가 존재한다면 구별이 불가능해 지기 때문이다. 고유성을 완벽하게 보장하려면 중앙관리시스템이 있어서 일련번호를 부여해 주면 간단하지만 동시다발적이고 독립적으로 개발되고 있는 시스템들의 경우 중앙관리시스템은 불가능하다. 개발주체가 스스로 이름을 짓도록 하되 고유성을 충족할 수 있는 방법이 필요하다. 이를 위하여 탄생한 것이 범용고유식별자(UUID)이며 국제기구에서 표준으로 정하고 있다.

UUID 표준에 따라 이름을 부여하면 고유성을 완벽하게 보장할 수는 없지만 실제 사용상에서 중복될 가능성이 거의 없다고 인정되기 때문에 많이 사용되고 있다.

## 정의

UUID는 16 [옥텟](https://ko.wikipedia.org/wiki/%EC%98%A5%ED%85%9F "옥텟") (128[비트](https://ko.wikipedia.org/wiki/%EB%B9%84%ED%8A%B8_(%EB%8B%A8%EC%9C%84) "비트 (단위)"))의 수이다. [표준 형식](https://ko.wikipedia.org/w/index.php?title=%ED%91%9C%EC%A4%80_%ED%98%95%EC%8B%9D_(%EC%88%98%ED%95%99)&action=edit&redlink=1 "표준 형식 (수학) (없는 문서)")에서 UUID는 32개의 [십육진수](https://ko.wikipedia.org/wiki/%EC%8B%AD%EC%9C%A1%EC%A7%84%EB%B2%95 "십육진법")로 표현되며 총 36개 문자(32개 문자와 4개의 하이픈)로 된 8-4-4-4-12라는 5개의 그룹을 하이픈으로 구분한다. 이를테면 다음과 같다.

550e8400-e29b-41d4-a716-446655440000

340,282,366,920,938,463,463,374,607,431,768,211,456개의 사용 가능한 UUID가 있다.

8-4-4-4-12 포맷 문자열은 16바이트의 UUID를 위한 레코드 레이아웃에 기반을 둔다:[[2]](https://ko.wikipedia.org/wiki/%EB%B2%94%EC%9A%A9_%EA%B3%A0%EC%9C%A0_%EC%8B%9D%EB%B3%84%EC%9E%90#cite_note-RFC_4122-2)

![[스크린샷 2022-11-21 오전 10.34.10.png]]

## 버전

-   버전 1 (MAC 주소)
-   버전 2 (DCE 보안)
-   버전 3 (MD5 해시)
-   버전 4 (랜덤)
-   버전 5 (SHA-1 해시)

## 이 식별자를 사용하는 언어 / 시스템[[편집](https://ko.wikipedia.org/w/index.php?title=%EB%B2%94%EC%9A%A9_%EA%B3%A0%EC%9C%A0_%EC%8B%9D%EB%B3%84%EC%9E%90&action=edit&section=5 "부분 편집: 이 식별자를 사용하는 언어 / 시스템")]

-   액션스크립트
-   아파치 Solr
-   C
-   C++
-   Caché 오브젝트스크립트
-   CakePHP
-   코코아/카본 (OS X)
-   코드기어 라드 스튜디오 (델파이/C++빌더)
-   코드퓨전
-   커먼 리스프
-   CouchDB
-   D
-   에펠
-   Erlang
-   파이어버드 서버
-   프리 파스칼 & 라자루스 IDE
-   해스켈
-   Haxe
-   자바
-   자바스크립트
-   KohanaPHP
-   Lasso
-   루아
-   OS X
-   MySQL
-   닷넷 프레임워크
-   OCaml
-   오라클 데이터베이스
-   펄
-   PHP
-   PostgreSQL
-   프로그레스 오픈에지 ABL
-   파이썬
-   레볼루션/RunRev
-   루비
-   SAP 비즈니스오브젝트 데이터 서비스
-   SQL 서버
-   Tcl
-   유닉스
-   Openstack