# POSIX CLI

## 소개

- POSIX(Portable Operating System Interface) - 유닉스 계열의 컴퓨터를 동일한 방법으로 제어할 수 있도록 만들어진 표준이다. 리눅스와 macOS가 이 표준을 따른다.
- CLI(Command Line Interface) vs GUI(Graphic User Interface)
- 유닉스 계열이 아닌 컴퓨터에서는 에뮬레이터가 필요하다. 윈도우에서는 Git Bash를 사용한다.

## 명령어 

||File|Directory|
|---|---|---|
|Create|editor|mkdir|
|Read|editor, cat, ls|ls|
|Update|editor, mv|mv|
|Delete|rm|rm|

## 디렉토리

- `pwd` - 현재 디렉토리
- `cd` - 디렉토리 변경하기
- `/` - 루트 디렉토리
- home directory - 기본 디렉토리로 `cd ~`을 입력하면 이동할 수 있다.
- `ls` - 현재 디렉토리의 파일 목록을 표시한다.
- `ls --help` 또는 `man ls` - 명령어의 메뉴얼을 표시한다.
- `ls -l` - 자세한 파일 목록을 표시한다.
-  `touch filename.txt` - 빈 파일을 생성한다.
- `touch .filename.txt` - 앞에 `.`을 붙이면 숨김 파일을 생성한다. `ls`에 표시되지 않는다.

## 디렉토리 CRUD
- `mkdir` - 디렉토리 만들기
- `./dirname` - 현재 디렉토리에서 이동하기
- `mv dirname1 dirname2` - 디렉토리 이름 수정하기
- `rm -r` - 디렉토리 삭제하기

## 절대경로 vs 상대경로
- 절대경로 - 현재 위치에 관계없이 결정된다.
- 상대경로 - 현재 위치에 따라서 결정된다.
- `../` 또는 `..` - 부모 디렉토리
- `clear` - 명령어 초기화

## 파일 CRUD
- `nano` - 텍스트 편집기 실행하기
- nano 단축키 정리

|단축키|동작|
|---|---|
|ctrl+w|text 검색|
|ctrl+\\ |text 검색하고 교체하기|
|ctrl+6|현재 cursor 위치부터 text 선택 시작. 이후 alt+6로 복사 후 선택 종료. 아니면 다시 ctrl+6를 입력하면 복사 없이 단순 종료.|
|alt+6|선택 구간 복사. 선택 구간이 없다면 현재 caret 이 있는 한 줄을 복사.|
|ctrl+u|붙여넣기|
|ctrl+/|줄 번호와 열을 입력한 후 그곳으로 이동|
|alt+shift+3|줄 번호 표시|

- `mv filename1 filename2` - 파일 이름 수정하기
- `mv filename1 ./dirname` - 파일 옮기기
- `rm` 파일 삭제하기

## GUI vs CLI
- GUI는 학습과 사용이 쉽다.
- CLI는 풍부하고 정확한 표현이 가능하다. CLI는 새로운 언어를 배우는 것과 비슷하다. 배우기는 어렵지만 한 번 배우고 나면 원활한 소통이 가능해진다.

## 자동화하기
`;` - 명령어 구분자

`mkdir dummy;cd dummy;touch hello.txt;cd ..;`

`&&` - AND 연산자로 실패하면 이후의 명령어는 무시된다.

`mkdir dummy&&cd dummy&&touch hello.txt;cd ..;`
