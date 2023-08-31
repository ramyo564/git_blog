# 프로젝트 소개

- Version 1 -> 장고 만으로 쇼핑몰 구축
- Version 2 -> REST-API 도 함께 동작 가능하도록 구축 (유닛테스트 & 통합테스트)
	- accounts -> 80% 
	- carts -> 0%
	- category -> 0%
	- orders -> 0%
	- store -> 0 %

## 목차

## Project Structure

Upgrade_Django4

├─ accounts 
│  ├─ admin.py
│  ├─ apps.py
│  ├─ forms.py
│  ├─ helpers.py
│  ├─ models.py
│  ├─ serializers.py
│  ├─ tests.py
│  ├─ urls.py
│  ├─ views.py
│  └─ views_api.py

- accounts 앱에서는 회원가입,로그인,로그아웃,대시보드,비밀번호 변경,재설정의 기능을 다루고 있습니다.
	- admin.py
		- AccountAdmin			
			- list_display : 관리자 페이지에서 사용자 프로필 목록에 표시할 정보 설정
			- list_display_links : 사용자 목록에서 특정 열을 클릭해서 사용자 상세 페이지로 이동할 수 있는 링크 설정
			- readonly_fields : 읽기 전용 필드로 사용자 계정에 대한 마지막 로그인 시간과 가입 날짜를 변경하지 못하도록 설정
			- ordering : 사용자 목록을 가입 날짜를 기준으로 내림차순으로 정렬
		- UserProfileAdmin
			- thumnail : 사용자 프로필 사진을 원형으로 표시하는 썸네일 이미지를 생성
			- list_display : 관리자 페이지에서 사용자 프로필 목록에 표시할 정보 설정
	- apps.py
		- AccountsConfig
			- default_auto_field : 모델 클래스에 대한 기본 자동 필드 타입을 지정
			- name : 앱의 이름을 지정, 해당 앱의 모든 기능과 리소스에 접근할 때 사용
	- forms.py
		- RegistrationForm
			- 사용자의 회원가입을 위한 폼 정의
			- 비밀번호와 확인 비밀번호 필드를 비교해 일치하는지 검사
			- 사용자의 이름, 성, 전화번호, 이메일, 비밀번호를 입력받음
			- 각 필드에 대한 플레이스 홀더와 클래스를 추가하고 필드들을 form-control 클래스로 스타일링
		- UserForm
			- 사용자 프로필 수정을 위한 폼 정의
			- 사용자의 이름과 성, 젆화번호를 수정할 수 있는 필드를 제공
			- 필드들을 form-control 클래스로 스타일링
	- helpers.py
		- get_current_host
			- request.is_secure() : 현재 요청이 보안 연결인지 확인 
			- request.get_host() : 현재 요청의 도메인을 가져옴
			- 위에서 얻은 프로토콜과 호스트를 이용해서 현재 호스트의 전체 URL 생성 -> 프로토콜은 HTTP 또는 HTTPS 둘 다 가능
	- models.py
		- MyAccountManager
			- create_user() : 사용자 생성을 담당하는 메서드
			- create_superuser() : 관리자 권한과 함께 슈퍼유저를 생성하고 저장
		- Account (AbstractBaseUser 상속)
			- 사용자 계정에 대한 정보 및 권한을 관리, AbstractBaseUser 를 상속받아 원하는 필드와 메서드를 추가로 정의 가능
			- 사용자 계정과 관련된 필드들과 권한 설정, 그리고 사용자 프로필과의 일대일 관계를 설정
		- UserProfile
			- 사용자의 프로필 정보, 프로필 사진, 주소등의 사용자 정보를 관리
	- serializers.py
		- AccountSerializer
			- Account 모델을 기반으로 사용자 계정 정보를 시리얼라이즈 함
		- UserProfileSerializer
			- UserProfile 모델을 기반으로 사용자 프로필 정보를 시리얼라이즈 함
		- UserRegisterSerializer
			- 사용자 회원가입을 위함 시리얼라이저
			- 필수 필드 및 유효성 검사 규칙 정의
			- 비밀번호와 확인 비밀번호가 일치하는지 확인하는 유효성 검사 실행
		- UserLoginSerializer
			- 사용자 로그인을 위한 시리얼라이저
			- 이메일과 비밀번호 플드를 정의
		- EmailVerificationSerializer
			- 이메일 인증을 위한 시리얼라이저
			- 사용자의 UIDB86 과 토큰 정보를 포함
	- urls.py
		- 클라이언트 요청을 처리하는 뷰에 있는 함수들과 연결
	- views_api.py
		- AccountViewSet
			-  `register` : POST 요청으로 받은 사용자 정보를 이용하여 새로운 사용자를 생성하고, 회원가입 인증 이메일을 보냄. 이를 위해 `UserRegisterSerializer`를 사용하여 유효성 검사를 수행하고, 유효한 경우 사용자를 생성하고 이메일 인증 링크를 발송
			- `check_email_verification` : 이메일 인증 링크를 확인하고, 인증을 처리하는 기능을 담당, POST 요청으로 받은 인증 정보를 검증하여 사용자의 이메일 인증 상태를 업데이트함
			- `login`  : POST 요청으로 받은 로그인 정보를 검증하여 사용자를 인증하고, 유효한 경우 JWT 토큰을 발급하여 인증된 사용자에게 반환. 또한 로그인 시 세션을 유지하고, 로그인 성공 또는 실패에 따라 적절한 응답과 메시지를 전송
			- `logout` : 현재 세션을 종료하고 로그아웃 메시지를 반환함. 사용자가 인증되어 있을 때만 로그아웃이 가능하도록 제한
			- `forgotPassword` : 개발중
			- `resetpassword_validate` : 개발중
			- `change_password` : 개발중
			- `dashboard` : 개발중

			- `my_orders` : 개발중
			- `order_detail` : 개발중
			- `edit_profile` : 개발중
	- views.py
		- **`register`** : 사용자 회원가입을 처리하는 뷰, POST 요청을 통해 회원가입 정보를 받아 사용자 계정을 생성하고 이메일 인증 메일을 보내는 기능을 구현
		- **`login`** : 사용자 로그인을 처리하는 뷰. POST 요청을 통해 사용자의 이메일과 비밀번호를 받아 인증하고, 장바구니 정보를 사용자에게 연결하는 등의 기능도 포함
		- **`logout`** : 사용자 로그아웃을 처리하는 뷰. 인증된 사용자를 로그아웃시키고, 메시지를 표시
		- **`activate`** : 사용자 계정 활성화를 처리하는 뷰. 이메일 인증 링크를 통해 사용자 계정을 활성화하는 기능을 구현
		- **`dashboard`** : 사용자 대시보드 페이지를 보여주는 뷰. 사용자의 주문 목록과 프로필 정보를 표시
		- **`forgotPassword`** : 비밀번호 재설정을 위한 이메일 전송을 처리하는 뷰. 입력된 이메일로 비밀번호 재설정 링크를 보내는 기능을 구현
		- **`resetpassword_validate`** : 비밀번호 재설정을 위한 유효성 검사를 처리하는 뷰. 재설정을 위한 링크의 유효성을 검증하고 세션에 사용자 ID를 저장
		- **`resetPassword`** : 비밀번호를 재설정하는 뷰. 새로운 비밀번호를 입력받아 사용자의 비밀번호를 변경
		- **`my_orders`** : 사용자의 주문 목록을 보여주는 뷰
		- **`edit_profile`** : 사용자 프로필 정보를 수정하는 뷰. 사용자 정보와 프로필 정보를 입력받아 업데이트
		- **`change_password`** : 사용자의 비밀번호를 변경하는 뷰.
		- **`order_detail`** : 주문 상세 정보를 보여주는 뷰. 주문에 대한 상세 정보와 총 가격을 표시.


├─ carts
│  ├─ admin.py
│  ├─ apps.py
│  ├─ context_processors.py
│  ├─ models.py
│  ├─ tests.py
│  ├─ urls.py
│  └─ views.py
├─ category
│  ├─ admin.py
│  ├─ apps.py
│  ├─ context_processors.py
│  ├─ models.py
│  ├─ tests.py
│  ├─ views.py
│  └─ __init__.py
├─ data.json
├─ greatkart
│  ├─ asgi.py
│  ├─ settings.py
│  ├─ static
│  ├─ tests
│  │  ├─ conftest.py
│  │  ├─ factories.py
│  │  ├─ test_accounts
│  │  │  ├─ test_endpoints.py
│  │  │  ├─ test_forms.py
│  │  │  ├─ test_models.py
│  │  │  ├─ test_views.py
│  │  │  └─ __init__.py
│  │  └─ __init__.py
│  ├─ urls.py
│  ├─ views.py
│  ├─ wsgi.py
│  └─ __init__.py
├─ manage.py
├─ orders
│  ├─ admin.py
│  ├─ apps.py
│  ├─ forms.py
│  ├─ models.py
│  ├─ tests.py
│  ├─ urls.py
│  ├─ views.py
│  └─ __init__.py
├─ pytest.ini
├─ README.md
├─ requirements.txt
├─ static
├─ store
│  ├─ admin.py
│  ├─ apps.py
│  ├─ forms.py
│  ├─ models.py
│  ├─ tests.py
│  ├─ urls.py
│  ├─ views.py
│  └─ __init__.py
└─ templates

## ERD

## 개발 환경 구축

설치 및 띠리릴ㅇ
뭐 설치해라 어떻게 해라 띠리리릭

## 테스트 
- 유닛 테스트
- 통합 테스트

## 어플리케이션 배포

## API 엔드포인트 목록 및 사용법

## 상세 설명 (각 앱에 대한 설명)


# 매력없는 프로젝트 -> 쇼핑몰?

- 마케터로 일할 때 플랫폼 사용에 대한 한계로 아쉬웠던 경험이 많았습니다.     *안되는 게 많아서 못 할 바에 내가 직접 만든다!* 라는 생각으로 쇼핑몰을 첫 프로젝트로 만들게 되었습니다.
- 또 다른 이유로는 회원가입 기능부터 CRUD 등 기본적인 기술들을 연습하기 할 수 있다고 생각했기 때문입니다.

## 기술스택

| 개발환경   | -                |
| ---------- | ---------------- |
| 언어       | Python - 3.11      |
| 프레임워크 | Django - 4.2.2      |
| DB         | PostgreSQL - 15.3 |
| API        |       카카오페이, PayPal, Daum 주소           |
| Devops           |    AWS - EC2, S3, RDS, Route53, VPC, IAM, Beanstalk               |

# About

개발 기간 : 2023.05 ~ 2023.06   
개발 인원 : 1명 (개인 프로젝트)
사이트 바로가기 : 👉 https://yohanyohan.com/

## 목차
- [User](#user)
	- 로그인 / 로그아웃
	- 회원가입 
		- 이메일 토큰 링크를 통한 본인인증
	- 대시보드
		- 프로필, 마이페이지, 주문조회
- [Review](#review)
- [Search](#search)
- [Payment](#payment)
- [Paginator](#paginator)
- [Cart](#cart)
- [Sort by](#sort-by)


## User

> 1. 장고의 기본 BaseUserManager, AbstractBaseUser 를 이용해서 회원가입 모델을 구현했습니다.
> 2. 핸드폰 번호의 유효성 검사의 경우 `PhoneNumberField` 라이브러리를 사용해 구현했습니다.
> 3. 회원가입을 할 때 가입한 이메일로 토큰을 보내고 해당 링크로 접속했을 때의 pk와 토큰이 일치할 경우에만 본인인증이 확인되어 계정이 활성화 되도록 구현했습니다.

### 회원가입 및 본인인증
- 비밀번호 일치 및 핸드폰, 이메일 유효성 검사를 구현했습니다.
- 회원가입을 했을 경우 본인인증된 이메일을 통해서만 계정이 활성화 됩니다.
	- 회원 가입시 기재한 이메일 주소로 토큰과 uid

![register](https://github.com/ramyo564/Upgrade_Django4/assets/103474568/22cbe0ec-4c48-4646-86e9-1deb2a45b891)


### 비밀번호 찾기
- 가입한 이메일 주소가 존재할 경우 해당 이메일이 전송됩니다.
- 회원가입과 같은 방식으로 본인인증이 진행되며 본인인증이 완료되면 새로운 비밀번호를 설정 할합니다.
- 새로운 비밀번호로 로그인에 성공하면 계정이 다시 활성화됩니다.

![](https://i.imgur.com/CTAytbI.gif)

### 프로필 사진 및 비밀번호 변경

- 회원가입 때 기본으로 생성된 프로필이 변경가능하며 비밀번호도 변경이 가능합니다.

![](https://i.imgur.com/Pssv5z9.gif)

### 주문번호 확인

![](https://i.imgur.com/7tOYvOQ.gif)


## Review

> Review 기능은 크게 두 가지로 나눠서 살펴볼 수 있습니다.
> 	1. 회원과 비회원 그리고 구매자와 비 구매자를 각각 나눠서 유저의 경로가 달라집니다.
> 	2. 아이템마다 각각 달리는 리뷰 개수 와 총 별점의 평균을 나타냅니다.


### 비회원일 때 댓글을 달 수 없는 기능

- 로그인이 되어있지 않은 경우 로그인 페이지가 나옵니다.
- 로그인이 되어있는 상태이지만 물건을 구매한 적이 없다면 리뷰를 달 수 없습니다.

## 페이지
https://i.imgur.com/i1PEiZx.gif
![https://i.imgur.com/i1PEiZx.gif](https://i.imgur.com/i1PEiZx.gif)

![](https://i.imgur.com/MIfz0Jx.gif)

![](https://i.imgur.com/MlAW39u.gif)


### 회원일 때 댓글을 달 수 있는 기능
![](https://i.imgur.com/oK37hjD.gif)
- 회원일 경우 리뷰를 남길 수 있으며 리뷰를 남김과 동시에 제품에 총 리뷰 개수가 카운팅 되며 별점은 전체 별점 총 평균에 반영됩니다.

### 평균 별점 반영 및 리뷰 개수 카운팅
![](https://i.imgur.com/bfLz6oI.gif)



## Search

> 검색 기능은 판매자가 상품을 등록할 때 설명이나 제품명이 키워드에 걸리면 반영해 주는 쿼리를 반영합니다.
> 해당 쿼리에 걸리는 상품 개수를 카운팅 합니다.

![](https://i.imgur.com/vNByR9X.gif)

## Payment
>  결제 방식은 SDK 와 REST API 두 가지 방법을 사용했고
>  SDK 방식은 페이팔, REST API 방식은 카카오 페이를 선택했습니다.

### 카카오페이 

![](https://i.imgur.com/Uvn04uA.gif)

### 페이팔 

![](https://i.imgur.com/bubUb5w.gif)


## Paginator 
> 장고에서 제공하는 Paginator를 사용하여 페이지 단위를 구현했습니다.  

![](https://i.imgur.com/52uAjlm.gif)

## Cart

> 1. 장바구니에서 아이템 추가 및 삭제를 구현했습니다.
> 2. 세션을 활용하여 비로그인 상태에서 장바구니에 물건을 담았다가 로그인을 했을 때 중복된 상품이 있을 경우는 해당 상품의 개수가 늘어나고 그렇지 않은 경우에는 새로 장바구니에 추가되도록 구현했습니다.
> 3. 주소 찾기는 DAUM API를 이용했습니다. 


https://i.imgur.com/KnIAk86.gif

![](https://i.imgur.com/KnIAk86.gif)

## Sort by

> 상품을 필터링할 때 다음과 같은 알고리즘으로 만들었습니다.

![](https://i.imgur.com/qdiJxze.png)

## 페이지 구현
https://i.imgur.com/s6p3mMl.gif
![https://i.imgur.com/s6p3mMl.gif](https://i.imgur.com/s6p3mMl.gif)


## 느낀점

막상 시작해보니 모르는ㄴ게 너무 많았고
기본적인 기능은 
그동안 파이썬으로 여러가지를 만들면서 장고를 다루면서 만나는 오류들은 그렇게 어렵지는 않았지만 프론트쪽의 자바스크립트가 정말 어려웠습니다.
AWS에 서버를 계속 접하는 오류들로 자료를 뒤지면서 올리는 것도 1주일 정도 걸렸던 것 같습니다

남들처럼 게시판 만들기 정도로 하는게 내 수준에 맞지 않나 괜히 이걸로 했나 라는 생각도 들었지만 분명히 끝까지 해내면 내 성장에 도움이 된다는 생각으로 열심히 했던 것 같스빈다.