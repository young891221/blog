# Parameter AOP 설정하기 간단 팁
보통 메소드단위의 AOP를 많이 사용하시는데 유저정보에 대한 값을 파라미터에서 AOP방식으로 캐치하여 User객체로 전환시키는 방법을 간단한 예제를 통해 알아보겠습니다.

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface User {
}
```
위의 예제소스를 보시면 User 어노테이션을 `PARAMETER`타입으로 선언하였습니다. 어노테이션의 매개변수가 필요하시다면 내부에 생성하시면 됩니다.

```java
@Controller
public class Controller {

    @GetMapping(value = "/test")
    public String test(@User User user, HttpSession session) {
        ... //반환된 user값을 사용하여 이후 로직 수행
        return "test";
    }
}
```
생성된 `@User`어노테이션을 사용하고자 Controller에 간단하게 반영해 봤습니다. 어노테이션을 만들고 적용했으니 이제 적용된 어노테이션을 포인트컷해서 타겟에 User객체를 바인딩 해보도록 하겠습니다.

```java
@Component
@Aspect
public class UserAspect {

    @Around("execution(* *(.., @User (*), ..))")
    public Object convertUser(ProceedingJoinPoint  joinPoint) throws Throwable {
        HttpSession session = ((ServletRequestAttributes) RequestContextHolder.currentRequestAttributes()).getRequest().getSession();
        User user = (User) session.getAttribute("user");
		... //user로직 처리
        Object[] args = Arrays.stream(joinPoint.getArgs()).map(data -> { if(data instanceof User) { data = finalUser; } return data; }).toArray();

        return joinPoint.proceed(args);
    }
}
```
여기서 팁들은 다음과 같습니다.

### 어드바이스, 파리미터용 포인트컷 적용
`@Around`는 어드바이스를 말합니다. 원래 메소드 형식에는 간단하게 `@Around("@annotation(com.test.User)")` 요런식으로도 정의할 수 있지만 파라미터 어노테이션에는 적용되지 않으므로 좀 더 섬세하게 포인트컷 할 수 있는 `execution`을 사용하였습니다. 
여기서 중요한 포인트는 `execution(* *(.., @User (*), ..))` 이 부분입니다. 파라미터 어노테이션의 핵심인데요 제가 한창 삽질했던 부분입니다... 
첫번째 `*`은 리턴타입을 나타내며 두 번째부터 시작하는 `*(.., @User (*), ..)`은  **`@User` 애노테이션이 선언된 부분의 양옆의 다른 파라미터 0개 이상을 허용하겠다는 패턴입니다.**

### AOP에서 HttpSession 가져오기
AOP에서는 Controller처럼 파라미터를 통해 HttpServletRequest를 가져오진 못하지만 RequestContextHolder를 통해 가져올 수 있습니다. 이를 통해 세션객체를 사용할 수 있습니다.

### 데이터 반환
로직 처리 후 `@User`어노테이션이 선언된 파라미터에 바인딩 될 데이터 반환하기 위해 `joinPoint.proceed(args)`의 매개변수로 컨버팅된 User 객체를 바꿔서 반환해 주면 됩니다. 이런식으로 처리하면 타겟 파라미터에 `args`에 담긴 오브젝트들이 
바인딩됩니다.(위의 예제에서는 User와 HttpSession) 여기서 주의하실 점은 `joinPoint.getArgs()`로 가져온 값에는 저희가 선언한 `User user, HttpSession session` 두 가지 모두 가져온다는 점입니다. 
따라서 선언된 파라미터가 위와 같이 2개이면 2개 모두를 반환시켜주어야 합니다. 안그러면 에러뜹니다ㅎㅎ
 
>혹시 이러한 데이터 반환방법 이외에 더 좋은 방법을 알고 계시다면 댓글 부탁드립니다.

### 참고
이외에 기본적인 AOP관련 자세한 내용은 [창천향로님의 블로그](http://jojoldu.tistory.com/71)나 [공식문서](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/aop.html)를 참고하면 많은 도움이 되실겁니다. 
