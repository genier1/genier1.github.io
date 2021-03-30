## 형상관리

- 파일 변화를 시간에 따라 기록했다가 나중에 특정 시점의 버전을 다시 꺼내올 수 있는 시스템
- 형상관리 시스템을 사용하면, 파일이나 프로젝트를 이전 상태로 되돌리고 진행 상황을 추적할 수 있다.
- 형상관리 시스템은 CVS - SVN - Git 순으로 진화해왔다.

## Git

![img-1](https://user-images.githubusercontent.com/19471818/112991244-dd178380-91a1-11eb-85ea-2a960d7227f8.png)

- 리눅스 토발즈가 리눅스 커널 개발에 이용하기 위해 개발한 시스템이다.
- Git은 데이터를 파일 시스템 스냅샷의 연속으로 취급하고 크기가 아주 작아서 매우 빠르다는 장점이 있다.
- SVN과 달리 여러 저장소에 분산되어 저장이 가능하다.(분산저장 방식)
- Git은 파일을 Committed, Modified, Staged 이렇게 세 가지 상태로 관리한다.
  - Committed란 데이터가 로컬 데이터베이스에 안전하게 저장됐다는 것을 의미한다.
  - Modified는 수정한 파일을 아직 로컬 데이터베이스에 커밋하지 않은 것을 말한다.
  - Staged란 현재 수정한 파일을 곧 커밋할 것이라고 표시한 상태를 의미한다.

## 대표적인 툴

### Github

- Git 호스팅 서비스의 선두주자
- 오픈소스 프로젝트들이 많이 있다.
- Private 사용을 위해서는 인당 월 $7이 과금된다.
  - 3명 이하의 인원의 경우 무료로 Private Repository를 생성할 수 있다.

### Gitlab

- Private Repo에 대해 인원 제한 없이 무료라는 장점이 있다.
- Github보다 느리다는 단점이 있으며, 외부 Repository를 상대적으로 가져오기 어렵다.

## 주요 명령어

### Git 초기 설정

```bash
# git 글로벌 설정(사용자명, 이메일) 
git config --global user.name “Your name”
git config --global user.email “Your email address”

# Git 저장소 초기화 
git init 

# 현재 설정 확인
git config --list

# Alias 설정
git config --global alias.ci commit # 이후 git ci라는 명령어로 commit할 수 있다.
```

### Add

작업 디렉토리 상의 내용을 스테이징 영역에 추가한다.

```bash
git add “파일/디렉토리 경로”
git add . # 현재 디렉토리의 모든 변경 내역을 스테이징에 추가 
git add -A # 작업 디렉토리의 모든 변경 내역을 스테이징에 추가 
```

### Commit

데이터를 로컬 저장소에 저장한다. 커밋메시지는 함께 남겨주는 것이 좋다.

```bash
git commit -m “커밋 메시지”
```

### Push

로컬 저장소에 있는 내용을 원격 저장소로 보낸다.

```bash
git push origin master
```

### Pull

원격 저장소에 있는 내용을 로컬 저장소로 가져와서 반영한다. pull은 fetch + merge와 같다.

```bash
git pull
```

### Fetch

원격 저장소의 내용을 확인만 하고 로컬 데이터와 병합은 하고 싶지 않을 경우 사용한다. 원격 저장소의 최신 이력을 확인할 수 있다.

```bash
git fetch origin 
```

### 출처

- [형상 관리 Git](https://brunch.co.kr/@yoonms/16)
- [[Git\] Git 명령어 정리](https://medium.com/@joongwon/git-git-명령어-정리-c25b421ecdbd)
- https://opentutorials.org/course/3837
- [Pro Git 한글판](https://git-scm.com/book/ko/v2)