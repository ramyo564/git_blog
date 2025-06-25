---
layout: single
title: "[ AI_Todo 프로젝트 개발기 - Backend #1 ] Backend 기록 모음"
categories: Spring_Project_AI_Todo
toc: true
toc_sticky: true
author_profile: false
sidebar: 
tags:
---
# AI_Todo 프로젝트 백엔드 기록 모음

- 백엔드 아키텍처를 설계하면서의 기록과정입니다.

## 아키텍처 설계

- 최종단계 (MSA) 를 고려한 아키텍처 설계
- 해당 설계는 모놀리식으로 구성되었습니다
- 모놀리식으로 구성된 이유는 서비스 출시전 예상 트래픽 예측 어려움
- 성능적인 부분때문에 이렇게 구성하게 되었습니다.

### 설계흐름

- 아키텍처 설계편
- 인증(Auth) 도메인 심층 구현편
- DDD 심화 - Factory 와 Aggregate로 도메인 모델 풍부하게 만들기
- 도메인 이벤트로 Auth와 User 서비스 똑똑하게 분리하기