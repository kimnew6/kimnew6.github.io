---
layout: single
title: "Authentication & Authorization"
---

# 인증 & 인가

## 인증 (Authentication)

- 유저의 신원(`Identification`), 즉 아이디와 비밀번호를 확인하는 절차.
- `Client`로 부터 전달받은 개인 정보(ex. ID, PW)와 `Database`에 저장되어있는 데이터를 비교.
- 개인 정보는 `Access Token`에 담아 관리. (발급 - Backend)

## 인가 (Authorization)

- 유저의 요청(`Request`)에 따른 권한(`Permission`), 즉 실행 가능 여부를 확인하는 절차.
- Ex. ‘(마이페이지 진입시) 해당 유저는 고객 정보를 볼 수는 있지만 수정 할 수는 없다.’
- 해당 유저의 정보는 `Access Token`에 저장되어 있음.
- 해당 유저의 권한도 `Access Token`에 저장된 데이터를 기반으로 확인.

---

## 회원가입 및 로그인 절차

- **회원 가입 절차**
    1. [User] 회원가입 페이지에 진입하여 ID, PW 등 가입에 필요한 개인 정보를 입력한다. 
    2. [Frontend] 회원가입 시 사용자가 입력한 개인 정보를 백엔드 서버로 전달한다.
    3. [Backend] 전달받은 개인 정보와 사용자 비밀번호를 암호화하여 DB(Database)에 저장한다.

- **로그인 절차**
    1. [User] 로그인에 필요한 ID, PW 를 입력한다.
    2. [Frontend] 로그인 시 사용자가 입력한 개인 정보를 백엔드 서버로 전달한다.
    3. [Backend] 사용자가 입력한 비밀번호를 암호화한다.
    4. [Backend] DB에 저장된 ID, 암호화된 비밀번호와 비교한다. (일치하면 로그인 성공)
    5. [Backend] (로그인 성공의 경우) `Access Token`을 발급하여 클라이언트에게 전송한다.
    6. [Frontend] 전송받은 `Access Token`을 브라우저 저장소에 저장하여 사용자 정보를 관리한다.

## 비밀번호 암호화

- 사용자의 비밀번호는 비밀번호 그대로 DB에 절대 저장하지 않는다.
    - DB가 해킹을 당하는 경우 유저의 비밀번호도 그대로 노출 된다.
    - 외부 해킹 뿐만 아니라 내부 개발자나 기타 인력이 사용자의 비밀번호를 열람할 수도 있다.
- 보안상의 이유로 사용자의 비밀번호는 반드시 암호화해서 저장해야 한다.
    - 암호화하여 저장된 비밀번호는 DB가 해킹을 당해도 그대로 노출되지 않는다.
    - 암호화하여 저장된 비밀번호는 내부 인력도 실제 비밀번호를 알 수가 없다.
- 비밀번호 암호화에는 `단방향 해쉬 함수(one-way hash function)`가 일반적으로 사용된다.
    
    ```
        In [21]: import hashlib
    
        In [22]: m = hashlib.sha256()
    
        In [23]: m.update(b"test password")
    
        In [24]: m.hexdigest()
        Out[24]: '0b47c69b1033498d5f33f5f7d97bb6a3126134751629f4d0185c115db44c094e'
    
        In [25]: m = hashlib.sha256()
    
        In [26]: m.update(b"test password2")
    
        In [27]: m.hexdigest()
        Out[27]: 'd34b32af5c7bc7f54153e2fdddf251550e7011e846b465e64207e8ccda4c1aeb'
    ```
    
    - 단방향 해시 함수는 원본 메시지를 변환하여 암호화된 메시지인 `다이제스트(digest)`를 생성한다.
    - 원본 메시지를 알면 암호화된 메시지를 구하기는 쉽지만, 암호화된 메시지로는 원본 메시지를 구할 수 없어서 `단방향성(one-way)` 이라고 한다.
    - 예를 들어, "test password"를 hash256 이라는 해쉬 함수를 사용하면 `0b47c69b1033498d5f33f5f7d97bb6a3126134751629f4d0185c115db44c094e` 값이 나온다.
    - 만일 "test password2"를 hash256 이라는 해쉬 함수를 사용하면 `d34b32af5c7bc7f54153e2fdddf251550e7011e846b465e64207e8ccda4c1aeb` 값이 나온다.
    - 실제 비밀번호는 비슷하지만 해쉬 함수 값은 완전히 달라진 것을 볼 수 있다.
    - 이러한 효과를 `avalance`라고 하는데, 비밀번호 해쉬 값은 해킹을 어렵게 만드는 하나의 요소이다.

## Bcrypt

- 단방향 해쉬 함수의 취약점
    - `Rainbow Table Attack` - 미리 해쉬값들을 계산해 놓은 테이블을 Rainbow Table이라고 한다.
    - 해시 함수는 원래 패스워드를 저장하기 위해서 설계된 것이 아니라 짧은 시간에 데이터를 검색하기 위해 설계된 것(`Remember Set`). 그렇기 때문에 해시 함수는 본래 처리 속도가 최대한 빠르도록 설계되었다.
    - 이러한 속성 때문에 공격자는 매우 빠른 속도로 임의의 문자열의 다이제스트와 해킹할 대상의 다이제스트를 비교할 수 있다. (MD5를 사용한 경우 일반적인 장비를 이용하여 1초당 56억 개의 다이제스트를 대입할 수 있다).
    - 이런 방식으로 패스워드를 추측하면 패스워드가 충분히 길거나 복잡하지 않은 경우에는 그리 긴 시간이 걸리지 않는다. (대부분 사용자의 패스워드는 길거나 복잡하지 않을 뿐 아니라, 동일한 패스워드를 사용하는 경우도 많다).
- 단방향 해쉬 함수의 취약점을 보안하기 위한 두 가지 방법 - `Salting` & `Key Stretching`
    
![image](https://user-images.githubusercontent.com/84711115/181264612-b846fb12-4f24-4782-960c-263356f35287.png)
  
    - `Salting`
        - 실제 비밀번호 이외에 추가적으로 랜덤 데이터를 더해서 해시값을 계산하는 방법.
        
    - `Key Stretching`
        - 단방향 해쉬값을 계산 한 후 그 해쉬값을 또 해쉬 하고, 또 이를 반복하는 것을 말한다.
        - 최근에는 일반적인 장비로 1초에 50억 개 이상의 다이제스트를 비교할 수 있지만, 키 스트레칭을 적용하여 동일한 장비에서 1초에 5번 정도만 비교할 수 있게 한다.
        - `GPU(Graphics Processing Unit)`를 사용하더라도 수백에서 수천 번 정도만 비교할 수 있다. 50억 번과는 비교할 수도 없을 정도로 적은 횟수다. 앞으로 컴퓨터 성능이 더 향상되면 몇 번의 반복을 추가하여 보완할 수 있다.
- `Bcrypt`
    
    ```
    In [40]: import bcrypt
    
    In [41]: bcrypt.hashpw(b"secrete password", bcrypt.gensalt())
    Out[41]: b'$2b$12$.XIJKgAepSrI5ghrJUaJa.ogLHJHLyY8ikIC.7gDoUMkaMfzNhGo6'
    
    In [42]: bcrypt.hashpw(b"secrete password", bcrypt.gensalt()).hex()
    Out[42]: '243262243132242e6b426f39757a69666e344f563852694a43666b5165445469397448446c4d366635613542396847366d5132446d62744b70357353'
    ```
    
    - Salting과 Key Stretching을 구현한 해쉬 함수중 가장 널리 사용되는 것이 Bcrypt이다.
    - Bcrypt는 처음부터 비밀번호를 단방향 암호화 하기 위해 만들이전 해쉬함수이다.

## JWT(JSON Web Tokens)

- 앞서 언급했듯이 유저가 로그인에 성공한 후에는 `access token`이라고 하는 암호화된 유저 정보를 첨부해서 request를 보내게 된다.
    - 유저 로그인:
    - 유저는 로그인 성공후 다음부터는 `Access Token`을 첨부해서 request를 서버에 전송함으로서 매번 로그인 해도 되지 않도록 한다.
    
    ```
    POST /auth HTTP/1.1
    Host: localhost:5000
    Content-Type: application/json
    
    {
        "username": "joe",
        "password": "pass"
    }
    ```
    
    - access token:
    
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json
    
    {
        "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGl0eSI6MSwiaWF0IjoxNDQ0OTE3NjQwLCJuYmYiOjE0NDQ5MTc2NDAsImV4cCI6MTQ0NDkxNzk0MH0.KPmI6WSjRjlpzecPvs3q_T3cJQvAgJvaQAPtk1abC_E"
    }
    ```
    
- 그러면 서버에서는 `access token`을 복호화 해서 해당 유저 정보를 얻게 된다.
    - 예를들어 access token `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGl0eSI6MSwiaWF0IjoxNDQ0OTE3NjQwLCJuYmYiOjE0NDQ5MTc2NDAsImV4cCI6MTQ0NDkxNzk0MH0.KPmI6WSjRjlpzecPvs3q_T3cJQvAgJvaQAPtk1abC_E` 를 복호화 하면 다음과 같은 정보를 얻는다:
    
    ```
    {
        user_id = 1 
    }
    ```
    
    - 복호화해서 얻은 유저 아이디를 통해 해당 유저가 누군지 알 수 있다.
- 이런 절차의 목적은 해당 유저가 매번 로그인 하지 않도록 하는 것이다.
- `access token`을 생성하는 방법은 여러가지가 있는데, 그 중 가장 널리 사용되는 기술중 하나가 바로 JWT(JSON Web Tokens)이다.
- JWT는 말 그대로 유저 정보를 담음 JSON 데이터를 암호화 해서 클라이언트와 서버간에 주고 받는 것이다.

![image](https://user-images.githubusercontent.com/84711115/181266946-4808c1be-49c8-4660-8cd7-c2f11ab7ef7f.png)


## JWT 토큰 구성
<img width="699" alt="스크린샷 2022-07-28 오전 12 25 11" src="https://user-images.githubusercontent.com/84711115/181286591-bd3ef6a5-503b-4c85-b280-5156e12d357d.png">

JWT는 세 파트로 나누어지며, 각 파트는 점로 구분하여 xxxxx.yyyyy.zzzzz 이런식으로 표현.    
순서대로 헤더 (Header), 페이로드 (Payload), 서명 (Sinature)로 구성.    
Base64 인코딩의 경우 “+”, “/”, “=”이 포함되지만 JWT는 URI에서 파라미터로 사용할 수 있도록 URL-Safe 한  Base64url 인코딩을 사용.   
    
Header는 토큰의 타입과 해시 암호화 알고리즘으로 구성. 첫째는 토큰의 유형 (JWT)을 나타내고, 두 번째는 HMAC, SHA256 또는 RSA와 같은 해시 알고리즘을 나타내는 부분.   

Payload는 토큰에 담을 클레임(claim) 정보를 포함. Payload 에 담는 정보의 한 ‘조각’ 을 클레임이라고 부르고, 이는 name / value 의 한 쌍으로 이뤄져있음.   
토큰에는 여러개의 클레임 들을 넣을 수 있다.   
클레임의 정보는 등록된 (registered) 클레임, 공개 (public) 클레임, 비공개 (private) 클레임으로 세 종류가 있다.   

마지막으로 Signature는 secret key를 포함하여 암호화되어 있다.

## 인가(Authorization)

- 인가도 JWT를 통해서 구현 될 수 있다.
- `Access token`을 통해 해당 유저 정보를 얻을 수 있음으로 해당 유저가 가지고 있는 권한(permission)도 확인 할 수 있다.

## 인가의 절차

1. Authentication 절차를 통해 `access token`을 생성한다. `access token`에는 유저 정보를 확인할 수 있는 정보가 들어가 있어야 한다 (예를 들어 user id).
2. 유저가 request를 보낼때 `access token`을 첨부해서 보낸다.
3. 서버에서는 유저가 보낸 `access token`을 복호화 한다.
4. 복호화된 데이터를 통해 user id를 얻는다.
5. user id를 사용해서 database에서 해당 유저의 권한(permission)을 확인하다.
6. 유저가 충분한 권한을 가지고 있으면 해당 요청을 처리한다.
7. 유저가 권한을 가지고 있지 않으면 Unauthorized Response(401) 혹은 다른 에러 코드를 보낸다.

# OAuth
**OAuth는 인터넷 사용자들이 비밀번호를 제공하지 않고 다른 웹사이트 상의 자신들의 정보에 대해 웹사이트나 애플리케이션의 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는, 접근 위임을 위한 개방형 표준이다. (위키백과)**     

예) 외부 어플리케이션(위프렉스)은 사용자 인증을 위해 Kakao과 Apple 및 Naver 등의 사용자 인증 방식을 사용합니다. 이 때, OAuth를 바탕으로 제 3자 서비스(위프렉스)는 외부 서비스(Kako, Apple, Naver)의 특정 자원을 접근 및 사용할 수 있는 권한을 인가받게 됩니다.      

## OAuth 참여자
OAuth 동작에 관여하는 참여자는 크게 세 가지로 구분할 수 있다.    
- Resource Server : Client가 제어하고자 하는 자원을 보유하고 있는 서버.   
  - Kakao, Google, Naver 등이 이에 속합니다.   

- Resource Owner : 자원의 소유자.    
  - Client가 제공하는 서비스를 통해 로그인하는 실제 유저가 이에 속한다.    

- Client : Resoure Server에 접속해서 정보를 가져오고자 하는 클라이언트(웹 어플리케이션).    

## OAuth Flow

### Client 등록
- Client(웹 어플리케이션)가 Resource Server를 이용하기 위해서는 자신의 서비스를 등록함으로써 사전 승인을 받아야 한다.

등록 절차를 통해 세 가지 정보를 부여받는다.
- Client ID : 클라이언트 웹 어플리케이션을 구별할 수 있는 식별자. 노출 무방.
- Client Secret : Client ID에 대한 비밀키로서, 절대 노출해서는 안 됨.
- Authorized redirect URL : Authorization Code를 전달받을 리다이렉트 주소.

Google 등 외부 서비스를 통해 인증을 마치면 클라이언트를 명시된 주소로 리다이렉트 시키는데, 이 때 Query String으로 특별한 Code가 함께 전달됩니다. 클라이언트는 해당 Code와 Client ID 및 Client Secret을 Resource Server에 보내, Resource Server의 자원을 사용할 수 있는 Access Token을 발급 받습니다. 등록되지 않은 리다이렉트 URL을 사용하는 경우, Resource Server가 인증을 거부합니다.

### Resource Owner의 승인
Resource Owner는 Client의 웹 어플리케이션을 이용하다가, 해당 주소로 연결되는 소셜 로그인 버튼을 클릭.    
Resource Owner는 Resource Server에 접속하여 로그인을 수행. 로그인이 완료되면 Resource Server는 Query String으로 넘어온 파라미터들을 통해 Client를 검사.
- 파라미터로 전달된 Client ID와 동일한 ID 값이 존재하는지 확인.
- 해당 Client ID에 해당하는 Redirect URL이 파라미터로 전달된 Redirect URL과 같은지 확인.    

검증이 마무리 되면, 명시한 scope에 해당하는 권한을 Client에게 정말로 부여할 것인가?
허용한다면 최종적으로 Resource Owner가 Resource Server에게 Client의 접근을 승인하게 된다.    

### Resource Server의 승인
Resource Owner의 승인이 마무리 되면 명시된 Redirect URL로 클라이언트를 리다이렉트 시킨다. 이 때 Resource Server는 Client가 자신의 자원을 사용할 수 있는 Access Token을 발급하기 전에, 임시 암호인 Authorization Code를 함께 발급한다.   

**Query String으로 들어온 code가 바로 Authorization Code!**   

Client는 ID와 비밀키 및 code를 Resource Owner를 거치지 않고 Resource Server에 직접 전달한다. Resource Server는 정보를 검사한 다음, 유효한 요청이라면 Access Token을 발급하게 된다.    
Client는 해당 토큰을 서버에 저장해두고, Resource Server의 자원을 사용하기 위한 API 호출시 해당 토큰을 헤더에 담아 보낸다.   

### API 호출
이후 Access Token을 헤더에 담아 API를 호출하면, 해당 계정과 연동된 Resource Server의 풍부한 자원 및 기능들을 내가 만든 웹 어플리케이션에서 사용할 수 있다.     

### Refresh Token
> *Refresh Token의 발급 여부와 방법 및 갱신 주기 등은 OAuth를 제공하는 Resource Server마다 상이하다.*

Access Token은 만료 기간이 있으며, 만료된 Access Token으로 API를 요청하면 401 에러가 발생한다. 보통 Resource Server는 Access Token을 발급할 때 Refresh Token을 함께 발급한다. Client는 두 Token을 모두 저장해두고, Resource Server의 API를 호출할 때는 Access Token을 사용한다. Access Token이 만료되어 401 에러가 발생하면, Client는 보관 중이던 Refresh Token을 보내 새로운 Access Token을 발급받게 된다.

---

### 참조
[wecode 노션](https://wecode.co.kr/)    
[Tecoble](https://tecoble.techcourse.co.kr/post/2021-07-10-understanding-oauth/)    
[OPENNARU](http://www.opennaru.com/opennaru-blog/jwt-json-web-token/)
