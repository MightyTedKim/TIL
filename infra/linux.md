# Linux_101

## 요약

동기

- 입사 후 linux 를 사용할 일이 생각보다 많았음
  - 프로젝트 : 로그 조회, 배포
  - 회사 : 테스트 환경 세팅,  docker 설치
- 애매하게 알고 있어서 구글링하는게 부끄러웠음
- 가장 기본적인 명령어를 공부하기 좋은 2시간짜리 무료 강의가 인프런에 있어서 공부함

기타

- 강의를 수강하기 전에 알던 명령어
  - tail, head, ls, grep, vi, nano, cat, whomai, mkdir, rm, alias, ln, jar, unzip, chmod
- 강의 시작 후 알게 된 명령어
  - wc, nl, sort, uniq, cut, tr, sed, awk. find, apropos, locate, which
- 실무에서 많이 사용했던 명령어
  - chmod, tail, grep, unzip, whoami

## 강의 시작

### 텍스트처리

- head
- tail
  - -f : follow, 끝나지 않고 기다림(로그 볼 때 유용)
  - -F : truncate 되는 경우 repoen 해서 follow함, logrotate
- wc
  - -l : 줄만 보고 싶을 때
  - 줄 | 단어 | 바이트
  - 예시
    - wc *.md
    - wc -l /etc/passwd | cut -d ' ' -f 1
      - (동일) wc -l /etc/passwd | awk '{ print $1 }'
- nl
  - 출력파일 rownum 매기기
  - nl /etc/passwd
    - (동일) cat /etc/passwd | nl
  - -ba : 공백도 포함

- sort
  - 정렬
  - 옵션
    - k, key=KEYDEF : key 에 의한 정렬 수행
    - t, field seperator : 필드 구분자 기본값은 공백 문자
  - 예시
    - sort /etc/passwd t: k 4,4 k 3,3
- uniq
  - 중복된 내용 제거하고 출력
  - 옵션
    - d, repeated : 중복된 내용만 출력
    - u, unique : 중복되지 않은 내용만 출력
    - i , ignore case
  - 예시
    - sort uniq_sample | uniq u | nl
- cut
  - 컬럼 잘라내기
  - 옵션
    - f, field: 필드(컬럼) 선택
    - d, delimeter : tab데신 사용할 구분자 지정
    - output-delimiter 
  - 예시
    - head /etc/passwd | cut -d ":" -f 1,2,7
    - wc /etc/passwd | cut -d ' ' -f 3
    - head /etc/passwd | cut -d: -f 1,7 --output-delimiter=">"
- tr
  - 변환, tr [OPTION]... SET1 [SET2]
  - 옵션
    - -d. --delete
    - set - CHAR1-CHAR2, [:blank:], [:space:], [:lower:] ...
  - 예시
    - head /etc/passwd | tr -d '/'
    - head /etc/passwd | tr ':' '\t'
    - head /etc/passwd | tr [:lower:] [:upper:]
- sed
  - stream editor
  - 옵션
    - {RANGE}P
    - {RANGE}D
    - /SEARCHPATTERN/P
  - 예시
    - head /etc/passwd | sed -n '2,5p'
    - head /etc/passwd | sed '1,5d'
    - head /etc/passwd | sed -n '/sy/p'
    - head /etc/passwd | sed 's/:/\t/g'
    - head /etc/passwd | sed -n '/games/,+2p'
- awk
  - 텍스트 처리 script language, awk options 'selection _crtieria {action }' input-file
  - 옵션
    - -F : file seperator
    - (변수) $1, $2, NR, NF, FS, RS, OFS, ORS
  - 예시
    - wc /etc/passwd | awk '{ print $4}'
    - head /etc/passwd | awk -F: '/sy/ {print $6}'
    - head /etc/passwd | awk -F: '/sy/ {print NR, $1}'

### 검색

- find
  - 조건에 맞는 파일을 찾아 명령을 수행, find [OPTIONS] path EXPR
  - 옵션
    - (조건) -name, -regex, -empty,-size, -type(d,f,l), -perm(권한)
    - (액션) -delete, -ls, -print, -printf, -exec, -esecdir, -ok, -okdir
  - 예시
    - find . -perm 0644 | wc -l
    - find . -name "*.py" -print -exec stat {} \;
    - find . -name "conf.py" -print -ok rm -f {} \;
- grep
  - 파일 내용 중 원하는 내용 찾기, grep [OPTIONS] PATTERN [FILE...]
  - 옵션
    - -r, recursive
    - -v, invert match
    - -q
  - 예시
    - grep "\<for\>" *.py
- apropos
  - man page 이름과 설명을 검색
  - 옵션
    - -s, 섹션
  - 예시
    - apropos --exact who
    - apropos print -s 1
- locate
  - 파일 위치,  updatedb가 저장해놓은 db파일내에서 검색해서 누락 있을 수도, locate [OPTION]... PATTERN
  - 옵션
    - -l, limit
  - 예시
    - locate app.py -n 5
    - locate --regex "\flask/app.py$" 
- which
  - 실행파일의 위치, (환경변수) path~의 실행파일
  - 옵션
    - 없음
  - 예시
    - which ls
    - which ls strace chmod