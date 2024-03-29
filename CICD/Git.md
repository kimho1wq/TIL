# Git

### Git이란
- 분산형 버전 관리 시스템(Version Control System, VCS)으로 여러 명의 사용자들 간의 작업을 추적하고 조율하는데 사용된다
- 여러명의 개발자가 하나의 소프트웨어 개발 프로젝트에 참여할 때, 여러 명이 동시에 작업하는 병렬 개발이 가능하다
- git은 소스코드를 여러 개발 PC와 저장소에 분산해서 저장하기 때문에 로컬 저장소에 커밋을 할 수 있으며, 로컬 저장소들을 이용하여 중앙 저장소의 복원도 가능하다

### Git flow
- Git으로 개발할 때 사용되는 방법론으로 총 5가지의 브랜치를 사용하여 운영하는 방식이다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/9d93fe43-6938-4645-8804-4b9bd88b9c31)
    - master: 기준이 되는 브랜치로 제품으로 출시될 수 있는 브랜치
    - develop: 다음 출시 버전을 개발하는 브랜치로, 개발자들이 이 브랜치를 기준으로 각자 작업한 기능들을 병합(merge)한다
    - feature: 단위 기능을 개발하는 브랜치로, 기능 개발이 완료되면 develop 브랜치와 병합한다
    - release: 이번 출시 버전을 준비하는 브랜치로, 배포를 위해 master 브랜치로 보내기 전에 먼저 QA(품질검사)를 진행한다
    - hotfix: 출시 버전에서 발생한 버그를 수정 하는 브랜치로, master 브랜치로 배포를 했는데 버그가 생겼을 떄 긴급 수정한다

### Git 관련 용어
- Repository: 저장소를 의미하며, 저장소는 히스토리, 태그, 소스의 가지치기 혹은 branch에 따라 버전을 저장한다
- Working Tree: 저장소를 어느 한 시점을 바라보는 작업자의 현재 시점
- Staging Area: 저장소에 커밋하기 전에 커밋을 준비하는 위치
- Commit: 현재 변경된 작업 상태를 점검을 마치면 확정하고 저장소에 저장하는 작업
- Head: 현재 작업중인 Branch를 가리킨다
- Branch: 가지 또는 분기점을 의미하며, 작업을 할때에 현재 상태를 복사하여 Branch에서 작업을 한 후에 완전하다 싶을때 Merge를 하여 작업을 한다
- Merge: 다른 Branch의 내용을 현재 Branch로 가져와 합치는 작업을 의미한다


### Git 명령어
- git init
  - 새로운 저장소 초기화하기
    - ```git init```
- git config
  - 전역 사용자명/이메일 구성하기
    - ```git config --global user.name "Your name"```
    - ```git config --global user.email "Your email address"```
  - 저장소별 사용자명/이메일 구성하기 (해당 저장소 디렉터리로 이동후)
    - ```git config user.name "Your name```
    - ```git config user.email "Your email address"```
      - user 설정이 되어 있지 않으면 Github에 있는 repository commit count 집계도 안되고 해당 커밋의 작성자 프로필 아이콘도 ? 로 표시된다
  - 전역 설정 정보 조회
    - ```git config --global --list```
  - 저장소별 설정 정보 조회
    - ```git config --list```
  - Git의 출력결과 색상 활성화하기
    - ```git config --global color.ui "auto"```
  - Git ssh token으로 자동 변환 시키기
    - ```git config --global url."${PRIVATE-ACCESS-TOKEN}:x-oauth-basic@github.com/kimho1wq".insteadOf "https://github.com/kimho1wq"```
  - config 명령어로 설정된 정보 삭제하기
    - ```git config --unset user.name```
  - config 설정 파일 확인
    - ```vim ~/.gitconfig```
  - id, password를 disk에 저장시키기
    - ```git config --global credential.helper store```
    - 저장된 로그인 정보는 ~/.git-credentials 경로에 저장된다
- git add
  - 새로운 파일을 추가하거나 존재하는 파일 스테이징하기
    - ```git add <파일>```
  - 파일의 일부를 스테이징하기
    - ```git add -p [<파일> [<파일> [기타 파일들…]]]```
  - add 명령에서 Git 대화 모드를 사용하여 파일 추가하기
    - ```git add -i``` 
  - 수정되고 추적되는 파일의 변경 사항 스테이징하기
    - ```git add -u [<경로> [<경로>]]```
- git commit
  - 스테이징된 파일 커밋하기
    - ```git commit -m "<메시지>"```
  - 수정되고 추적되는 모든 파일의 변경 사항 커밋하기
    - ```git commit -m "<메시지>" -a```
  - 마지막 커밋 고치기
    - ```git commit -m "<메시지>" --amend```
  - 이전 커밋을 수정하고 커밋 메시지를 재사용하기
    - ```git commit -C HEAD --amend```
- git reset
  - 커밋되지 않고 스테이징된 변경 사항 재설정하기
    - ```git reset HEAD <파일> [<파일>]```
  - 원격 git 저장소의 master을 내 local에 덮어 씌울경우
    - ```
      git fetch --all
      git reset --hard origin/master
      git pull origin master
      ```
- git branch
  - 지역 브랜치 목록 보기
    - ```git branch```
  - 원격 브랜치 목록 보기
    - ```git branch -r```
  - 지역과 원격을 포함한 모든 브랜치 목록 보기
    - ```git branch -a```
  - 현재 브랜치에서 새로운 브랜치 생성하기
    - ```git branch <새로운 브랜치>```
  - 다른 시작 지점에서 브랜치 생성하기
    - ```git branch <새로운 브랜치> <브랜치를 생성할 위치>```
  - 기존의 브랜치를 새로운 브랜치로 덮어쓰기
    - ```git branch -f <기존 브랜치> [<브랜치를 생성할 위치>]```
  - 삭제할 브랜치가 현재 브랜치에 합쳐졌을 경우에만 삭제하기
    - ```git branch -d <삭제할 브랜치>```
  - 삭제할 브랜치가 현재 브랜치에 합쳐지지 않았어도 삭제하기
    - ```git branch -D <삭제할 브랜치>```
- git checkout
  - 다른 브랜치 체크아웃하기
    - ```git checkout <브랜치>```
  - 현재 브랜치에서 새로운 브랜치 생성하고 체크아웃하기
    - ```git checkout -b <새로운 브랜치>```
  - 작업 트리의 변경 사항 돌려놓기
    - ```git checkout HEAD <파일> [<파일>]```
  - 브랜치를 옮기거나 브랜치명 변경하기
    - ```git checkout -m <기존 브랜치> <새로운 브랜치>```
  - <새로운 브랜치>가 존재하지 않을 경우 무조건 덮어쓰기
    - ```git checkout -M <기존 브랜치> <새로운 브랜치>```
- git merge
  - 다른 브랜치를 현재 브랜치로 합치기
    - ```git merge <브랜치>```
  - 커밋하지 않고 합치기
    - ```git merge --no-commit <브랜치>```
  - 브랜치의 이력을 다른 브랜치에 합치기
    - ```git merge --squash <브랜치>```
- git cherry-pick
  - 선택하여 합치기
    - ```git cherry-pick <커밋명>```
  - 커밋하지 않고 선택하여 합치기
    - ```git cherry-pick -n <커밋명>```
- git log
  - 모든 이력 보기
    - ```git log```
  - 변경 사항을 보여주는 패치와 함께 로그 표시하기
    - ```git log -p```
  - 1개의 항목만 보이도록 로그 개수 제한하기
    - ```git log -1```
  - 20개의 항목과 패치만 보이도록 로그 제한하기
    - ```git log -20 -p```
  - 6개월 동안의 커밋 로그 보기
    - ```git log --since="6 hours"```
  - 이틀 전까지의 커밋 로그 보기
    - ```git log --before="2 days"```
  - HEAD보다 세 개 이전의 커밋 로그 보기
    - ```git log -1 HEAD-3```
    - ```git log -1 HEAD^^^```
    - ```git log -1 HEAD~1^^```
  - 두 지점 사이의 커밋 로그 보기
    - ```git log <시작 지점>…<끝 지점>```
  - 각 항목의 로그 이력 한 줄씩 보기
    - ```git log --pretty=oneline```
  - 각 항목마다 영향 받은 줄의 통계 보기
    - ```git log --stat```
  - 커밋할 시점의 파일 상태 보기
    - ```git log --name-status```
  - 로그에서 복사와 붙여 넣은 정보 보기
    - ```git log -C -C -p -1 <특정 지점>```
- git diff
  - 현재 작업 트리와 인덱스의 차이점 보기
    - ```git diff```
  - 인덱스와 저장소의 차이점 보기
    - ```git diff --cached```
  - 작업 트리와 저장소의 차이점 보기
    - ```git diff HEAD```
  - 작업 트리와 특정 위치 간의 차이점 보기
    - ```git diff <시작 지점>```
  - 저장소의 두 지점 사이의 차이점 보기
    - ```git diff <시작 지점> <끝 지점>```
  - 차이점의 통계 보기
    - ```git diff --stat <시작 지점> [<끝 지점>]```
- git blame
  - 파일의 커밋 정보 줄 단위로 보기
    - ```git blame <파일>```
  - 파일의 줄 단위의 복사, 붙여 넣기, 이동 정보 보기
    - ```git blame -M <파일>```
  - 파일의 줄 단위의 이동과 원본 파일 정보 보기
    - ```git blame -C -C <파일>```
- git clone
  - 저장소 복제하기
    - ```git clone <저장소>```
  - 마지막 200개의 커밋만 포함하여 저장소 복제하기
    - ```git clone --depth 200 <저장소>```
- git remote
  - 새로운 원격 저장소 추가하기
    - ```git remote add <원격 저장소> <저장소 url>```
  - 원격 저장소에서 쓸모가 없어진 원격 브랜치 제거하기
    - ```git remote prune <원격 저장소>```
  - 원격 저장소를 제거하고 관련된 브랜치도 제거하기
    - ```git remote rm <원격 저장소>```
- git fetch
  - origin 저장소에서 합치지 않고 지역 브랜치로 변경 사항 가져오기
    - ```git fetch```
  - 원격 저장소에서 합치지 않고 지역 브랜치로 변경 사항 가져오기
    - ```git fetch <원격 저장소>```
- git pull
  - 원격 저장소에서 변경 사항을 가져와 현재 브랜치에 합치기
    - ```git pull <원격 저장소>```
  - origin 저장소에서 변경 사항을 가져와 현재 브랜치에 합치기
    - ```git pull```
- git push
  - 지역 브랜치를 원격 브랜치에 푸싱하기
    - ```git push <원격 저장소> <지역 브랜치>:<원격 브랜치>```
  - 지역 브랜치를 동일한 이름의 원격 브랜치에 푸싱하기
    - ```git push <원격 저장소> <지역 브랜치>```
  - 새로운 로컬 브랜치를 원격 저장소에 푸싱하기
    - ```git push <원격 저장소> <지역 브랜치>```
- git rm
  - stage에 올라간 파일의 캐시를 지운다 (.gitignore가 작동 안할 때 주로 사용)
    - ```git rm -r --cached .```





