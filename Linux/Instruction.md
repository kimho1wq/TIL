# Instruction


### 용량 확인
- du
  - ```du [option] [폴더]```
  - option
    - -a: 하위 폴더의 모든 파일 정보를 출력
    - -c: 모든 파일의 사용정보와 합계 출력
    - -h: 표시 단위를 KB, MB, G 등
    - -s: 총 사용량만 출력
    - -X [파일명]: 지정한 파일명은 제외


### 백그라운드 실행
- screen
  - 설치
    - ```sudo apt-get install screen```
  - screen 만들기
    - ```screen -S [name]```
  - screen 목록 보기
    - ```screen -list```
    - ```
      There are screen on:
          3827.sb     (...) (Attached)
          3243.editor (...) (Attached)
      ```
  - screen 나오기
    - ```Ctrl + a, d```
  - screen 재 접속하기
    - ```
      screen -r [name]
      screen -x [name]
      ```
  - window 만들기
    - ```Ctrl + a, c```
  - window 목록 보기
    - ```Ctrl + a, w```
  - window 이동하기
    - ```
      Ctrl + a, a: 바로 이전 창으로 이동
      Ctrl + a, 0: 0번째 창으로 이동
      Ctrl + a, 1: 1번째 창으로 이동
      ```
  - window 종료하기
    - ```exit```


### 공유 폴더
- samba
  - 설치
    - ```sudo apt-get install samba```
  - 계정 추가, 패스워드 설정
    - ```sudo smbpasswd -d [id]```
  - 공유 폴더 생성
    - ```mkdir [share dir]```
  - 설정
    - ```sudo vi /etc/samba/smb.conf```
    - ```
      [mydir]                            # 네트워크에 보여질 공유 이름
        comment = my own directory  # 디렉토리 설명
        path = /home/user1/share_dir         # 공유 디렉토리 절대 경로
        valid users = kimho1wq              # 접속을 허용할 계정
        writeable = yes
        create mask = 0664
        directory mask = 0775
      ```
  - 포트 개방
    - ```
      sudo ufw allow 139      # 139 포트 개방
      sudo ufw allow 445      # 445 포트 개방
      ```
  - daemon 실행
    - ```
      sudo /etc/init.d/smbd restart     # samba server 재시작
      sudo service smbd start           # samba server 시작
      sudo service smbd stop            # samba server 멈춤
      sudo service smbd restart         # samba server 재시작(단, 권한 입력 필요)
      ```
  - 윈도우 탐색기 연결
    - ```제어판 -> 사용자 계정 -> 자격증명관리 -> windows 자격 증명```
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/910c0500-7be3-4965-9b51-976186ae0304)

### 마운트
- 연구실 서버 마운트
  - ```$ sudo mount -t cifs //172.16.163.135/SDB ./nas -o user=,pass=,rw,vers=1.0```












