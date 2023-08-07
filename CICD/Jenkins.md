# Jenkins

### Jenkins 기본 개념
- Java Runtime Environment에서 동작
- 배포, 테스트, 도커 빌드 등의 각각의 컴포넌트들을 하나의 플러그인으로 모듈화 되어있다
- 시크릿 키 등의 다양한 플러그인을 활용해서 각종 자동화 작업을 처리할 수 있다
- 일련의 자동화 작업의 순서들의 집합인 Pipeline을 통해 CI/CD 파이프라인을 구축한다
- Plugins
  - Credentials Plugin
    - Jenkins는 그냥 단지 서버이기 때문에 배포에 필요한 각종 리소스에 접근하기 위해서는 여러 가지 중요 정보들을 저장하고 있어야 한다
    - AWS token, Git access token, secret key, ssh(username, password)등의 정보들을 저장할 때 사용한다
    - 이러한 정보를 저장하기 위해서 젠킨스는 Private Network에서 동작한다
  - Git Plugin: jenkins에서 git에 대한 소스코드를 긁어와서 빌드할 수 있도록 도와준다
  - Docker plugin and Docker Pipeline: Docker agent를 사용하고 jenkins에서 도커를 사용하기 위한 플러그인
- Pipeline
  - 파이프라인이란 CI/CD 파이프라인을 jenkins에 구현하기 위한 일련의 플러그인들의 집합이자 구성이다
  - 즉, 여러 플러그인들을 이 파이프라인에서 용도에 맞게 사용하고 정의함으로써 파이프라인을 통한 서비스가 배포된다.
  - Pipeline DSL(Domain Specific Language)로 작성되며, 두 가지 형태의 Pipeline Syntax가 존재한다
    - Declarative (더 최신이고 가독성이 좋음)
    - Scripted Pipeline
  - Pipeline의 Section 구성
    - Agent section
      - jenkins는 여러 slave node를 두고 일을 시킬 수 있는데, 어떤 node가 일을 하게 할 것인지를 지정한다
      - 젠킨스 노드 관리에서 새로 노드를 띄우거나 혹은 docker 이미지를 통해 처리할 수 있다
    - Stage section
      - 일종의 카테고리로, 어떤 일들을 처리할 것인지 일련의 stage를 정의한다
    - Post section
      - stage가 끝난 이후의 결과에 따라서 후속 조치를 취할 수 있다
      - 즉, 성공 시에 성공 이메일, 실패하면 중단 혹은 건너뛰기 등등, 작업 결과에 따른 행동을 취할 수 있다
    - Steps section
      - 한 stage 안에서 여러 가지 Steps로 구성되며 작업들을 실행할 수 있다
  - Pipeline 구성 예시
    - pipeline => agent => stage=> step 순으로 작업이 세분화된다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/f7a15527-4eef-48f6-917f-8c7c262c59ae)

### EC2에 Jenkins 설치하기
- yum 패키지 업데이트
  - ```$ sudo yum -y update```
- java 설치
  - ```$ sudo yum list java*```
  - version에 맞춰서 설치
    - ```$ sudo yum -y install java-11-amazon-corretto.x86_64```
    - ```$ sudo yum -y install java-1.8.0-openjdk-devel.x86_64```
  - version 확인
    - ```$ java -version```
- jenkins repository 추가
  - yum이 jenkins를 설치해야 하는 곳을 알려주기 위해 추가해줘야 한다
  - ```$ sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo```
- EPEL 설치
  - EPEL(Extra Packages for Enterprise Linux)은 Fedora Project에서 제공되는 저장소로 각종 패키지의 최신 버전을 제공하는 community 기반의 저장소
  - ```$ sudo amazon-linux-extras install -y epel```
- jenkins GPG Key 추가
  - Jenkins를 설치할 때 신뢰할 수 있는 소스로부터 제공되는 파일이라는 것을 증명하기 위해 로컬 GPG 키링에 Jenkins GPG Key를 추가해줘야 한다
  - ```$ sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key```
- jenkins 설치
  - ```$ sudo yum install jenkins```
- jenkins 서버 시작
  - ```$ sudo service jenkins start```
  - ```$ sudo systemctl start jenkins```
- 8080 포트 LISTEN 여부 확인
  - `````netstat -na | grep 8080```
- 프리티어 EC2 스왑 파티션 생성 (상위 EC2 사용한다면 패스)
  - 프리티어 EC2는 메모리가 부족하기 때문에 사용하다 보면 메모리 부족으로 프로그램이 멈출 수 있다
  - 그러므로 직접 스왑 파티션을 생성해줘서 사용하는 데 지장이 없도록 해준다
  - ```$ free -h```
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/e3acb012-4ec4-454d-8d6b-627929b13970)
  - ```
    // swap 파일을 생성해준다. (메모리 상태 확인 시 swap이 있었지만 디렉토리 파일은 만들어줘야한다.)
    $ sudo mkdir /var/spool/swap
    $ sudo touch /var/spool/swap/swapfile
    $ sudo dd if=/dev/zero of=/var/spool/swap/swapfile count=2048000 bs=1024

    // swap 파일을 설정한다.
    $ sudo chmod 600 /var/spool/swap/swapfile
    $ sudo mkswap /var/spool/swap/swapfile
    $ sudo swapon /var/spool/swap/swapfile

    // swap 파일을 등록한다.
    $ sudo vi /etc/fstab
      > 파일이 열리면 해당 파일 아래쪽에 하단 내용 입력 후 저장
      > /var/spool/swap/swapfile    none    swap    defaults    0 0

    // 메모리 상태 확인
    $ free -h
    ```
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/887c972a-ec6a-47b5-98d0-225adf811029)
- jenkins 접속
  - http://your-ec2-dns-주소:8080 으로 접속하면 아래와 같은 화면이 뜬다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/c3f99a59-f742-4ae6-8a96-7cfa1412f151)
- 초기 접속 비밀번호 입력
  - 명령어 실행 후 나온 문자열을 복사해서 빈 칸에 입력
  - ```$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword```
- jenkins plugin 설치
  - Install suggested plugins: 디폴트로 설정된 Plugin을 전부 설치하는 것 (보통 이걸 선택)
  - Select plugins to install: 우측은 필요한 것만 골라서 설치하는 것이다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/a424a541-aa45-4d98-bfea-b7f1da5c06f8)
- jenkins 사용자 계정 생성
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/6b9bd54b-9a8d-4a1a-abfa-735829b0893e)


### Github, jenkins, deploy server 연동
- git 설치
  - ```$ sudo yum install -y git```
- ssh 접속을 위한 key 생성
  - github은 ssh Public Key로 인증 후 사용할 수 있다
  - ```
    $ mkdir /var/lib/jenkins/.ssh
    $ cd /var/lib/jenkins/.ssh
    $ ssh-keygen -t rsa -f /var/lib/jenkins/.ssh/jenkins
    ```
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/4d864279-8a64-4856-86a7-83349aacbf41)
- github에 pub key 등록
  - Deploy keys -> Add deploy key
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/f6e4b565-03b8-4fb7-ae21-aebab4105c04)  
  - 공개키 등록
    - ```cat /var/lib/jenkins/.ssh/jenkins.pub```
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/8b0f2de4-c9c1-4132-bf7a-440671ef200f)
- jenkins에 git plugin 설치
  - jenkins 관리 > Plugins
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/9f61af9a-fa50-479a-b419-015a8f9a82bd)
  - Available plugins > git plugin
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/ba5bd27b-a169-4e22-ad87-a9f06f2cfa0d)
- jenkins 새로운 item 생성
  - Freestyle project로 생성
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/0a486da1-b183-4de4-b085-e40875092a7c)
- jenkins에 배포 서버의 private key 등록 (ec2의 pem키)
  - jenkins 관리 > Credentials
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/7f06d2f1-5805-4b95-82e2-a169e2924d1b)
  - (global) > Add credentials
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/bfbd2daf-297a-40eb-909d-3bd9a2099ff4)
  - new credentials > create
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/eeee98e6-ad9f-4013-b297-0919d1a1a51f)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/1248ea47-7cf1-44c1-9ef2-409d3dad3dd7)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/0c16aace-3b0e-4a90-b8ce-23cb4896cd52)
- project 배포 설정
  - project > 구성
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/15d0639e-93e3-46e4-a697-d50ad60e4aca)
  - git repository 연동 (credential key 사용)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/8bd7280f-1029-44e0-a6bf-9decfd140ec3)
  - Build Steps
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/0f7ad65e-93df-4c0d-a241-7b8d25e01e8b)
  - 빌드 후 조치
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/feb0756c-cf89-4cc1-af56-4bba2ccb4960)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/852ee8a9-6ff0-4d75-99fd-d46f023d20e2)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/6dcf2c7d-0c80-4220-96fa-24edd88e3d11)
      - 해당 script는 jenkins user로 실행하기 때문에 권한문제가 발생가능성이 있다
      - Name : 좀전에 설정한 본인이 지정한 SSH Server 이름을 선택한다
      - Verbose output in console 을 체크하면 빌드할 때 상세 내역이 표시 된다
      - Source files :  전송할 파일을 지정한다. 혹시 전체 파일 이동을 하려면 **/* 과 같이 입력 하면 된다
      - Remove prefix : Source files에서 지정한 경로의 하위 폴더를 지우는 기능으로 위의 예시같이 입력한다면 폴더를 제외하고 jar 파일만 전송하게 된다
      - Remote directory : SSH Server로 지정한 서버의 원격지 폴더이다
      - Exec command : 파일 전송이 모두 끝난 이후에, SSH Server로 지정한 서버에서 실행될 스크립트를 지정할 수 있는 기능










