# GIT

## GIT의 3대 목적
1. 버전관리
2. 백업
3. 협업

## GIT CLI의 장점
- GUI 프로그램이 없어도 GIT을 다룰 수 있다.
- 자동화가 가능하다.
- 서버 환경에서도 사용 가능하다.

## 설정

`git config --global core.editor "nano"` - 기본 에디터 변경

다양한 diff tool이 존재하므로 필요하면 찾아본다.

.gitignore 파일로 GIT에서 무시할 파일이나 경로를 지정할 수 있다.

`warning: LF will be replaced by CRLF in bora.txt. The file will have its original line endings in your working directory`가 뜨면 `git config --global core.autocrlf true`을 적용한다.

## 버전관리
`git init .`

현재 디렉토리의 버전관리를 시작한다.

`.git` 디렉토리가 생성되는데 이곳이 저장소(repository)로 버전 정보들이 저장된다.

- Working Tree - 작업 공간
- Staging Area - 인덱스. 버전 관리하고 싶은 파일을 올리는 곳.
- Repository - 저장소

명령에 따라 Working Tree → Staging Area → Repository로 변경 사항이 이동한다.

버전관리를 하지 않을 파일은 untracked 상태로 내버려두면 된다.

`git status` - 작업 공간의 상태를 표시한다.

`git add` - 해당 파일이나 경로를 Staging Area에 올린다. `.`만 넣으면 현재 경로.

`git commit` - 버전을 생성한다. `-m "Message 1"`로 메시지 입력 가능. `-a`로 add까지 수행. `--amend`로 메시지 수정.

`git log` - 버전 기록을 표시한다.

|옵션|설명|
|---|---|
|`--stat`|통계 정보|
|`-p`|패치|
|`--all`|모든 브랜치|
|`--graph`|브랜치 그래프|
|`--oneline`|버전 간략히|

`git diff` - 변경 사항을 표시한다.

`git checkout` - `HEAD`를 옮겨서 이전 버전이나 브랜치를 돌아다닐 수 있다. 뒤에 `main`을 붙이면 원래대로 돌아간다.

`git reset --hard` - 해당 버전으로 버전을 되돌린다. 이전 버전들은 모두 삭제된다. 인덱스와 작업 공간도 모두 리셋된다.

`git revert` - 해당 버전의 변경 사항을 되돌리게 커밋한다. 이전 버전들은 모두 유지된다.

## 백업

- Local Repository - 로컬 저장소
- Remote Repository - 원격 저장소
- Git hosting - 원격 저장소를 빌려주는 곳이다. 대표적인 곳으로 GitHub와 GitLab이 있다.

` git remote add <저장소 별명, 보통 origin 사용> <원격 저장소 주소>`

로컬 저장소와 원격 저장소를 연결한다.

`git remote -v` - 원격 저장소의 주소를 확인한다.

`git clone <원격 저장소 주소>` - 저장소를 그대로 복사해온다.

`git push` - 로컬 저장소에서 원격 저장소로 업로드한다.

처음 push할 때는 연결 설정이 필요하다. 시키는 대로 한다.

`git pull` - 원격 저장소에서 로컬 저장소로 다운로드한다.

### 일반적인 Git 순서

pull → 작업 → add → commit → push

## 브랜치와 충돌

