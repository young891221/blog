# Spring Boot & OAuth2 기반 소셜 댓글 시스템 개발하기
회사에서의 잉여력 + 개인의 잉여력을 가지고 회사에도 도움이 되고 개인적인 공부에도 도움이 되는 프로젝트를 진행해 보고 싶었습니다. 
제가 뉴스나 혹은 기타 블로그글을 볼 때도 대부분 글의 마지막까지 내려 댓글을 보는데 대부분이 소셜댓글이었습니다. 그래서 공부도 하고 제 서비스에 댓글기능도 붙일겸 직접 소셜 인증(페이스북, 구글, 트위터, 카카오) & 간단한 댓글 Getting Started를 
개발하여 보기로 야심찬 계획을 세웠습니다. 그대로 사용할 순 없겠지만 Getting Started 느낌의 심플한 프로젝트로 구성해 보았습니다. 모든 소스는 [github](https://github.com/young891221/spring-boot-social-comment)에 있습니다.

### 개요
목표는 페이스북, 구글, 트위터, 카카오 등 국내에서 많이 쓰이는 서비스들의 OAuth인증을 통한 댓글 시스템 구현하기!<br>
여기서 트위터를 제외한 다른 인증은 모두 OAuth2를 사용합니다. Spirng에는 이를 구현한 고마운 라이브러리인 Spring Social과 **Spring Security OAuth2**가 있습니다. 전자의 경우 마지막 업데이트가 2년전이고 정해진 디비 스키마에 데이터가 저장되는 방식이라 
커스터마이징한 개발이 가능한 후자를 선택하였습니다. Spring Security OAuth2는 인증과 개인정보 API가 내부로직을 몰라도 될 만큼 편리하게 구현되어 있어 그냥 갔다 쓰시면 됩니다. 그래도 개발자라면 어떻게 동작하는지 정도는 알아야 겠다는 마음가짐(?) 
때문에 동작 프로세스 정도는 소스를 까보면서 분석해 보았습니다.(추후 세션에서...)<br>
>cf.트위터 developer 사이트를 들어가 보시면 [Application-only authentication](https://dev.twitter.com/oauth/application-only)으로 OAuth2를 제공해 준다고 설명되어 있습니다. 
하지만 이는 Client Credentials Grant로 Application 본인에 대한 인증만 사용할 수 있고 유저에 대한 정보를 가져올 수 없어서 제가 만드는 프로젝트에서는 부합하지 못하였습니다. 다음 세션에서 더 자세히 설명하겠습니다. 

### 필요한 정보 모으기(OAuth2란?)
OAuth는 임시 인증을 위한 방식으로 Token을 사용하는데 이를 표준적인 방법으로 통일한 것입니다. OAuth2는 OAuth protocol의 2버전입니다. 이 프로토콜은 3rd party를 위한 범용적인 인증 표준이 됩니다.
OAuth2에서 제공하는 인증 타입 방식은 현재 4가지가 있습니다.(Authorization grant types, Implicit Grant, Resource Owner Password Credentials Grant, Client Credentials Grant 등) 
그중 저에게 필요한 방식은 **Authorization grant types**입니다. Authorization grant types는 웹 서버에서 long-lived access token을 사용하여 사용자 인증을 처리하는 방식으로 제가 선택한 
페이스북, 구글, 카카오가 사용하는 방식입니다. 아래 그림의 Flow를 보시면 더 이해하기 쉽습니다.

<p align="center">
<img src="/images/Spring/oauth2/auth_code_flow.png"/>
</p>

- Resource Owner: 인증이 필요한 유저
- Client: 웹 사이트
- Authorization Server: 페이스북/구글/카카오 서버
- Resource Server: 페이스북/구글/카카오 서버

반면에 개요에서 말한 트위터가 제공하는 **Client Credentials Grant** 방식은 client 자신이 resource owner가 되는 방식입니다. 이 방식은 client 이외의 다른 resource owner로부터 정보를 얻을 수 있는 권한이 없습니다. 
**애초에 resource owner가 없으므로 사용자의 개인정보를 얻을 수 없는 방식입니다.** 따라서 제가 만드려는 소셜 댓글 플랫폼에는 사용할 수 없었습니다.(그래서 트위터는 OAuth1방식으로...ㅠㅠ)

<p align="center">
<img src="/images/Spring/oauth2/client_credentials_flow.png"/>
</p>

>번외로 Implicit Grant 방식은 Authorization grant types처럼 서버와 서버에서 인증을 수행하는 방식으로 클라이언트가 token이나 secret이 노출되지 않는 것과는 다르게 javascript처럼 resource owner쪽에서 전적으로 인증을 수행하는 방식입니다.
 
### 프로젝트 환경
다음은 프로젝트 개발 환경과 사용한 라이브러리입니다.
- Java8
- Spring Boot 1.5.2
- Spring Security Oauth2
- Spring Social Twitter
- JPA
- lombok
- logback
- Gradle 3.5
- h2
- embedded redis(for session)
- Freemarker
- handlebars.js

### 설계
인증요청시 Spring Security에 설정된 필터를 통해 인증이 수행되고 인증이 완료되면 redis를 사용해 세션을 담습니다. 회원에 대한 정보는 h2 db에 심플한 정보를 담고 사용합니다.
<p align="center">
<img src="/images/Spring/oauth2/architecture.png"/>
</p>

### 기본 구현 프로세스
소셜관련 clientId, clientSecret의 정보를 application.yml에 넣어줍니다.
<p align="center">
<img src="/images/Spring/oauth2/social_config.png"/>
</p>

Security설정에서 `OAuth2ClientAuthenticationProcessingFilter`라는 인증처리용 필터를 하나 가져와서 소셜별로 필요한 config들을 설정해 줍니다. 그리고 마지막에 `FilterRegistrationBean`라는 놈에게 소셜 필터를 set해주고 빈으로 등록해 줍니다. 
`FilterRegistrationBean`는 Security 이외에도 필터를 한곳에 몰아서 등록해도 상관 없습니다.
<p align="center">
<img src="/images/Spring/oauth2/source1.png"/>
</p>

인증이 완료되면 위의 필터의 `setAuthenticationSuccessHandler`에서 설정한 경로로 리다이렉트되는데 저는 AOP를 사용하여 `@SocialUser`라는 파라미터를 가진 놈들에게 세션에 있는 user 데이터를 바로 반환하거나 인증이 완료된 후 
userDetails에 관한 정보를 User 객체에 맵핑하여 db에 저장해 주고 애노테이션에 선언된 user 객체에 바인딩시켜주는 방식을 사용하였습니다. 한마디로 `@SocialUser`를 선언한 파라미터는 user에 대한 정보를 가져올 수 있습니다. 
<p align="center">
<img src="/images/Spring/oauth2/source1.png"/>
</p>

이 부분에 대한 자세한 소스는 [이곳](https://github.com/young891221/spring-boot-social-comment/blob/master/social-comment/src/main/java/com/social/aop/UserAspect.java)을 참조하세요.
<br>
댓글은 1댑스의 대댓글 기능을 만들고자 하였으나 귀차니즘이 발동하여...그냥 일반적인 댓글 하나씩만 달도록만 하였습니다.

### 결과
로그인 인증만 테스트해 볼 수 있는 페이지와 댓글기능 사용시 인증할 수 있는 페이지 2개로 구성되어 있습니다. 댓글을 제공 방식은 view나 Json으로 데이터를 전송하는 방식을 생각해 보았습니다.
- http://localhost:8080/comment/{service}/{id}
- http://localhost:8080/json/comment/{service}/{id}
<p align="center">
<img src="/images/Spring/oauth2/view_json.png"/>
</p>
<p align="center">
<code>css는 넘나 어렵네요..</code>
</p>

