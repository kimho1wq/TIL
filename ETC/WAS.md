# WAS(Web Application Server)

### WAS 란 
- WAS는 클라이언트 요청에 대해 동적인 처리를 담당하는 영역으로, 웹 서버와 달리 애플리케이션 로직을 실행할 수 있도록 구성되어 있다
- WAS는 비지니스 로직의 처리를 위한 DB 연동, 보안, 로깅 등의 기능을 제공하며 Tomcat, JBoss, WebLogic 등이 있다 
  - ![제목 없음](https://github.com/kimho1wq/TIL/assets/15611500/fbefc8cf-ade3-4307-a2d9-522aeeafc422)
- JSP, Servlet 구동환경을 제공해주기 때문에 서블릿 컨테이너 혹은 웹 컨테이너로 불린다
  - 컨테이너(container): 동적인 데이터들을 가공하여 정적인 파일로 만들어주는 모듈이다
  - 서블릿(servlet): 클라이언트의 요청을 받고 요청을 처리하여 결과를 클라이언트에게 제공하는 자바 인터페이스로, HttpServlet을 상속받으며 동적인 처리를 하는 역할을 담당한다
  - 서블릿 컨테이너(servlet container): 서블릿들을 모아 관리하며, 새로운 요청이 들어올 때마다 새로운 스레드를 생성하고 제거하는 역할을 수행한다


### WAS와 Web Server의 분리 이유
- WAS는 Web Server와 Web Container의 역할을 모두 할 수 있지만, 효율적인 운영을 위해 Web Server와 WAS를 분리해야 한다
  - 분리하기 전(WAS)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/878de7c3-bd34-415d-96c2-fca55ed55fea)
  - 분리한 후(Web Server + WAS)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/6949b22a-6d69-4c34-8240-97d06b60f0fc)
- 1.서버 부하 방지
  - WAS와 웹 서버는 분리하여 서버의 부하를 방지해야 한다
  - WAS는 DB 조회나 다양한 로직을 처리하고, 단순한 정적 컨텐츠는 웹 서버에서 처리해줘야 한다
  - 만약 정적 컨텐츠까지 WAS가 처리한다면 부하가 커지게 되고, 수행 속도가 느려질 것이다
- 2.보안 강화
  - SSL에 대한 암호화, 복호화 처리에 웹 서버를 사용하여 서비스 로직에 보안을 강화해야 한다
- 3.여러 대의 WAS 연결 가능
  - 로드 밸런싱을 위해 웹 서버를 사용할 수 있다
  - 여러 개의 서버를 사용하는 대용량 웹 어플리케이션의 경우, 웹 서버와 WAS를 분리하여 무중단 운영을 위한 장애 극복에 쉽게 대응할 수 있다
- 4.여러 웹 어플리케이션 서비스 가능
  - 하나의 서버에서 PHP, JAVA 애플리케이션을 함께 사용할 수 있다 


### 톰캣(Tomcat) 이란
- Tomcat이란 아파치 재단에서 만든 오픈 소스 WAS(Web Application Server) 이다
- Tomcat은 Java Servlet과 JSP가 실행할 수 있는 환경을 제공하여 동적인 페이지를 생성하고, DB 연결 및 데이터 조작과 다른 응용프로그램들과 상호 작용이 가능하다
- 그리고 웹 서버(Web Server)와 달리 요청에 대해 동적인 페이지를 만들어 유연하게 응답할 수 있다
- Tomcat의 디렉토리 구조
  - bin: 톰캣 서버의 동작을 제어할 수 있는 스크립트 및 실행 파일
  - conf: 톰캣의 기본적인 설정 파일
  - lib: 아파치와 같은 다른 웹 서버와 톰캣을 연결해주는 바이너리 모듈들
  - webapps: 톰캣이 제공하는 웹 애플리케이션의 기본 위치
  - logs: 서버의 로그 파일이 저장
  - work: jsp 컨테이너와 다른 파일들이 생성하는 임시 디렉토리
  - temp: 임시 저장 폴더



















