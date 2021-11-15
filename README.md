# Artineer Spring 5주차 강의노트

## Remind

#### Q1. 의존성 주입을 할 때 사용하는 스프링 어노테이션은 ?
- @Autowired
- 스프링은 의존성을 관리하기 위해 만들어진 프레임워크
- @Autowired를 통해 의존성 주입
- 필드나 생성자레벨에 넣어주면 스프링 컨테이너에 빈들이 등록되고 빈들을 바로 주입 받아 사용한다.

#### @Q2. @Autowired 어노테이션이 필드에 존재하냐, 생성자에 존재하냐에 따라 필드주입, 생성자주입으로 갈린다. 스프링은 기본적으로 생성자 주입을 선호하는데 그 이유는?
- 순환참조 이슈가 있기 때문
- 순환참조란 a와 b 두 객체가 있을 때 a는 b를 의존하고 있고 b도 a를 의존하는 상황
- 객체지향에선 좋지 않은 구조
- a객체를 만들 때 b가 필요, b를 만들 때 a가 필요하기 때문에 코드에서 데드락 발생.

#### Q3. 빌더 패턴이 가지는 이점?
- lombok이라는 라이브러리를 사용하면 @builder라는 어노테이션을 통해 빌더패턴을 사용할 수 있다
- 필드 값을 주입할 때 함수 값들이 필드 이름으로 되어있기 때문에 명시적 주입 가능
- Immutable 하게 코드 작성 가능
  - setter를 통해 주입하는 것 보다 불변하게 코드 작성 가능
-  필드가 많을 경우 생성자 오버로딩을 많이 하지 않고 주입 가능
  -  필드가 여러 개 있다면 생성자로써 주입할 수 있는 코드를 만든다면 만들어야 하는 생성자가 많아진다.

#### Q4. Restful API 제공하는 기본적인 Http Method 4가지
- GET : 조회
- POST : 생성
- PUT : 수정
- DELETE : 제거

#### Q5. DTO 객체를 생성하는 이유?
- Domain Layer과 비즈니스 로직을 처리하고 실제 화면에 내용을 표시할 Presentation Layer이 다르기 때문에 구분하기 위함.
  - Presentation Layer에서 사용하기 위함
- 두 계층간 객체의 역할은 다르다

#### Q6. Transaction 이란?
- 특정 비즈니스에서 한 업무 단위
- ACID
  - Atomic (원자성) : 업무가 하나의 일련 과정이어야 한다
  - Consistency (일관성) : 업무 결과가 작업 끝난 후에도 유지되어야 한다.
  - Isolation (고립성) : 업무 처리 시 다른 업무가 끼어들 수 없어야 한다.
  - Durability (영구성) : 작업 내용이 영구히 저장되어야 한다. (이건 데이터베이스 기준이기 때문에 어플리케이션 레벨에서는 달라질 수 있다.)

#### Q7. 정적 팩토리 메서드 (of Pattern)
- 객체 생성하는 코드를 팩토리 메서드 형태로 제공
  - 정해진 형태로 사용 한다면 한 곳에 객체를 만들어 놓고 사용하는 것
- 코드 중복 최소화
- 비즈니스와 객체 생성에 대한 영역을 구분할 수 있음

#### Q8. 컨트롤러 앞단에서 예외처리 해주도록 스프링에서 제공해주는 기능 ?
- @ControllerAdvice
  - 예외처리에 종속된 어노테이션은 아님
  - 컨트롤러 이전에 무언가를 한다. 라는 것이 이 어노테이션의 요점
- @RestControllerAdvice

#### Q9. 자바 8 이후 null 처리를 강제하기 위해서 추가된 기능 ?
- Optional

#### Q10. 테스트코드를 작성하는 이유
- 테스트 코드는 다른 개발자들에게 하나의 문서로도 사용된다
  - 내가 만든 함수에 대한 테스트를 명시적으로 보여줌으로써 다른 개발자들은 이 테스트 코드를 보고 내가 만든 함수가 어떻게 동작하는지를 보다 정확히 알 수 있다.
  - 작성된 함수가 동작할 때 흔하지 않은 예외 케이스들을 접할 수 있는데 그럴때 마다 테스트 케이스를 추가해두면 이런 부분에 대해서도 추후 다른 개발자들에게 인계가 강제적으로 가능하다.
- 테스트 코드가 실패했을 시 빌드 실패가 되도록 강제할 수 있어서 후 리팩토링이나 비지니스가 수정되어 소스를 수정해야 할 때 방어책이 될 수 있다.

## 통합 테스트
- 유닛 테스트는 특정 모듈만을 테스트하기 위해 Mocking을 했지만 통합 테스트는 필요 없다.
  - 실제 객체를 사용하기 때문
- @SpringBootTest를 사용하면 모든 빈을 컨테이너에 등록할 수 있다.
- WebApplicationContext는 스프링프레임워크(스프링부트가 아님)에서 많이 사용했었다.
  - 스프링부트에서는 자동 설정됐기 때문
  - 통합 테스트에서는 실제 객체를 가져오기 위해 사용한다.

#### ArticleIntegrationTest.java

~~~java
@SpringBootTest
public class ArticleControllerIntegrationTest {

    @Autowired
    private WebApplicationContext context;

    @Autowired
    private ArticleRepository articleRepository;

    private MockMvc mvc; // API테스트 할 때 API를 요청하는 클라이언트를 대신 해줌

    @BeforeEach
    public void setUp() {
        mvc = MockMvcBuilders
                .webAppContextSetup(context)
                .build();
    }

    @Test
    @DisplayName("post() 실행되면 article 객체가 새로 생성되어야 한다")
    public void post_whenItIsOccured_thenArticleShouldbeStored() throws Exception {
        // given
        ArticleDto.ReqPost requestBodyStub = ArticleDto.ReqPost.builder()
                .title("title")
                .content("content")
                .build();

        mvc.perform(post("/api/v1/article/")
                        .characterEncoding("utf-8")
                        .accept(MediaType.APPLICATION_JSON) // 어떤 타입으로 줘야 하는지 알려주는 코드
                        .contentType(MediaType.APPLICATION_JSON) // 실제로 내려오는 코드
                        .content(new ObjectMapper().writeValueAsString(requestBodyStub))
                )
                .andDo(print())
                .andExpect(status().isOk());

        // when
        long size = articleRepository.count();

        // then
        assertThat(size).isEqualTo(1);
    }
}
~~~

## Validation
- 클라이언트가 서버한테 요청을 보냈을 때 정상적인지 체크하는 로직

#### ApiCode.java

~~~java
public enum ApiCode {
    /* ... */
    BAD_REQUEST("CM0002", "요청 정보가 올바르지 않습니다"); /* 새로운 Code 추가 */
    // 잘못된 값이 전달되면 상위 응답으로 내려감.
    
    /* ... */
}
~~~

#### Asserts.java

~~~java
// 입력 문자열이 null 이거나 빈 공백 일 수 있기 때문에 이를 검증하기 위한 isBlank 함수를 만든다.
public class Asserts {
    
    /* ... */
  
    public static void isBlank(@Nullable String str, ApiCode code, String msg) {
        if(Strings.isBlank(str)) {
            throw new ApiException(code, msg);
        }
    }
}
~~~

#### ArticleController.java

~~~java
@RequiredArgsConstructor
@RequestMapping("/api/v1/article")
@RestController
public class ArticleController {
    
   /* ... */ 
    
    @PostMapping
    public Response<Long> post(@RequestBody ArticleDto.ReqPost request) {
      Asserts.isBlank(request.getTitle(), ApiCode.BAD_REQUEST, "'title' 은 필수 값입니다.");
      Asserts.isBlank(request.getContent(), ApiCode.BAD_REQUEST, "'content' 는 필수 값입니다.");
      
      return Response.ok(articleService.save(Article.of(request)));
    }
    
    /* ... */
}
~~~

### Spring Validation으로 Validation
- Controller에서 검증한다는 것은 Controller의 일이 많아지는 것이다
  - 모든 Controller가 다 해야하기 때문에 Validation의 업무가 반복이 된다.
  - 그래서 Controller에서 빼는 방법을 사용한다.
- 관련 의존성 추가

~~~java
// build.gradle

dependencies {
  // ...
  
  implementation 'org.springframework.boot:spring-boot-starter-validation'
  
  // ...
}
~~~

- 검증하고자 하는 객체에 @Valid 어노테이션을 추가한다.

#### ArticleController.java

~~~java
public class ArticleController {
    
    // ...
  
    @PostMapping
    public Response<Long> post(@RequestBody @Valid ArticleDto.ReqPost request) {
        return Response.ok(articleService.save(Article.of(request)));
    }
    
    // ...
}
~~~

#### ArticleDto.java

~~~java
public class ArticleDto {
    @Getter
    @Builder
    public static class ReqPost {
        @NotBlank
        String title;
        @NotBlank
        String content;
    }
    
    // ...
}
~~~

- Validation 처리가 중요한 이유는 API를 사용하는 클라이언트에게 어떤 값이 잘못됐는지 정보를 제공할 수 있다는 점이다.
- Spring Validation는 코드를 추상화 시켜서 짤 수 있지만 메세지가 아쉽다
  - 커스터마이징이 불가능
- 직접 하게 된다면 메세지는 디테일하지만 Validation하는 코드가 반복된다

#### ControllerExceptionHandler.java

- Spring Validation의 오류 내용을 좀 더 알기 쉽도록 하는 코드
- 내용이 깔끔하지 않지만 필요한 내용은 모두 담고 있다.

~~~java
@RestControllerAdvice
public class ControllerExceptionHandler {
    
    // ...
  
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Response<String> methodArgumentNotValidException(MethodArgumentNotValidException e) {
        return Response.<String>builder().code(ApiCode.BAD_REQUEST).data(e.getMessage()).build();
    }
}
~~~

## Optional) @RequestParam, @RequestBody
- @RequestParam은 GET 통신에서 QueryString을 받을 수 있다.
- @PathVariable은 URI에 포함된 값을 변수로 가져오는 어노테이션
- @RequestBody는 POST기반 통신에서 Body를 받아오는 어노테이션
- @RequestBody 기반으로 들어온 데이터가 검증 실패할 경우 MethodArgumentNotValidException 오류를 던지지만 @RequestParam 은 ConstraintViolationException 오류를 던진다.
- RequestParam을 대비하기 위한 코드

~~~java
@RestControllerAdvice
public class ControllerExceptionHandler {
    // ...
  
    /* @RequestBody 데이터 검증 실패시 발생한다. */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Response<String> methodArgumentNotValidException(MethodArgumentNotValidException e) {
        return Response.<String>builder().code(ApiCode.BAD_REQUEST).data(e.getMessage()).build();
    }

    /* @RequestParam 데이터 검증 실패시 발생한다. */
    @ExceptionHandler(ConstraintViolationException.class)
    public Response<String> constraintViolationException(ConstraintViolationException e) {
        return Response.<String>builder().code(ApiCode.BAD_REQUEST).data(e.getMessage()).build();
    }
}
~~~