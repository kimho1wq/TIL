# Annotation
- 어노테이션의 기본적인 목적은 자바의 메타데이터로 사용하기 위함이다
- 어노테이션 작성법은 '접근 제한자 @interface 어노테이션명 { 내용 }' 이다

### Spring Annotation
- @Component
  - 빈을 선언하는 클래스를 작성하기 위해 넣는 어노테이션으로, Class의 이름을 camelCase로 변경한 것이 Bean id로 사용된다
  - ```@Component(value="myComponent")``` 지정 가능
- @ComponentScan
  - 컴포넌트 스캔은 @Configuration, @Controller, @Service 등 다른 @Component가 붙은 클래스들을 각 필터에 맞게 스캔하고 빈에 등록한다
  - basePackages = 패키지명, basePackageClasses = 클래스 옵션으로 스캔 범위를 지정할 수 있다
  - @SpringBootApplication가 정의된 곳이 default basePackage가 된다
- @Import
  - 다른 스프링 설정 클래스를 가져올 수 있다
- @Bean
  - @Configuration 선언한 빈 설정 클래스에 빈 선언을 담당하는 어노테이션으로, 메소드에만 넣을 수 있다. 메소드 이름이 빈 이름이 된다
  - ```@Bean(name="configMethod")``` 지정 가능
- @Autowired
  - 스프링 빈을 가져오는 방법으로 Type에 따라 알아서 Bean을 주입 해주며 필드, 메소드, 생성자에다가 넣을 수 있다
  - 매칭되는 type이 2개 이상이면 시그니처 명칭에 맞는 빈을 가져오지만, 그 외의 경우는 NoUniqueBeanDefinitionException 예외가 발생한다
  - @Qualifier("id")
    - @Autowired와 같이 쓰이며, 같은 타입의 Bean 객체가 있을 때 해당 아이디를 적어 원하는 Bean이 주입될 수 있도록 한다
  - @Resource
    - @Autowired와 마찬가지로 Bean 객체를 주입해주는데 차이점은 Autowired는 타입으로, Resource는 이름으로 연결한다
- @PropertySource
  - 해당 프로퍼티 파일을 Environment로 로딩하게 해준다
  - ```
    @PropertySource("classpath:/settings.properties")
    public class TestConfig {
        @Autowired 
        Environment env;
    
        env.getProperty("testbean.name");
    }
    ```
- @ConfigurationProperties
  - yaml 파일의 프로퍼티 파일을 읽으며 default로 classpath:application.properties 파일이 조회된다
  - prefix = "myPrefix" 옵션으로 동일한 prefix 를 갖는 hierarchical properties를 읽을 수 있다
- @Primary
  - @Autowired 어노테이션을 통해 빈을 주입 시 이 어노테이션을 가진 빈을 우선적으로 가져온다
  - ```
    @Bean
    @Primary
    public RedisTemplate<String, Object> redisTemplate() { ... }
    ```
- @Required
  - 반드시 설정해야 하는 필요한 속성들을 정의해야 할 때 사용하며, 정의 하지 않으면 BeanInitializationException 예외가 발생한다
- @Value
  - 메소드, 필드 등에다가 스프링에서 설정한 값을 주입하며 주로 application.properties 및 스프링이나 자바 property 값을 가져올 때 쓴다
  - ```
    @Value("${value.from.file}")
    private String valueFromFile;
    ```
- @Lazy
  - 어노테이션을 선언한 빈은 스프링 시작 시 빈을 초기화 하지 않고, 실제로 처음 사용될 때 로딩이 이뤄지게 하는 방법이다
- @Scope
  - 빈의 생명주기를 설정하며 기본값은 singleton 이다
- @Profile
  - 이 어노테이션이 달린 빈은 특정 프로파일(profile)에서만 동작하도록 설정할 수 있다
  - ```
    spring.profile=dev
    @Profile("dev")  -> 빈 초기화 o
    public class MyConfig {}
    @Profile("!dev") -> 빈 초기화 x
    public class MyConfig {}
    ```

### Spring Boot Annotation
- @SpringbootApplication
  - 스프링 부트의 기본적인 설정을 해준다
    - ```
      @Target(ElementType.TYPE)
      @Retention(RetentionPolicy.RUNTIME)
      @Documented
      @Inherited
      @SpringBootConfiguration
      @EnableAutoConfiguration
      @ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
              @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
      public @interface SpringBootApplication {
          ...
      }
      ```
- @Target
  - 이 어노테이션이 붙을 수 있는 타입에 대해 지정한다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/4e5a0b33-9f4f-49a0-ba14-d68b175fd43e)
- @Retention
  - 어노테이션을 언제까지 살려둘 지를 결정하며 default값은 CLASS이다
  - ```
    public enum RetentionPolicy {
        SOURCE,
        CLASS,
        RUNTIME
    }
    ```
    - SOURCE : complie될 때 어노테이션을 메모리에서 해제. 즉, 소스코드(.java)까지만 남아있는다.
    - CLASS : .class파일에 어노테이션을 기록, 이후 메모리에서 해제. 즉, 런타임때는 남아있지 않음.
    - RUNTIME : RUNTIME때까지 살아있는 어노테이션
- @Documented
  - 이 어노테이션은 JavaDoc로 API를 만들때 해당 어노테이션에 대한 설명도 추가해주는 어노테이션이다
- @Inherited
  - 상속받은 서브 클래스에도 해당 어노테이션을 갖도록 한다
- @SpringBootConfiguration
  - 설정값들을 스캔하고 이 어노테이션을 인덱스에 추가해 스프링이 뜰때 우선적으로 스캔할 수 있게한다
- @EnableAutoConfiguration
  - 이 어노테이션은 Bean을 등록하는 설정파일이며, spring.factories에 있는 수많은 설정들이 자동으로 조건에 따라 적용되어 Bean이 생기는 것이다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/d65a121f-2586-447c-8e86-09df64227748)


### Spring MVC Annotation
- @RestController
  - 이 어노테이션의 클래스의 모든 컨트롤러 메소드는 @ResponseBody 어노테이션이 달린 반환 값을 달고 다니게 된다
  - Spring에서 View로 응답하지 않는 Controller를 의미하며 json 형태로 반환한다
- @ResponseBody
  - 응답 객체를 클라이언트가 요구하는 요청 내용 유형(Content-Type)에 따라 응답하도록 도와주는 어노테이션이다
  - 보통은 Jackson 모듈에 의해 json 유형으로 응답하며, 클라이언트가 xml 응답을 요청하면 xml 응답을 해주기도 하고, 경우에 따라 응답 유형을 mimeType 에 따라 설정할 수 있다
- @RequestMapping
  - 해당 메소드는 이 어노테이션이 설정한 경로를 호출했을 때 메소드가 설정한 응답값을 받게 되며, 클래스에 달면 클래스에 설정한 경로를 기준삼아 각 메소드는 클래스 경로의 하위 경로로 추가되어 경로가 잡히게 된다
  - 특정 HTTP 메소드에만 응답 가능하도록 설정할 수 있다
  - ```@RequestMapping(method = RequestMethod.GET, value = "/path")``` == ```@GetMapping("/path")```
- @RequestParam
  - 메소드 인자에서 요청값을 받을 때, 요청 주소(URL)에 ? 뒤로 시작하는 질의 문자열, POST 전송 시 요청받을 키/값, 등을 받을 수 있다
  - Spring Webflux를 사용한다면 POST 값은 못 받고 @ModelAttribute 어노테이션을 사용해야 한다
  - ```@RequestParam(value="name", defaultValue="testValue", required=false) testName```
- @RequestBody
  - HttpMessageConverter를 이용하여 요청이 온 데이터(JSON이나 XML형식)를 바로 Class나 model로 매핑하기 위한 Annotation이다
  - ```
    @RequestMapping(value = "/book", method = RequestMethod.POST)
    public ResponseEntity<?> someMethod(@RequestBody Book book) { }
    ```
- @RequestHeader
  - Request의 header값을 가져올 수 있다
  - ```public void testMethod(@RequestHeader(value="Accept-Language") String acceptLanguage) {}```
- @RequestPart
  - Request로 온 MultipartFile을 바인딩해준다
  - ```@RequestPart("file") MultipartFile file```
- @PathVariable
  - 동적 라우팅 구성 시(/path/to/{id}) 동적 라우팅에 대한 바인딩 값을 가져오는 인자 어노테이션이다
  - ```@PathVariable("id") int id```
- @ModelAttribute
  - view에서 전달해주는 parameter를 Class(VO/DTO)의 멤버 변수로 binding 해준다
  - binding 기준은 <input name="id" /> 처럼 어떤 태그의 name값이 해당 Class의 멤버 변수명과 일치해야하고 setmethod명도 일치해야한다
  - ```public void show(@ModelAttribute("member") Person person) { }```
- @SessionAttributes
  - Session에 data를 넣을 때 쓰는 Annotation이다
- @Valid
  - 유효성 검증이 필요한 객체임을 지정한다
- @InitBinder
  - @Valid 애노테이션으로 유효성 검증이 필요하다고 한 객체를 가져오기전에 수행해야할 method를 지정한다
- @RestControllerAdvice
  - @ControllerAdvice + @ResponseBody를 합쳐놓은 것이다
  - @ControllerAdvice
    - 클래스 위에 사용하고, 어떤 예외를 잡아낼 것인지는 각 메소드 상단에 @ExceptionHandler(예외클래스명.class)를 붙여서 기술한다
  - @ExceptionHandler(클래스명.class)
    - 해당 클래스의 예외를 캐치하여 처리한다
- @ResponseStatus
  - 사용자에게 원하는 response code와 reason을 return해주는 Annotation이다
  - ```@ResponseStatus(value = HttpStatus.NOT_FOUND, reason = "my page URL changed..")```
- @Transactional
  - 데이터베이스 트랜잭션을 적용하고 싶은 method에 사용하는 Annotation이다
  - method 내부에서 일어나는 데이터베이스 로직이 전부 성공하게되거나 데이터베이스 접근중 하나라도 실패하면 다시 롤백한다
  - ```@Transaction(readOnly=true, rollbackFor=Exception.class, timeout = 10)```
- @CookieValue
  - 쿠키 값을 parameter로 전달 받을 수 있는 방법으로 해당 쿠키가 존재하지 않으면 500 에러를 발생시킨다
  - ```
     // 쿠키의 key가 auth에 해당하는 값을 가져옴 
    public String view(@CookieValue(value="auth") String auth)```
    ```
- @CrossOrigin
  - CORS 보안상의 문제로 브라우저에서 리소스를 현재 origin에서 다른 곳으로의 AJAX요청을 방지하는 것
  - ```
    //기본 도메인이 http://jeong-pro.tistory.com 인 곳에서 온 ajax요청만 받아주겠다.
    @CrossOrigin(origins = "http://jeong-pro.tistory.com", maxAge = 3600)
    ```
- @Cacheable
  - method 앞에 지정하면 해당 method를 최초에 호출하면 캐시에 적재한다
  - 다음 호출부터는 캐시에서 결과를 가져와서 return하므로 method 호출 횟수를 줄여줄 수 있다
  - 메서드 호출에 사용되는 자원이 많고 자주 변경되지 않을 때 사용하고 나중에 수정되면 캐시를 없애야 한다
  - ```@Cacheable(value="cacheKey")```
- @CacheEvict
  - 캐시에서 데이터를 제거하는 트리거로 동작하는 method에 붙이는 Annotation이다
  - 결과값이 변경이 일어났는데도 기존의 데이터(캐시된 데이터)를 불러와서 오류가 발생할 수 있기 때문에 사용한다
  - ```@CacheEvict(value="cacheKey")```
- @Scheduled
  - Linux의 crontab처럼 정해진 시간에 실행해야하는 경우에 사용한다
  

### Lombok Annotation
- @NoArgsConstructor
  - 기본생성자를 자동으로 추가한다
  - Entity Class를 프로젝트 코드상에서 기본생성자로 생성하는 것은 금지하고, JPA에서 Entity 클래스를 생성하는것은 허용하기 위해 추가한다
- @AllArgsConstructor
  - 모든 필드 값을 파라미터로 받는 생성자를 추가한다
- @RequiredArgsConstructor
  - final이나 @NonNull인 필드 값만 파라미터로 받는 생성자를 추가한다
- @Getter
  - Class 내 모든 필드의 Getter method를 자동 생성한다
- @Setter
  - Class 내 모든 필드의 Setter method를 자동 생성한다
- @ToString
  - Class 내 모든 필드의 toString method를 자동 생성한다
- @EqualsAndHashCode
  - equals()와 hashCode() method를 오버라이딩 해주는 Annotation이다
  - callSuper 속성을 통해 equals와 hashCode 메소드 자동 생성 시 부모 클래스의 필드까지 감안할지 안 할지에 대해서 설정할 수 있다
- @Builder
  - 어느 필드에 어떤 값을 채워야 할지 명확하게 인지할 수 있도록 하기 위해 사용되고, constructor와 마찬가지로 생성 시점에 값을 채워준다
- @Data
  - @Getter @Setter @EqualsAndHashCode @AllArgsConstructor을 포함한 Lombok에서 제공하는 필드와 관련된 모든 코드를 생성한다


### JPA Annotation
- @Entity(name="테이블 명")
  - 실제 DB의 테이블과 매칭될 Class임을 명시한다
  - Entity Class 이름을 언더스코어 네이밍(_)으로 테이블 이름을 매칭한다
    - ```SalesManage.java -> sales_manager table```
  - public이나 protected 접근지정자인 기본 생성자가 필수이다
  - final 클래스, enum , interface , inner 클래스에는 사용할 수 없다
  - Table 컬럼에 매핑한 필드에 final을 사용하면 안 된다
- @Table
  - Entity Class에 매핑할 테이블 정보를 알려준다
  - Annotation을 생략하면 Class 이름을 테이블 이름 정보로 매핑한다
- @Id
  - 해당 테이블의 PK 필드를 나타낸다
- @GeneratedValue
  - PK의 생성 규칙(Long 타입의 Auto_increment 등)을 나타낸다
    - IDENTITY: PK 생성을 데이터베이스에게 위임한다(DB에 의존)
    - SEQUENCE: DB 시퀀스를 사용하여 기본 키 할당(DB에 의존)
    - TABLE: 키생성 테이블을 만들어서 사용
    - ```@GeneratedValue(strategy = GenerationType.IDENTITY)```
    - Oracle은 시퀀스를 제공하고, MySQL은 AUTO_INCREMENT 기능을 제공한다
- @Column
  - 테이블의 컬럼을 나타내며, 굳이 선언하지 않더라도 해당 Class의 필드는 모두 컬럼이 된다
  - 생략하면 필드명을 사용해서 컬럼명과 매핑되고, 기본값 외에 추가로 변경이 필요한 옵션이 있을 경우 사용한다
  - ```
    @Column(length = 500, nullable = false)
    @Column(columnDefinition = "TEXT", nullable = false)
    ```
- @Enumerated
  - Enum 타입을 Entity 클래스의 속성으로 사용하기 위한 Annotation이다
    - @Enumerated(EnumType.ORDINAL): Enum 순서(1, 2) 값을 DB에 저장
    - @Enumerated(EnumType.STRING): Enum 이름("MAN","WOMAN")을 DB에 저장
- @MappedSuperclass
  - 공통 매핑 정보가 필요할 때, 부모 클래스에 선언하고 속성만 상속 받아서 사용하고 싶을 때 사용한다
- @EntityListeners
  - Entity의 변화를 감지하고 데이블의 데이터를 조작하는 일을 수행한다
  - JPA에서는 아래의 7가지 Event를 제공한다
    - @PrePersist : Persist(insert)메서드가 호출되기 전에 실행되는 메서드
    - @PreUpdate : merge메서드가 호출되기 전에 실행되는 메서드
    - @PreRemove : Delete메서드가 호출되기 전에 실행되는 메서드
    - @PostPersist : Persist(insert)메서드가 호출된 이후에 실행되는 메서드
    - @PostUpdate : merge메서드가 호출된 후에 실행되는 메서드
    - @PostRemove : Delete메서드가 호출된 후에 실행되는 메서드
    - @PostLoad : Select조회가 일어난 직후에 실행되는 메서드
  - Spring JPA에서 AuditingEntityListener.class를 제공한다 (config의 @EnableJpaAuditing가 필요)
    - ```
      @MappedSuperclass
      @EntityListeners(value = AuditingEntityListener.class)
      public abstract class BaseTimeEntity {
          @CreatedDate //데이터가 생성될 때(@PrePersist) 업데이트
          private LocalDateTime createdDate;

          @LastModifiedDate //데이터가 업데이트 될때(@PreUpdate) 업데이트
          private LocalDateTime modifiedDate;
      }
      ```
















