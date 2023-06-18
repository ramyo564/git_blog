---

layout: single
title: " [Django DRF] Django 기본 패키지 (1) "
categories: Django
tag: [Python,"[Django DRF] Project eCommerce RESTful API",]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# 장고 기본 패키지는 뭘까?

Package    Version
---------- -------
asgiref    3.7.2
Django     4.2.2
pip        23.1.2
setuptools 65.5.0
sqlparse   0.4.4
tzdata     2023.3

현재 장고를 설치하면  장고 4.2.2 버전 기준으로 깔리며 위와 같은 패키지가 형성된다.

위와 같은 패키지가 뭔지 궁금하면 pypi에 검색하면 된다.

[pypi](https://pypi.org/)

Python Package Index(Python Package Index)는 Python의 공식 소프트웨어 저장소다.
pip를 이용해 설치하는 패키지들은 모두 pypi에 있다
numpy랑 비슷하다.
  
인덱스로 PyPI를 사용하면 자유 소프트웨어 라이센스 또는 POSIX와의 호환성 같은 메타데이터에 대해 키워드를 기준으로 패키지를 검색하거나 필터를 통해 패키지를 검색할 수 있다.

서로 다른 운영 체제 및 Python 버전에 따라 다른 형식이 있다.

## asgiref

[asgiref](https://pypi.org/project/asgiref/)
장고를 돌아가게 하는 패키지중 하나로 템플릿등을 브라우저에 리턴해주고 장고랑 커뮤니케이션을 하는 패키지다.

## sqlparse

[sqlparse](https://pypi.org/project/sqlparse/)
sqlparse is a non-validating SQL parser for Python. It provides support for parsing, splitting and formatting SQL statements.
장고가 작업 시 일반적인 SQL 작업을 수행할 수 있도록 도와주는 기능을 제공하는 모듈이다.

이런식으로 패키지로 깔리는게 어떤 작업을 수행하는지 간략하게 볼 수 있다.