# Spring Boot + Envers로 엔티티 이력관리하기
너무나 쉽고 편한 기능이라 쓸까말까 고민했었는데 한글화된 문서가 거의 없어서 일단 쓰기로 했습니다. 삽질보다는 공식 문서의 영문 해석능력에 따라 적용시간을 단축시킬 수 있습니다.  
또, 스프링 캠프 2017에서 김영한님이 발표해 주신 자료영상을 찾아 보시면 이해하기 훨씬 수월하실 겁니다. 

## 개요
Hibernate Envers 프로젝트는 각각의 대상 엔티티의 이력관리를 간편하게 도와줍니다. 정말 간단하게 적용하자면 `@Audited` 어노테이션만 붙이면 끝납니다. 사실 Envers를 알기전까지 저는 
제가 대상으로 하는 히스토리용 테이블을 따로 생성하여 json 형식으로 데이터를 저장하고 불러오곤 하였습니다. 뭐 상황에 따라 적용하는 방식의 이점이 있겠지만 Envers를 적용한다면 대상 테이블과 
똑같은 테이블에 rev(revinfo PK), revtype(생성, 수정, 삭제 구분)컬럼이 추가로 생성되며 이력관리를 할 수 있습니다. 뭐 이런 기능 필요없이 "나는 내가 직접 테이블을 생성하고 이력에 대한 로직을 짤거야!" 
라는 분들도 계시겠지만...빠르게, JPA에 맞게, 모던하게 사용하고자 하시는 분들에게 **Envers**를 추천해 드립니다. <br>
그럼 본격적으로 Envers를 파헤쳐 보도록 하겠습니다. 우리의 목표는 아주 심플한 예제로 빠르게 Envers를 이해하고 적용하는게 목표입니다! 모든 소스는 [Github](https://github.com/young891221/Spring-Boot-Envers-Example)에서 보실 수 있습니다.

## 프로젝트 환경
뭐 이것저것 많이 들어간것 같지만 사실상 JPA, Envers, Web이 핵심입니다.
- Java8
- Spring Boot 1.5.6
- Spring Data Envers
- Spring Starter Web
- Spring Stater Data Jpa
- Spring Boot Starter Test
- MySql
- lombok
- Gradle 3.5

## Envers 적용하기
먼저 application.yml에 datasource부터 셋팅하겠습니다. 저희는 스프링 부트를 사용하기 때문에 다음과 같이 간단하게 기본적인 datasource 정보를 입력하여 디비와 연동할 수 있습니다. 한 가지 특별한 점은 `jpa.hibernate.ddl-auto: create`로 설정한 부분입니다. 
이 부분은 프로젝트가 구동될때 마다 새롭게 테이블을 생성한다고 명시하는 부분입니다. 실제로 서비스에서는 절대 사용하면 안되는 설정이지만 전 테스트를 돌릴때 마다 새로 테이블을 생성해서 결과를 볼 수 있도록 일부러 설정해 놓았습니다.(예제소스의 간편화를 위해...)

```yaml
spring:
  datasource:
    url: jdbc:mysql://{경로}
    username: 
    password: 
    driver-class-name: com.mysql.jdbc.Driver
  jpa:
    hibernate:
      ddl-auto: create

```

위의 `jpa.hibernate.ddl-auto: create`설정이 마음에 안드시는 분들은 build.gradle에서 하이버네이트 플러그인 설정을 통해 자동으로 테이블을 생성할수도 있습니다.

```groovy
...

plugins {
	id "org.hibernate.gradle.tools" version "1.2.5"
}

apply plugin: "hibernatetools-gradle-plugin"

...
```

설정 후 인텔리제이에서 다음과 같이 하이버네이트 플러그인을 실행하면 테이블이 생성됩니다.
 
<p align="center">
<img src="/images/Hibernate/envers/1.png"/>
</p>

이제 테스트용 도메인과 클래스 몇개를 추가해 보도록 하겠습니다. 도메인은 아주 심플합니다.

```java
@Data
@Entity
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Audited //이력관리용 어노테이션
public class Book implements Serializable {
    private static final long serialVersionUID = 8530213963961662300L;

    @Id
    @GeneratedValue
    private Long id;

    @Column
    private String title;

    @Column
    private Timestamp publishedAt;
}
```

Book 클래스는 롬복을 사용하여 구성하였습니다. 실질적으로 위의 코드에는 가장 중요한 `@Audited`어노테이션이 있습니다. "이력관리하고 싶은 타겟 엔티티가 있다!"하면 `@Audited`만 붙이면 해결이 됩니다. 쉽죠?<br> 
좀 더 풀어서 설명하자면 Book 도메인에 `@Audited`를 사용하여 이력관리를 하기 위해서는 이력관리용 테이블로 book_aud 테이블(이름 변경 가능)과 revinfo 테이블이 필요합니다. 
book_aud는 book의 필드값 3개(id, title, publishedAt)와 이력관리 ID값인 `rev`, 타입을 나타내는 `revtype`으로 설계됩니다. `revtype`값의 의미는 다음과 같습니다.
#### revtype
- 0 : insert
- 1 : update
- 2 : delete

이 부분이 좀 아쉽긴 하네요. 타입을 숫자로 구분해 놓는게;; 서버에서 enum을 쓰던 명확하게 구분이 필요할듯 합니다.<br> 
테이블은 미리 만들어 놓아도 되고 저는 예제용으로 테이블이 자동생성되도록 설정했고 굳이 따로 만들진 않았습니다. 자동생성된 book_aud 테이블 스키마는 다음과 같습니다.

<p align="center">
<img src="/images/Hibernate/envers/2.png"/>
</p>

만약, 이력관리하고 싶지 않은 필드들을 커스터마이징하게 바꾸고 싶다면 `@Audited`를 필드값에 할당해도 상관없습니다. 아래와 같이 바꾸면 title 필드는 이력테이블에서 제외됩니다. 

```java
@Data
@Entity
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Book implements Serializable {
    private static final long serialVersionUID = 8530213963961662300L;

    @Id
    @GeneratedValue
    private Long id;

    @Column
    private String title;

    @Column
    @Audited
    private Timestamp publishedAt;
}
```

<p align="center">
<img src="/images/Hibernate/envers/3.png"/>
</p>

자, 이제 이력관련 테이블을 만들었고 어노테이션을 사용하여 간편하게 이력들을 관리할 수 있습니다. 이제 계속해서 추가되는 이력들을 조회하고 싶어지는 데요. 일일이 쿼리를 짜서 만들까요? 아니죠, 이것도 이미 다 만들어져 있습니다~(너무 다 만들어져 있나;;)<br>
이력관련 테이블을 조회하기위해 미리 만들어져 있는 **RevisionRepository 인터페이스**를 살펴보겠습니다. 

```java
@NoRepositoryBean
public interface RevisionRepository extends Repository<T, ID> {
	//최근 리비전 조회
	Revision<N, T> findLastChangeRevision(ID id);
	//id를 사용하여 해당 id의 모든 리비전 조회
	Revisions<N, T> findRevisions(ID id);
	//리비전을 페이징 처리하여 조회
	Page<Revision<N, T>> findRevisions(ID id, Pageable pageable);
	//특정 리비전 조회
	Revision<N, T> findRevision(ID id, N revisionNumber);
}
```

우리는 이를 잘 활용하면 됩니다. BookRepository 인터페이스에 RevisionRepository를 상속받도록 합니다.

```java
public interface BookRepository extends JpaRepository<Book, Long>, RevisionRepository<Book, Long, Integer> {
}
```

RevisionRepository 인터페이스사용을 위해서는 추가적인 설정이 필요합니다. 어노테이션 한 줄로 모든 설정은 끝납니다.

```java
@EnableJpaRepositories(repositoryFactoryBeanClass = EnversRevisionRepositoryFactoryBean.class) //EnversRevisionRepositoryFactoryBean 인스턴스 생성
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

자, 모든 환경과 코드 작성이 끝났습니다. 실제로 잘 돌아가는지 확인하기 위해 몇 가지 테스트 코드를 작성해 보겠습니다. 테스트 코드는 @DataJpaTest를 사용하려다가 그냥 MySQL에 직접 넣고 빼며 테스트하고 싶어서 @SpringBootTest를 사용하였습니다. 
직접 코드를 보면서 확인해 보겠습니다.

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class JpaEnversTest {
    private static boolean isInit = false;

    @Autowired
    private BookRepository bookRepository;

    @Before
    public void Book_데이터_생성() {
        if(!isInit) {
            //10개 Book 데이터 저장
            IntStream.rangeClosed(1, 10).forEach(index ->
                    bookRepository.save(Book.builder().title("테스트" + index).publishedAt(Timestamp.valueOf(LocalDateTime.now())).build())
            );

            //1번 삭제
            bookRepository.delete(Long.valueOf(1));

            //2번 수정하기 3번 진행
            Book book2 = bookRepository.findOne(Long.valueOf(2));
            IntStream.rangeClosed(1, 3).forEach(index -> {
                book2.setTitle("수정" + index);
                bookRepository.save(book2);
            });
            isInit = true;
        }
    }

    @Test(expected = InvalidDataAccessApiUsageException.class)
    public void Book_Revision_NULL_데이터_검색() {
        Revision revision = bookRepository.findLastChangeRevision(Long.valueOf(1));
        Book book = (Book) revision.getEntity();
        Integer revisionNumber = (Integer) revision.getRevisionNumber();

        assertThat(book.getTitle(), is("테스트1"));
        assertThat(revisionNumber, is(1));
    }

    @Test
    public void Book_Revision_검색() {
        Revision revision1 = bookRepository.findRevision(Long.valueOf(1),1);
        Book book1 = (Book) revision1.getEntity();
        Integer revisionNumber1 = (Integer) revision1.getRevisionNumber();

        assertThat(book1.getTitle(), is("테스트1"));
        assertThat(revisionNumber1, is(1));

        Revision revision2 = bookRepository.findLastChangeRevision(Long.valueOf(2));
        Book book2 = (Book) revision2.getEntity();
        Integer revisionNumber2 = (Integer) revision2.getRevisionNumber();
        DateTime dateTime = revision2.getRevisionDate();

        assertThat(book2.getTitle(), is("수정3"));
        assertThat(revisionNumber2, is(14));
    }

    @Test
    public void Book_Revision_Page_검색() {
        Page<Revision<Integer, Book>> bookPage = bookRepository.findRevisions(Long.valueOf(2), new PageRequest(0, 10));
        assertThat(bookPage.getTotalElements(), is(Long.valueOf(4)));
        assertThat(bookPage.getContent().get(0).getEntity().getTitle(), is("테스트2"));
        assertThat(bookPage.getContent().get(1).getEntity().getTitle(), is("수정1"));
        assertThat(bookPage.getContent().get(2).getEntity().getTitle(), is("수정2"));
        assertThat(bookPage.getContent().get(3).getEntity().getTitle(), is("수정3"));
    }

    @Test
    public void Book_Revisions_검색() {
        Revisions<Integer, Book> revisions = bookRepository.findRevisions(Long.valueOf(2));
        assertThat(revisions.getContent().size(), is(4));
        assertThat(revisions.getContent().get(0).getEntity().getTitle(), is("테스트2"));
        assertThat(revisions.getContent().get(1).getEntity().getTitle(), is("수정1"));
        assertThat(revisions.getContent().get(2).getEntity().getTitle(), is("수정2"));
        assertThat(revisions.getContent().get(3).getEntity().getTitle(), is("수정3"));
    }
}
```
