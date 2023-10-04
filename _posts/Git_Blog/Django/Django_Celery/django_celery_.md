---
layout: single
title: " [Django Celery] Celery (1)"
categories: Django_Celery
tags:
  - Python
  - Celery
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Celery

팀프로젝트를 하는데 셀러리를 좀 더 확실하게 마스터하기 위해서 공부     

## Celery란?

- 파이썬에서 작업을 비동기식으로 실행하는데 사용하는 분산 작업 대기 시스템이다.
- 셀러리를 사용하면 시간 소모적이고 리소스 집약적인 작업을 오프로드할 수 있다.

### Celery Tasks

- Running machine learning models
- Sending confirmation emails
- Web scraping and crawling
- Processing images
- Generating reports

*기본 어플리케이션*
![](https://i.imgur.com/MnbfStU.png)

*셀러리*

![](https://i.imgur.com/yrZzS8n.png)


Celery addresses this challenge by providing a task queue.       
Job queue System.      
It's going to allow us to define these time consuming.      
Tasks as independent units of work known as tasks.     

So instead of executing these tasks.     
Immediately within the main application, salary is going to push them into a task queue.      
There, they will wait to be processed.      

So here in this example now we've removed B, we pushed B task over to the new server and that's thengoing to be processed by this new server.     

And in the meantime, utilizing asynchronous programming techniques, we're able to continue processing the request and just letting the user know that, okay, we're processing your request.     

It's going to take a couple of seconds.      

![](https://i.imgur.com/6wOSG0S.png)


여기서 서버는 여러 서버에 로드할 수 있다.

## Celery Tasks

- Asynchronous Task Execution
- Distributed Task Queue
- Task Scheduling and Periodic Tasks
- Result Handling
- Error Hadling and Retry Mechanism
- Monitoring and Management

## Python Celery Implementation

- Flask, Pyramid, Bottle
- Standalone Python
- Data processiong and analysis
- API development


샐러리는 비동기 작업, 분산 작업 큐, 작업 예약 결과, 오류 처리 및 모니터링 기능을 향상시키는데 좋은 툴이다.      