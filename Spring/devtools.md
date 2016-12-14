# Spring boot + devtools로 편리한 개발환경 구축
Java Application 개발시 소스가 변할때 마다 서버를 재가동시키고 일일이 버튼을 누르는 일이 매우 귀찮았습니다.(저만 그런가...?)
이러한 비생산적인 요소를 줄이기 위해 이리저리 찾아보다 devtools에 대해 알게되었고 한국어로된 문서를 좋아하시는 분들을 위해 작성해 봅니다.
해당 글은 [document문서][1]를 요약하고 Intellij에서 빠른 환경구축을 할 수 있게끔 작성하였습니다.
> **Point:** 기존에 사용되어진 비슷한 tool로 [Spring Loaded][2]도 있지만 devtools는 보다 편리한 환경세팅 제공합니다.

### 특징
##### Property Defaults
- devtools는 thymeleaf, Freemarker, Groovy Templates, Velocity, Mustache 등 다양한 템플릿에 대한 개발 캐싱을 모두 자동 비활성화 처리하였습니다.
##### Automatic Restart
- 어떠한 classpath 파일이 변경되면 자동으로 응용 프로그램이 다시 시작됩니다.
##### LiveReload
- LiveReload는 소스에 변화가 있을 때 application이 자동으로 브라우저 새로 고침을 트리거 할 수 있게 해주는 프로토콜입니다.
- livereload.com에서 Chrome, Firefox, Safari용 플러그인을 설치하여 사용할 수 있습니다.(npm의 hot-reload-server처럼 새로고침없이 바로 갱신됩니다.)

### 간단 설정
![](/images/Spring/devtools/1.build.png "build.gradle 설정")<br>build.gradle파일에 devtools dependency를 추가해 줍니다.

![](/images/Spring/devtools/2.registry_path.png)<br>intellij에서 Action을 찾는 단축키인 `ctrl+shift+A`로 registry를 검색합니다.

![](/images/Spring/devtools/3.registry_setting.png)<br>`compiler.automake.allow.when.app.running`을 체크해 줍니다.

![](/images/Spring/devtools/4.settings.png)<br>Settings -> Build -> Compiler에서 `Build project automatically`를 체크해 줍니다.

### Property Setting
기본 default설정이 되어 있지만 커스텀하게 설정을 바꾸고 싶으면 방법은 다음 두가지 방법이 있습니다.

![](/images/Spring/devtools/5.yml.png)<br>application.yml 설정

![](/images/Spring/devtools/6.application.png)<br>서버코드 main class 설정

### 추가팁
livereload기능을 사용하기 위해선 Chrome, Firefox, Safari용 플러그인을 각각 사용하시는 브라우저에 추가해 주어야 합니다.
크롬의 경우를 예를 들어 설명하겠습니다.

![](/images/Spring/devtools/7.livereload.png)<br>[chrome livereload plugin 다운][3]

![](/images/Spring/devtools/8.button.png)<br>livereload 기능 on!

![]()


devtools 설정이 모두 완료되었습니다. 모든 소스는 [guithub][4]에서 보실 수 있습니다.

[1]: https://spring.io/blog/2015/06/17/devtools-in-spring-boot-1-3
[2]: https://github.com/spring-projects/spring-loaded
[3]: https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei?hl=ko
[4]: 