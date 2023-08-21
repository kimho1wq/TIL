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
    



### CORS(Cross-Origin Resource Sharing)
- 브라우저에서 보안적인 이유로 HTTP 요청을 HTTP-header를 이용해 제한하는 메커니즘
- cross-origin 이란 다음 중 한가지라도 다른 경우를 의미한다
  1. 프로토콜: http와 https는 프로토콜이 다르다
  2. 도메인: domain.com과 other-domain.com은 다르다
  3. 포트 번호: 8080포트와 3000포트는 다르다
- CORS가 없으며 모든 곳에서 데이터를 요청할 수 있게 되고, 다른 곳에서 서버를 흉내내며 사용자의 정보를 탈취할 수 있다
- 요청 헤더 목록
  - Origin
  - Access-Control-Request-Method
    - preflight 요청을 할 때 실제 요청에서 어떤 HTTP method를 사용할 것인지 서버에게 알리기 위해 사용된다
  - Access-Control-Request-Headers
    - preflight 요청을 할 때 실제 요청에서 어떤 header를 사용할 것인지 서버에게 알리기 위해 사용된다
- 응답 헤더 목록
  - Access-Control-Allow-Origin
    - 브라우저가 해당 origin이 자원에 접근할 수 있도록 허용하고, *은 credentials이 없는 요청에 한해서 모든 origin에서 접근이 가능하도록 허용한다는 의미이다
  - Access-Control-Allow-Methods
    - preflight 요청에 대한 대한 응답으로 허용되는 메서드들을 나타낸다
  - Access-Control-Allow-Headers
    - preflight 요청에 대한 대한 응답으로 실제 요청 시 사용할 수 있는 HTTP 헤더를 나타낸다
- CORS의 동작
  - Simple requests
    1. 서버로 요청을 한 후, 응답이 왔을 때 브라우저가 요청한 origin과 응답한 헤더 access-control-request-headers의 값을 비교한다
    2. 비교한 값이 유효한 요청이라면 리소스를 응답하고, 유효하지 않은 요청이라면 브라우저에서 이를 막고 에러가 발생한다
  - Preflight
    1. Origin 헤더에 현재 요청하는 origin과, Access-Control-Request-Method 헤더에 요청하는 HTTP method와, Access-Control-Request-Headers 요청 시 사용할 헤더, OPTIONS method를 사용해서 내용 없이 헤더만 서버로 요청한다
    2. 브라우저가 서버에서 응답한 헤더가 유효한 요청이라면 원래 요청으로 보내려던 요청을 다시 보내여 리소스를 응답받는다



  
### 세션 vs 토큰 기반의 인증 시스템
- 세션(Session)
  - 서버 측에서 사용자들의 정보를 기억하기 위해 세션을 유지하며, 메모리나 디스크 등을 통해 관리한다
  - 서버(세션) 기반의 인증 시스템은 클라이언트로부터 요청을 받으면, 클라이언트의 상태를 계속 유지하며 이러한 서버를 Sateful 서버라고 한다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/e4ef0107-2ef6-4e0d-87a4-e0d96313e05f)
  - 사용자의 정보를 메모리나 DB에 저장하는데 서버가 확장될 수록 부하가 커지며, 확장을 위해 세션을 분산시키는 시스템을 설계해야 한다
- 토큰(Token)
  - 인증받은 사용자들에게 토큰을 발급하고, 서버에 요청을 할 때 헤더에 토큰을 함께 보내도록 하여 유효성 검사를 하는 방식
  - 서버 기반의 인증 시스템과 토큰이 클라이언트 측에 저장되기 때문에 서버는 Stateless한 구조를 갖는다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/b2302b97-cc36-4fec-995d-8a5dc49cc678)
  - 클라이언트와 서버의 연결고리가 없기 때문에 확장하기에 매우 적합하다
  - 토큰 기반의 인증 시스템에서는 토큰에 선택적인 권한만 부여하여 발급할 수 있으며 OAuth의 경우 다른 서비스에 대한 확장성(Extensibility)도 있다
  - 서버 기반 인증 시스템의 문제점 중 하나인 CORS를 해결할 수 있는데, 이런 구조를 통해 assests 파일(Image, html, css, js 등)은 모두 CDN에서 제공하고, 서버 측에서는 API만 다루도록 설계할 수 있다




### JWT(Json Web Token)
- JWT(Json Web Token)란 Json 포맷을 이용하여 사용자에 대한 속성을 저장하는 Claim 기반의 Web Token이다
- JWT는 토큰 자체를 정보로 사용하는 Self-Contained 방식으로, 주로 회원 인증이나 정보 전달에 사용된다
- JWT는 HTTP 통신을 할 때마다 헤더에 담아서 보내야 하므로 static 변수 또는 로컬 스토리지에 저장하게 된다
- 로그아웃 시 로컬 스토리지의 JWT를 제거하고, 사용했던 토큰을 blacklist라는 DB 테이블에 넣어 해당 토큰의 접근을 막는다
- JWT 구조
  - Header, Payload, Signature의 3 부분으로 이루어지며 각각의 부분을 이어 주기 위해 '.' 구분자를 사용하여 구분한다
  - Json 형태인 각 부분은 Base64Url로 인코딩 되어 표현된다
  - Base64Url는 암호화된 문자열이 아니기 때문에 같은 문자열에 대해 항상 같은 인코딩 문자열을 반환한다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/0e5951bd-034f-49d1-96ef-ef58cbb15831)
  - Header(헤더)
    - typ: 토큰의 타입을 지정(ex. JWT)
    - alg: 서명(Signature)를 해싱하기 위한 알고리즘을 지정하며 토큰 검증에도 사용(ex, HS256 or RSA)
  - PayLoad(페이로드)
    - 토큰에서 사용할 정보의 조각들인 클레임(Claim)이 담겨 있으며 Json형태로 다수의 정보를 넣을 수 있다
    - 등록된 클레임(registed claims)은 사전 정의되어 사용할 것이 권고되는 정보이다
      - ```
        iss : 이 데이터의 발행자를 뜻한다
        iat : 이 데이터가 발행된 시간을 뜻한다
        exp : 이 데이터가 만료된 시간을 뜻한다
        sub : 토큰의 제목
        aud : 토큰의 대상
        nbf : 토큰이 처리되지 않아야 할 시점을 의미하고, 이 시점이 지나기 전엔 토큰이 처리되지 않는다
        jti : 토큰의 고유 식별자
        ```
    - 공개 클레임(public claims)은 보통 충돌 방지를 위해 URI 형식으로 키를 지정한다
    - 비공개 클레임(private claims)은 통신을 주고받는 당사자끼리 협의해 키와 값을 정한다
  - Signature(서명): 토큰을 인코딩하거나 유효성 검증을 할 때 사용하는 암호화 코드이다
- 생성된 토큰은 HTTP 통신을 할 때 key-value 형태로 사용된다
  - ```json
    { 
        "Authorization": "Bearer {생성된 토큰 값}"
    }
    ```
- JWT의 단점
  - 토큰 자체에 정보를 담고 있고, 정보가 많아질수록 토큰의 길이가 늘어나 네트워크에 부하를 줄 수 있다
  - 페이로드 자체는 암호화 된 것이 아니기 때문에 JWE(JSON Web Encryption)로 암호화 하거나 중요 데이터를 넣어서는 안된다
  - 상태를 저장하지 않기(Stateless) 때문에 임의로 삭제가 불가능하므로 토큰 만료 시간을 넣어주어야 한다
  - 토큰은 클라이언트 측에서 관리하여 저장해서 사용해야 한다


### Spring Security + JWT 구현
- ![image](https://github.com/kimho1wq/TIL/assets/15611500/cdfb7aa7-1d76-4460-b97c-adfa4ee96dc0)
  - https://wildeveloperetrain.tistory.com/57
- JWT
  - JwtAuthenticationFilter: 클라이언트 요청 시 JWT 인증을 하기 위해 설치하는 Custom Filter로 UsernamePasswordAuthenticationFilter 이전에 실행된다
    - ```java
      @RequiredArgsConstructor
      public class JwtAuthenticationFilter extends GenericFilterBean {

          private static final String AUTHORIZATION_HEADER = "Authorization";
          private static final String BEARER_TYPE = "Bearer";

          private final JwtTokenProvider jwtTokenProvider;
          private final RedisTemplate redisTemplate;

          @Override
          public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

              // 1. Request Header 에서 JWT 토큰 추출
              String token = resolveToken((HttpServletRequest) request);

              // 2. validateToken 으로 토큰 유효성 검사
              if (token != null && jwtTokenProvider.validateToken(token)) {
                  // (추가) Redis 에 해당 accessToken logout 여부 확인
                  String isLogout = (String)redisTemplate.opsForValue().get(token);
                  if (ObjectUtils.isEmpty(isLogout)) {
                      // 토큰이 유효할 경우 토큰에서 Authentication 객체를 가지고 와서 SecurityContext 에 저장
                      Authentication authentication = jwtTokenProvider.getAuthentication(token);
                      SecurityContextHolder.getContext().setAuthentication(authentication);
                  }
              }
              chain.doFilter(request, response);
          }

          // Request Header 에서 토큰 정보 추출
          private String resolveToken(HttpServletRequest request) {
              String bearerToken = request.getHeader(AUTHORIZATION_HEADER);
              if (StringUtils.hasText(bearerToken) && bearerToken.startsWith(BEARER_TYPE)) {
                  return bearerToken.substring(7);
              }
              return null;
          }
      }
      ```
  - JwtTokenProvider: JWT 토큰 생성, 토큰 복호화 및 정보 추출, 토큰 유효성 검증의 기능이 구현된 클래스
    - ```java
      @Component
      public class JwtTokenProvider {

          private static final String AUTHORITIES_KEY = "auth";
          private static final String BEARER_TYPE = "Bearer";
          private static final long ACCESS_TOKEN_EXPIRE_TIME = 30 * 60 * 1000L;              // 30분
          private static final long REFRESH_TOKEN_EXPIRE_TIME = 7 * 24 * 60 * 60 * 1000L;    // 7일

          private final Key key;

          public JwtTokenProvider(@Value("${jwt.secret}") String secretKey) {
              byte[] keyBytes = Decoders.BASE64.decode(secretKey);
              this.key = Keys.hmacShaKeyFor(keyBytes);
          }

          // 유저 정보를 가지고 AccessToken, RefreshToken 을 생성하는 메서드
          public UserResponseDto.TokenInfo generateToken(Authentication authentication) {
              // 권한 가져오기
              String authorities = authentication.getAuthorities().stream()
                      .map(GrantedAuthority::getAuthority)
                      .collect(Collectors.joining(","));

              long now = (new Date()).getTime();
              // Access Token 생성
              Date accessTokenExpiresIn = new Date(now + ACCESS_TOKEN_EXPIRE_TIME);
              String accessToken = Jwts.builder()
                      .setSubject(authentication.getName())
                      .claim(AUTHORITIES_KEY, authorities)
                      .setExpiration(accessTokenExpiresIn)
                      .signWith(key, SignatureAlgorithm.HS256)
                      .compact();

              // Refresh Token 생성
              String refreshToken = Jwts.builder()
                      .setExpiration(new Date(now + REFRESH_TOKEN_EXPIRE_TIME))
                      .signWith(key, SignatureAlgorithm.HS256)
                      .compact();

              return UserResponseDto.TokenInfo.builder()
                      .grantType(BEARER_TYPE)
                      .accessToken(accessToken)
                      .refreshToken(refreshToken)
                      .refreshTokenExpirationTime(REFRESH_TOKEN_EXPIRE_TIME)
                      .build();
          }

          // JWT 토큰을 복호화하여 토큰에 들어있는 정보를 꺼내는 메서드
          public Authentication getAuthentication(String accessToken) {
              // 토큰 복호화
              Claims claims = parseClaims(accessToken);

              if (claims.get(AUTHORITIES_KEY) == null) {
                  throw new RuntimeException("권한 정보가 없는 토큰입니다.");
              }

              // 클레임에서 권한 정보 가져오기
              Collection<? extends GrantedAuthority> authorities =
                      Arrays.stream(claims.get(AUTHORITIES_KEY).toString().split(","))
                      .map(SimpleGrantedAuthority::new)
                      .collect(Collectors.toList());

              // UserDetails 객체를 만들어서 Authentication 리턴
              UserDetails principal = new User(claims.getSubject(), "", authorities);
              return new UsernamePasswordAuthenticationToken(principal, "", authorities);
          }

          // 토큰 정보를 검증하는 메서드
          public boolean validateToken(String token) {
              try {
                  Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token);
                  return true;
              } catch (io.jsonwebtoken.security.SecurityException | MalformedJwtException e) {
                  log.info("Invalid JWT Token", e);
              } catch (ExpiredJwtException e) {
                  log.info("Expired JWT Token", e);
              } catch (UnsupportedJwtException e) {
                  log.info("Unsupported JWT Token", e);
              } catch (IllegalArgumentException e) {
                  log.info("JWT claims string is empty.", e);
              }
              return false;
          }

          private Claims parseClaims(String accessToken) {
              try {
                  return Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(accessToken).getBody();
              } catch (ExpiredJwtException e) {
                  return e.getClaims();
              }
          }

          public Long getExpiration(String accessToken) {
              // accessToken 남은 유효시간
              Date expiration = Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(accessToken).getBody().getExpiration();
              // 현재 시간
              Long now = new Date().getTime();
              return (expiration.getTime() - now);
          }
      }
      ``` 
- Security
  - WebSecurityConfig: Secutiry 설정을 위한 class로 WebSecurityConfigurerAdapter를 상속받는다
    - ```
      @RequiredArgsConstructor
      @EnableWebSecurity
      public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

          private final JwtTokenProvider jwtTokenProvider;
          private final RedisTemplate redisTemplate;

          @Override
          protected void configure(HttpSecurity httpSecurity) throws Exception {
              httpSecurity
                      .httpBasic().disable()
                      .csrf().disable()
                      .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                      .and()
                      .authorizeRequests()
                      .antMatchers("/api/v1/users/sign-up", "/api/v1/users/login", "/api/v1/users/authority", "/api/v1/users/reissue", "/api/v1/users/logout").permitAll()
                      .antMatchers("/api/v1/users/userTest").hasRole("USER")
                      .antMatchers("/api/v1/users/adminTest").hasRole("ADMIN")
                      .and()
                      .addFilterBefore(new JwtAuthenticationFilter(jwtTokenProvider, redisTemplate), UsernamePasswordAuthenticationFilter.class);
                      // JwtAuthenticationFilter를 UsernamePasswordAuthentictaionFilter 전에 적용시킨다.
          }

          // 암호화에 필요한 PasswordEncoder Bean 등록
          @Bean
          public PasswordEncoder passwordEncoder() {
              return new BCryptPasswordEncoder();
          }
      }
      ```   
  - CustomUserDetailsService: 인증에 필요한 UserDetailsService interface를 구현하는 클래스로 loadUserByUsername 메서드를 통해 Database에 접근하여 사용자 정보를 가지고 온다
    - ```
      @Service
      @RequiredArgsConstructor
      public class CustomUserDetailsService implements UserDetailsService {

          private final UsersRepository usersRepository;

          @Override
          public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
              return usersRepository.findByEmail(username)
                      .map(this::createUserDetails)
                      .orElseThrow(() -> new UsernameNotFoundException("해당하는 유저를 찾을 수 없습니다."));
          }

          // 해당하는 User 의 데이터가 존재한다면 UserDetails 객체로 만들어서 리턴
          private UserDetails createUserDetails(Users users) {
              return new User(users.getUsername(), users.getPassword(), users.getAuthorities());
          }
      }
      ``` 
  - SecurityUtil: 클라이언트 요청 시 JwtAuthenticationFilter에서 인증되어 SecurityContextHolder에 저장된 Authentication 객체 정보를 가져오기 위한 클래스
    - ```
      public class SecurityUtil {

          public static String getCurrentUserEmail() {
              final Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
              if (authentication == null || authentication.getName() == null) {
                  throw new RuntimeException("No authentication information.");
              }
              return authentication.getName();
          }
      }
      ``` 
- UserService
  - ```java
    @RequiredArgsConstructor
    @Service
    public class UsersService {

        private final UsersRepository usersRepository;
        private final Response response;
        private final PasswordEncoder passwordEncoder;
        private final JwtTokenProvider jwtTokenProvider;
        private final AuthenticationManagerBuilder authenticationManagerBuilder;
        private final RedisTemplate redisTemplate;

        public ResponseEntity<?> signUp(UserRequestDto.SignUp signUp) {
            if (usersRepository.existsByEmail(signUp.getEmail())) {
                return response.fail("이미 회원가입된 이메일입니다.", HttpStatus.BAD_REQUEST);
            }

            Users user = Users.builder()
                    .email(signUp.getEmail())
                    .password(passwordEncoder.encode(signUp.getPassword()))
                    .roles(Collections.singletonList(Authority.ROLE_USER.name()))
                    .build();
            usersRepository.save(user);

            return response.success("회원가입에 성공했습니다.");
        }

        public ResponseEntity<?> login(UserRequestDto.Login login) {

            if (usersRepository.findByEmail(login.getEmail()).orElse(null) == null) {
                return response.fail("해당하는 유저가 존재하지 않습니다.", HttpStatus.BAD_REQUEST);
            }

            // 1. Login ID/PW 를 기반으로 Authentication 객체 생성
            // 이때 authentication 는 인증 여부를 확인하는 authenticated 값이 false
            UsernamePasswordAuthenticationToken authenticationToken = login.toAuthentication();

            // 2. 실제 검증 (사용자 비밀번호 체크)이 이루어지는 부분
            // authenticate 매서드가 실행될 때 CustomUserDetailsService 에서 만든 loadUserByUsername 메서드가 실행
            Authentication authentication = authenticationManagerBuilder.getObject().authenticate(authenticationToken);

            // 3. 인증 정보를 기반으로 JWT 토큰 생성
            UserResponseDto.TokenInfo tokenInfo = jwtTokenProvider.generateToken(authentication);

            // 4. RefreshToken Redis 저장 (expirationTime 설정을 통해 자동 삭제 처리)
            redisTemplate.opsForValue()
                    .set("RT:" + authentication.getName(), tokenInfo.getRefreshToken(), tokenInfo.getRefreshTokenExpirationTime(), TimeUnit.MILLISECONDS);

            return response.success(tokenInfo, "로그인에 성공했습니다.", HttpStatus.OK);
        }

        public ResponseEntity<?> reissue(UserRequestDto.Reissue reissue) {
            // 1. Refresh Token 검증
            if (!jwtTokenProvider.validateToken(reissue.getRefreshToken())) {
                return response.fail("Refresh Token 정보가 유효하지 않습니다.", HttpStatus.BAD_REQUEST);
            }

            // 2. Access Token 에서 User email 을 가져옵니다.
            Authentication authentication = jwtTokenProvider.getAuthentication(reissue.getAccessToken());

            // 3. Redis 에서 User email 을 기반으로 저장된 Refresh Token 값을 가져옵니다.
            String refreshToken = (String)redisTemplate.opsForValue().get("RT:" + authentication.getName());
            // (추가) 로그아웃되어 Redis 에 RefreshToken 이 존재하지 않는 경우 처리
            if(ObjectUtils.isEmpty(refreshToken)) {
                return response.fail("잘못된 요청입니다.", HttpStatus.BAD_REQUEST);
            }
            if(!refreshToken.equals(reissue.getRefreshToken())) {
                return response.fail("Refresh Token 정보가 일치하지 않습니다.", HttpStatus.BAD_REQUEST);
            }

            // 4. 새로운 토큰 생성
            UserResponseDto.TokenInfo tokenInfo = jwtTokenProvider.generateToken(authentication);

            // 5. RefreshToken Redis 업데이트
            redisTemplate.opsForValue()
                    .set("RT:" + authentication.getName(), tokenInfo.getRefreshToken(), tokenInfo.getRefreshTokenExpirationTime(), TimeUnit.MILLISECONDS);

            return response.success(tokenInfo, "Token 정보가 갱신되었습니다.", HttpStatus.OK);
        }

        public ResponseEntity<?> logout(UserRequestDto.Logout logout) {
            // 1. Access Token 검증
            if (!jwtTokenProvider.validateToken(logout.getAccessToken())) {
                return response.fail("잘못된 요청입니다.", HttpStatus.BAD_REQUEST);
            }

            // 2. Access Token 에서 User email 을 가져옵니다.
            Authentication authentication = jwtTokenProvider.getAuthentication(logout.getAccessToken());

            // 3. Redis 에서 해당 User email 로 저장된 Refresh Token 이 있는지 여부를 확인 후 있을 경우 삭제합니다.
            if (redisTemplate.opsForValue().get("RT:" + authentication.getName()) != null) {
                // Refresh Token 삭제
                redisTemplate.delete("RT:" + authentication.getName());
            }

            // 4. 해당 Access Token 유효시간 가지고 와서 BlackList 로 저장하기
            Long expiration = jwtTokenProvider.getExpiration(logout.getAccessToken());
            redisTemplate.opsForValue()
                    .set(logout.getAccessToken(), "logout", expiration, TimeUnit.MILLISECONDS);

            return response.success("로그아웃 되었습니다.");
        }

        public ResponseEntity<?> authority() {
            // SecurityContext에 담겨 있는 authentication userEamil 정보
            String userEmail = SecurityUtil.getCurrentUserEmail();

            Users user = usersRepository.findByEmail(userEmail)
                    .orElseThrow(() -> new UsernameNotFoundException("No authentication information."));

            // add ROLE_ADMIN
            user.getRoles().add(Authority.ROLE_ADMIN.name());
            usersRepository.save(user);

            return response.success();
        }
    }
    ```




















