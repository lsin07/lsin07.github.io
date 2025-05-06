---
title: GDB 간단 사용법 (GDB Cheat Sheet)
description: GDB 사용법 한눈에 보기
date: 2025-05-01 20:00:00 +0900
categories: [SW Tools]
tags: [gdb]     # TAG names should always be lowercase
---

> PDF 버전도 준비되어 있습니다. [여기](/assets/files/2025-05-01-gdb-cheat-sheet/gdb_cheat_sheet.pdf){:target='_blank'}를
클릭해서 확인해 보세요.
{: .prompt-tip}

| 분류       | 명령어                                           | 설명                                                    | 예시                                               |
|-----------|--------------------------------------------------|---------------------------------------------------------|----------------------------------------------------|
| **시작**    | `gcc -g -o <output> <src>`                | 디버깅 심볼 포함 컴파일                                   | `gcc -g -o main main.c`                           |
|           | `gdb [program]`                                 | GDB 실행                                               | `gdb main`                                        |
| **실행**    | `run` (`r`)                                | 프로그램 실행 (인수 전달 가능)                           | `run arg1 arg2`                                   |
| **중단점**  | `break` (`b`) `<location>`                     | 중단점 설정<br>(함수명, 파일:라인, 주소 등)                 | `break main`<br>`b file.c:42`                     |
|           | `info break` (`i b`)                     | 중단점 정보 확인                                               |                                                  |
|           | `delete` (`d`) `[num]`                                  | 중단점 삭제<br>(num 생략 시 전체 삭제)                     | `delete 1`                                        |
|           | `disable` (`dis`) `[num]`                                 | 중단점 비활성<br>(num 생략 시 전체 비활성)   | `disable 2`                                       |
|           | `enable` (`en`) `[num]`                                 | 중단점 활성<br>(num 생략 시 전체 활성)              | `disable 2`                                       |
| **제어**    | `next` (`n`)                               | 다음 소스라인 실행<br>(함수 안으로 들어가지 않음)           | `next`                                            |
|           | `step` (`s`)                               | 다음 소스라인 실행<br>(함수 내부로 진입)                   | `step`                                            |
|           | `continue` (`c`)                           | 중단점 또는 프로그램 종료<br>지점까지 계속 실행             | `continue`                                        |
|           | `finish` (`fin`)                                        | 현재 함수 끝까지 실행 후 복귀                            | `finish`                                          |
|           | `until [location]`                                  | 지정 위치(라인)까지 계속 실행                           | `until 100`                                       |
|           | `return [value]`                                  |    현재 함수 실행을 중단하고 빠져나감                 | `return -1`                                       |
| **조사**    | `print` (`p`) `<expr>`                       | 변수나 표현식 값 출력                                   | `print i`<br>`p arr[0]`                           |
|           | `display <expr>`                                   | 매 스텝마다 지정 식의 값을<br>자동 출력                     | `display x`                                       |
|           | `info locals`                                   | 현재 스코프 지역 변수 목록 및 값                         | `info locals`                                     |
|           | `info args`                                     | 현재 함수 인수 정보                                     | `info args`                                       |
|           | `list [location]`                                     | 소스 코드 출력<br>(location 생략 시 현위치 코드 출력)      | `list 100`<br>`list function`              |
| **스택**    | `backtrace` (`bt`)                         | 콜 스택(함수 호출 흐름) 출력                            |                                        |
|           | `frame [num]`                                   | 특정 스택 프레임<br>(호출 레벨)으로 이동                     | `frame 2`                                         |
| **메모리**  | `x/<count><format> <addr>`                        | 메모리 내용 검사<br>(b:byte, h:halfword, w:word, x:hex)   | `x/4xb &buffer`                                   |
|           | `set {<type>}<addr> = <value>`                    | 메모리 또는 변수 값 직접 변경                            | `set {int} &i = 5`                                |
| **기타**    | `help [command]`                                   | GDB 내장 도움말 보기                                    | `help break`                                      |
|           | `quit` (`q`)                                | GDB 종료                                                | `quit`                                            |
