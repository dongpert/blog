---
title: 우분투 필수 개념과 명령어
date: 2018-02-13 19:09:56
category:
- os
- ubuntu
tags:
- ubuntu
---
## 1. 터미널/콘솔에서 시스템 종료 명령 실행
`poweroff shotdown -P now, halt -p, init 0`
~~~
# shutdown -P + 10  =>  10분 후에 종료(P: poweroff)
# shutdown -r 22:00 =>  오후 10시에 재부팅(r: reboot)
# shutdown -c       =>  예약된 shutdown을 취소(c: cancel)
# shutdown -k +15   =>  현재 접속한 사용자에게 15분 후에 종료된다는 메시지를 보내지만 실제로 종료는 안됨
~~~
## 2. 시스템 재부팅
`reboot, shotdown -r now, init 6`
## 3. 로그아웃
`logut, exit`
## 4. 가상콘솔
가상 콘솔이란 '가상의 모니터'라 생각하면 이해하기 쉽다. 우분투는 총 7개의 가상 콘솔을 제공한다. 즉 컴퓨터 한대에 모니터 일곱 개가 연결된 효과를 낼 수 있다는 의미다.

`단축키: CTRL + ALT + F1 ~ F7`

## 5. 런레벨

| 런레벨 | 영문모드 | 설명 | 비고 |
| :- | :- | :- | :- |
| 0 | Power Off | 종료모드 | |
| 1 | Rescue | 시스템복구 | 단일 사용자 모드 |
| 2 | Multi-User | | 사용하지 않음 |
| 3 | Multi-User | 텍스트 모드의 다중 사용자 모드 | |
| 4 | Multi-User | | 사용하지 않음 |
| 5 | Graphical | 그래픽 모드의 다중 사용자 모드 | |
| 6 | Reboot | | |

### 런레벨 모드 확인

`ls -l /lib/systemd/system/runlevel?.target`
