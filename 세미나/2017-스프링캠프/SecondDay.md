# 스프링 캠프 2017(일요일)

## keynote(정상혁님)
네이버 PaaS 소속

### RowMapperResultSetExtractor

### AbstractCachingViewResolver
- veiwResolver를 캐시해서 씀
- LRU 캐시를 쉽게 구현한 케이스

### ObjectUtils.nullSafeEqauls() 관련 최적화
- jhoeller님은 PR을 잘 안받아 줌
- 간단한 debug level log 수정도..

### List vs ArrayList 논쟁
- 사소한 댓글도 주변분일 수 있다
- 심지어 댓글을 쓴 사람이 발표자(?)일 수 있다

### 백기선님
- 현 MS, 구 네이버 개발자

### 임정묵님
- 현 네이버 소속 개발자
- 한국의 개발자가 스프링부트 커미터 5위권!(리스펙)

### 발표자의 기여 시도
- 배치에 대한 버그를 테스트코드와 몇개의 로직 수정으로 PR반영(master 반영!)
- 기타 여러가지 commit!

### 기여방법
- Contributing.md 참조
    - JIRA에 이슈 먼저 등록
    - CLA 서명
- 기여 대상으로 분류된 이슈부터 보기
    - Spring Boot에서 ideal-for-contribution 라벨이 붙은 이슈
- Nicoll 아저씨가 PR을 잘 받아주신다.
- 실험적인 프로젝트에 기여
    - pivotal 주요 개발자들의 개인 계정에 있는 경우도(실험적으로 하는 프로젝트가 왠지 뜰거 같다는 느낌이 들 때)

### 돌아보니
- 코드 구경 -> 댓글 -> 기여 시도
- Spring/JSUG가 더 적극적인 행동/넓은 사람들에 다가가는 디딤돌

## Reactive Spring(Spring 5 & Reactor)(정윤진님)
- 조쉬 롱(Josh Long)이라는 자바 챔피언이라는 분과의 화상 통화!
- 어제 토비님은 코드레벨로 들어갔다면 이번 세션은 어떠한 모습일지 중복되지 않는 컨텐츠를 담고 있습니다.
- pivotal에서 일하시는 분!

### 아이스브리킹!(간략하게 전체적인 모습 설명)
- Netflix 2008
    - Netflix의 기원에 대해 설명
    - 2008년 장애때의 상황 설명
        - 거대한 디비와의 커넥션이 발생하며 굉장히 큰 손실이 있었다는 내용
        - 이때 깨달은 점은 장애고립이 되지 않았다.
        - 디비의 장애가 전체 시스템에 영향을 주는...
- Netflix 2015
    - 마이크로서비스 아키텍처를 구성
- 아마존 닷컴
    - 녹색으로 박스처진 부분들이 각각 하나의 서비스다.
    - 하나의 페이지에 굉장히 많은 서비스가 들어가 있는 마이크로 서비스 구성
    
>이러한 서비스의 분산이 리액티브 서비스가 지원하는 가치관!

### Spring 5
- Spring 5 Key: **JDK9, HTTP/2, Reactive**
- 현재 5.0.0.M5
- GA Target Date: 6월20일
    - Spring 5.1 - 2017년 12월
    - JDK9 - 7월27일
- Major baseline 업데이트

### JDK8+ Baselilne
- JDK 업그레이드 주의
- Java8 Type 사용 가능

### JDK9 지원
- Jigsaw - 새로운 Module System
- HTTP/2 지원
    - NIO, java.util.concurrent 사용

### Jigsaw
- 5.0 M5 현재 완벽한 지원 x

### HTTP/2
- google, twitter 등 주요 사이트에서 사용중
- TLS(SSL) everywhere
- binary protocol
- Servlet 4.0(Spring 5.1 부터 지원 예정)
- 특정 was에만 국한되지 않고 범용성을 지원하기 위해 노력(tomcat, jetty, undertow)

### Project Reactor
- Classic Scale!
- 현재 기술들은 과거의 기술들에 의존하는 부분들이 꽤 있다!
- 요청이 들어오면 요청에 대한 쓰레드가 필요하게 된다.
- 각각의 스레드에 대한 Processing Latency(요청처리)가 다르다.
- 그래서 ThreadPool을 생성하고 가용가능한 쓰레드를 배치하게 된다.
- 이보다 더 진화해서 로드 벨런서가 가용가능한 서버를 체크하여 사용하게 된다.(하지만 이도 옛날 방식)
- 결국 이러한 서버 늘리는 구조는 아마존 사장님의 돈을 늘려주는 방향으로..

>여기서 다른 방향의 장점을 취할 수 있는 것이 리액티브 프로그래밍이라 생각한다. 결국에는 Non Blocking Runtimes 문제! 쓰레드를 각각 필요한 만큼 만들지 않으면 되자나!

- 요청을 일종의 퍼블리싱 입장
- 쓰레드를 만들어서 포커싱하기 보다는 ??
- 동일한 리소스를 가지고 더 많은 작업을 처리하게 되는 장점

### 리액티브가 주는 장점(중요)
- 응답성
    - 요청이 들어오면 적시에(최소시간내) 응답을 줄 수 있어야 한다.
    - 시스템은 언제나 응답가능한 상태로 존재
- 탄력성
    - 서버들을 동적으로 늘리거나 리퀘스트가 줄어들면 동적으로 줄어들어야 하는데 이러한 상황에서 탄력적으로 대응할 수 있어야 한다.
- 회복성
    - 장애에 직면한 상태에서도 응답성을 유지할 수 있어야 한다.
    - 크리티컬한 시스템에만 국한되지 않는다.
- 메세지 중심
    - 리액티브 시스템은 느슨한 결합의 방법을 사용하는 컴포넌트 사이에서 비동기의 방법으로 메세지를 전달한다.
    - 메세지 방식이 명확하게 일의 분류를 해놓고 자신은 자신의 일에만 더 집중할 수 있게 해준다.(던저준 작업이 끝나지 않더라도 나는 내일을 처리한다)

### Reactive Programming
- 분산 시스템에서도 일관된 동작(consistence & response)을 보장해 주는 시스템
- Microservice가 지향하는 방향

### Reactive Streams 스펙
- 인프라간 상호 운용성에 집중(웹서버, 데이터 저장소 드라이버 및 웹 프레임워크)
- 논-블로킹 백프레셔 제공하는 비동기 스트림 프로세싱 표준
    - **블로킹과 논블로킹의 차이는 들어온 요청에 대해 민 타임내에 처리될 수 있다면 이를 논블로킹(비동기)라고 생각**한다.(발표자분의 생각!)
    - 백프레셔 
        - 수도관에 대한 얘기가 제일 많이 나옴
        - 배관에 따른 적정압력이 있는데 초과하는 양이 들어올 때를 백프레셔라 말함!
        - 가져가려는 놈이 얼마나 가져가려는지 몰르는데 무조건 요청을 때려 넣을 때
- 단순한 API 구조

### Backpressure의 동작
- Subscription에 이벤트를 계속 던져주면 밀릴 수 밖에 없다.
- 이러한 상태가 되면 Publisher의 역할은 어떠한 큐에 이벤트를 넣어줘서 기다리게 해준 다는지 Subscription를 확장시켜 주는 역할을 수행해 준다.

### 요즘 핫한 이유
- 클라우드와 분산 시스템이 굉장히 요구가 많아졌다.

### Stream을 처리하는 방식
- mono - 하나 받고 땡!
- flux - 연속적으로 데이터가 나올때 까지 여러개를 받고자 할 때

## 코드레벨 구경
슬라이드 참조

### 디버깅
- Hooks onopertor를 통해 어디서 발생됐는지 찾을 수 있다.
- 그래도 보통 일이 아니다..


## Dooray
Do + Horay!

### Spring + Mybatis

### 다양한 DBMS 지원
- ANSI SQL?
- DBMS마다 달라지는 SQL

### ORM/JPA
- ORM 프레임워크가 중간에서 객체와 관계현 맵핑

### JPA
- 데이터 접근 추상화
- 표준
- 단순히 인터페이스 구현만으로 CRUD 중복 코드를 최소화할 수 있다.

### JPA 적용 과정/해결
- DB 샤딩
- AUTO_INCREMENT로 채번한 ID는 Shard마다 중복 가능
- global unique해야 되는데 Identity 사용 불가능
- 채번 테이블(Table 전략)
- 추가 요구 사항: ID가 순차적이지 않았으면 좋겠다.
- UUID를 사용 직접 ID값 생성
- Entity저장할 때마다 매번 ID 값을 지정해줘야..
- IdentifierGenerator를 구현하여 ID값을 생성

### 복합키 문제 해결
- embeddable로 생성
- Project Lombok을 사용하여 문제 해결

### 검색 색인용으로 쓸 Entity가 필요하다
- 필드 몇개가 추가로 더 필요
- 처음에는 기존 Entity에 연관관계만 추가해서 별도의 DTO로 처리
- 서비스용 쿼리와 검색 색인용 쿼리 분리 필요
- 서로 다른 별도의 엔티티로 분리
- 한 테이블에 대해 여러 개의 Entity를 만들어도 된다!
- 엔티티에 모든 컬럼을 쓸 필요는 없다.(저장하지 않을거면)
- 엔티티의 모든 연관관계를 다 쓸 필요도 없다.
- 한 컬럼에 대해 여러 개의 필드나 연관관계를 맵핑해도 된다.

### 중복 코드 제거
- @MapperdSupperclass 사용하여 자식 클래스에 Entity 맵핑 정보를 상속
- 추상클래스를 두고 자식클래스가 필요한 부분을 추가로 작성가능

### 연관관계 맵핑
- Hibernate 바이트코드 수정
    - LAZY 로딩 구현은 구현체에게 위임되어 있다.
    - optional이 false인 부분에 lazy를 적용해서 해결

### Repository
- JPARpositry만 구현하여 사용하면 노출되지 않는 메소드로 인해 혼란스러울 수 있다
- 필요한 메소드만 선택적으로 노출 가능하다.

### 메소드 이름으로 쿼리 생성 이런 경우?
- 원하는 필드값만 가져오는 쿼리는 안되고 Entity를 가져온다.
- 원하는 값만 가져오기 위해서는 JPQL, Criteria API를 사용하여 직접 짜야 된다.
- JPQL
    - DMBS에 의존적이지 않은 객체지향 쿼리
    - 문법적 실수 가능
- Criteria
    - 동적 쿼리 작성이 용이
    - 컴파일 타임에 오류를 발견할 수 있고 IDE의 도움도 가능
    - 너무 장황하고 복잡하게 사용해야 한다.
- QueryDSL
    - 결국에는 이걸로
    - 정적타입을 사용해서 JPQL을 코드로 작성할 수 있도록 해 주는 프레임 워크
    - 4 vs 3 버전의 차이는?
        - select(), from(), where(), fetch() 등의 form을 사용
        - 나중에 적용하려니 문제가 생김
        
### Query 3.0 vs 4.0
- 페키지명이 변경
- 메소드명이 모두 변경
- SubQuery 작성방식 변경
- DTO 생성 변경

### 흔히 하는 실수
- 네이밍 룰이 기본 리포지토리 이름에 Postfix를 가지고 찾는다

### 성능 문제
- SQL Query가 많은 경우
    - N + 1 문제
    - 연관관계 LAZY 사용
    - OSIV 설정
    - Fetch Join 사용(연관관계를 갖는 애들을 한번에 가져옴)    

### 쿼리 수행 결과 분석 툴 활용
- Adapt!
- 쿼리 비용이 비싸거나 수행빈도 이런것들을 Scouter를 통해 알려줌

## Spring Cloud Stream을 활용한 실시간 콘텐츠 배포 시스템 구축기(이경일님)
- 카카오 쇼핑플랫폼개발
- 커머스플랫폼 상품 도메인 개발담당
- 파일럿 프로젝트 생성에는 선두자!
- Spring Cloud에 관해 들어보지 못했거나 아직 사용은 해보지 못한 개발자가 대상

### 콘텐츠란?
- 인터넷망을 통해 디지털화된 데이터
- 오늘의 콘텐츠 주제는 비즈니스 로직을 통해 가공된 데이터를 미리 만들어 놓는 것

- 대용량 처리를 위해 캐시서버가 여러 개있다.
- Cache Server가 늘수록 계속 부화가 발생

### 콘텐츠 배포의 패러다임을 바꿈
- 중간에 메세지 큐를 넣고 캐시 서버는 메세지 큐를 바라보고 콘텐츠를 가져오는 느낌
- Pub/Sub Pattern이라고 한다.
- 성능적으로 보면 병렬적으로 처리하여 훨씬 효율지 좋다.

### 발표 키워드
- Spring Cloud Stream
- Spring Cloud & Spring Boot
- Stream & Batch
- Pub/Sub
- Message queue

### 일단 시작 - 필요한 것은
- Message Queue - kafka, ActiveMQ, RabbitMQ
- 발행-구독
- 역할 분담

### 어떤 것을 쓸까?
- JVM - Java - Spring Boot
- Message Queue - Kafka(Pub/Sub)
- Spring Cloud Stream

### Spring Cloud Stream과 비교
- 좋은 점은
    - 비즈니스 로직과 설정의 분리
    - YML, Properties의 자동완성기능 사용가능
    - 코드량 감소

### Spring Cloud란 무엇일까요?
- Pivotal과 Netflix의 합작품
- Netflix OSS
- Spring Boot에서 동작

### Sream이란?
- 연속적인 데이터의 흐름
- Queue를 이용

### 데이터 처리 방식
- Batch(Micro Batch)
- Stream
    - 파이프라인을 생각
    - 파이프라인은 물이 흐르지만 스트림에는 데이터가 흐른다.
    - 수도꼭지는 자기가 마음에 들게 달고 그에 따라 데이터를 처리!
    
### Spring Cloud Stream이란
- Event.Message-Driven Microservice
- 간단한 애노테이션으로 설정 가능

### Message Queue는 어떤 것을?
- kafka를 선택
    - FileSystem에 저장
        - 용량 무제한, 영속성
    - TCP/IP
    - 뛰어난 성능?
        - 하드가 느리긴 하지만 Sequential 저장은 빠르다.(순차적 접근)
        - OS Page Cache 사용(OS가 사용할 가용램이 많이 확보되어야 한다)
    - 최소 1번은 메시지 전달을 보장함
- RabbitMQ
    - Memory에 저장
    - AMQP
    - 다양한 기능
    - 자체적으로 clustering 지원

>kafka를 선택한 이유는 컨텐츠 자체를 메시지큐에 담기 위해서 이다. pub/sub 모델에 집중

### Spring Cloud Sream 사용법
- 스프링 사이트에서 stream kafka를 이용해서 zip파일을 생성

### 데모 시나리오
- contesnts publisher에서 다음에 접속해서 index.html을 가져옴
- pub: event store(kafka)에 저장
- sub: Subscriber가 store에서 가져온다.

### 정리
- 배포방식이 변경: Push -> Pub/Sub
    - 대폭적인 성능향상 유연한 확장 가능
    - Infra관점의 관리 포인트 증가
- Spring Colud Strem 사용
    - 간편한 애너테이션
    - 개발자는 비즈니스 로직에 집중할 수 있음
- 만약 Kafka를 직접 운영한다면?
    - Kafka Manager(Kafka UI 관리 툴)
    - Heap Memory는 크게 잡지 말 것(32G Memory 서버 기준 6G 정도)
    - request가 증가하면 적당한 값으로 조정

### 팁
- 운영 중 신규 Consumer를 추가 투입 시
    - 기존 메시지를 한꺼번에 Subscribe 함
    - 새로 유입되는 메시지부터 받기를 원하면 autoOffserReset: latest
- 다중 Patition을 사용할 경우
    - 기본설정은 모든 파티션에 Assign
    - ConsumerGroup 사용, Consumer별로 다른 파티션에 Assign 가능
- Consumer가 죽으면 살아있는 Consumer에 자동 배포됨
- 같이 쓰면 좋은 Spring Cloud Component
    - Eureka: Service Descovery & Registry
    - Config Server: Dynamic한 설정의 분리
    
## Spring Boot(권용근님)

### 배경
- 파일럿 프로젝트때마다 신입들의 config 설정이 달라짐
- osiv를 몰랐던 것에 대한 망신

### 스프링 부트란?
- `starter Dependency`
- `auto configuration`

### 스프링 부트에 대한 오해잡기
- SpringFramework > Spring Boot가 옳바르다.
- 필요하다면 커스터마이징하게 언제든 걷어낼 수 있다.
- 스프링부트는 지원해 주는 도구이다.

### 왜 부트를 이해해야 할까?
- Spring Boot에 대해 모르고 설정하는 것 보다 원인을 파악하고 잡는게 더 효율적이다.
- `spring-boot-starter-freemarker`를 사용하면 굳이 일일이 빈을 생성해 줄 필요가 없다.

### Reference Guide
- 목차만 보더라도 이해할 수 있는 가이드 문서
- 시간과 기회가 된다면 부트에 대한 이해를 도울 수 있다고 생각한다.
- 책은 스프링 부트 코딩 공작소!(얇은 책)
- 스프링부트 프로그래밍 입문(예제 위주)
- Spring Boot 허니문님이 직접 작성중!

### Release Note
- 레퍼런스 가이드를 다 봤다고 해서 업데이트 되는 점들을 파악하기 힘들다.
- 이전 -> 다음 버전의 차이점을 설명해 준다.

### 직접 보기!
- `Starter Dependency`
- 시작하기 좋은 여러개의 dependency를 한번에 갖고 있다.
- 자세한 사항은 슬라이드에 예제를 담고 있다.
- version 간 호환성도 찾아야 하는데 그러한 version 검증을 미리해주고 있다.

### Auto Configuration
- datasource를 예제로 써서 설명
- application.properties, yml에 해당 설정만 해주면 수동으로 빈을 만들 필요없이 설정이 가능하다.
- Spring-boot-autoconfigure에 포함된다.

### 조건의 의한 Auto Configuration
- @SpringBootApplication의 exclude 메소드를 사용하여 candidate에서 제외시킨다.
- Conditional 인터페이스의 matches가 ture이면 Bean을 등록, 확장 애노테이션이 여러 개 있다.

### Properties
- `Spring-boot-autocinfigure`안에 properties나 yml로 더 간단하게 설정하게 해준다.
- 안을 살펴보면 디폴트로 설정되어 있다.

### 직접 소스 까보기

### 결론
- 스프링 부트는 스프링 프레임워크를 지원해주는 도구
    - Starter Dependency
    - Auto Configuration
    - Properties
- 무엇을 해주는지는 알고 쓰자!
    - 알면 알수록 개발 생산성이 높아질 것!

### 스프링 데이터 Envers(김영한님)
- JPA 프로그래밍 저자
- 너무 쉽다~
- 현 우아한 형제들 다니신다.

### 데이터 관점의 공통 관심사
- 테이블별로 등록자, 수정자, 수정일 등 공통된 필드를 등록한다.
- 지루한 반복...
- 변경 이력(과거에는 어떤 데이터?)

>꺼림직하고 어떻게 자동적으로 될거 같은데? 안되는게...

### 스프링 데이터 Auditing
- Auditing == 감사 == 감독하고 검사
- 누가, 언제 생성 or 변경 검사

### 설정
- @EnableJpaAuditing
- 어디서 가져올지만 정의하면 된다
- 모든 설정은 애노테이션으로만 설정가능
- MappedSuperClass로 상속하는 기능(등록일, 수정일, 수정자 등 고통된 필드 묶고 사용)

### 하이버네이트 Envers
- 하이버테이트 핵심 모듈
- JPA 스펙에 정의된 모든 매핑 감사
- 엔티티의 변경 이력을 자동으로 관리
- 변경 이력 관리 방식
    - 히스토리를 계속 쌓는 방식으로 관리
    - REV == 리비전 식별자
    - REVTYPE: 등록(0), 수정(1), 삭제(2)
    
### Revision
- Revinfo 테이블 제공
    - 이쪽에서 테이블 통합 관리
    - 가격에 대한 정책이 바뀌면 통합 적용/관리
- 트랜잭션 단위의 통합 Revision(개정) 키 관리
- Revision 생성 시간 관리
- 사용자 정의 필드 추가 가능

### 고급기능
- 필드 변경 여부 관리
    - @Audited(withModifiedFlag = true)
    - AUD 테이블에 필드마다 수정 상태 컬럼 추가
    - 어떤 필드를 수정했는지 알 수 있게 해준다. 이를 검색조건에 활용 가능
- 같은 트랜잭션에서 함께 변경된 엔티티를 검색
    - 한번에 할 수 있는 방법을 제공해 준다.
    - getCrossTypeRevisionChangesReader() 사용
- 기타 어노테이션
    - @Audited(targetAuditMode = NOT_AUDITED): 연관관계가 있는 엔티티를 감사하지 않음
    - @AuditOverride: 상속관계

### 스프링 데이터 Envers
- 숨겨진 프로젝트
- 하이버네이트 Envers를 편리하게 조회하도록 도움
- 복잡함을 직관적으로 인터페이스 제공(RevisionRepository 인터페이스)
- 편리한 메타데이터 조회

### 단점
- 복잡한 조회 -> 하이버네이트 Envers 직접사용
- 버전업이 잘 안됨(사실 기능이 별로 없다)
- 스프링 데이터가 지원하는 Querydsl 관련 기능과 함께 사용하려면 코드를 약간 수정해야 한다.



