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
  - tail, head, ls, grep, vi, nano, cat, whomai, mkdir, rm, alias, ln
- 강의 시작 후 알게 된 명령어
  - 

## 강의 시작

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
  - 
- awk
  - 