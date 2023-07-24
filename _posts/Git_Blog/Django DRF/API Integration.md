---

layout: single
title: " [Django DRF] React DjangoDRF project (3) "
categories: Django
tag: [Python,"[Django DRF] DjangoDRF + React chat project",]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# API Integration

## Build : Install Axios and create first API request (CORS intro)

```
npm i axios
```

Axios는 브라우저와 Node.js에서 모두 동작하는 자바스크립트 라이브러리로, HTTP 클라이언트를 구현하는데 사용된다. 주로 웹 애플리케이션에서 서버와 데이터 통신을 할 때 사용되며, RESTful API와 같은 웹 서비스에 쉽게 접근하고 데이터를 주고받을 수 있도록 도와준다. Axios는 Promise 기반의 비동기 방식으로 동작하며, 쉽고 간결한 API를 제공하여 HTTP 요청을 보내고 응답을 처리할 수 있다.

장점:

1. 사용이 간편하고 간결한 API: Axios는 간단하고 직관적인 API를 제공하여 개발자가 쉽게 HTTP 요청을 생성하고 응답을 처리할 수 있도록 도와준다.
2. 브라우저와 Node.js에서 모두 사용 가능: Axios는 브라우저와 Node.js 환경 모두에서 사용할 수 있으며, 동일한 방식으로 사용할 수 있어서 유용하다.
3. Promise 기반 비동기 처리: Axios는 Promise를 사용하여 비동기적으로 HTTP 요청을 처리하므로, 비동기 코드를 쉽게 관리할 수 있다.
4. 다양한 기능 제공: Axios는 HTTP 요청과 응답을 다양한 방식으로 커스터마이징할 수 있는 기능들을 제공한다. 예를 들어, 요청과 응답 데이터의 변환, 요청 취소, 인터셉터(interceptor)를 활용한 전처리/후처리 등이 가능하다.

단점:

1. 무겁고 추가적인 의존성: Axios는 브라우저 또는 Node.js 환경에서 동작하도록 설계되어 있으며, 라이브러리 자체가 상당히 크기 때문에 다른 더 가벼운 HTTP 클라이언트보다 무겁고 더 많은 의존성을 가지고 있을 수 있다. 하지만 이는 큰 문제가 되지 않는 경우가 많다.
2. 모든 기능의 지원이 필요하지 않을 수 있음: 애플리케이션에 따라서는 Axios가 제공하는 모든 기능이 필요하지 않을 수 있다. 따라서 필요한 기능만 사용하는 더 가벼운 라이브러리를 선택하는 것이 더 적합할 수 있다.

전반적으로 Axios는 편리하고 강력한 HTTP 클라이언트 라이브러리로서, 대부분의 웹 애플리케이션에서 사용되고 있다. 하지만 프로젝트의 요구사항과 크기에 따라서 다른 HTTP 클라이언트 라이브러리를 선택하는 것도 고려할만하다.

사실 Axios 를 사용한다고 해서 DRF-Spectacular 처럼 특별한 이득은 없다. 자바스크립트로도 HTTP Requests 를 처리할 수 있지만 Axios 는 다양한 기능들을 제공하고 매우 인기 있는 라이브러리다.

### Django-cors-headers

Django 에서 쓰는 포트랑 바이트에서 쓰는 포트 너버가 다르기 때문에 이 부분의 문제를 해결하기 위해서
아래의 라이브러리를 사용해줘야 한다.
(포트넘버가 다르면 엑세스 할 수 가 없다.)
```python
pip install django-cors-headers
```

[django-cors-headers공문](https://pypi.org/project/django-cors-headers/)

![](https://i.imgur.com/02yh2wi.png)

MIDDLEWARE 에 넣을 때는 맞는 순서에 넣어야 한다.

![](https://i.imgur.com/4gvmIV4.png)

```python
CORS_ALLOWED_ORIGINS = [

    "http://localhost:5173",

]
```
바이트 기본 포트 넘버 5173 을 넣어줄 경우 정상적으로 데이터 접근이 가능해진다.

## Build : Configuring Cross - Origin Resource Sharing (CORS)

[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
[CORS 블로그 글](https://evan-moon.github.io/2020/05/21/about-cors/)

## Build : Create a global configuration file

### config.ts
```typescript
export const BASE_URL = "http://127.0.0.1:8000/api";

export const MEDIA_URL = "http://127.0.0.1:8000";
```

## Build : Axios Interceptor

