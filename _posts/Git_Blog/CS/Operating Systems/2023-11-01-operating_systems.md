---
layout: single
title: " [운영체제] 운영체제 공부 시작"
categories: CS
tags:
  - CS
  - OperatingSystems
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 운영체제란 무엇인가?

- 컴퓨터 하드웨어 바로 위에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층
- 협의의 운영체제(커널) - 좁은 의미
	- 운영체제의 핵심 부분으로 메모리에 상주하는 부분
- 광의의 운영체제 - 넓은 의미 - 윈도우 (파일복사등)
	- 커널 뿐 아니라 각종 주변 시스템 유틸리티를 포함하는 개념

## 운영 체제의 구조

![](https://i.imgur.com/Sl1zkP8.png)


## 운영 체제의 목적
- 컴퓨터 시스템의 자원을 효율적으로 관릴
	- 프로세서, 기억장치, 입출력 장치 등의 효율적 관리
		- 사용자간의 형평성 있는 자원 분배
		- 주어진 자원으로 최대한의 성능을 내보내도록
	- 사용자 및 운영체제 자신의 보호
	- 프로세스, 파일, 메시지 등을 관리

## 운영 체제의 분류

### 동시 작업 가능 여부
- 단일 작업 (single tasking)
	- 한 번에 하나의 작업만 처리
	- MS-DOS 프롬프트 상에서 한 명의 수행을 끝내기 전에 다른 명령을 수행시킬 수 없음
- 다중 작업 (multi tasking)
	- 동시에 두 개 이상의 작업 처리
	- UNIX, MS Windows 등에서 한 명령의 수행이 끝나기 전에 다른 명령이나 프로그램을 수행할 수 있음

### 사용자의 수
- 단일 사용자 (single user)
	- MS-DOS, MS Windows (개인 컴퓨터)
- 다중 사용자 (multi user)
	- UNIX, NT server

### 처리 방식
- 일괄 처리 (batch processing)
	- 작업 요청의 일정량 모아서 한꺼번에 처리
	- 작업이 완전 종료될 때까지 기다려야 함
		- 초기 Punch Card 처리 시스템 (그런게 있었다고 함)
- 시분할 (time sharing)
	- 여러 작업을 수행할 때 컴퓨터 처리 능력을 일정한 시간 단위로 분할하여 사용
	- 일괄 처리 시스템에 비해 짧은 응답 시간을 가짐
		- Unix
	- interactive한 방식
	- 일이 5개가 있다면 0,1초에 첫 번째 0.2초에 두 번째 0.3초에 세번째 작업등 골고루 돌아가면서 진행 ->컴퓨터는 한 개지만 사용자에게 할당을 하여 사용자 각자 개인 컴퓨터가 있다고 느껴지는 걸로 생각하면 됨
- 실시간 (realtime OS)
	- 정해진 시간 안에 어떠한 일이 반드시 종료됨이 보장되어야 하는 실시간 시스템을 위한 OS
		- 원자로/ 공장제어/ 미사일제어/ 반도체 장비
- 실시간 시스템의 개념 확장
	- Hard realtime system 경성 실시간 시스템
	- Soft realtime system 연성 실시간 시스템
		- 영화 처럼 24프레임에 맞춰 상영은 해야하지만 당장 끊겨도 상관 없는 예시
		- 사실 우리가 쓰는 운영체제는 시분할임
- 일괄 처리 (batch processing)
	- 작업 요청의 일정량 모아서 한꺼번에 처리
	- 작업이 완전 종료될 때까지 기다려야함
		- 초기 Punch Card 처리 시스템 (고대 시스템이라고 생각하면됨 OMR 카드)
		- 인터렉티브 사지 않음

## 용어 정리
- Multitasking
- Multiprogramming
- Time sharing
- Multiprocess
- 구분
	- 위 용어들은 컴퓨터에서 여러 작업을 동시에 수행하는 것을 뜻한다.
	- Multiprogramming은 여러 프로그램이 동시에 메모리에 올라가 있음을 강조
	- Time sharing은 CPU의 시간을 분할하여 나누어 쓴다는 의미 강조
	- Multiprocess는 하나의 컴퓨터에 CPU 프로세서가 여러 개 붙어 있음을 의미

## 운영체제의 예
### 유닉스 (UNIX)
- 코드의 대부분을 C언어로 작성
- 높은 이식성 - 모든 컴퓨터에서 사용하기 좋다는 말
- 최소한의 커널 구조
- 복잡한 시스템에 맞게 확장 용이
- 소스 코드 공개
- 프로그램 개발에 용이
- 다양한 버전
	- System V, FreeBSD, SunOS, Solaris
	- Linux

### DOS(Disk Operating System)
- MS 사에서 1981sus IBM-PC를 위해 개발
- 단일 사용자용 운영체제, 메모리 관리 능력의 한계(주 기억 장치 : 640kb)

### MS Windows
- MS 사의 다중 작업용 GUI 기반 운영체제
- Plug and Play, 네트워크 환경 강화
- DOS 용 응용 프로그램과 호환성 제공
- 불안정성 (초창기S)
- 풍부한 자원 소프트웨어

### Handheld device 를 위한 OS
- PalmOS, Pocket PC (WinCE), Tiny OS





[출처 링크](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09)