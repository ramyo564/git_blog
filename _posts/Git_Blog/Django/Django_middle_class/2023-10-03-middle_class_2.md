---
layout: single
title: " [Django DRF] middle_class (2)"
categories: Django_DRF_middle_class
tags:
  - Python
  - DRF
  - Docker
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Docker

미뤄왔던 도커 연습

![](https://media1.giphy.com/media/yelQbR4M2wk3JkZByk/giphy.gif?cid=ecf05e47d0selc82cctxbf9k8xs99bcybju4t1l2eicdr092&ep=v1_gifs_gifId&rid=giphy.gif&ct=g)


- Dockerfile : 도커 이미지를 만드는 방법 지시
	- 도커 컨테이너 생성 지침이 포함된 읽기 전용 템플릿
- Container : 기본적으로 생성, 시작, 중지, 이동 또는 작업이 가능한 실행 가능한 이미지 인스턴스
	- 도커 API 또는 CLI를 사용해서 컨테이너를 삭제


## Dockerfile Config

1. Build Dep Wheels
2. Run Application

```python
ARG PYTHON_VERSION=3.11.2-bullseye

FROM python:${PYTHON_VERSION} as python


FROM python as python-build-stage
ARG BUILD_ENVIRONMENT=local

RUN apt-get update && apt-get install --no-install-recommends -y \
    build-essential \
    libpq-dev

COPY ./requirements .

RUN pip wheel --wheel-dir /usr/src/app/wheels \
  -r ${BUILD_ENVIRONMENT}.txt


FROM python as python-run-stage
ARG BUILD_ENVIRONMENT=local

ARG APP_HOME=/app

ENV PYTHONDONTWRITEBYTECODE 1

ENV PYTHONUNBUFFERED 1

ENV BUILD_ENV ${BUILD_ENVIRONMENT}

WORKDIR ${APP_HOME}

RUN apt-get update && apt-get install --no-install-recommends -y \
  libpq-dev \
  gettext \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*


COPY --from=python-build-stage /usr/src/app/wheels /wheels/

RUN pip install --no-cache-dir --no-index --find-links=/wheels/ /wheels/* \
  && rm -rf /wheels/

COPY ./docker/local/django/entrypoint /entrypoint
RUN sed -i 's/\r$//g' /entrypoint
RUN chmod +x /entrypoint

COPY ./docker/local/django/start /start
RUN sed -i 's/\r$//g' /start
RUN chmod +x /start

COPY . ${APP_HOME}

ENTRYPOINT [ "/entrypoint" ]
```

### Docker build

- 공부도 할 겸 한 줄씩 분석해 보자

```python
ARG PYTHON_VERSION=3.11.2-bullseye
FROM python:${PYTHON_VERSION} as python
```

[참고](https://medium.com/swlh/alpine-slim-stretch-buster-jessie-bullseye-bookworm-what-are-the-differences-in-docker-62171ed4531d#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImI5YWM2MDFkMTMxZmQ0ZmZkNTU2ZmYwMzJhYWIxODg4ODBjZGUzYjkiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMDk3NTc4NjMzNTUwNTg5OTI5NjgiLCJlbWFpbCI6InlvaGFuMDMyeW9oYW5AZ21haWwuY29tIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsIm5iZiI6MTY5NjI3NTMyNSwibmFtZSI6InlvaGFuIGphbmciLCJwaWN0dXJlIjoiaHR0cHM6Ly9saDMuZ29vZ2xldXNlcmNvbnRlbnQuY29tL2EvQUNnOG9jS29fZi1aUHhFcHozMDRwX01QSVhaR1RQUVZMSjZkRHI0bnF6YnFzTVVkPXM5Ni1jIiwiZ2l2ZW5fbmFtZSI6InlvaGFuIiwiZmFtaWx5X25hbWUiOiJqYW5nIiwibG9jYWxlIjoia28iLCJpYXQiOjE2OTYyNzU2MjUsImV4cCI6MTY5NjI3OTIyNSwianRpIjoiNGYxOWVjOWE2NDliYWRmM2QxMTYxZWM4N2VlNTNmODNiZDY4ODgyMCJ9.GPuxne_Npk0j_K3J3IboTEOYfhMP56PIuX9RwHmMjZwy6AXWJTPOC2AfBocdBYqlWwTQ-HYu8OuM7PqDS4aPaqKB_XlKP0oJrRFtcLMkjGy34nc8pG7_RZkum6hJFwJUqRmnFVT-o7S65-26eCCgFYP4u9rmykkDytV8IHnts3xt7dFbuSxeVhFIu2txI7Tqz1d4213g9EXUpvqFLSwLlF1X5uabrRaMhjt3BI-V-EA9kyqwsR0ztFCDImd1wCwMqAT-CFQqxS8_8HmIdQy5Scp8r2IZ-OjdrssMhDz514rVK50OwjaJQGtn_GRwMqSSk2QuT_2mDIfY21sPzYcl9w)

- ARG는 Docerfile에서 사용할 변수를 정의하는 지시어다. 
	- PYTHON_VERSION 이라는 변수를 만들고 이 값은 3.11.2-bullseye
	- bullseye는 코드명인데 위에 참고를 클릭하면 자세히 나온다.
		- Debian 은 안정적인 Linux 배포판인데 bullseye는 Debian 11에서 사용할 수 있는 표준 패키지 및 라이브러리 세트가 포함되어 있다.
			- aws에서 mysql 라이브러리를 설치할 때 패키지에 포함이 되어 있지 않아서 수동으로 깔아줬던걸 떠올리면 된다.
	- 윈도우 사용자는 -windowsservercore 를 사용하면 된다.
		- 윈도우로 개발할 때 뭔가 제약이 많아서 이참에 리눅스를 깔았다.
					- WSL를 설치해서 사용해볼 예정! AWS에 서버를 올릴 때 처럼 고생할 듯 하다
- `FROM python:${PYTHON_VERSION} as python` 
	- 도커 이미지를 빌드할 때 기본 이미지를 지정하는 지시어다.

도커에서 멀티 스테이지 빌드를 구현하는데 이를 통해 중간 이미지를 생성하고 필요한 파일을 추출한 다음 최종 이미지르 생성하 때 중간 이미지의 일부만 복사할 수 있다.     
이로써 최종 이미지의 크기를 줄이고, 빌드 과정을 최적화 할 수 있다.

### python-build-stage

```python
FROM python as python-build-stage
ARG BUILD_ENVIRONMENT=local

RUN apt-get update && apt-get install --no-install-recommends -y \
    build-essential \
    libpq-dev
    
COPY ./requirements .

RUN pip wheel --wheel-dir /usr/src/app/wheels \
  -r ${BUILD_ENVIRONMENT}.txt
```

- FROM 지시문에서 새로운 빌드 스테이지를 정의한다. 이 때 스테이지 이름은 python-build-stage다
	- 멀티 스테이지 빌드는 빌드 프로세스를 단순화 하고, 최종 이미지의 크기를 최적화하며, 빌드 시간을 단축하는 데 유용하다.
	- 첫 번째 스테이지에서 사용한 모든 빌드 도구 및 임시 파일은 최종 이미지에 포함되지 않는다.
- `RUN apt-get update && apt-get install --no-install-recommends -y ...`:
	- RUN 지시문은 Docker 컨테이너 내에서 명령을 실행하는 역할을 한다.
	- apt-get update는 패키지 목록을 업데이트하고 install은 패키지를 설치하는데 이 때 추천 패키지는 설치하지 않도록 한다. -y는 사용자 개입 없이 명령을 자동으로 yes 로 응답
	- build-essential 은 C 및 C++ 빌드 도구를 포함하고 있으며 libpq-dev 은 PostgresSQL 개발 라이브러리를 설치한다.
- `COPY ./requirements .`
	- 현재 디랙토리 경로를 갖고 온다.
- `RUN pip wheel --wheel-dir /usr/src/app/wheels \ ....`
	- wheel 은 파이썬 패키지를 빌드 및 배포하기 위한 도구다.
	- `BUILD_ENVIRONMENT` 가 실제하는 local.txt 다.
	- ![](https://i.imgur.com/a0aM2H5.png)

### python-run-stage

- 1 단계에서 wheel을 만들고
- 2 단게에서는 어플리케이션을 실행한다.


```python
FROM python as python-run-stage
ARG BUILD_ENVIRONMENT=local

ARG APP_HOME=/app

ENV PYTHONDONTWRITEBYTECODE 1

ENV PYTHONUNBUFFERED 1

ENV BUILD_ENV ${BUILD_ENVIRONMENT}

WORKDIR ${APP_HOME}

RUN apt-get update && apt-get install --no-install-recommends -y \
  libpq-dev \
  gettext \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*
  
```


- `ARG APP_HOME=/app`
	- 컨테이너에 저장될 디렉토리
- `ENV PYTHONDONTWRITEBYTECODE 1`
	- 모듈을 가져올 때 PC 파일인 바이트코드 파일 작성을 건너뛰도록 함
		- 파이썬은 모듈을 가져올 때 일반적으로 소스 코드를 바이트코드로 컴파일해서 PC에 저장한다. 미리 컴파일된 바이트 코드를 직접 로드할 수 있으므로 이후에 모듈을 가져오는 속도가 빨라진다.
		- 하지만 도커 컨테이너에서 디스크 공간 사용량 및 시스템을 클린하게 유지하기 위해 이런 파일들은 잘 생성하지 않는다고 한다.
- `ENV PYTHONUNBUFFERED 1`
	- 파이썬 출력이 버퍼링 되지 않도록 설정 -> 터미널로 직접 전송 -> 어플리케이션으로 출력을 실시간 표시
- `RUN apt-get update && apt-get install....`
	- **libpq-dev**:
	    - "libpq-dev"는 PostgreSQL 데이터베이스와 관련된 C 라이브러리와 개발 헤더 파일을 포함하는 패키지다.
	    - 이 패키지는 PostgreSQL 데이터베이스와 상호 작용하고 PostgreSQL 애플리케이션을 개발할 때 필요한 라이브러리와 헤더 파일을 제공한다.
	    - 예를 들어, PostgreSQL 데이터베이스와 연동하는 Python 애플리케이션을 빌드하거나 컴파일할 때 "libpq-dev" 패키지의 기능이 필요하다.
	- **gettext**:
	    - "gettext"는 다국어 지원과 문자열 번역을 지원하는 라이브러리와 도구를 제공하는 패키지다.
	    - 이 패키지는 다국어 애플리케이션을 개발할 때 사용된다. 애플리케이션의 문자열을 번역하고 다양한 언어로 표시할 수 있는 기능을 제공한다.
	    - "gettext" 패키지는 메시지 카탈로그를 작성하고 관리하는 데 도움이 되며, 애플리케이션에서 다양한 언어로의 번역을 지원한다.
- `apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false`:
	- `purge` 명령은 패키지를 완전히 제거하는 데 사용된다. 이 명령은 패키지를 제거한 후 설정 파일과 관련된 파일을 제거한다.
	- `-y` 옵션은 패키지 제거 과정에서 모든 질문에 자동으로 "yes"로 답하는 옵션이다.
	- `--auto-remove` 옵션은 패키지를 제거한 후 자동으로 관련 의존성 패키지도 제거하도록 설정한다.
	- `-o APT::AutoRemove::RecommendsImportant=false` 옵션은 "apt-get auto-remove" 명령을 실행할 때 추천(recommended) 패키지 중 중요한 패키지는 제거하지 않도록 설정한다.
- `rm -rf /var/lib/apt/lists/*`:
	- `/var/lib/apt/lists/` 디렉토리에 저장된 패키지 목록 캐시 파일을 모두 제거한다. 이렇게 해서 불필요한 디스크 공간을 확보할 수 있다.


```python
COPY --from=python-build-stage /usr/src/app/wheels /wheels/

RUN pip install --no-cache-dir --no-index --find-links=/wheels/ /wheels/* \
  && rm -rf /wheels/
```


- `COPY --from=python-build-stage /usr/src/app/wheels /wheels/`:  
    - `COPY` 지시문은 호스트 머신 또는 이전 스테이지에서 파일 또는 디렉토리를 Docker 이미지로 복사하는 데 사용된다.
    - 여기서는 "python-build-stage" 스테이지에서 생성된 이미지 내부의 `/usr/src/app/wheels` 디렉토리의 내용을 현재 스테이지의 `/wheels/` 디렉토리로 복사한다.
    - 이 작업은 이전 스테이지에서 빌드된 Python 패키지 (Wheel 파일)를 현재 스테이지로 가져온다.
- `RUN pip install --no-cache-dir --no-index --find-links=/wheels/ /wheels/* \`
    - `RUN` 지시문은 Docker 컨테이너 내에서 명령을 실행하는 역할을 한다. 
        - `--no-cache-dir`: pip의 캐시를 사용하지 않도록 설정한다. 이렇게 하면 캐시된 파일을 사용하지 않고 패키지를 직접 다운로드하여 설치한다.
        - `--no-index`: 패키지 인덱스 (예: PyPI)를 사용하지 않도록 설정한다. 이렇게 하면 로컬 디렉토리에서 직접 패키지를 찾는다.
        - `--find-links=/wheels/`: 패키지를 찾을 때 사용할 추가 패키지 저장소를 지정한다. 여기서는 `/wheels/` 디렉토리를 저장소로 지정하여 이전 단계에서 복사한 Wheel 파일을 설치한다.
    - `/wheels/*`는 `/wheels/` 디렉토리에 있는 모든 Wheel 파일을 설치하라는 것을 의미한다.
- . `&& rm -rf /wheels/`:
    - 이 부분은 Wheel 파일을 설치한 후에 `/wheels/` 디렉토리를 삭제하는 명령이다. Wheel 파일은 이미 설치되었으므로 삭제하여 이미지 크기를 최적화한다. 이러한 작업은 Docker 이미지를 가볍게 유지하고 불필요한 파일을 제거하는 데 좋다.


```python
COPY ./docker/local/django/entrypoint /entrypoint
RUN sed -i 's/\r$//g' /entrypoint
RUN chmod +x /entrypoint

COPY ./docker/local/django/start /start
RUN sed -i 's/\r$//g' /start
RUN chmod +x /start

```

- `COPY ./docker/local/django/entrypoint /entrypoint`:
    - 호스트 머신의 `./docker/local/django/entrypoint` 파일을 Docker 이미지 내의 `/entrypoint` 경로로 복사한다.
	    - Docker 컨테이너에서 실행할 "entrypoint" 스크립트 파일을 복사하는 작업
- `RUN sed -i 's/\r$//g' /entrypoint`:  
    - `sed` 명령을 사용하여 `/entrypoint` 파일 내의 Windows 스타일 줄 바꿈 문자(`\r`)를 제거하고, Unix 스타일 줄 바꿈 문자(`\n`)로 변환하는 작업을 수행한다.
    - 이러한 변환은 일반적으로 Windows 환경에서 작성된 스크립트를 Linux 기반의 Docker 컨테이너에서 실행할 때 필요한 작업이다. Windows 환경에서는 줄 바꿈 문자가 `\r\n`으로 표현되는데, Unix/Linux 환경에서는 줄 바꿈 문자가 `\n`으로 표현되기 때문이다.
	    - `s`: 이 부분은 "substitute"를 나타낸다. 즉, 대체 작업을 수행하겠다는 것을 나타낸다.
	    - `/`: 슬래시 (/)는 정규 표현식 패턴을 구분하는 구분자다. 패턴을 찾고 대체할 내용을 구분하기 위해 사용된다.
	    - `\r`: 이 부분은 특수한 문자로서, 캐리지 리턴(Carriage Return) 문자를 나타낸다. 캐리지 리턴 문자는 줄 바꿈 문자로 사용되며, Windows 환경에서는 줄 바꿈 문자가 `\r\n`으로 표현된다.
	    - `$`: `$`는 정규 표현식에서 패턴의 끝을 나타낸다. 즉, 패턴이 줄의 끝에 있는 경우를 나타낸다.
	    - `//`: 두 개의 슬래시는 찾을 패턴과 대체할 내용 사이를 구분한다.
	    - `g`: 이 부분은 "global"을 나타낸다. 이 옵션을 사용하면 문자열 내에서 모든 해당 패턴을 찾아서 대체한다. 즉, 한 줄에 여러 번 나타나는 경우에도 모두 대체한다.
- `RUN chmod +x /entrypoint`:
    - `chmod` 명령을 사용하여 `/entrypoint` 파일에 실행 권한을 부여하는 작업을 수행한다.
	    - chmod는 기본적으로 파일이나 디렉터리의 권한을 변경하는데 사용하는 Unix 커맨드다.
	    - +x 는 파일에 실행 권한을 추가하라는 명령이다.


1. **Django/entrypoint**:
    - "Django/entrypoint"는 Django 웹 애플리케이션을 시작하는 데 사용되는 스크립트다. Django 애플리케이션을 초기화하고 서버를 시작하는 역할을 수행한다.
2. **start**:
    - "start"는 일반적으로 Docker 컨테이너 내에서 애플리케이션 또는 서비스를 시작하는 데 사용되는 스크립트다.
3. **Celery worker**:
    - "Celery"는 Python 기반의 분산 작업 큐 시스템이다. "Celery worker"는 이 큐 시스템에서 작업을 처리하는 데 사용되는 컴퓨팅 노드 또는 프로세스를 나타낸다.  "Celery worker" 스크립트는 백그라운드에서 비동기 작업을 수행하기 위해서다.
4. **Flower**:
    - "Flower"는 Celery 작업 큐 모니터링 도구다. "Flower" 서비스를 실행하면 Celery 작업 큐의 상태를 모니터링하고 관리할 수 있으며, 대시보드를 통해 작업 큐의 상태를 시각적으로 확인할 수 있다.


```python
COPY . ${APP_HOME}
ENTRYPOINT [ "/entrypoint" ]
```

1. `COPY . ${APP_HOME}`:
    - `.`는 현재 Dockerfile이 있는 디렉토리 내의 모든 파일과 하위 디렉토리를 나타낸다. 즉, 현재 Dockerfile이 있는 디렉토리의 내용을 Docker 이미지 내의 `${APP_HOME}` 경로로 복사하라는 의미다.
    - `${APP_HOME}`는 Docker 이미지 내에서 작업 디렉토리로 사용할 경로를 나타낸다.
2. `ENTRYPOINT [ "/entrypoint" ]`:
    - `ENTRYPOINT` 지시문은 Docker 컨테이너가 시작될 때 실행할 실행 파일 또는 스크립트를 지정한다.
    - 컨테이너가 시작될 때 `/entrypoint` 스크립트를 실행하도록 지정한다.
    - 즉, 컨테이너가 시작되면 `/entrypoint` 스크립트가 실행되며, 해당 스크립트에 정의된 동작이 수행된다.


## Environment Variables and Shell Scripts

데이터 베이스에 연결 할 수 있는지 먼저 확인하기 위해 Entrypoint 쉘 스크립트를 만들어준다.

![](https://i.imgur.com/COpsaDF.png)

*.postgres*

```python
POSTGRES_PORT=5432
POSTGRES_DB=authors-live
POSTGRES_USER=abc
POSTGRES_PASSWORD=pass123456
DATABASE_URL=postgres://alphaogilo:Pass123456@postgres:5432/authors-live
```

*entrypoint*
```python
#!/bin/bash

set -o errexit

set -o pipefail

set -o nounset

if [ -z "${POSTGRES_USER}" ]; then
  base_postgres_image_default_user='postgres'
  export POSTGRES_USER="${base_postgres_image_default_user}"
fi

export DATABASE_URL="postgres://${POSTGRES_USER}:${POSTGRES_PASSWORD}@${POSTGRES_HOST}:${POSTGRES_PORT}/${POSTGRES_DB}"

python << END

import sys
import time
import psycopg2

suggest_unrecoverable_after = 30
start = time.time()

while True:
  try:
    psycopg2.connect(
      dbname="${POSTGRES_DB}",
      user="${POSTGRES_USER}",
      password="${POSTGRES_PASSWORD}",
      host="${POSTGRES_HOST}",
      port="${POSTGRES_PORT}",
    )
    break

  except psycopg2.OperationalError as error:
    sys.stderr.write("Waiting for PostgreSQL to become available...\n")
    if time.time() - start > suggest_unrecoverable_after:
      sys.stderr.write(" This is taking longer than expected. The following exception may be indicative of an unrecoverable error: '{}'\n".format(error))
  time.sleep(1)

END

>&2 echo "PostgreSQL is available"

exec "$@"
```

### Unix/Linux 쉘 스크립트

```python
#!/bin/bash

set -o errexit

set -o pipefail

set -o nounset
```


#### 해시뱅
`#!/bin/bash` 는 스크립트 파일의 첫 줄에 있는 특별한 주석으로 스크립트가 어떤 종류의 쉘을 사용해서 실행되어야 하는지 지정한다. `해시뱅 (hashbang)` 이라고도 불린다.     

Bash는 Unix 및 Linux 시스템에서 매우 일반적으로 사용되는 명령줄 쉘이다.      
스크립트 파일의 첫 줄에 이러한 해시뱅을 포함하면, 해당 스크립트 파일을 실행할 때 운영 체제는 지정된 쉘 (이 경우 Bash)을 사용하여 스크립트를 실행하게 된다.      

예를 들어, 스크립트 파일이 `myscript.sh` 라는 이름으로 저장되어 있고 첫 줄에 `#!/bin/bash` 가 포함되어 있다면, 스크립트를 실행하려면 다음과 같이 명령어를 사용할 수 있다:

```python
chmod +x myscript.sh   # 스크립트 파일에 실행 권한을 부여합니다.
./myscript.sh         # 스크립트를 실행합니다.
```

위의 명령어는 `myscript.sh` 파일을 Bash 쉘에서 실행하게 되며, `#!/bin/bash` 라인에 지정된 쉘을 사용하여 스크립트를 실행한다.



1. `set -o errexit` 또는 `set -e`: 이 명령은 "errexit" 옵션을 설정한다. 이 옵션을 활성화하면 스크립트 내부의 어떤 명령이라도 비정상적인 종료 코드(에러를 나타내는 코드)를 반환하면 스크립트가 즉시 종료된다.      
   이렇게 함으로써 스크립트는 오류가 발생하면 실행을 중단하고, 잘못된 또는 예상치 못한 데이터로 계속 실행되는 것을 방지할 수 있다.
2. `set -o pipefail`: 이 명령은 "pipefail" 옵션을 설정한다. 기본적으로 파이프로 연결된 명령어들(예: `command1 | command2`)을 실행할 때, 파이프 라인의 종료 코드는 마지막 명령어(`command2` 예시에서)의 종료 코드가 된다.       
   "pipefail"을 활성화하면 이 동작이 변경된다. 파이프 라인에서 어떤 명령이라도 비정상적인 종료 코드를 반환하면 전체 파이프 라인이 실패로 간주되며 스크립트가 해당 오류 코드로 종료된다. 이렇게 함으로써 파이프된 명령어에서 발생한 오류를 무시하지 않도록 해야한다.
3. `set -o nounset` 또는 `set -u`: 이 명령은 "nounset" 옵션을 설정한다. 이 옵션을 활성화하면 스크립트는 정의되지 않은 변수에 대한 참조를 오류로 처리하고 종료한다. 이것은 변수가 값을 할당하기 전에 참조되는 경우를 방지하고, 초기화되지 않은 변수를 참조하여 예상치 못한 동작 및 버그를 방지하기 위한 안전 기능이다.

### if문

```python
if [ -z "${POSTGRES_USER}" ]; then
  base_postgres_image_default_user='postgres'
  export POSTGRES_USER="${base_postgres_image_default_user}"
fi
```

- 환경 변수가 값이 있는지 확인한다. `-z` 옵션은 문자열의 길이가 0인지 (비어있는지) 확인한다.
	- 앞에 조건이 참일 경우 then 뒤에 실행
- `base_postgres_image_default_user='postgres'` base_postgres_image_default_user 라는 변수를 설정하고 그 값은 postgres 다
- `export`는 환경 변수(environment variable)를 설정하고, 해당 변수를 현재 쉘과 그 하위 프로세스에서 사용할 수 있도록 만드는 명령어다. 환경 변수는 프로그램이나 스크립트가 실행될 때 사용되는 설정이나 데이터를 저장할 수 있다.
	- ARG와 차이점은 export는 환경변수 -> 현재 쉘 및 하위 프로세스에서 사용할 수 있다.
	- ARG는 프로그램이 실행될 때 프로그램의 입력 데이터 또는 옵션,작업을 지정할 때 사용한다.
- 결론적으로 POSTGRES_USER에 환경 변수가 설정되어 있지 않을 경우 posggres라는 기본 값으로 설정하는 작업니다.
- fi는 if 문을 종료할 때 사용한다.


```python
export DATABASE_URL="postgres://${POSTGRES_USER}:${POSTGRES_PASSWORD}@${POSTGRES_HOST}:${POSTGRES_PORT}/${POSTGRES_DB}"

python << END
```

- 환경변수 DATABASE_URL은 *.postgres* 에서 정보를 갖고 온다.
- python << END는 해당 시점부터 파이썬 코드가 실행되는 지점이라고 생각하면 된다.

```python
python << END
import os

database_url = os.environ.get("DATABASE_URL")
if database_url:
    print(f"Database URL: {database_url}")
else:
    print("DATABASE_URL environment variable is not set.")
END

```

- 이런 식으로 중간에 파이썬 코드가 들어갈 때 python << END로 시작해서 끝낼 때 END로 끝내주면 된다.

### 파이썬 코드

```python
python << END

import sys
import time
import psycopg2

suggest_unrecoverable_after = 30
start = time.time()

while True:
  try:
    psycopg2.connect(
      dbname="${POSTGRES_DB}",
      user="${POSTGRES_USER}",
      password="${POSTGRES_PASSWORD}",
      host="${POSTGRES_HOST}",
      port="${POSTGRES_PORT}",
    )
    break

  except psycopg2.OperationalError as error:
    sys.stderr.write("Waiting for PostgreSQL to become available...\n")
    if time.time() - start > suggest_unrecoverable_after:
      sys.stderr.write(" This is taking longer than expected. The following exception may be indicative of an unrecoverable error: '{}'\n".format(error))
  time.sleep(1)

END
```

- 데이터 베이스에 연결을 시도한 후 성공하면 break으로 종료 시킨다.
- 연결이 실패하면 일정시간동안 대기시키고 너무 오래 지속되면 error를 띄운다.
- time.time() 은 현재 시간을 초로 계산하는데 결론은 연결에 실패했을 경우 1초 쉬었다가 30초가 지나도 연결을 못 하면 errer를 띄운다.

### 마지막 부분
```python
>&2 echo "PostgreSQL is available"
exec "$@"
```

1. `>&2 echo "PostgreSQL is available"`: 이 부분은 메시지를 표준 오류 스트림(`stderr`)에 출력하는 역할을 한다.
    - `echo "PostgreSQL is available"`: 이 명령은 "PostgreSQL is available" 메시지를 표준 오류 스트림에 출력한다.
    - 이 메시지는 PostgreSQL 데이터베이스가 사용 가능한 경우에 출력된다.
	    - echo는 파이썬에서 print와 비슷하다.
    - 이렇게 하면 어느 부분에서 오류가 났는지 좀 더 쉽게 알 수 있다.

2. `exec "$@"`: 이 부분은 스크립트에 전달된 인수(`"$@"`)를 실행하라는 의미다. 
    - `"$@"`: `"$@"`은 스크립트에 전달된 모든 인수를 그대로 전달한다. 스크립트가 다른 명령 또는 스크립트를 호출할 때, 이전에 스크립트에 전달된 모든 인수를 유지하면서 전달하려고 할 때 사용된다.
    - `exec`: `exec` 명령은 현재 프로세스를 대체하거나 실행할 명령 또는 스크립트로 교체한다. 따라서 스크립트가 실행을 마치면, 스크립트 자체가 아닌 `"$@"`로 지정된 명령 또는 스크립트가 실행된다.

결과적으로, PostgreSQL 데이터베이스가 사용 가능한 경우 "PostgreSQL is available" 메시지를 출력하고, 그런 다음 `"$@"`로 지정된 명령 또는 스크립트를 실행한다. 이렇게 함으로써 PostgreSQL의 사용 가능 여부를 확인하고 그에 따라 다른 작업을 수행할 수 있다.     


## Start Script Config

```python
#!/bin/bash

set -o errexit

set -o pipefail

set -o nounset

python manage.py migrate --no-input
python manage.py collectstatic --no-input
exec python manage.py runserver 0.0.0.0:8000 
```

#### .dockerignore

```
.idea
venv
**/.git
.gitignore
.vscode
```

- gitignore처럼 도커로 이미지를 만들 때 필요 없는 부분은 제외시켜준다.


## Configure Docker Compose

*postgres/Dockerfile*

```python
FROM postgres:15-bullseye
```

*local.yml*
```python
version: "3.9"

services:
    api:
        build:
            context: .
            dockerfile: ./docker/local/django/Dockerfile
        volumes:
            - .:/app:z
            - static_volume:/app/staticfiles
            - media_volume:/app/mediafiles
        ports:
            - "8000:8000"
        env_file:
            - ./.envs/.local/.django
            - ./.envs/.local/.postgres
        depends_on:
            - postgres
            - mailhog
        command: /start
        networks:
            - authors-api
    postgres:
        build:
            context: .
            dockerfile: ./docker/local/postgres/Dockerfile
        volumes:
            - local_postgres_data:/var/lib/postgresql/data
            - local_postgres_data_backups:/backups
        env_file:
            - ./.envs/.local/.postgres
        networks:
            - authors-api
    mailhog:
        image: mailhog/mailhog:v1.0.0
        container_name: mailhog
        ports:
            - "8025:8025"
        networks:
            - authors-api
networks:
    authors-api:
        driver: bridge

volumes:
    static_volume:
    media_volume:
    local_postgres_data: {}
    local_postgres_data_backups: {}

```

![](https://i.imgur.com/xSaPn2A.png)


```python
version: "3.9"
services:
    api:
        build:
            context: .
            dockerfile: ./docker/local/django/Dockerfile

        volumes:
            - .:/app:z
            - static_volume:/app/staticfiles
            - media_volume:/app/mediafiles

        ports:
            - "8000:8000"

        env_file:
            - ./.envs/.local/.django
            - ./.envs/.local/.postgres

        depends_on:
            - postgres
            - mailhog

        command: /start

        networks:
            - authors-api


    postgres:
        build:
            context: .
            dockerfile: ./docker/local/postgres/Dockerfile

        volumes:
            - local_postgres_data:/var/lib/postgresql/data
            - local_postgres_data_backups:/backups

        env_file:
            - ./.envs/.local/.postgres

        networks:
            - authors-api
 
    mailhog:
        image: mailhog/mailhog:v1.0.0
        container_name: mailhog
        ports:
            - "8025:8025"

        networks:
            - authors-api

networks:
    authors-api:
        driver: bridge

volumes:
    static_volume:
    media_volume:
    local_postgres_data: {}
    local_postgres_data_backups: {}
```


- build:
	- context: .은 현재 디랙토리를 가리킨다. 컨텍스트 키는 빌드 구성 내에서 파일 및 디렉터리 위치를 지정하는 데 사용된다.
	- dockerfile: 은 도커파일이 어디 있는지 위치를 넣으면 된다.
- volumes: 데이터를 유지하고 호스트 시스템과 컨테이너 간에 또는 호스트 시스템 간에 파일을 공유하는 데 사용한다.
	- 첫 번째 볼륨에서는 현재 디렉토리를 앱 슬래시 앱에 매핑한다고 선언한다. 기본적으로 호스트 파일과 폴더를 컨테이너에 매핑하는 데 사용한다. 호스트의 코드를 변경하면 Docker 컨테이너의 코드도 변경된다.
	- `:z` 는 올바른 SELinux 컨텍스트와 SELinux 용 보안 매커니즘이다.
	- `:z`는 도커 컨테이너와 호스트 시스템 간의 볼륨 마운트 방법을 지정하는 도커의 옵션 중 하나다. 이 옵션은 SELinux (Security-Enhanced Linux) 시스템에서 특히 중요하며, SELinux가 활성화된 시스템에서 도커 볼륨 마운트에 영향을 미친다.
	- SELinux는 리눅스 시스템에서 추가적인 보안 기능을 제공하는 보안 모듈 중 하나다. SELinux는 파일 및 프로세스 액세스에 대한 엄격한 보안 정책을 시행하므로, 도커 컨테이너와 호스트 시스템 간의 파일 액세스 및 권한 문제가 발생할 수 있다.
	- `:z` 옵션은 SELinux 환경에서 도커 컨테이너로 볼륨을 마운트할 때 SELinux 정책을 준수하도록 도커에 지시한다. 즉, 컨테이너 내에서 호스트 파일 시스템으로의 액세스가 SELinux 정책을 따르도록 강제된다.
- command: / start : 시작 쉘 스크립트로 장도 서버를 실행하는 데 사용되는 쉘 스크립트다. 파일을 다운로드하고 마이그레이션이 진행될 거다.
- networks: 서비스를 서로 연결하는 데 사용된다. 여기서 bridge는 도커의 기본 네트워크 드라이버인 bridge 모드로 설정한다는 의미다.
- bridge 모드는 컨테이너가 호스트 시스템과 통신할 수 있도록 하는 기본 네트워크 모드다.
- volumes 는 컨테이너 내부의 파일 시스템이나 데이터를 컨테이너 외부에 지속적으로 저장하고 관리하는데 사용된다. 컨테이너가 종료되더라도 데이터를 보존하고 여러 컨테이너 간에 데이터를 공유하며 데이터를 백업하거나 복원하는 데 사용한다.

## Run docker-compose config

```
해당 루트 파일에서 아래의 명령문 입력
docker compose -f local.yml config
```

yml 파일이 잘 작성 되었다면 아래와 같이 내가 설정한 걸 다시 한 번 확인 할 수 있다.

![](https://i.imgur.com/Hm8eq10.png)

내용을 확인하고 문제가 없다면 다음 명령어 입력

```
docker compose -f local.yml up --build -d --remove-orphans
```

1. `-f local.yml`: Docker Compose 파일을 명시적으로 지정한다. 이 경우 `local.yml` 파일을 사용하여 Compose 프로젝트를 실행한다. Docker Compose 파일은 컨테이너 및 서비스 정의, 네트워크, 볼륨, 환경 변수 등을 정의하는 파일이다.
2. `up`: Docker Compose 프로젝트를 실행한다. 이 명령은 컨테이너를 빌드하고 시작하며, 서비스 간의 상호 작용을 설정하고 네트워크를 구성한다.
3. `--build`: 이 옵션은 컨테이너를 빌드해야 할 때 사용된다. 즉, 이미지가 존재하지 않거나 변경된 경우 해당 이미지를 다시 빌드한다.
4. `-d`: 이 옵션은 컨테이너를 백그라운드에서 실행한다. 컨테이너가 백그라운드에서 실행되면 터미널 창이 차지되지 않고도 도커 컨테이너의 로그를 보거나 다른 작업을 수행할 수 있다. 
5. `--remove-orphans`: 이 옵션은 현재 실행 중인 컨테이너와 관련 없는 서비스를 삭제한다. 즉, Docker Compose 파일에서 정의되지 않은 서비스 또는 컨테이너를 정리한다.


![](https://i.imgur.com/p4sBLNC.png)


![](https://i.imgur.com/LbgYqSD.png)

![](https://i.imgur.com/dPG27Bj.png)

로그 내용이 없어서 찾아보니 이슈가 있는 것 같다.

[참고](https://github.com/docker/for-win/issues/12986)

```python
$ docker compose -f local.yml logs
```

콘솔에서 확인했을 때는 잘 돌아가는 걸 확인할 수 있다.

![](https://i.imgur.com/xxXeR1y.png)

![](https://i.imgur.com/QDOnCyv.png)

![](https://i.imgur.com/IMJUM9k.png)

![](https://i.imgur.com/2s1UWaa.png)

연결이 잘 되었는지 확인

```
docker volume inspect drf_practice_1_local_postgres_data
```

![](https://i.imgur.com/pRwrGqb.png)
