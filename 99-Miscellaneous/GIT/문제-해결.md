# 트러블슈팅

- `warning: LF will be replaced by CRLF in bora.txt. The file will have its original line endings in your working directory`가 뜨면 `git config --global core.autocrlf true`을 적용한다.

- 한글이 숫자로 깨져서 나오면 `git config --global core.quotepath false`을 적용한다.

- .gitignore이 제대로 적용되지 않으면 `git rm -r --cached .`로 캐시를 삭제하고 다시 커밋한다.