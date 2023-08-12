# Junit

### 단위 테스트
- 단위 테스트(Unit Test)는 프로그램의 기본 단위인 모듈(Module)을 구현 단계에서 명세서의 내용대로 정확히 구현되었는지를 테스트하는 것이다
- 테스트가 가능한 최소 단위로 나눠서 테스트를 수행하며 개발 수명주기(Development LifeCycle)의 정황과 시스템에 의존적이면서도 시스템의 다른 부분에서 격리하여 독립적으로 수행해야 하는 테스트이다
- 단위 테스트의 장점
  - 개발단계 초기에 문제를 발결할 수 있게 도와줌
  - 리팩토링 또는 라이브러리 업그레이드 등에서 기능 확인을 도와줌(회귀 테스트)
  - 기능에 대한 불확실성 감소
  - 시스템에 대한 실제 문서 또는 예제로써 사용가능
  - 빠른 피드백과 기능을 안전하게 보호 가능
- 단위 테스트 기법 비교
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/d292b2ab-9ae0-4c36-9a6b-2afdc971ab8d)
- 종류: JUnit(Java), DBUnit(DB), CppUnit(C++), NUnit(.net), PyUnit(Python)


### JUnit 이란
- Java의 단위테스트를 수행해주는 대표적인 Testing Framework이다
- JUnit5 = JUnit Platform + JUnit Jupiter + JUnit Vintage
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/d3057b26-b9de-487a-bc5b-1b8cacd8975f)
  - JUnit Platform
    - 테스트를 발견하고 테스트 계획을 생성하는 TestEngine 인터페이스를 가지고 있다
    - Platform은 TestEngine을 통해서 테스트를 발견하고 실행하고 결과를 보고한다
  - JUnit Jupiter: JUnit5에 새롭게 추가된 Jupiter API를 사용하여 테스트 코드를 발견하고 실행한다
  - JUnit Vintage: 기존에 JUnit4 버전으로 작성한 테스트 코드를 실행할때 vintage-engine 모듈을 사용
- spring-boot-starter-test를 사용하여 JUnit5 환경을 구성할 수 있다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/6c7e2910-c28f-48ed-a252-e87ee9d9ff1c)
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/274683a1-81c2-44df-be93-2d297c7aef05)
  - 포함 라이브러리
    - JUnit5 + JUnit4의 호환성을 위한 vintage-engine
    - Spring test & SpringBoot Test
    - AssertJ: Assertion을 제공하며 체이닝 메소드를 제공하는 라이브러리. 대표적으로 assertThat과 같은 메소드를 사용하여 테스트
    - Hamcrest: Java Matcher 라이브러리. 대표적으로 assertThat과 같은 메소드를 사용하여 테스트
    - Mockito: 개발자가 동작을 직접 제어할 수 있는 가짜(Mock) 객체를 지원하는 테스트 프레임워크
    - JSONassert: JSON 용 Assertion Java 라이브러리
    - JsonPath: JSON 용 XPath


### JUnit Annotation
- @ExtendWtih(클래스명.class)
  - 단위 테스트에 공통적으로 사용할 확장 기능을 선언해주는 역할을 한다
  - ApplicationContext를 만들고 관리하는 작업을 설정된 class로 이용하겠다는 의미 이다
    - JUnit4의 @RunWith(SpringRunner.class) 와 같은 어노테이션으로 JUnit5부터 사용
  - SpringExtension.class: Junit5와 Spring Test Context 프레임워크와 통합해 사용할 경우
  - MockitoExtension.class: JUnit5와 Mockito를 연동해 테스트를 진행할 경우에 사용한다
- @SpringBootTest
  - 모든 빈들을 스캔하고 애플리케이션 컨텍스트를 생성하여 테스트를 실행한다
  - 특정 계층만 테스트가 필요한 상황에선 모든 빈을 탐색하고 등록하기 때문에 불필요하게 무거워지고 시간이 오래 걸릴 수 있다
    - value/properties: 애플리케이션 실행에 필요한 프로퍼티를 key=value 형태로 추가할 수 있음
    - args: 애플리케이션의 arguments로 값을 전달할 수 있음
    - classes: 애플리케이션을 로딩할 때 사용되는 컴포넌트 클래스들을 정의할 수 있음
    - webEnvironment: 웹 테스트 환경을 설정할 수 있음
      - MOCK (default)
        - 웹 기반의 애플리케이션 컨텍스트를 생성하지만 MOCK 환경으로 제공하여 내장 서버가 시작되지 않음
        - 웹 환경이 클래스패스에 없다면 웹이 아닌 애플리케이션 컨텍스트를 생성함
        - 웹 기반의 Mock 테스트를 위해 @AutoConfigureMockMvc 또는 @AutoConfigureWebTestClient와 함께 사용할 수 있음
      - RANDOM_PORT
        - 웹 기반의 애플리케이션 컨텍스트를 생성하여 실제 웹 환경을 제공함
        - 내장 서버도 실행되며 사용하지 않는 랜덤 포트를 listen함
        - 별도의 쓰레드에서 실제 서버가 구동되어 @Transactional이 동작하지 않을 수 있다
      - DEFINED_PORT
        - 웹 기반의 애플리케이션 컨텍스트를 생성하고 실제 웹 환경을 제공함
        - 내장 서버도 실행되며 지정한 포트(default 8080)를 listen함
        - 별도의 쓰레드에서 실제 서버가 구동되어 @Transactional이 동작하지 않을 수 있다
      - NONE
        - SppringApplication로 애플리케이션 컨텍스트를 생성함
        - 하지만 mock이나 다른 것들을 포함해 어떠한 웹 환경도 제공하지 않음
- @WebMvcTest
  - 컨트롤러 테스트를 위한 Annotation으로 내장된 서블릿 컨테이너가 랜덤 포트로 실행된다
  - 애플리케이션 컨텍스트를 만들 때 컨트롤러와 연관된 빈들만을 제한적으로 찾아서 등록하기 때문에, 일반적인 @Component나 @ConfigurationProperties 빈들은 스캔되지 않는다
  - @Controller, @RestController, @ControllerAdvice, @RestControllerAdvice, WebMvcConfigurer, HandlerMethodArgumentResolver 등등 만 스캔되므로 @Import를 사용하여 스캔되게 할 수 있다
- @DataJapTest
  - JPA 레포지토리 테스트를 위한 Annotation으로 @Entity가 있는 엔티티 클래스들을 스캔하며 테스트를 위한 TestEntityManager를 사용해 JPA 레포지토리들을 설정해준다
  - @Component나 @ConfigurationProperties 빈들은 스캔되지 않는다
  - @DataJpaTest에는 @Transactional 어노테이션이 들어있어서 기본적으로 모든 테스트가 롤백된다
  - H2와 같은 내장 데이터베이스가 클래스 패스에 존재한다면 내장 데이터베이스가 자동 구성된다
- @Test: 해당 메소드가 Test대상임을 알려줌
- @BeforeAll: static 메소드의 형태로, 해당 클래스의 모든 Test가 수행되기 전에 딱 한번 호출된다
- @AfterAll: static 메소드의 형태로, 해당 클래스의 모든 Test가 수행된 후에 딱 한번 호출된다
- @BeforeEach: 각 Test가 수행되기 전에 매번 호출된다
- @AfterEach: 각 Test가 수행된 후에 매번 호출된다


### JUnit Mock
- Mock이란 실제 객체를 만들기에는 비용과 시간이 많이 들거나 의존성이 크게 걸쳐져 있어서 테스트 시 제대로 구현하기 어려울 경우 가짜 객체를 만들어서 사용하는 기술이다
- Mock에 대한 용어
  - 테스트 더블(Test Double)
    - 테스트를 진행하기 어려운 경우 이를 대신해 테스트를 진행 할 수 있도록 만들어주는 객체를 의미한다
    - Mock 객체와 유사한 의미를 가지며 테스트 더블이 좀더 상위 의미로 사용된다
  - 더미 객체(Dummy Object)
    - 단순히 인스턴스화될 수 있는 수준으로만 객체를 구현한다
    - 인스턴스화된 객체가 필요할 뿐 해당 객체의 기능까지는 필요하지 않은 경우에 사용한다
  - 테스트 스텁(Test Stub)
    - dummy object 보다 좀더 구현된 객체로 어떤 행위가 호출됐을 때 특정 값으로 리턴시켜주는 형태가 Stub이다
    - 특정 상태를 가정해서 하드코딩된 형태이기 때문에 로직에 따른 값의 변경은 테스트 할 수 없다
  - 페이크 객체(Fake Object)
    - 여러 상태를 대표할 수 있도록 구현된 객체로 실제 로직이 구현된 것처럼 보이게 한다
    - 실제로 DB에 접속해서 비교할 때와 동일한 모양이 보이도록 객체 내부에 구현 할 수 있다
  - 테스트 스파이(Test Spy)
    - 테스트에 사용되는 객체, 메소드의 사용 여부 및 정상 호출 여부를 기록하고 요청시 알려준다
    - 특정 메소드가 호출 되었을 때 또 다른 메서드가 실행이 되어야 한다와 같은 행위 기반 테스트가 필요한 경우 사용한다
  - Mock 객체(Mock Object)
    - 행위를 검증하기 위해 사용되는 객체를 지칭하며 수동으로 만들 수도 있고 프레임워크를 통해 만들 수 있다
    - Mock Object는 행위 검증(behavior verification)에 사용하고, Stub은 상태 검증(state verification)에 사용한다

- Mock Annotation
  - @Mock
    - 실제로 메서드를 갖고 있지만 내부 구현이 없는 상태인 Mock 객체를 생성한다
    - when(), doReturn()/thenReturn(), thenThrow() 등을 사용해 메소드의 리턴 값 또는 예외 발생을 정할 수 있다
    - doReturn: 메소드를 실제 호출하지 않으면서 리턴 값을 임의로 정할 수 있다
    - thenReturn: 메소드를 실제 호출하지만, 리턴 값을 임의로 정의할 수 있다
  - @MockBean
    - 해당 객체를 Mock된 빈으로 등록함
    - Spring Boot container가 필요하고 bean이 container에 존재 해야 할때 사용
  - @Spy
    - 모든 기능을 가지고 있는 완전한(진짜) 객체를 생성한다
    - Mock과 달리 Stub이 필요한 부분을 지정할 수 있고, stub하지 않은 메소드들은 원본 메소드 그대로 사용한다
  - @SpyBean
    - 해당 객체를 Spy된 빈으로 등록함
    - Spring Boot container가 필요하고 bean이 container에 존재 해야 할때 사용
  - @InjectMocks
    - @Mock 또는 @Spy로 생성된 가짜 객체를 자동으로 주입시켜주는 객체이다
    - Service 객체를 @InjectMocks 어노테이션을 사용해 생성하고, Service단에서 사용할 Repository와 같은 객체들은 @Mock 어노테이션을 사용할 수 있다

### Junit Test Method
- Assert 메소드
  - assertArrayEquals(a, b): 배열 A와 B가 일치함을 확인
  - assertEquals(a, b): 객체 A와 B가 같은 값을 가지는지 확인
  - assertEquals(a, b, c): 객체 A와 B가 값이 일치함을 확인(a: 예상값, b:결과값, c: 오차범위)
  - assertSame(a, b): 객체 A와 B가 같은 객체임을 확인
  - assertTrue(a): 조건 A가 참인지 확인
  - assertNotNull(a): 객체 A가 null이 아님을 확인
- Mockito 메소드
  - doNothing().when(rabbitService).checkCount(count);
  - doReturn(count).when(rabbitService).getAttempts(count);
  - doThrow(RabbitServerException.class).doReturn(DEQUEUE_MESSAGE).when(rabbitService).dequeueMessage(QUEUE_ENUM);
  - verify(T mock, times(n)): stubing한 메소드가 n번 실행 됐는지 검증





























