<p align="center">
<img src="/images/Tobi/tobi_intro.jpg"/>
</p>

토비의 스프링을 공부하며 단순히 읽는걸로는 항상 집중력이 끊기고 졸리기 때문에...블로그에다 포스팅을 올린다는 목적으로 정리해 보려구 합니다. 물론 모든 코드를 담을 수는 없겠지만...개인적으로 제가 새롭게 알고 깨우친 부분과 기초 혹은 핵심이라고 생각하는 이론에 대해서도 정리하고자 합니다. 혹시나 토비의 스프링을 보기전, 아니면 볼 없두가 안나는 분들에게 도움이 됐으면 합니다.

### 스프링이란?
- 스프링: 자바 엔터프라이즈 애플리케이션 개발에 사용되는 애플리케이션 프레임워크

### 스프링 컨테이너
  - 설정정보를 참고로 해서 애플리케이션을 구성하는 오브젝트를 생성, 관리
  - 독립적 동작도 가능하지만, 보통 웹 모듈에서 동작하는 서비스나 서블릿으로 등록해서 사용

### 프레임워크(프로그래밍 모델)
- 애플리케이션을 구성하는 오브젝트가 생성되고 동작하는 방식에 대한 틀을 제공
- 애플리케이션 코드가 어떻게 작성되야 하는지에 대한 기준 제시

##### 1.IOC/DI
- 오브젝트의 생명주기와 의존관계에 대한 프로그래밍 모델
- 스프링의 근간이 된다. (Why? 유연하고 확장성 있게 해주는 개체지향 설계 원칙, 디자인 패턴의 핵심 원리를 담고 있기 때문에)

##### 2.서비스 추상화
- 구체적인 기술과 환경에 종속되지 않도록 해주는 방법
- 이식성이 뛰어나며 유연한 애플리케이션을 만들게 해준다.

##### 3.기술API
- 스프링은 다양한 영역에서 바로 활용가능한 방대한 기술API를 제공한다.
- 모든 기술은 표준 자바 엔터프라이즈 플랫폼에 기반을 두고 있다.

> **Point**: 클래스는 스프링 컨테이너 위에서 오브젝트로 만들어져 동작하게 만들고, 코드는 스프링의 프로그래밍 모델을 따라서 작성하고, 엔터프라이즈 기술을 사용할 떄는 스프링이 제공하는 기술 API와 서비스를 활용하도록 해주면 된다.

### 스프링을 효과적으로 익히는 단계
1)스프링의 핵심 가치와 원리에 대한 이해
- 스프링의 핵심 가치와 그것이 가능하도록 해주는 세 가지 핵심 기술(IOC/DI, 서비스 추상화, 기술API)

2)스프링의 기술에 대한 지식과 선택 기준 정립
- 기본 원리 파악 이후 스프링이 이를 어떻게 다양한 방법으로 확장했는지 파악
- 다양한 방법을 선택하는 문제에 있어서 기술영역별로 효과적으로 다루는 법을 배우는 것(그때그때의 최선을 기술과 접근 방법 선택)

3)스프링의 적용과 확장
- 스프링의 다양한 기술을 실제 개발에 어떤 식으로 적용하는 가
- 스프링이 제공하는 기술을 그대로 적용하는 것 외에도 그것을 확장하거나 추상화해서 사용하는 방법을 배워야 한다.

### 스프링 3.1에 추가된 새로운 기능
- 강화된 자바 코드를 이용한 빈 설정
  - XML을 전혀 사용하지 않고 작성 가능
  - 다양한 애노테이션 추가(XML의 전용 커스텀 태그를 대체할 수 있는, @Enable로 시작하는 전용 애노테이션 제공)
- 런타임 환경 추상화
  - 런타임 환경을 추상화한 환경 오브젝트를 제공
  - 실행환경에 따라 달라지는 빈 설정을 효과적으로 관리하게끔 프로파일과 각종 프로퍼티 정보를 컨테이너를 통해 일관된 방식으로 제공할 수 있게 해주는 프로퍼티 소스다.
- JPA 지원 확장과 하이버네이트4 지원
- 새로운 DispatcherServlet 전략과 플래시 맵
  - MVC기능을 확장하기 편해짐
  - Post/Redirect/Get패턴에 사용할 수 있는 플래시 맵 기능 추가
- 캐시 추상화
  - AOP를 이용한 메소드 레벨의 캐시 추상화 기능이 추가
  - 캐시 구현기술에 독립적인 방식으로 애플리케이션 빈에 캐시 기능을 적용할 수 있게 됐다.(ex.EhCache)

### 들어가며
스프링을 공부하고 적용한 이후에 자신이 좀 더 나은 개발자가 되었는가를 확인해 보는게 좋다.
- 스프링을 사용하고 자신의 코드가 좀 더 깔끔하고 단순하면서 객체지향 원칙에 충실하게 작성됐는지
- 생산성과 품질이 더 나아졌는지
- 이전보다 더 많은 테스트 코드를 작성하고 있는지
- 유연하고 확장이 손쉬운 애플리케이션이 만들어지고 있는지
- **무엇보다 스프링을 사용하고 나서 개발이 좀 더 즐거워졌는지**
- 만약 스프링을 공부했지만 스프링의 사용법에 조금 익숙해진 것을 빼고 나아진 게 없다면 잘못 공부한 것이다.

> **point**: 이책에 나오는 예제코드를 따라하는걸 권장한다. 로드 존슨이 실증적인 접근 방법을 설명하며 얘기한 "컴퓨터에게 물어보라"는 이야기가 있다.
