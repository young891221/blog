# 스프링 캠프 2017(토요일)
올해 첫 대규모 세미나인 스프링 캠프에 참석하게 되었습니다. 어벤져스급 연사님들이 나오시는 세미나에서 하루 동안 많은 꿀팁들을 얻을 수 있었습니다. 모든 것을 다 정리하고 싶은 
욕심은 넘쳤지만 정신없이 듣느라 간략하게 제 느낌(?)데로 정리해 보았습니다.

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/1.jpeg"/>
</p>

## 1.keynote(박성철님)
멍하게 듣다가 시간이 흘러갔습니다. 예전 피씨의 탄생 초창기부터 현재까지 그리고 앞으로는 어떻게 흘러갈지를 생각해 볼 수 있는 시간이였습니다.

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/2.jpeg"/>
</p>

### 프레임
- 근접학: 사람은 친밀감에 따라 허용하는 접근 범위가 달라진다.
- 디지털 기반의 혁신(디지털 트랜스포테이션)

### 앞으로 35년?
거대한 프레임 속에서 개발자들은 어떻게 해야 할까? 빅데이터, 리액티브, 머신러닝... 이러한 기술들이 왜 필요할까?

### 소프트웨어 정신이 담긴 책
- 소프트웨어 개발(스티브 맥코넬)
- 클린 코더(로버트 마틴)
- 소프트웨어 장인(로버트 마틴)

## 2.프로세스와 스레드, NIO 그리고 리액티브 스트림(부종민님)
초반에 프로세스와 스레드에 대한 기본 개념에서 부터 이를 비동기 프로그래밍에서 어떻게 이해할 수 있는지 전체 기본 사이클에 대해 설명해 주셨습니다. 너무 많은 양을 다루시다 보니 
빠르게 넘어간 부분들이 많아 상세히 정리하지 못하여 아쉬웠습니다ㅠㅠ(자세한 사항은 나중에 슬라이드에서..)

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/3.jpeg"/>
</p>

### 프로세스 - 독립된 실행의 단위(스케쥴러의 유무로 나눌 수 있음)
- 과거
    - cpu - 프로세스가 독점
    - memory - 프로세스가 메모리 공간을 자유롭게 사용

>프로세스 = 스레드 + 메모리공간

- 가상메모리는 프로세스 간 침범을 거부한다
- cpu 하나가 1초, 메인 cpu를 돌리는데 6분 그래서 L1, L2, L3를 두어 캐싱 측면에서 스레드 모델 성능이 좋다.
- ThreadPool - 스레드를 미리 만들어 놓고 재사용 하는 것
- 비용감소
    - cpu, memory: 스레드 생성 비용 감소
- 스레드에 한번에 많은 요청이 와서 full이 될 경우를 대비해 스레드 관리자를 사용해서 Queue를 통해 분산한다.
- JVM 스레드 비용을 줄이기 위해서는 Gavage Collection에 신경을 많이 써야 한다.

### 비동기
필요한 상황은?
- CPU expensive
- I/O blocking: 이 상황에서 스레드가 더 많이 쌓인다.
    - 그림을 보고 이해(멀티 쓰레드로 병렬적으로 돌려서 시간을 단축)
- 비동기 코드가 어려운 점: 실행 순서를 잡기 어렵다.
- 객체는 heap 영역에 저장되며 heap영역에 저장된 것은 스레드간 공유가 가능하므로 이를 통해 쓰레들간의 자원을 공유한다.
- 스레드들 간의 동기화 작업 필요(wait, notify)
    - notify를 빼먹는 다면? 더 추상화된 기술은 Future Interface
    - V get(): 값을 가져오기 위해 wait, noify를 하나로 추상화
    
<p align="center">
<img src="/images/Seminar/2017-SpringCamp/4.jpeg"/>
</p>

### 느낀점
- 쓰레드를 직접 사용하는건 쉽지 않다.(지식 + 경험)
- Future는 단일값만 처리하기에 복합적인 값은 어렵다.
- threadpool에 대해서도 이해가 필요하다.
    - 꽉 찬 상황에서 큐에 계속 테스크가 쌓이는 데 작업이 오래걸리는 경우 응답시간이 계속해서 느려진다.
    - 잘못 사용한 treadpool: newCachedThreadPool(클라이언트가 많이 들어왔을때 그만큼 thread의 필요개수가 증가하여 서버가 느려짐)

### 도입
- class CompletableFuture
    - interface Future<T>
    - interface completionStage<T>

### NIO(New IO)
아직 풀지 못한 상황은 ThreadPool Full이 난 상황 -> Non Blocking

### 정리

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/5.jpeg"/>
</p>

### 자기개발 투자에 대한 정리
- 범위를 좁혀보자!
    - 현장에서 가장 많이 쓰이는 자바, 스프링
    - 핫한 리액티브 프로그래밍
- backpressure: back pressure valve(배압밸브)

>코드시연은 예제로~(이후의 슬라이드를 참조!)

## 3.Async & Spring(토비님)
갓토비님의 발표! 이해하기 쉽고 명료하며 막힘이 없었습니다. 마치 발표 내내 토비의 스프링을 읽는 느낌이였습니다.
- 시작은 본인 소개로 "토비의 스프링이란 얇은 책을 쓴 사람입니다!"

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/6.jpeg"/>
</p>

### 동기 vs 비동기
- 어떠한 개념을 숫자로 치환해서 생각하는 개념
- Singleton: 1(이건 확 와닿았다)
- Dependency Injection: 3(서드 파티 라이브러리를 주입한다는 개념)
- Synchronous/Asychronous: 2+(함께 시간을 맞춘다는 뜻 / 시간을 맞추지 않은...)
- A와 B가 시작시간 또는 종료시간이 일치하면 동기, 일치하지 않으면 비동기
- 어떠한 작업들이 연속적인 흐름으로 처리된다면 동기, 그 반대는 비동기(자세한 사항은 슬라이드의 그림을 참조하면 더 명확하게 이해된다)

### @Async
- String 타입은 Async가 지원하는 타입이 아니라 null이 반환된다.
- 그렇다면 허용되는 타입은?
    - void
    - Future<T>
    - ListenableFuture<T> - 결과를 호출할 때 callback을 사용한다는게 listenable하다는 의미
    - CompletableFuture<T> 

### SimpleAsyncTaskExcetor
- @Async를 아무 설정없이 사용하면 낭비 된다.
- 쓰레드 풀은 쓰레드 자원을 재사용하고 버린다.(쓰레드의 효율성 업)
- 다음 타입의 빈이 하나만 존재해야 한다.
    - Executor
    - ExecutorService
    - TaskExector

### 비동기 @MVC
결론은 Completable...을 꼭 써라!

### 비동기 논블로킹 API 호출
- RestTemplate은 동기-블록킹 방식(만약 3초간 통신이 지연된다면 쓰레드는 계속 잡고 있다)
- 이를 위해 `AsyncRestTemplate`를 써서 비동기적으로 사용하다.
- 적용하면 기존에는 100초 걸릴 것이 1.xxx초가 걸린다(사진)
- 이는 쓰레드를 100개로 써서 작업해 버리기 때문이다.
- Netty를 같이 써야 한다. 쓰레드를 1개만 잡도록!

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/7.jpeg"/>
</p>
<p align="center">
<img src="/images/Seminar/2017-SpringCamp/8.jpeg"/>
</p>

### 비동기 API 호출의 조합과 결합은 어떻게?
CompletableFuture의 이름의 뜻이 무엇인가? 예외가 생기든 결과가 생기든 Complete한 결과를 만들어 줄 수 있다.

### 결론
- @MVC
- @Async와 적절한 TaskExecutor(쓰레드 범위 지정)
- Adf(?)
- TaskExecutor(쓰레드풀)의 전략적 활용이 중요

### 비동기 스프링 기술은?
- 모르면 그냥 쓰지 말자
- 설명할 수 있어야 하고
- 증명할 수 있어야 한다.

### 비동기 스프링 기술을 사용하는 이유?
- IO가 많은 서버 앱에서 서버 자원의 효율적으로 사용해 성능을 높이려고(낮은 레이턴시 높은 처리율)

### 단점
- 잘못쓰면 코드는 복잡
- 디버깅 힘들다
- 그런데 아무런 혜택이 없을 수도 있다.

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/9.jpeg"/>
</p>

## 4.Spring Web Flux(토비님)
원시적인 코드로 시작하여 이를 왜 쓰고 어떠한 점이 단점이고 어떠한 점이 장점인지 정확하게 알려주셨습니다. 원시적인 코드는 '아 이러한 형식이구나! 그런데, 실제로 사용하기에는 직관적이지도 않고 
별로겠네~'라고 생각하면 기다렸다는 듯이 '어떤 분들은 이게 별로라고 생각하시죠? 제가 별로였으면 발표주제로 가지고 나왔겠습니까! 하핳' 이러시면서 WebFlux의 최적화된 마지막 구조까지 보여주셨습니다. 
이것도 마치 토비의 스프링을 읽는 느낌이...오늘의 제일 감명깊은 명강의였습니다.

### Spring 5.0
- 구 Spring-web-Reactive
- 스프링 5의 메인 테마는 원래 JDK9이었는데 이제는 WebFlux으로 바뀌었다.
- 스프링 리액티브 스택의 웹 파트 담당

### 용도
- 비동기-논블록킹 리액티브 개발에 사용
- 효율적으로 동작하는 고성능 웹 어플리케이션 개발
- 서비스간 호출이 많은 마이크로서비스 아키텍처에 적합

### 2가지 개발방식 지원
- 애노테이션 방식(@MVC)
- 새로운 함수형 모델
    - RouterFunction
    - HandlerFunction

### 새로운 요청-응답 모델
- 서블릿 스택과 API에서 탈피
    - 서블릿 API는 리액티브 함수형 스타일에 적합하지 않음
- ServerRequest, ServerResponse

### 지원 웹 서버/컨테이너
- Servlet 3.1+ (Tomcat, Jetty, ...)
- Netty
- Undertow

### 함수형 스타일
스프링이 웹 요청을 처리하는 방식
- 요청 매핑 - @RequestMapping
- 요청 바인딩 - 웹 URL, 헤더, 쿠키, 바디
- 핸들러 실행 - 전달 받은 요청 정보를 이용해 로직을 수행하고 결과를 리턴
- 핸들러 결과 처리(응답 생성)

### 함수형 스타일
- 요청 매핑 - RouterFunction
- 요청 바인딩 - HandlerFunction
- 핸들러 실행
- 핸들러 결과 처리(응답 생성)

>결과값은 꼭 Mono값에 넣어서 반환해야 한다. 기타 약속된 반환타입으로만...

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/10.jpeg"/>
</p>

### RouterFunction의 빈 등록
- RouterFunction 타입의 @Bean으로 만든다.
- 하지만 기존의 컨트롤러 처럼 여러 메소드를 하나의 빈으로 등록하는 것이 아니라 하나의 메소드만 빈으로 등록하는 방법이다.

### 하나의 빈에 여러개의 router를 정의할 수 있는 방법
- and(), andRoute()
- nest() - prefix의 개념

### WebFlux 함수형 스타일의 장점
- 모든 웹 요청 처리 작업을 명시적인 코드로 작성
    - 메소드 시그니처 관례와 타입 체크가 불가능한 애노테이션에 의존하는 @MVC 스타일보다 명확
    - 정확한 타입 체크 가능
- **추상화에 유리**
- **테스트 작성의 편리함** - 간단한 단위 테스트가 가능(기존에는 컨테이너를 띄워서 해야 하고 불편하지만...)

### 단점
- 함수형 스타일의 코드 작성이 편하지 않으면 이해하기 어려움
- 익숙한 방식으로도 가능한데 뭐하러

### @MVC WebFlux
- 애노테이션과 메소드 형식의 관례를 이용하는 @MVC방식과 유사
- 매핑만 애노테이션 방식을 이용
- 거의 그대로 사용하지만 Mono, Flux로만 리턴

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/11.jpeg"/>
</p>

### WebFlux만으로 성능이 좋아질까?
- 비동기-논블록킹 구조의 장점은 블록킹 IO를 제거하는 데서 나온다.
- HTTP 서버에서 논블록킹 IO는 오래 전부터 지원

### 개선할 블록킹 IO
- 데이터 액세스 리포지토리
- HTTP API 호출
- 기타 네트워크 서비스 호출

### JPA - JDBC 기반 RDB 연결
- 현재는 답이 없다.
- 향후 DB쪽에서 비동기 작업에 대한 지원이 있을것이란 희망적인 이야기
- @Async 비동기 호출과 CFuture를 리액티브로 연결하고...

### 본격 리액티브 데이터 액세스 기술
- MongoDB
- Redis
- Cassandra

## 5.Spring Cloud Data Flow(정윤진님)
Pivotal에서 일하고 계신분...(우왕!) 세련된 슬라이드와 그림으로 구성되어 있어 이해하기 좋았습니다. 대신 그만큼 정리하기 보다는 듣게 되는 강의!

<p align="center">
<img src="/images/Seminar/2017-SpringCamp/12.jpeg"/>
</p>
<p align="center">
<img src="/images/Seminar/2017-SpringCamp/14.jpeg"/>
</p>

- Spring Cloud Data Flow에 HTTP가 요청 된다면 이에 대한 pipeline이 필요하다
- 등록을 하고 데이터에 대한 pipeline을 만들고 마지막에 부트를 이용하여 클라우드 환경에 배포하는 것이 Spring Cloud Data Flow이다.
- `spring-cloud-stream-app-starters`

## 6.gㅏ벼운 RPC, gRPC(빠르고 가벼운 Polyglot RPC framework)(오명운님)
제목이 처음에는 오타인줄 알았는데 발표를 듣고 보니 개그에 욕심이 있으신 분이셨습니다. 평소에 블로그나 페북 프로필 사진이 인상적이여서 꼭 보고 싶은 분이셨는데 드디어 봤습니다...
깔끔하고 재미있는 진행으로 시간가는줄 몰르고 강의를 들을 수 있었습니다.

### Remote Procedure call
- 원격과의 연결
- 호출/데이터 형식을 어떻게 맞출 것인가
>->stub

### RPC는 망했나?
- 아니다, 구현체가 별로 였을 뿐..
- 게임, 실시간 금융 거래, 모니터링 등

### Stubby
- stub의 애칭을 써서 만든거 같다.
- 오픈소스가 아니다.
- 초당 백억...작업 처리

### gRPC
- Stubby의 후계자
- 지알피씨
- 10개 언어 지원
- 2016년 8월 1.0.0 출시

### gRPC가 REST보다 나은 점
- 가볍다!
- Binary Protocol
    - Text보다 더 적은 데이터 공간으로 처리 가능
    - CPU, 네트워크 성능 업!
- HTTP/2
    - Connection Mulitplexing
    - Header Compression 가능(네트워크 성능!)
    - Client/Server 양방향 Streaming 가능(적용 분야 다양성)

### gRPC가 REST보다 나쁜 점
- 브라우저에서는 아직 사용 불가
- client 쪽 업데이트
- 데이터가 NOT Human Readable
- REST보다는 조금 복잡

### gRPC가 REST보다 나은 점
<p align="center">
<img src="/images/Seminar/2017-SpringCamp/16.jpeg"/>
</p>

- proto 파일 하나면 10가지 대세 언어로 자동 생성 가능
- Polyglot - 다른 언어로 된 서버와 통신하는데 무리가 없다.
- JSON, XML, Thrift
- 복잡해보이지만 반은 자동으로 먹고 들어간다.

### gRPC는 이런 곳에 쓰면 좋다
- 브라우저 필요 없는 백엔드 서버 간 통신
- 자원이 빈약한 디바이스와 서버 간 통신
- 바이트/호출/CPU 수 등으로 과금되는 곳에 비용 절약

### gRPC 사용 사례
<p align="center">
<img src="/images/Seminar/2017-SpringCamp/17.jpeg"/>
</p>

### gRPC 4가지 스트리밍 방식

### gRPC 3가지 stub
- BlockingStub
- (Async)Stub - Async 방식을 기본으로 함..
- FutureStub

### 서버 개발 순서
<p align="center">
<img src="/images/Seminar/2017-SpringCamp/18.jpeg"/>
</p>
<p align="center">
<img src="/images/Seminar/2017-SpringCamp/19.jpeg"/>
</p>

>감기기운이 있어 제대로 집중을 못했던게 아쉽습니다. 일찍 자고 내일을 기약해봅니다.<br>
내일은 같은 팀 권용근씨의 Spring Boot에 대한 발표가 있습니다. 살짝 듣기로도 많은 꿀팁들을 공유해 주신다는 소문이!? boot에 관해 알고 있더라도 참석하시면 많은 것을 얻을 것 같습니다.