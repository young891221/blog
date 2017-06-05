# Spring Boot & OAuth2 기반 소셜 댓글 시스템 개발하기 1
회사에서 제가 맡은 주 서비스인 허브줌에는 댓글기능이 없습니다. 계속해서 서비스는 커져가고 있고 그만큼 서비스에 대한 저의 사랑(?)도 켜져서 허브줌에게 좋은 아이템을 선물해 주고 싶었습니다. 
제가 뉴스나 혹은 기타 블로그글을 볼 때도 댓글을 꼭 마지막으로 보는데 대부분이 소셜댓글이었습니다. 몇몇 인기있는 곳을 서칭해 보니 주로 페이스북 플러그인을 사용하던가 커스터마이징하게 개발하여 사용하고 있었습니다. 
그래서 공부도 할겸 직접 OAuth2 & 댓글 플랫폼(?)을 개발하여 보았습니다. 어디 회사든 자신들의 기획과 요구사항이 다르므로 그대로 가져다 사용할 순 없겠지만 공통된 뼈대는 같게끔 심플한 프로젝트가 되도록  
노력하였습니다. 1편은 OAuth2 인증에 관하여 2편은 댓글에 관해서 정리하였습니다.

### 개요
목표는 페이스북, 구글, 트위터, 카카오 등 국내에서 많이 쓰이는 서비스들의 인증을 바탕으로 댓글 시스템 구현하기!<br>
여기서 트위터를 제외한 다른 인증은 모두 OAuth2를 사용합니다. Spirng에는 이를 구현한 고마운 라이브러리인 Spring Security OAuth2가 있습니다. 우리는 이를 그냥 갔다 쓰면 됩니다. 그래도 개발자라면 어떻게 동작하는지 정도는 알아야 겠다는 마음가짐(?) 
때문에 동작 프로세스 정도는 소스를 까보면서 분석해 보았습니다.(추후 세션에서...)<br>
트위터 사이트를 들어가 보시면 [Application-only authentication](https://dev.twitter.com/oauth/application-only)으로 OAuth2를 제공해 준다고 설명되어 있습니다. 
하지만 이는 Client Credentials Grant로 Application 본인에 대한 인증만 사용할 수 있고 유저에 대한 정보를 가져올 수 없어서 제가 만드는 프로젝트에서는 부합하지 못하였습니다. 다음 세션에서 더 자세히 설명하겠습니다. 

### 필요한 정보 모으기(OAuth2란?)
OAuth는 임시 인증을 위한 방식으로 Token을 사용하는데 이를 표준적인 방법으로 통일한 것입니다. OAuth2는 OAuth protocol의 2버전입니다. 이 프로토콜은 3rd party를 위한 범용적인 인증 표준이 됩니다.
OAuth2에서 제공하는 인증 타입 방식은 현재 4가지가 있습니다.(Authorization grant types, Implicit Grant, Resource Owner Password Credentials Grant, Client Credentials Grant 등) 
그중 저에게 필요한 방식은 **Authorization grant types**입니다. Authorization grant types은 웹 서버에서 long-lived access token을 사용하여 사용자 인증을 처리하는 방식으로 제가 선택한 
페이스북, 구글, 카카오가 사용하는 방식입니다. 아래 그림의 Flow를 보시면 더 이해하기 쉽습니다.

<p align="center">
<img src="/images/spring/oauth2/auth_code_flow.png"/>
</p>

- Resource Owner: 인증이 필요한 유저
- Client: 웹 사이트
- Authorization Server: 페이스북/구글/카카오 서버
- Resource Server: 페이스북/구글/카카오 서버

반면에 개요에서 말한 트위터가 제공하는 **Client Credentials Grant** 방식은 client 자신이 resource owner가 되는 방식입니다. 이 방식은 resource owner로부터 얻을 수 있는 권한이 없습니다. 
애초에 resource owner가 없으므로 사용자의 개인정보를 얻을 수 없는 방식입니다. 따라서 제가 만드려는 소셜 댓글 플랫폼에는 사용할 수 없었습니다.(그래서 트위터는 OAuth1방식으로...ㅠㅠ) 

<p align="center">
<img src="/images/spring/oauth2/client_credentials_flow.png"/>
</p>

>번외로 Authorization grant types은 서버와 서버에서 인증을 수행하는 방식으로 클라이언트가 token이나 secret이 노출되지 않지만 Implicit Grant 방식은 javascript처럼 resource owner쪽에서 전적으로 인증을 수행하는 방식입니다.
 
### 프로젝트 환경
- Java8
- Spring Boot 1.5.2
- Spring Security Oauth2
- Spring Social Twitter
- JPA
- lombok
- logback
- Gradle 3.5
- h2, redis


