---
title: "메모리와 캐시 메모리 [컴퓨터 구조]"
description: "메모리와 캐시 메모리 [컴퓨터 구조]"
date: 2022-08-07
update: 2023-08-07
tags:
  - 컴퓨터 구조

series: "컴퓨터 구조"
---

## RAM
RAM에는 실행할 프로그램의 명령어와 데이터가 저장됩니다.
여기서 중요한 점은 전원을 끄면 RAM에 저장된 명령어와 데이터가 모두 날아간다는것 입니다. 이런 저장장치를 `휘발성 저장장치`라고 합니다.

반면 전원을 꺼도 내용이 유지되는 저장장치를 `비휘발성 저장장치`라고 합니다.
ex) 하드디스크, SSD, USB 메모리

>![](https://velog.velcdn.com/images/97gkswn/post/df6950b3-4076-4363-bab1-4347668129b0/image.png)
- 보조기억장치는 전원을 꺼도 내용을 유지하지만 CPU는 보조기억장치에 접근하지 못합니다.
- 그래서 보조기억장치에는 `보관할 대상`을 저장하고 휘발성 저장 장치인 RAM에는 `실행할 대상`을 저장합니다. 
- 그래서 CPU가 실행하고 싶은 프로그램이 보조기억장치에 있다면 이를 RAM으로 복사하여 저장한 뒤 실행하게 됩니다.

## RAM용량이 크면?
RAM용량이 크면 어떤 점이 좋을까요?
예를 들어 보조기억장치에 프로그램 A,B,C가 있고 RAM용량이 이 프로그램을 한 개씩만 저장할 수 있다고 하면 A실행하고 있다가 CPU가 B를 실행하고자 할 때 보조기억장치에 접근해서 B를 다시 가지고 와야할 것 입니다.

하지만 RAM이 프로그램 A,B,C 세 개 모두 저장할 만큼 용량이 충분하다면 CPU가 B를 실행하고자 할 때 그냥 RAM에 저장돼있는 B를 실행하면 되기때문에 보조장치에서 여러 번 가져오는 수고를 덜 수 있습니다. 

>결론은 RAM용량이 커지면 프로그램 실행속도가 커지는 것은 맞지만 무작정 비례해서 실행속도가 좋아지는 것은 아닙니다. 많은 프로그램을 동시에 실행시키기에 유리하기는 합니다.

## RAM의 종류
### DRAM 
>Dynamic RAM의 줄임말입니다.
저장된 데이터가 동적으로 사라지는 RAM을 의미하는데 시간이 지나면 저장된 데이터가 사라져서 주기적으로 다시 저장을 해야하는 RAM입니다.

이런 단점에도 일반적으로 우리가 쓰는 RAM은 DRAM입니다.
소비 전력이 낮고 저렴하고 대용량으로 설계하기가 유리하기 때문입니다.

### SDRAM
>SRAM과는 상관이 없고 클럭에 맞춰 동작하며 클럭마다 CPU와 정보를 주고받을 수 있는 RAM입니다.

### DDR SDRAM
> 대역폭을 넓혀 속도를 빠르게 만든 SDRAM으로 최근에 가장 많이 쓰는 RAM입니다.
`대역폭` : `데이터를 주고 받는 길의 너비`    ex) 자동차 도로

## 저장 장치 계층 구조
저장장치는 다음과 같은 명제가 있습니다.
1. CPU와 가까운 저장장치는 빠르고 멀면 느리다.
2. 속도가 빠른 저장장치는 저장 용량이 작고 비싸다.

컴퓨터에 사용하는 저장장치는 `CPU에 얼마나 가까운가`를 기준으로 계층구조를 만들 수 있습니다. 이를 `저장 장치 계층 구조`라고 하는데요.
피라미드를 생각하시면 맨 위 부터
레지스터->메모리->보조기억장치 순으로 있습니다.

## 캐시 메모리
CPU가 메모리에 접근하는 속도는 레지스터에 접근하는 속도보다 느립니다.
CPU의 연산속도가 아무리 빨라도 메모리에 접근하는 속도가 느리면 크게 쓸모가 없겠죠?
그래서 등장한 저장장치가 캐시 메모리입니다.
>캐시 메모리는 CPU와 메모리 사이에 위치하고 레지스터보다 용량이 크고 메모리보다 빠른 SRAM기반의 저장장치 입니다.

비유를 하자면 메모리는 멀리 있지만 물건이 많은 `대형마트`이고 
캐시 메모리는 `대형마트`보다 좀 더 가까이 있지만 물건이 상대적으로 적은 `편의점`으로 생각하시면 됩니다.
