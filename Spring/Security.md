# Security

### Spring Security 란
- Spring 기반의 애플리케이션의 보안(인증과 인가, 권한 등)을 담당하는 스프링 하위 프레임워크이다
- 인증과 권한에 대한 부분을 Filter 흐름에 따라 처리하며, Filter는 Dispatcher Servlet으로 가기 전에 적용되므로 가장 먼저 URL 요청을 받는다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/cc304b35-0fda-4a13-bac2-f58238645b91)
    1. 사용자가 아이디 비밀번호로 로그인을 요청함
    2. AuthenticationFilter에서 UsernamePasswordAuthenticationToken을 생성하여 AuthenticaionManager에게 전달
    3. AuthenticaionManager는 등록된 AuthenticaionProvider(들)을 조회하여 인증을 요구함
    4. AuthenticaionProvider는 UserDetailsService를 통해 입력받은 아이디에 대한 사용자 정보를 DB에서 조회함
    5. 입력받은 비밀번호를 암호화하여 DB의 비밀번호화 매칭되는 경우 인증이 성공된 UsernameAuthenticationToken을 생성하여 AuthenticaionManager로 반환함
    6. AuthenticaionManager는 UsernameAuthenticaionToken을 AuthenticaionFilter로 전달함
    7. AuthenticationFilter는 전달받은 UsernameAuthenticationToken을 LoginSuccessHandler로 전송하고, SecurityContextHolder에 저장함
- 인증과 인가를 위해 Principal을 아이디로, Credential을 비밀번호로 사용하는 Credential 기반의 인증 방식을 사용한다
  - 인증(Authentication): 해당 사용자가 본인이 맞는지를 확인하는 절차
  - 인가(Authorization): 인증된 사용자가 요청한 자원에 접근 가능한지를 결정하는 절차이다 
  - Principal(접근 주체): 보호받는 Resource에 접근하는 대상
  - Credential(비밀번호): Resource에 접근하는 대상의 비밀번호


### Spring Security 모듈
- Spring Security 주요 모듈
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/11745ed1-2a00-4c29-9538-8daff96cc5d2)
- SecurityContextHolder
  - 보안 주체의 세부 정보를 포함하여 응용프래그램의 현재 보안 컨텍스트에 대한 세부 정보가 저장된다
  - SecurityContextHolder.MODE_INHERITABLETHREADLOCAL 방법과 SecurityContextHolder.MODE_THREADLOCAL 방법을 제공한다
- SecurityContext
  - Authentication을 보관하는 역할을 하며, SecurityContext를 통해 Authentication 객체를 꺼내올 수 있다
- Authentication
  - 현재 접근하는 주체의 정보와 권한을 담는 인터페이스이다
  - SecurityContextHolder를 통해 SecurityContext에 접근하고, SecurityContext를 통해 Authentication에 접근할 수 있다
  - ```
    public interface Authentication extends Principal, Serializable {
        Collection<? extends GrantedAuthority> getAuthorities(); // 현재 사용자의 권한 목록을 가져옴
        Object getCredentials(); // credentials(주로 비밀번호)을 가져옴
        Object getDetails();
        Object getPrincipal(); // Principal 객체를 가져옴.
        boolean isAuthenticated(); // 인증 여부를 가져옴
        void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException; // 인증 여부를 설정함
    }
    ```
  - UsernamePasswordAuthenticationToken
    - Authentication을 implements한 AbstractAuthenticationToken의 하위 클래스로, User의 ID가 Principal 역할을 하고, Password가 Credential의 역할을 한다
    - 첫 번째 생성자는 인증 전의 객체를 생성하고, 두번째 생성자는 인증이 완려된 객체를 생성한다
    - ```
      public abstract class AbstractAuthenticationToken implements Authentication, CredentialsContainer {}
      public class UsernamePasswordAuthenticationToken extends AbstractAuthenticationToken {
          private final Object principal; // 주로 사용자의 ID에 해당함
          private Object credentials; // 주로 사용자의 PW에 해당함
    
          // 인증 완료 전의 객체 생성
          public UsernamePasswordAuthenticationToken(Object principal, Object credentials) {
	      	super(null);
	      	this.principal = principal;
	      	this.credentials = credentials;
      		setAuthenticated(false);
      	  }
    
          // 인증 완료 후의 객체 생성
          public UsernamePasswordAuthenticationToken(Object principal, Object credentials,
	      		Collection<? extends GrantedAuthority> authorities) {
      		super(authorities);
      		this.principal = principal;
      		this.credentials = credentials;
      		super.setAuthenticated(true); // must use super, as we override
      	  }
      }
      ```
  - AuthenticationProvider
    - 실제 인증에 대한 부분을 처리하며, 인증 전의 Authentication객체를 받아서 인증이 완료된 객체를 반환하는 역할을 한다
    - AuthenticationProvider 인터페이스를 구현해서 Custom한 AuthenticationProvider을 작성해서 AuthenticationManager에 등록하면 된다
    - ```
      public interface AuthenticationProvider {
          // 인증 전의 Authenticaion 객체를 받아서 인증된 Authentication 객체를 반환
          Authentication authenticate(Authentication var1) throws AuthenticationException;
          boolean supports(Class<?> var1);
      }
      ```
  - ProviderManager
    - AuthenticationManager를 implements한 ProviderManager는 실제 인증 과정에 대한 로직을 가지고 있는 AuthenticaionProvider를 List로 가지고 있다
    - 인증이 성공하면 2번째 생성자를 이용해 인증이 성공한(isAuthenticated=true) 객체를 생성하여 Security Context에 저장한다
    - 인증 상태를 유지하기 위해 세션에 보관하며, 인증이 실패한 경우에는 AuthenticationException를 발생시킨다
    - ```
      public class ProviderManager implements AuthenticationManager, MessageSourceAware, InitializingBean {
          public List<AuthenticationProvider> getProviders() { return providers; }
          public Authentication authenticate(Authentication authentication) throws AuthenticationException {
	      	  Class<? extends Authentication> toTest = authentication.getClass();
	      	  AuthenticationException lastException = null;
	      	  Authentication result = null;
	      	  boolean debug = logger.isDebugEnabled();
      
              //for문으로 모든 provider를 순회하여 처리하고 result가 나올 때까지 반복한다.
	      	  for (AuthenticationProvider provider : getProviders()) {
                  ....
	      		  try {
	      			  result = provider.authenticate(authentication);
		      		  if (result != null) {
		      			  copyDetails(authentication, result);
		      			  break;
		      		  }
		      	  }
		      	  catch (AccountStatusException e) {
			          prepareException(e, authentication);
			          // SEC-546: Avoid polling additional providers if auth failure is due to
			      	  // invalid account status
			      	  throw e;
		      	  }
                  ....
		      }
		      throw lastException;
	      }
      }
      ```
  - WebSecurityConfigurerAdapter
    - ProviderManager에 직접 Custom구현한 CustomAuthenticationProvider를 등록하는 방법은 WebSecurityConfigurerAdapter를 상속해 만든 클래스(SecurityConfig)에서 할 수 있다
    - WebSecurityConfigurerAdapter의 상위 클래스에서는 AuthenticationManager를 가지고 있기 때문에 CustomAuthenticationProvider를 등록할 수 있다
    - ```
      @Configuration
      @EnableWebSecurity
      public class SecurityConfig extends WebSecurityConfigurerAdapter {
          @Bean
          public AuthenticationManager getAuthenticationManager() throws Exception {
              return super.authenticationManagerBean();
          }
          @Bean
          public CustomAuthenticationProvider customAuthenticationProvider() throws Exception {
              return new CustomAuthenticationProvider();
          }
          @Override
          protected void configure(AuthenticationManagerBuilder auth) throws Exception {
              auth.authenticationProvider(customAuthenticationProvider());
          }
      }
      ```

### Spring Security 구현
- build.gradle에 dependency 추가
  - ```implementation 'org.springframework.boot:spring-boot-starter-security'```
- 정적 자원을 제공하는 클래스 생성
  - ```java
    @Configuration
    public class WebMvcConfig implements WebMvcConfigurer {
        private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/static/", "classpath:/public/", "classpath:/",
            "classpath:/resources/", "classpath:/META-INF/resources/", "classpath:/META-INF/resources/webjars/" };
        
        @Override
        public void addViewControllers(ViewControllerRegistry registry) {
            // /에 해당하는 url mapping을 /common/test로 forward한다.
            registry.addViewController( "/" ).setViewName( "forward:/index" );
            // 우선순위를 가장 높게 잡는다.
            registry.setOrder(Ordered.HIGHEST_PRECEDENCE);
        }
    
        @Override
        public void addResourceHandlers(ResourceHandlerRegistry registry) {
            registry.addResourceHandler("/**").addResourceLocations(CLASSPATH_RESOURCE_LOCATIONS);
        }
    }
    ```
- SpringSecurity에 대한 기본적인 설정들을 추가
  - ```java
    @Configuration
    @EnableWebSecurity
    @RequiredArgsConstructor
    public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
        
        private final UserDetailsService userDetailsService;
    
        // 정적 자원에 대해서는 Security 설정을 적용하지 않음
        @Override
        public void configure(WebSecurity web) {
            web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());
        }
    
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.csrf().disable()
                    .authorizeRequests()
                    .antMatchers("/admin").hasRole("ADMIN") // /admin 요청에 대해서는 ROLE_ADMIN 역할을 가지고 있어야 함
                    .antMatchers("/api/v1/**").hasRole(Role.USER.name())
                    .antMatchers("/", "/css/**", "/images/**",
                        "/js/**", "/h2-console/**", "/profile").permitAll()
                    .anyRequest().authenticated()
                .and()
                    .formLogin()  // 로그인하는 경우에 대해 설정함
                    .loginPage("/user/loginView") // 로그인 페이지를 제공하는 URL을 설정함
                    .successForwardUrl("/index") // 로그인 성공 URL을 설정함
                    .failureForwardUrl("/index") // 로그인 실패 URL을 설정함
                    .permitAll()
                .and()
                    .logout()
                    .logoutSuccessUrl("/")
                .and()
                    .addFilterBefore(customAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
        }
        @Bean
        public BCryptPasswordEncoder bCryptPasswordEncoder() {
            return new BCryptPasswordEncoder();
        }
    
        @Bean
        public CustomAuthenticationFilter customAuthenticationFilter() throws Exception {
            CustomAuthenticationFilter customAuthenticationFilter = new CustomAuthenticationFilter(authenticationManager());
            customAuthenticationFilter.setFilterProcessesUrl("/user/login");
            customAuthenticationFilter.setAuthenticationSuccessHandler(customLoginSuccessHandler());
            customAuthenticationFilter.afterPropertiesSet();
            return customAuthenticationFilter;
        }

        @Bean
        public CustomLoginSuccessHandler customLoginSuccessHandler() {
            return new CustomLoginSuccessHandler();
        }

        @Bean
        public CustomAuthenticationProvider customAuthenticationProvider() {
            return new CustomAuthenticationProvider(bCryptPasswordEncoder());
        }

        @Override
        public void configure(AuthenticationManagerBuilder authenticationManagerBuilder) {
            authenticationManagerBuilder.authenticationProvider(customAuthenticationProvider());
        }
    }
    ```

- Custom Class 생성
  - CustomAuthenticationFilter
    - ```java
      public class CustomAuthenticationFilter extends UsernamePasswordAuthenticationFilter {

          public CustomAuthenticationFilter(AuthenticationManager authenticationManager) {
              super.setAuthenticationManager(authenticationManager);
          }

          @Override
          public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
              UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(request.getParameter("userEmail"), request.getParameter("userPw"));
              setDetails(request, authRequest);
              return this.getAuthenticationManager().authenticate(authRequest);
          }
      }
      ```
  - CustomAuthenticationProvider
    - ```java
      @RequiredArgsConstructor
      public class CustomAuthenticationProvider implements AuthenticationProvider {

          private final UserDetailsService userDetailsService;
          private final BCryptPasswordEncoder passwordEncoder;

          @Override
          public Authentication authenticate(Authentication authentication) throws AuthenticationException {
              UsernamePasswordAuthenticationToken token = (UsernamePasswordAuthenticationToken) authentication;
    
              // AuthenticaionFilter에서 생성된 토큰으로부터 아이디와 비밀번호를 조회함
              String userEmail = token.getName();
              String userPw = (String) token.getCredentials();
    
              // UserDetailsService를 통해 DB에서 아이디로 사용자 조회
              UserDetailsVO userDetailsVO = (UserDetailsVO) userDetailsService.loadUserByUsername(userEmail);
              if (!passwordEncoder.matches(userPw, userDetailsVO.getPassword())) {
                  throw new BadCredentialsException(userDetailsVO.getUsername() + "Invalid password");
              }

              return new UsernamePasswordAuthenticationToken(userDetailsVO, userPw, userDetailsVO.getAuthorities());
          }
    
          @Override
          public boolean supports(Class<?> authentication) {
              return authentication.equals(UsernamePasswordAuthenticationToken.class);
          }
      }
      ```
  - CustomLoginSuccessHandler
    - ```java    
      public class CustomLoginSuccessHandler extends SavedRequestAwareAuthenticationSuccessHandler {

          @Override
          public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response,
                                          Authentication authentication) throws IOException {
              SecurityContextHolder.getContext().setAuthentication(authentication);
              response.sendRedirect("/");
          }
      }
      ```

### OAuth2 란
- OAuth2(Open Authorization 2.0)는 인증을 위한 개방형 표준 프로토콜로, 다양한 환경의 사용자 인증 절차를 표준화 한 것이다
- Third-Party 프로그램에게 리소스 소유자를 대신하여 리소스 서버에서 제공하는 자원에 대한 접근 권한을 위임하는 방식을 제공한다
- 용어
  - 리소스 소유자(Resource Owner)
    - 구글 등의 로그인을 할 사용자(User) 본인의 정보에 접근할 수 있는 자격을 승인하는 주체
    - 클라이언트를 인증(Authorize)하는 역할을 수행하고, 인증이 완료되면 동의를 통해 권한 획득 자격(Authorization Grant)을 클라이언트에게 부여한다
  - 클라이언트(Client): Resource Owner의 리소스를 사용하고자 접근 요청을 하는 어플리케이션(Application)
  - 리소스 서버(Resource Server): Resource Owner의 정보가 저장되어 있는 서버(구글 등)
  - Authorization Server(권한 서버): 인증/인가를 수행하는 서버로 클라이언트의 접근 자격을 확인하고 Access Token을 발급하여 권한을 부여하는 역할을 수행한다
  - Authentication(인증): 접근 자격이 있는지 검증하는 단계
  - Authorization(인가): 자원에 접근할 권한을 부여하고 리소스 접근 권한이 담긴 Access Token을 제공한다
  - Access Token: 리소스 서버에게서 리소스 소유자의 정보를 획득할 때 사용되는 만료 기간이 있는 Token
  - Refresh Token: Access Token 만료시 이를 재발급 받기위한 용도로 사용하는 Token
- 권한 부여 방식
  - Authorization Code Grant(권한 부여 승인 코드 방식)
    - 권한 부여 승인을 위해 자체 생성한 Authorization Code를 전달하는 방식
    - 간편 로그인 기능에서 사용되는 방식으로 클라이언트가 사용자를 대신하여 특정 자원에 접근을 요청할 때 사용되는 방식이다
    - Refresh Token의 사용이 가능한 방식으로, 보통 타사의 클라이언트에게 보호된 자원을 제공하기 위한 인증에 사용된다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/9f068607-5bf3-488b-9d89-2056b744610f)
      - 권한 부여 승인 요청 시 response_type=code로 지정하여 요청한다
      - 사용자가 로그인을 하면 권한 서버는 권한 부여 승인 코드 요청 시 전달받은 redirect_url로 Authorization Code를 전달한다
      - Authorization Code는 권한 서버에서 제공하는 API를 통해 Access Token으로 교환된다
  - Implicit Grant(암묵적 승인 방식)
    - 자격증명을 안전하게 저장하기 힘든 클라이언트(JavaScript등의 스크립트 언어를 사용한 브라우저)에서 사용하는 방식
    - 권한 부여 승인 코드 없이 바로 Access Token이 발급되며, 위험을 방지하기 위해 만료기간을 짧게 설정하여 전달한다
    - Refresh Token 사용이 불가능한 방식
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/7df4f49a-bd80-48b7-9cd2-14694f1c31c5)
      - 권한 부여 승인 요청 시 response_type=token으로 설정하여 요청한다
      - 권한 서버는 client_secret를 사용해 클라이언트를 인증하지 않는다
      - 로그인이 완료되면 권한 서버는 Authorization Code가 아닌 Access Token를 redirect_url로 바로 전달한다(URL로 전달됨)
  - Resource Owner Password Credentials Grant(자원 소유자 자격증명 승인 방식)
    - 간단하게 username, password로 Access Token을 받는 방식
    - 클라이언트가 타사의 외부 프로그램이 아니라 자신의 서비스에서 제공하는 어플리케이션일 경우에만 사용되는 인증 방식이다
    - Refresh Token 사용 가능한 방식
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/4269dd09-c9ac-4628-8c0a-0a43c3f90ed2)
      - 제공하는 API를 통해 username, password을 전달하여 Access Token을 받는다
      - 이 방식은 권한 서버, 리소스 서버, 클라이언트가 모두 같은 시스템에 속해 있을 때 사용되어야 하는 방식이다
  - Client Credentials Grant(클라이언트 자격증명 승인 방식)
    - 클라이언트의 자격증명만으로 Access Token을 획득하는 방식
    - 클라이언트 자신이 관리하는 리소스 혹은 권한 서버에, 해당 클라이언트를 위한 제한된 리소스 접근 권한이 설정되어 있는 경우 사용한다
    - 자격증명을 안전하게 보관할 수 있는 클라이언트에서만 사용되어야 하며, Refresh Token은 사용할 수 없다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/f7fda089-f67d-4072-a70f-14b53c9dc4e8)
- build.gradle에 dependency (2.x 버전) 추가
  - ```implementation('org.springframework.boot:spring-boot-starter-oauth2-client')```



### OAuth2 서비스 등록
- 구글 서비스 등록
  - 구글 클라우드 플랫폼 주소로 들어간다
    - 주소: https://console.cloud.google.com
  - 프로젝트 선택 > 새 프로젝트 > 원하는 이름으로 프로젝트 생성
  - API 및 시버스 > 사용자 인증 정보 > 사용자 인증 정보 만들기 > OAuth 클라이언트 ID
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/cc0f3954-2c04-4f05-ba83-12eb4fd1962c)
  - OAuth 클라이언트 ID > 유형 선택하고 이름 작성, 승인된 리디렉션 URI 추가 > 만들기
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/c11b1e54-d4ab-4933-b93a-0fd7b487001a)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/d5504762-44f8-48a6-b720-8b81017801bf)
    - 승인된 리디렉션 URI
      - 서비스에서 파라미터로 인증 정보가 주었을 때 인증이 성공하면 구글에서 리다이렉트할 URL
      - 스프링 부트 2 버전의 시큐리티에서는 기본적으로 {도메인}/login/oauth2/code/{소셜서비스코드}
      - 별도록 리다이렉트 URL을 지원하는 Controller을 만들 필요 없음
- 네이버 서비스 등록
  - 네이버 오픈 API로 이동한다
    - 주소: https://developers.naver.com/apps/#/list (https://developers.naver.com/main/)
  - 애플리케이션 등록 > 애플리케이션 이름 입력 > 사용 API 선택 > 제공 정보 선택 (기본값: 회원이름, 이메일, 프로필 사진) 
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/b07e0c76-42c5-45cd-a65c-31c5b2bc68ff)
  - 환경 추가 > 서비스 URL 입력 > 네이버 아이디로 로그인 입력 > 등록하기
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/faa40b1f-9afe-4756-aa62-de6b0026b0e6)
  - 클라이언트 ID, 클라이언트 Secret GET
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/4f1936e1-5d2f-4545-b184-17fabc0a51a8)
- application-oauth.properties 생성
  - 구글 연동
    - ```
      spring.security.oauth2.client.registration.google.client-id=(클라이언트 ID)
      spring.security.oauth2.client.registration.google.client-secret=(클라이언트 보안 비밀번호)
      spring.security.oauth2.client.registration.google.scope=profile,email
      ```
      - scope의 기본값: openid,profile,email
      - openid라는 scope이 있으면 OpenId Provider로 인식하기 때문에 OpenId Provider인 서비스(구글)와 그렇지 않은 서비스로 나눠서 OAuth2Service를 만들어야 한다
  - 네이버 연동
    - ```
      spring:
        security:
          oauth2:
            client:
              registration:
                naver:
                  client-id: 클라이언트ID
                  client-secret: 클라이언트Secret
                  redirect-uri: "{baseUrl}/{action}/oauth2/code/{registrationId}" # (== http://localhost:8080/login/oauth2/code/naver)
                  authorization-grant-type: authorization_code
                  scope: name, email, profile_image
                  client-name: Naver
              provider:
                naver:
                  authorization_uri: https://nid.naver.com/oauth2.0/authorize
                  token_uri: https://nid.naver.com/oauth2.0/token
                  user-info-uri: https://openapi.naver.com/v1/nid/me
                  user_name_attribute: response
      ```
      - authorization_uri
        - 로그인 버튼을 클릭하였을 때 이동할 네이버 로그인 URL을 의미한다
        - 사용자가 로그인 연동에 동의하였을 경우 동의 정보를 포함하여 Callback URL로 전송된다
      - token_url
        - Callback으로 전달받은 정보를 이용하여 접근 토큰을 발급받을 URL을 의미한다
        - 접근 토큰(Access Token)을 이용하여 프로필 API를 호출하거나 오픈API를 호출하는것이 가능하다
        - ![image](https://github.com/kimho1wq/TIL/assets/15611500/6920129d-6d89-477d-bb81-a4cf344c5fea)
        - ```https://nid.naver.com/oauth2.0/token?grant_type=authorization_code&client_id=jyvqXeaVOVmV&client_secret=527300A0_COq1_XV33cf&code=EIc5bFrl4RibFls1&state=9kgsGTfH4j7IyAkg```
      - user_name_attribute
        - 요청이 승인되면 출력 결과는 response의 이름을 가진 JSON데이터로 주어지기 때문에 기준이 되는 user_name을 response로 입력해줘야 한다
        - ```
          {
              "resultcode": "00",
              "message": "success",    
              "response": { 
                  "email": "openapi@naver.com",
                  "nickname": "OpenAPI",
                  "profile_image": "https://ssl.plastic.net/static/pwe/address/nodata_33x33.gif",
                  "age": "40-49",
                  "gender": "F",
                  "id": "33742276",
                  "name": "오픈 API",
                  "birthday": "10-01"
              }
          }
          ```

### OAuth2-Client 동작 과정
- OAuth2에서 가장 기본이 되는 방식이며 간편 로그인에 사용되는 Authorization Code Grant 방식을 사용한다
  1. Client는 Authorization Server로 접근 권한을 요청한다(Client는 우리가 개발하는 Application을 의미)
  2. Client로부터 접근 권한 요청을 받은 Authorization Server는 소셜 로그인을 할 수 있는 로그인 창을 띄워 준다
  3. User(Resource Owner)는 해당 소셜 로그인 창을 통해 로그인을 진행한다
  4. Authorization Server는 User가 입력한 회원 정보가 맞는지 여부를 판단한 뒤, 권한 승인 코드(Authorization Code)를 반환하게 된다
  5. Client는 Authorization Code를 통해 Resource Server에 보호된 자원을 요청할 수 있는 Access Token을 요청한다
  6. Access Token을 전달받은 Client는 해당 토큰을 통해 Resource Server에 필요한 요청을 보내게 된다
- OAuth2UserService 인터페이스의 구현체(CustomOAuth2UserService)
  - ```
    @Service
    public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {
        @Override
        public OAuth2User loadUser(OAuth2UserRequest oAuth2UserRequest) throws OAuth2AuthenticationException {
            OAuth2UserService oAuth2UserService = new DefaultOAuth2UserService();
            OAuth2User oAuth2User = oAuth2UserService.loadUser(oAuth2UserRequest);
            ...
        }
    }
  - OAuth2UserRequest 객체에는 소셜에서 사용자 인증을 마친 최종 access token을 가지고 있다
  - 즉, 소셜 로그인 이후 loadUser() 메서드를 타기 전에, 위에서 본 권한 부여 코드 승인 방식의 iv(4)번과 v(5)번 과정이 내부적으로 진행된다
- FilterChain
  - ```
    @RequiredArgsConstructor
    @EnableWebSecurity
    @Configuration
    public class WebSecurityConfigure {
        private final CustomOAuth2UserService customOAuth2UserService;
    
        @Bean
        public SecurityFilterChain filterChain(HttpSecurity http) throws Exception { 
            ...
            //oauth2Login
            http.oauth2Login()
                .authorizationEndpoint()
                .baseUri("/oauth2/authorize")
                .authorizationRequestRepository(cookieAuthorizationRequestRepository)
                .and()
                    .redirectionEndpoint()
                    .baseUri("/oauth2/callback/*")
                .and()
                    .userInfoEndpoint()
                    .userService(customOAuth2UserService)
            ...
            return http.build();
        }
    }
    ```
  - http.oauth2Login() 설정 시 체이닝 되는 loginPage() 메서드로 커스텀 로그인 페이지를 설정할 수 있다
  - 설정하지 않으면, OAuth2LoginConfigurer 클래스의 init() 메서드에 의해 DefaultLoginPageGeneratingFilter 클래스의 DEFAULT_LOGIN_PAGE_URL = "/login"이 로그인 페이지로 설정된다
- Authorization Code
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/fe194f62-b38e-4f13-b09f-81b5ca6201c0)
  - Resource Owner가 해당 소셜 로그인 진행하고 나면 OAuth2LoginAuthenticationFilter 클래스의 attemptAuthentication() method가 동작
  - attemptAuthentication()는 Authorization Server로부터 전달된 code를 HttpServletRequest로부터 가져오게 된다
- Access Token
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/b41fb235-cd85-4b73-91b1-c9b3d63e2a62)
  - OAuth2AuthorizationCodeAuthenticationProvider 클래스의 authenticate()가 동작
  - authenticate()에서 accessTokenResponseClient의 구현체인 DefaultAuthorizationCodeTokenResponseClient의 getTokenResponse()가 동작
  - getTokenResponse()에서 AuthorizationCode를 통해 Access Token을 요청하게 됩니다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/f5917f2f-84cd-44d6-9015-b983ca474c66)
    - requestEntityConverter의 구현체는 OAuth2AuthorizationCodeGrantRequestEntityConverter가 사용된다
    - convert() 메서드를 살펴보면 client_id, redirect_uri, grant_type, code 등을 가지고 Authorization Server로 POST 요청을 보내는 것을 확인할 수 있다
    

















