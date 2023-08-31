

## 1️. 지원자 성명 : 장요한 

안녕하세요 ! 주니어 개발자로 취업을 준비하고 있는 장요한입니다 :D

<br></br>

## 2️. 애플리케이션의 실행 방법
(엔드포인트 호출 방법 포함)

- 환경
	- python 3.11
	- Mysql  8.0.32

#### 1. 프로젝트 다운로드

```
git clone https://github.com/ramyo564/wanted-pre-onboarding-backend.git
```
- ZIP으로 다운로드 받거나 git clone을 해줍니다.


#### 2. 로컬 환경 테스트
core/settings.py

```python
# AWS SECRET_KEY
SECRET_KEY = os.environ.get("SECRET_KEY")

# AWS DB test
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'WANTED',
        'USER': os.environ.get('DB_USER_NAME'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST'),
        'PORT': '3306',
        'OPTIONS': {
            'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
        }
    }
}
# 시크릿 키

# 시크릿 키

# SECRET_KEY = 'django-insecure-uuy^a395%$pujr991+ou&*sj@=$q^gal1i6cy0t&lycpye4^)i'

# MySql 테스트

# DATABASES = {
#     'default': {
#         'ENGINE': 'django.db.backends.mysql',
#         'NAME': 'name 입력',
#         'USER': 'root',
#         'PASSWORD': 'password 입력',
#         'HOST': 'localhost',  # 또는 MySQL 호스트 주소
#         'PORT': '3306',       # MySQL 포트 번호
#     }
# }
```
-  core/settings.py 파일 안에 있는 AWS SECRET_KEY, AWS DB test 를 주석 처리해줍니다.
- 로컬 테스트에 있는 임시 시크릿 키 와 MySql 테스트 부분을 활성화 해줍니다.
- 로컬환경에서 실행하기 위해서 아래와 같이 코드를 변경해주시면 됩니다.

```python

# AWS SECRET_KEY
# SECRET_KEY = os.environ.get("SECRET_KEY")

# AWS DB test
# DATABASES = {
#     'default': {
#         'ENGINE': 'django.db.backends.mysql',
#         'NAME': 'WANTED',
#         'USER': os.environ.get('DB_USER_NAME'),
#         'PASSWORD': os.environ.get('DB_PASSWORD'),
#         'HOST': os.environ.get('DB_HOST'),
#         'PORT': '3306',
#         'OPTIONS': {
#             'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
#         }
#     }
# }

# 시크릿 키
SECRET_KEY = 'django-insecure-uuy^a395%$pujr991+ou&*sj@=$q^gal1i6cy0t&lycpye4^)i'

# 디버그 모드
DEBUG = True

# MySql 테스트
DATABASES = {
     'default': {
         'ENGINE': 'django.db.backends.mysql',
         'NAME': 'name 입력',
         'USER': 'root',
         'PASSWORD': 'password 입력',
         'HOST': 'localhost',  # 또는 MySQL 호스트 주소
         'PORT': '3306',       # MySQL 포트 번호
     }
 }
```


#### 3. 가상환경을 세팅

1. 파이썬이 설치 되었다면 터미널에서 requirements.txt가 있는 경로로 이동해줍니다. 해당 경로에서 가상환경을 만들어줍니다.
	- 가상환경 설치는 다음과 같습니다 (파이썬이 설치 되어 있어야 합니다.)

```
python -m venv venv
```

![](https://i.imgur.com/o2QZKaA.png)


- 올바른 경로로 만든게 성공했다면 requirements.txt 파일이 있는 경로에 venv 파일이 만들어집니다.

2. 가상환경 활성화

- Windows
```
source venv/Scripts/activate
```
- Mac
```
source venv/bin/activate
```

- 올바르게 실행 되었다면 터미널에 (venv)라고 터미널창에서 확인 가능합니다.

#### 4. 환경설정

1. 가상환경이 활성화 되어있다면 현재 라이브러리 목록을 확인해줍니다.

```python
pip list
```
- 새로운 환경이므로 Package 리스트에 pip, setuptools 만 보이면 정상입니다.


2. 필요한 라이브러리를 설치해줍니다.

```python
pip install -r requirements.txt
```

- 설치시 몇 분 걸릴 수 있습니다.
- 다시 pip list를 통해 requirements.txt에 있는 목록과 일치하는지 확인해줍니다.

3. migration

- settings.py 에서 로컬환경을 다시 한 번 확인해주고 문제가 없다면 패키지 설치후 밑의 명령어를 실행해줍니다. (한글로 시크릿 키와 Mysql 테스트는 주석처리 되어 있어야 오류가 없습니다 :D )

```python
python manage.py migrate
```

4. create_superuser (option)

```python
python manage.py createsuperuser
```

- 윈도우 환경에서 오류가 난다면 아래의 명령어를 사용해 보세요

```python
winpty python manage.py createsuperuser
```

- 이메일주소와 비밀번호를 입력하면 됩니다.
	- (비밀번호 생성시 터미널에서 아무 것도 안나오는 게 정상입니다 :ㅇ)

5. Run server

- 마이그레이션이 끝났다면 아래의 명령어를 실행해서 서버를 실행

```python
python manage.py runserver
```

5. End-point 호출방법

![](https://i.imgur.com/GffnW9g.png)

- `http://127.0.0.1:8000/ ` 서버가 정상적으로 실행된다면 아래의 url로 스웨거를 호출하면 됩니다.

```
http://127.0.0.1:8000/api/schema/docs/
```

![](https://i.imgur.com/KaJh9CZ.png)

- admin 환경을 보고 싶다면 

```python
http://127.0.0.1:8000/admin/
```
- 이전에 createsuperuser로 생성한 아이디와 비밀번호로 로그인하면 admin 환경에서도 데이터 베이스를 확인할 수 있습니다.
- 혹시 스웨거가 익숙하지 않으신 분은 -> 
### 1. user

1. 회원가입 기능
    - HTTP method : POST
    - end-point: `/api/user/register/`
    - json 형식으로 전송
    
    ```json
    {
        "email": "user@example.com",
        "password": "stringst"
    }
    ```
    
1. 로그인 기능
    - HTTP method : POST
    - end-point: `/api/user/login/`
    - json 형식으로 전송
    
    ```json
    {
        "email": "user@example.com",
        "password": "stringst"
    }
    ```

### 2. get_article

1. 게시글 목록 조회 (pagination 적용)
    - HTTP method : GET
    - end-point: `/api/get_article/`
        - next page: `GET /get_article/?page=2`
2. 특정 게시글 조회
    - HTTP method : GET
    - end-point: `/api/get_article/{id}/`

### 3. article

1. 게시글 생성
    - HTTP method : POST
    - end-point: `/api/article/create_post/`
    
    ```json
    - H 'Authorization: Bearer {JWT}'
    - d '{
            "title": "create title",
            "content": "create content"
        }'
    ```
    
2. 특정 게시글 수정
    - HTTP method : PATCH
    - end-point: `/api/article/{id}/update_article/`
    
    ```json
    - H 'Authorization: Bearer {JWT}'
    - d '{
            "title": "update title",
            "content": "update content"
        }'
    ```
    
3. 특정 게시글 삭제
    - HTTP method : DELETE
    - end-point: `/api/article/{id}/delete_article/`
    ```json
    - H 'Authorization: Bearer {JWT}'
    ```

<br></br>

## 3️. 데이터베이스 테이블 구조

![원티드 데이터베이스 테이블 구조](https://github.com/ramyo564/wanted-pre-onboarding-backend/assets/103474568/504e9776-a4f7-4dae-a487-1cc0f8d26de9)


<br></br>

## 4️. 구현한 API의 동작을 촬영한 데모 영상 링크

[데모 영상 링크](https://drive.google.com/file/d/13pSKPmodLz71K3XdAlkvbhOxyz1IwkS_/view)
구현링크 -> https://yohanyohan.com/api/schema/docs/

<br></br>

## 5️. 구현 방법 및 이유에 대한 간략한 설명

1. Django(DRF)와 MySQL을 사용해서 router를 구현하였고, AWS Elastic Beanstalk 를 사용해서 배포를 진행했습니다.
	- 과제에 있어서 서버 관리, 운영 체제 및 패치 관리 등이 필요 없으므로 배포와 관리가 단순한 Elastic Beanstalk 으로 배포하였습니다.
	  
2. django에 내장되어 있는 ORM을 사용했습니다.
	- 복잡한 쿼리 작성이 필요없고 데이터 베이스 레코드를 객체로 다룰 수 있으며 CRUD 작업이 단순화하기에 적합하여 내장 ORM을 사용했습니다.
	  
3. RESTful API로 설계를 위해 Django REST framework (DRF) 를 사용해서 구현했습니다. 
	- DRF로 구현한 이유는 시리얼라이저, 뷰셋, 라우터, 인증관리, 확장성, 보안, 쿼리셋 최적화등 API를 개발할 때 다양한 이점이 있고 무엇보다 현업에서 가장 많이 사용해서 선택했습니다.
	- 장고에서 이용할 수 있는 API 개발 라이브러리는 **Tastypie**, **Django Piston**, **jango JSON-RPC** 등이 있는 걸로 알고 있습니다
	  
4. drf-spectacular 를 사용해서 API 명세서 스키마 및 문서를 구현했습니다.
	- 해당 라이브러리를 사용한 이유는 다음과 같습니다.
	- API의 문서를 자동화 할 수 있는 점
	- Swagger UI 및 ReDoc가 통합되어 있어 시각적인 장점
		- 위와 같은 장점으로 클라이언트,백엔드,프론트엔드 모두 Postman과 같은 별도의 API 개발 툴 없이도 쉽게 API 개발을 확인하기 편리하다고 생각해서 사용했습니다.
		  
5. User 모델을 확장해 추가적인 필드 및 메서드를 정의했습니다.
	- 커스텀 유저 매니저를 사용한 이유는 관리자와 사용자 관련 정보를 중복 없이 하나의 테이블에 저장하고 외래키 사용시 정규화 원칙을 준수할 수 있어서 사용했습니다.
	- EmailField 를 사용해서 이메일 유효성 검사를 자동으로 수행할 수 있도록 사용했습니다. @는 자동으로 포함되는지 검사합니다.
	- password 는 8자 이상 유효성 검사는 django의 내장 클래스 MinLengthValidator 를 사용해서 구현했습니다.
	- password 암호화는 장고의 내장 함수를 사용했습니다. 장고에서는 기본적으로  [PBKDF2](https://en.wikipedia.org/wiki/PBKDF2) 알고리즘을 사용합니다.  
	  django 에서 따로 `Argon2`  ,  `bcrypt` 등의 알고리즘 사용이 가능하지만 이럴 경우 추가로 라이브러리를 설치해야되고 지금 과제에서는  `PBKDF2`  도 충분하다고 생각해서 django의 기본 내장 함수를 사용했습니다.
	  
6. JWT 구현은 `djangorestframework-simplejwt` 를 사용했습니다. 
	- 해당 라이브러리를 사용한 이유는 DRF에서 원활하게 사용 가능하고 적은 코드로 JWT 인증 구현이 가능해서 선택했습니다.
	- 뷰에서 이메일주소와 비밀번호의 유효성을 통과하면 access token 과 refresh token을 발급해줍니다.
  - 현 과제에 적합하게 access token 발급 기간을 길게 설정했습니다.
	  
7. 페이지네이션은 DRF에서 제공하는 `PageNumberPagination` 기능을 사용해서 구현했습니다.
	- API 엔드포인트를 통해 게시글 목록이 요청되면 `ArticleListViewSet` 에 정의된 쿼리셋  `queryset` 을 사용해서 Article 모델의 모든 게시글 데이터를 가져옵니다.
	- `CustomPagination` 클래스에서 정의된 페이지 네이션 로직에 따라 `page_size`에 설정된 값이 데이터 페이지별로 분할됩니다.
	- 해당 기능을 사용한 이유는 개발속도와 편의성 때문에 사용했습니다.
	  
8. 특정 게시글 조회는 뷰셋의 `ReadOnlyModelViewSet` 상속받아 간단하게 구현했습니다.
	-  `ArticleListViewSet` 는 `ReadOnlyModelViewSet` 를 상속받았기 때문에 `Article` 모델의 데이터를 `ArticleSerializer` 를 사용해 직렬화에 클라이언트에 반환 합니다.
	- 특정 게시글의 id 정보와 함께 요청이 들어오면 `get_serializer_class(self)` 메서드에서 정의한`CustomArticleSerializer` 가 사용됩니다.
	- `CustomArticleSerializer` 는 `Article` 의 `author`  필드가 참조하는 `CustomUser` 모델의 `email` 을 참조해 해당 게시글에 대한 데이터 값을 직렬화 합니다.
	  
9.  게시글 수정은 PATCH 메서드를 사용했습니다.
	- PATCH 를 사용한 이유는 기존의 글을 수정하는 부분이므로 리소스의 일부분만 업데이트 하는 PATCH를 사용했습니다.
	- 또한 PATCH는 전체를 업데이트 하는 PUT 보다는 데이터 양의 절약과 네트워크 부하 감소에 대한 장점이 있어서 PATCH로 사용했습니다.
	- 구현은 DRF 에서 제공하는 퍼미션 클래스  `IsAuthenticated` 를 사용해서 권한이 있는 사용자만 접근을 허용하고 업데이트할 때 JWT 토큰과 함께 반환하여 게시글 수정을 업데이트 합니다. 토큰이나 유저정보가 유효하지 않는다면 권한 오류를 반환합니다.
	  
10. 게시글 삭제는 위와 같은 로직으로 토큰을 발급받아 권한이 확인된 유저만이 데이터를 삭제할 수 있게 구현했습니다.

<br></br>

## 6️. API 명세서

API 명세서 스키마 다운로드 -> https://yohanyohan.com/api/schema/

| HTTP method | 기능                          | end-point                         | auth required |
| ----------- | ----------------------------- | --------------------------------- | ------------- |
| POST        | 회원가입                      | /api/user/register                | X             |
| POST        | 로그인                        | /api/user/login                   | X             |
| GET         | 게시글 목록 조회 (pagination) | /api/get_article                  | X             |
| GET         | 특정 게시글 조회              | /api/get_article/{id}/            | X             |
| POST        | 새로운 게시글 생성            | /api/article/create_post/         | O             |
| PATCH       | 특정 게시글 수정              | /api/article/{id}/update_article/ | O             |
| DELETE      | 특정 게시글 삭제              | /api/article/{id}/delete_article/ | O              |

  
### **1. user**
#### **1. 회원가입**
- End-point: `/api/user/register/`
- Request
    - Method: `POST`
    - Permissions: `AllowAny`
    - Request Body

```json
{
  "email": "user@example.com",
  "password": "stringst"
}
```

- Response
    1. Successful Response

```json
Code 201
{
  "message": "유저 등록 성공"
}
```

    2. Error Response
        - 이미 있는 email로 입력한 경우

```json
Code 400 Error: Bad Request
{
  "email": [
    "custom user with this email already exists."
  ]
}
```

	3. Error Response
		- @가 없는 경우

```json
Code 400 Error: Bad Request
{
  "email": [
    "Enter a valid email address."
  ]
}
```

	4. Error Response
		- 이메일 혹은 패스워드를 빈 칸으로 두었을 경우

```json
Code 400 Error: Bad Request
{
  "email": [
    "This field may not be blank."
  ],
  "password": [
    "This field may not be blank."
  ]
}
```

	5. Error Response
		- 페스워드 8자 미만

```json
Code 400 Error: Bad Request
{
  "password": [
    "Ensure this field has at least 8 characters."
  ]
}
```


#### **2. 로그인**
- End-point: `/api/user/login/`
- Request
    - Method: `POST`
    - Permissions: `AllowAny`
    - Request Body

```json
{
  "email": "user@example.com",
  "password": "stringst"
}
```

- Response
    1. Successful Response
        - JWT(JSON Web Token) 생성 후 발급

```json
Code 200
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNjkyMTI1MTc0LCJpYXQiOjE2OTIwMzg3NzQsImp0aSI6IjczNzgwOTM2NDJiNTQ0ZTM5Yzg5OWYyMGM3NmI2N2VjIiwidXNlcl9pZCI6Mn0.fXmAj6OqBGbaZ_VZ8QEwJiLfcsZHHTmMCbWjuW6s_dg",
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTY5MzMzNDc3NCwiaWF0IjoxNjkyMDM4Nzc0LCJqdGkiOiJmNWRiMmQ0NTExZmY0ZmU2OTEwZGI3ODM2NWIyMjRkMiIsInVzZXJfaWQiOjJ9.Fw_0EgbV5wJos5E41Uh58rLX0mdCShynJRtsI194SEw",
  "message": "로그인 성공"
}
```


    1. Error Response
        - email or password를 입력하지 않은 경우

```json
Code 400 Error: Bad Request
{
  "email": [
    "This field may not be blank."
  ],
  "password": [
    "This field may not be blank."
  ]
}
```

    3. Error Response
        - (유효성 검사) email은 맞게 입력했으나 password가 8자 미만 및 틀린 비밀번호

```json
Code 401 Error: Unauthorized
{
  "error": "이메일 혹은 비밀번호가 유효하지 않습니다."
}
```

### **2. get_article**

#### **1. 게시글 목록 조회 (pagination 적용)**
- End-point: `/api/get_article/`
- Request
    - Method: `GET`
    - Permissions: `AllowAny`
- Response
    1. Successful Response
        - page num = 2

```json
Code 200
{
  "count": 3,
  "next": "http://127.0.0.1:8000/api/get_article/?page=2",
  "previous": null,
  "results": [
    {
      "id": 1,
      "author": "user@example.com",
      "title": "sssss",
      "content": "string",
      "created_at": "2023-08-14T09:52:08.342076Z",
      "updated_at": "2023-08-14T10:09:09.237076Z"
    },
    {
      "id": 2,
      "author": "user@example.com",
      "title": "string",
      "content": "string",
      "created_at": "2023-08-14T09:52:10.472070Z",
      "updated_at": "2023-08-14T09:52:10.472070Z"
    }
  ]
}
```

    1. Error Response
        - 페이지가 존재하지 않을 경우

```json
Code 404 Error: Not Found
{
  "detail": "Invalid page."
}
```

#### **2. 특정 게시글 조회**

**2. 게시글 생성**
- End-point: `/api/get_article/{id}/`
	  EX) `/api/get_article/1/`
- Request
    - Method: `GET`
    - Permissions: `AllowAny`
    - Request Body
- Response
    1. Successful Response

```json
{
Code 200
  "id": 1,
  "author": "user@example.com",
  "title": "sssss",
  "content": "string",
  "created_at": "2023-08-14T09:52:08.342076Z",
  "updated_at": "2023-08-14T10:09:09.237076Z"
}
```

    1. Error Response
        - 페이지가 존재하지 않을 경우

```json
Code 404 Error: Not Found
{
  "detail": "Invalid page."
}
```

### **3. article**

#### **1. 게시글 생성**
- End-point: `/api/article/create_post/`
- Request
    - Method: `POST`
    - Permissions: `IsAuthenticated`
    - Headers: 'Authorization: Bearer {JWT}'
    - Request Body

```json
{
  "title": "string",
  "content": "string"
}
```

- Response
    1. Successful Response
        - 게시글 생성 완료

```json
Code 201
{
  "id": 4,
  "title": "string",
  "content": "string",
  "created_at": "2023-08-14T19:10:57.437353Z",
  "updated_at": "2023-08-14T19:10:57.437353Z"
}
```

    1. Error Response
        - title 혹은 content를 입력하지 않은 경우

```json
Code 400 Error: Bad Request
{
  "title": [
    "This field may not be blank."
  ],
  "content": [
    "This field may not be blank."
  ]
}
```

    2. Error Response
        - 로그인 하지 않았을 경우

```json
Code 401 Error: Unauthorized
{
  "detail": "Authentication credentials were not provided."
}
```


#### **2. 게시글 수정**
- End-point: `/api/article/{id}/update_article/`
- Request
    - Method: `PATCH`
    - Permissions: `IsAuthenticated`
    - Headers: 'Authorization: Bearer {JWT}'
    - Request Body

```json
{
  "title": "string",
  "content": "string"
}
```

- Response
    1. Successful Response

```json
Code 200
{
  "id": 1,
  "title": "string",
  "content": "string",
  "created_at": "2023-08-14T09:52:08.342076Z",
  "updated_at": "2023-08-14T19:16:51.464804Z"
}
```

    1. Error Response
        - 제목 혹은 내용이 비어있을 경우

```json
Code 400 Error: Bad Request
{
  "title": [
    "This field may not be blank."
  ],
  "content": [
    "This field may not be blank."
  ]
}
```

    2. Error Response
        - 로그인 하지 않았을 경우

```json
Code 401 Error: Unauthorized
{
  "detail": "Authentication credentials were not provided."
}
```

    3. Error Response
        - 권한이 없는 유저가 게시글을 업데이트 하려고 할 경우

```json
Code 403 Error: Forbidden
{
  "detail": "해당 글을 업데이트할 권한이 없습니다."
}
```

    4. Error Response
        - 존재하지 않는 게시글을 업데이트 하려고 할 경우

```json
Code 404 Error: Not Found
{
  "detail": "Not found."
}
```

#### **3. 게시글 삭제**
- End-point: `/api/article/{id}/delete_article/`
- Request
    - Method: `DELETE`
    - Permissions: `IsAuthenticated`
    - Headers: 'Authorization: Bearer {JWT}'

- Response
    1. Successful Response

```json
code 204
```

    1. Error Response
        - 로그인 하지 않은 경우

```json
Code 401 Error: Unauthorized
{
  "detail": "Authentication credentials were not provided."
}
```

    2. Error Response
        - 권한이 없는 유저가 게시글을 삭제를 하려고 할 경우

```json
Code 403 Error: Forbidden
{
  "detail": "해당 글을 삭제할 권한이 없습니다."
}
```

    3. Error Response
        - 존재하지 않는 게시글을 삭제를 하려고 할 경우

```json
Code 404 Error: Not Found
{
  "detail": "Not found."
}
```



<br></br>

## 가산점 추가 부분

### 1. 🔫 통합 테스트 또는 단위 테스트 코드
<! 테이터베이스를 꼭 연결 한 후에!!>

![테스트코드](https://github.com/ramyo564/wanted-pre-onboarding-backend/assets/103474568/4f4efab0-852e-4347-ae70-5df459b599ab)
pytest를 통해 모델과 엔드포인트 테스트를 진행했습니다.
너무 늦게 알게되서 제가 얻을 수 있는 가산점을 최대한 얻기 위해서 시작했지만 막상 테스트 코드를 작성하면서
그 동안 제가 알고 있었다고 착각하고 있었던 부분도 많이 확인하게 되어서 정말 좋은 시간이였습니다!
테스트코드를 계속 붙잡고 있다가는 기간안에 제출하지 못 할 것 같아 User 모델 부분을 마무리 못 하게 되어
Coverage 100% 를 못 채우고 우선 제출하게 되었습니다.


### 2. 🚀 AWS EC2 배포
- [서비스 배포 link](https://yohanyohan.com/api/schema/docs/)
- [API 스키마 다운로드](https://yohanyohan.com/api/schema/)
- 접속하시면 별도의 환경 설정 없이 바로 API 테스트를 진행하실 수 있습니다.

#### 서비스 아키텍쳐

![원티드 서비스 아키텍쳐](https://github.com/ramyo564/wanted-pre-onboarding-backend/assets/103474568/a8abfe94-c6ec-431d-8a76-83d032cfecd4)



# API 제작 과제 기본 요구사항
https://github.com/Hounds1/wanted-pre-onboarding-backend#api-%EC%A0%9C%EC%9E%91-%EA%B3%BC%EC%A0%9C-%EA%B8%B0%EB%B3%B8-%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD


======      

https://github.com/ramyo564/wanted-pre-onboarding-backend/tree/main#1%EF%B8%8F-%EC%A7%80%EC%9B%90%EC%9E%90-%EC%84%B1%EB%AA%85--%EC%9E%A5%EC%9A%94%ED%95%9C

https://github.com/ramyo564/wanted-pre-onboarding-backend/tree/main#2%EF%B8%8F-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%EC%9D%98-%EC%8B%A4%ED%96%89-%EB%B0%A9%EB%B2%95

https://github.com/ramyo564/wanted-pre-onboarding-backend/tree/main#3%EF%B8%8F-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%ED%85%8C%EC%9D%B4%EB%B8%94-%EA%B5%AC%EC%A1%B0

https://github.com/ramyo564/wanted-pre-onboarding-backend/tree/main#4%EF%B8%8F-%EA%B5%AC%ED%98%84%ED%95%9C-api%EC%9D%98-%EB%8F%99%EC%9E%91%EC%9D%84-%EC%B4%AC%EC%98%81%ED%95%9C-%EB%8D%B0%EB%AA%A8-%EC%98%81%EC%83%81-%EB%A7%81%ED%81%AC

https://github.com/ramyo564/wanted-pre-onboarding-backend/tree/main#5%EF%B8%8F-%EA%B5%AC%ED%98%84-%EB%B0%A9%EB%B2%95-%EB%B0%8F-%EC%9D%B4%EC%9C%A0%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B0%84%EB%9E%B5%ED%95%9C-%EC%84%A4%EB%AA%85

https://github.com/ramyo564/wanted-pre-onboarding-backend/tree/main#6%EF%B8%8F-api-%EB%AA%85%EC%84%B8%EC%84%9C

https://github.com/ramyo564/wanted-pre-onboarding-backend/tree/main#%EA%B0%80%EC%82%B0%EC%A0%90-%EC%B6%94%EA%B0%80-%EB%B6%80%EB%B6%84

지원자 서명 장요한 부분

4., 환경설정 부분 정리ㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇ

혹시 스위거가 익숙하지 앟으신분들은 링크 첨부
