# Spring Boot Data Rest로 Restful한 API 개발하기
서버 개발자라면 Rest API를 공부해야 합니다. 왜 그럴까요?
첫 째로, 현재 네트워크 통신 아키텍처로 가장 각광받는건 Rest입니다. 이는 누구도 부인할 수 없는 현실이죠. 계속 더 좋고 다양한 아키텍처들(GraphQL...)이 나오고 있지만 향후 2~3년간 Rest는 아마도(?) 변함없이 굳건할 것입니다. 
둘 째로, 갈수록 다양해 지는 브라우저(크롬, 익스, 파폭, 웨일, 스윙..) 및 안드로이드, 아이폰 환경 등 여러 클라이언트 환경에게 데이터를 제공해 주기 위해서 대부분의 회사는 Rest API를 개발합니다. 
그렇기 때문에 서버 개발자라면 당연히 Rest가 뭔지, Restful하게 어떻게 개발할지를 공부해야 합니다. Rest가 뭔지 알았다면 그동안 @RestController로 작성했던 
스프링 MVC 프로젝트들이 떠오를 것입니다. @RestController로 보통 json 규격의 데이터를 제공해 주는데요. 스프링 부트에서는 훨씬 빠르게, 간편하게 그리고 커스터마이징도 쉽게(?) Rest프로젝트를 제공하고 있습니다. 
이제 이유를 알았으니 하나씩 순서데로 파봅시다!

## Rest란?
Rest는 Representational State Transfer으로 쉽게 말해 웹의 장점을 최대화 할 수 있는 통신을 위한 네트워크 아키텍처입니다. 동시에 웹의 기본 아키텍처 원칙입니다. 이는 서버와 클라이언트가 서로 통신하는 리소스에 대해 복자한 방식으로 
상호작용을 할 수 있게 해줍니다. 핵심적인 제약 조건은 웹의 경우 HTML이 사용된 미디어에 대해 서버와 클라이언트가 모두 허용해야 한다는 것입니다. Rest를 만든 ()는 여러가지 원칙을 만들었습니다. 그 원칙을 제대로 지키면서 
Rest 아키텍처를 만드는 것을 Restful이라고 칭합니다. 그럼 Rest 원칙들을 살펴보겠습니다.
- client-server
- stateless
- cache
- uniform interface
- layered system
- core-on-demand(optional)

## Restful하려면 어떻게 해야될까?
Restful하려면 Rest의 원칙부터 알아야 됩니다. 



## @RestController로 Restful한 API를 만들어 보자!


## Spring Boot Data Rest

