역방향 프록시는 클라이언트 요청을 가져와서 하나 이상의 프록시 서버에 요청을 보내고 응답을 가져오고 서버의 응답을 클라이언트에 전달하는 서비스입니다. 

성능과 확장성 때문에 NGINX는 HTTP 및 비 HTTP 서버의 역방향 프록시로 자주 사용됩니다. 일반적인 역방향 프록시 구성은 Nginx를 Node.js, Python 또는 Java 애플리케이션 앞에 배치하는 것입니다.

Nginx를 역방향 프록시로 사용하면 다음과 같은 몇 가지 추가적인 이점을 얻을 수 있습니다.

로드 밸런싱 - Nginx는 클라이언트의 요청을 프록시 서버에 분산하기 위해 로드 밸런싱을 수행하여 성능, 확장성 및 신뢰성을 향상시킬 수 있습니다.

캐싱 - Nginx를 역방향 프록시로 사용하면 미리 렌더링된 버전의 페이지를 캐시하여 페이지 로드 시간을 단축할 수 있습니다. 이 기능은 프록시 서버의 응답에서 수신한 콘텐츠를 캐싱하고 이 콘텐츠를 사용하여 매번 동일한 콘텐츠를 프록시 서버에 연결할 필요 없이 클라이언트에 응답하는 방식으로 작동합니다.

SSL 터미네이션 - Nginx는 클라이언트와의 연결에 대한 SSL 끝점 역할을 할 수 있습니다. 수신 SSL 연결을 처리 및 해독하고 프록시 서버의 응답을 암호화합니다.

압축 - 프록시 서버가 압축된 응답을 보내지 않는 경우 클라이언트로 보내기 전에 응답을 압축하도록 Nginx를 구성할 수 있습니다.

DDoS 공격 완화 - 수신 요청과 단일 IP 주소당 연결 수를 일반 사용자에게 일반적인 값으로 제한할 수 있습니다. 또한 Nginx를 사용하면 클라이언트 위치와 "User-에이전트" 및 "Referer"와 같은 요청 헤더 값을 기준으로 액세스를 차단하거나 제한할 수 있습니다.

이 문서에서는 Nginx를 역방향 프록시로 구성하는 데 필요한 단계를 간략히 설명합니다.

![](https://k.kakaocdn.net/dn/bRJ6In/btq4bB49G3B/FNdqeeamFw6H99zUgKwzn0/img.png)

Linux : Nginx Reverse Proxy 설정 방법, 예제, 명령어

## 전제조건

Ubuntu, CentOS 또는 Debian 서버에 Nginx가 설치되어 있다고 가정합니다.

## Nginx를 역방향 프록시로 사용

Nginx를 HTTP 서버에 대한 역방향 프록시로 구성하려면 도메인의 서버 블록 구성 파일을 열고 해당 파일 내부에 위치 및 프록시 서버를 지정합니다.

```
server {
    listen 80;
    server_name www.example.com example.com;

    location /app {
       proxy_pass http://127.0.0.1:8080;
    }
}
```

프록시 서버 URL은 proxy_pass 지시어를 사용하여 설정되며 프로토콜, 도메인 이름 또는 IP 주소로 HTTP 또는 HTTPS를 사용할 수 있으며 선택적 포트와 URI를 주소로 사용할 수 있습니다.

위의 구성은 Nginx에게 모든 요청을 /app 위치에 http://127.0.0.1:8080으로 프록시 서버로 전달하도록 지시합니다.

Ubuntu 및 Debian 기반 배포에서는 서버 블록 파일이 /etc/nginx/site-available 디렉토리에 저장되고, 반면 Cent OS에는 저장됩니다./etc/nginx/conf.d 디렉토리에 있습니다.

위치 및 proxy_passdirections의 작동 방식을 더 잘 설명하기 위해 다음 예를 들어 보겠습니다.

```
server {
    listen 80;
    server_name www.example.com example.com;

    location /blog {
       proxy_pass http://node1.com:8000/wordpress/;
    }
}
```

방문자가 http://example.com/blog/my-post,에 접속할 경우, Nginx는 이 요청을 http://node1.com:8000/wordpress/my-post으로 대리합니다.

프록시 서버의 주소에 URI, (/wordpress/)가 포함된 경우 프록시 서버에 전달되는 요청 URI가 지시어에 지정된 URI로 바뀝니다. URI 없이 프록시 서버의 주소를 지정하면 전체 요청 URI가 프록시 서버로 전달됩니다.

## 요청 헤더를 전달

Nginx는 요청을 프록시할 때 클라이언트의 프록시 요청인 호스트 및 연결에서 두 개의 헤더 필드를 자동으로 정의하고 빈 헤더를 제거합니다. 호스트가 $proxy_host 변수로 설정되고 연결이 닫히도록 설정됩니다.

프록시 연결에 대한 헤더를 조정하거나 설정하려면 proxy_set_header 지시어와 헤더 값을 차례로 사용합니다. 여기서 사용 가능한 모든 요청 헤더 목록과 허용되는 값을 찾을 수 있습니다. 헤더가 프록시 서버에 전달되지 않도록 하려면 해당 헤더를 빈 문자열 ""로 설정하십시오.

다음 예에서는 호스트 헤더 필드의 값을 $host로 변경하고 값을 빈 문자열로 설정하여 Accept-Encoding 헤더 필드를 제거합니다.

```
location / {
    proxy_set_header Host $host;
    proxy_set_header Accept-Encoding "";
    proxy_pass http://localhost:3000;
}
```

구성 파일을 수정할 때마다 Nginx 서비스를 재시작해야 변경 사항이 적용됩니다.

## Nginx를 비 HTTP 프록시 서버에 대한 역방향 프록시로 구성

Nginx를 HTTP 프록시 서버가 아닌 서버에 대한 역방향 프록시로 구성하려면 다음 지시사항을 사용할 수 있습니다.

fastcgi_pass - FastCGI 서버에 대한 역방향 프록시입니다.

uwsgi_pass - uwsgi 서버에 대한 역방향 프록시입니다.

scgi_pass - 역방향 프록시를 SCGI 서버로 보냅니다.

memcached_pass - Memcached 서버에 대한 역방향 프록시입니다.

가장 일반적인 예 중 하나는 Nginx를 PHP-FPM의 역방향 프록시로 사용하는 것입니다.

```
server {

    # ... other directives

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
}
```

## 일반 Nginx 역방향 프록시 옵션

요즘은 HTTPS를 통한 컨텐츠 제공이 표준이 되었습니다. 이 섹션에서는 권장되는 Nginx 프록시 매개 변수 및 헤더를 포함하여 HTTPS Nginx 역방향 프록시 구성의 예를 제공합니다.

```
  location/ {
    proxy_pass http://127.0.0.1:3000;
    proxy_http_version  1.1;
    proxy_cache_bypass  $http_upgrade;

    proxy_set_header Upgrade           $http_upgrade;
    proxy_set_header Connection        "upgrade";
    proxy_set_header Host              $host;
    proxy_set_header X-Real-IP         $remote_addr;
    proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Host  $host;
    proxy_set_header X-Forwarded-Port  $server_port;
  }
```

proxy_http_version 1.1 - 프록시를 위한 HTTP 프로토콜 버전을 정의합니다. 기본적으로 1.0으로 설정됩니다. 웹 소켓 및 활성 연결을 유지하려면 버전 1.1을 사용해야 합니다.

proxy_cache_bypass $http_upgrade - 캐시에서 응답을 가져오지 않을 조건을 설정합니다.

Upgrade $http_upgrade 및 Connection "upgrade" - 응용프로그램이 웹 소켓을 사용하는 경우 헤더 필드가 필요합니다.

Host $host - 다음 우선 순위의 $host 변수에는 요청 라인의 호스트 이름 또는 호스트 요청 헤더 필드의 호스트 이름 또는 요청과 일치하는 서버 이름이 포함됩니다.

X-Real-IP $remote_addr - 실제 방문자 원격 IP 주소를 프록시 서버로 전달합니다.

X-Forwarded-$proxy_add_x_forwarded_for - 클라이언트가 프록시 처리한 모든 서버의 IP 주소를 포함하는 목록입니다.

X-Forwarded-Proto $scheme - HTTPS 서버 블록 내에서 사용할 경우 프록시 서버의 각 HTTP 응답이 HTTPS로 다시 작성됩니다.

X-Forwarded-Host $host - 클라이언트가 요청한 원래 호스트를 정의합니다.

X-Forwarded-Port $server_port - 클라이언트가 요청한 원래 포트를 정의합니다.

기존 SSL/TLS 인증서가 없는 경우 certbot을 사용하여 Ubuntu 18.04, CentOS 7 또는 Debian 서버에서 무료 SSL 암호화 인증서를 얻습니다.