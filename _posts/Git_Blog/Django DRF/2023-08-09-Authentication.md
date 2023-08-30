---

layout: single
title: " [Django DRF] React DjangoDRF project (5) "
categories: Django
tag: [Python,"[Django DRF] DjangoDRF + React chat project",]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---

# Authentication


## Build: Installing Dajngorestframework - simplejwt

[SimpleJWT](https://django-rest-framework-simplejwt.readthedocs.io/en/latest/)

```
pip install djangorestframework-simplejwt
```

settings.py

```python
REST_FRAMEWORK = {

    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',

    'DEFAULT_AUTHENTICATION_CLASSES': [

        # 'rest_framework.authentication.SessionAuthentication',

        'rest_framework_simplejwt.authentication.JWTAuthentication',

    ]

}
```

기존 세션방식은 주석처리

urls.py
{% raw %}
```python
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView,
)

urlpatterns = [
    ...
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    ...
]
```

Simple JWT를 사용하는 것은 Json 웹 토큰 인증을 배우는 좋은 방법이면서 JWT HTTP only 쿠키를 구현해 애플리케이션을 확장하는 방법을 고려할 수 있다.
### JWT란?

JWT는 JSON Web Token의 약자로, 두 개체 간에 정보를 안전하게 전달하기 위한 간결하고 자체 포함적인 방식이다. 인증의 맥락에서 JWT는 서버와 클라이언트 간에 사용자 정보를 안전하게 전달하여 클라이언트가 서버에 자신을 인증하고 보호된 리소스에 접근할 수 있도록 한다.

JWT 인증의 동작 방식을 간단히 설명하면 다음과 같다:

1. **인증 과정**:
    
    - 사용자가 자신의 자격 증명(예: 사용자 이름과 비밀번호)을 서버에 전송한다.
    - 서버는 자격 증명을 검증하고, 올바른 경우 JWT를 생성한다.
    - JWT는 클라이언트에게 다시 전송된다.
2. **JWT 구조**:
    
    - JWT는 Header, Payload, Signature 세 부분으로 구성된다.
    - Header에는 토큰의 유형과 서명 알고리즘 등의 정보가 들어 있다.
    - Payload에는 클레임(Claim)이 들어 있으며, 사용자와 관련된 정보와 추가 데이터를 담는다.
    - Signature는 토큰의 발송자가 자신이 주장하는 사람임을 검증하고 메시지가 전송 중에 변경되지 않았음을 확인하는 데 사용된다.
3. **상태 없음과 자체 포함**:
    
    - JWT는 상태 없음(Stateless)으로 동작한다. 즉, 서버는 사용자 세션에 대한 정보를 저장할 필요가 없다. 필요한 모든 정보가 JWT 자체에 포함되어 있다.
    - JWT는 자체 포함(Self-contained)으로 동작한다. 즉, 사용자 인증에 필요한 모든 데이터가 토큰에 포함되어 있으므로 클라이언트는 토큰을 보유하고 이를 사용하여 후속 요청을 인증할 수 있다.
4. **권한과 클레임**:
    
    - 서버는 JWT Payload에 다양한 클레임을 포함하여 사용자의 역할, 권한 및 기타 관련 정보를 표현할 수 있다.
    - 이러한 클레임은 서버가 클라이언트가 특정 리소스에 접근할 수 있는 권한이 있는지를 결정하는 데 사용된다.
5. **토큰 검증**:
    
    - 클라이언트가 후속 요청을 서버에 보낼 때, 토큰을 요청 헤더에 포함시킨다(일반적으로 "Authorization" 헤더에 포함된다).
    - 서버는 그러면 토큰의 서명을 검증하여 토큰의 정품성과 무결성을 확인한다.
    - 서명이 유효하면 서버는 토큰에 포함된 정보를 신뢰하고 요청을 처리한다.
6. **토큰 만료와 갱신**:
    
    - JWT는 만료 시간을 가질 수 있습니다("exp" 클레임에서 정의됨). 이를 통해 토큰이 일정 기간 동안만 유효하도록 할 수 있다.
    - 토큰이 만료되면 클라이언트는 리프레시 토큰(제공된 경우)을 사용하여 새로운 액세스 토큰을 요청할 수 있다. 리프레시 토큰은 사용자가 다시 로그인할 필요 없이 새로운 액세스 토큰을 얻는 데 사용된다.
7. **사용 사례**:
    
    - 인증(Authentication): JWT는 웹 애플리케이션과 API에서 사용자 인증에 널리 사용된다. 세션 저장소 없이 사용자가 보호된 리소스에 접근할 수 있도록 해준다.
    - 단일 로그인(SSO): JWT는 단일 로그인 시나리오에서 사용될 수 있으며, 사용자가 한 번 로그인하면 여러 애플리케이션이나 서비스에 접근할 수 있다.
    - 정보 교환: JWT는 사용자 정보를 안전하고 간결하게 교환하기 위해 사용되며, 사용자와 파티 간의 정보 교환에 적합하다.
    - 모바일 애플리케이션: JWT는 모바일 애플리케이션에서 사용자를 인증하고 API에 접근하는 데 자주 사용된다.
8. **보안 고려 사항**:
    
    - JWT를 전송할 때는 반드시 HTTPS를 사용하여 무단 인터셉션을 방지해야 한다.
    - Payload에 민감한 데이터를 포함시킬 때는 주의해야 한다. JWT는 데이터를 암호화하지 않기 때문에 민감한 정보는 포함시키지 않는 것이 좋다. JWT는 가벼운 데이터용으로 사용하고 비밀번호와 같은 민감한 정보를 저장하지 않도록 한다.
    - 토큰의 만료 시간은 신중하게 고려해야 한다. 만료 시간을 짧게 설정하면 보안성이 향상되지만, 사용자가 자주 새로운 토큰을 요청해야 할 수 있다.

종합적으로, JWT 인증은 간단함, 간결함, 그리고 유연성을 갖춘 특징으로 인해 널리 사용된다. 이를 통해 현대적인 웹 애플리케이션에서 안전하고 효율적인 사용자 인증과 권한 부여를 할 수 있다.     


JWT는 간결하고 안전한 특징으로 인해 인증 메커니즘으로 널리 사용되며, 현대적인 웹 애플리케이션과 API와의 통합이 쉽다.      

### 무결성 보장

무결성 보장(Integrity)은 데이터가 원본과 변경되지 않은 상태로 유지되는 것을 의미한다. 데이터의 무결성이 보장되면 데이터가 변조되거나 손상되지 않는다. JWT에서 무결성은 서명(Signature)을 통해 보장된다.     

JWT는 데이터를 담고 있는 Payload와 서명 부분으로 구성된다. 서버는 Payload에 사용자 정보와 클레임(Claim)을 포함시킨다. 그리고 서명은 해당 Payload의 무결성을 검증하는 데 사용된다. 서명은 비밀 키(Secret Key)를 사용하여 생성되며, 비밀 키는 서버만 가지고 있다. 이로 인해 클라이언트는 토큰을 볼 수 있지만, 서명을 검증하거나 변경할 수 없다.     

따라서 JWT를 사용하면 서버는 토큰을 생성할 때 Payload에 포함된 정보를 서명하여 무결성을 보장한다. 클라이언트가 서버로 요청을 보낼 때, 서버는 해당 요청에 포함된 토큰의 서명을 확인하여 토큰이 변경되지 않았음을 검증한다. 이를 통해 데이터의 변조나 위조를 방지하고 안전한 통신을 보장할 수 있다.     


### 일반 세션과 JWT 비교 

일반 세션과 JWT의 차이점과 JWT가 더 좋은 점은 다음과 같다:

1. **상태(State)의 보존**:
    
    - 일반 세션: 서버는 사용자 세션을 유지하기 위해 상태를 서버 측에 저장해야 합니다. 이로 인해 서버의 리소스 사용과 확장성에 영향을 미칠 수 있다.
    - JWT: JWT는 상태 없음(Stateless)으로 동작한다. 서버는 토큰을 생성하여 클라이언트에게 전달하고, 클라이언트가 토큰을 보유하고 있으며, 토큰을 통해 인증을 처리한다. 따라서 서버는 사용자 상태를 저장할 필요가 없으며, 확장성이 용이하다.
2. **세션 저장소 필요성**:
    
    - 일반 세션: 사용자 세션 데이터를 저장하기 위해 별도의 세션 저장소가 필요하다.
    - JWT: JWT는 토큰 자체에 사용자 정보를 담고 있기 때문에 세션 저장소가 필요하지 않다. 이로 인해 서버 구성이 간단해지고 유지보수가 용이하다.
3. **확장성**:
    
    - 일반 세션: 세션 저장소의 용량이 한계가 있을 수 있고, 서버 간의 상태 공유에 문제가 생길 수 있다.
    - JWT: 상태 없음(Stateless)으로 동작하는 JWT는 서버 간의 상태 공유가 필요 없으므로 확장성이 용이하다.
4. **마이크로서비스와 API**:
    
    - JWT: JWT는 토큰 자체에 인증 정보를 포함하고 있으므로 마이크로서비스 아키텍처와 RESTful API에 적합하다. 서비스 간에 JWT를 전달하고 사용자 인증을 처리하기 용이하다.
    - 일반 세션: 세션은 일반적으로 서비스 내부에서만 사용되며 서비스 간에 공유하기 어렵다.

JWT는 세션을 사용하는 전통적인 방식과 달리 클라이언트-서버 간의 상태 보존을 요구하지 않고, 보안적으로 강력한 서명을 통해 무결성을 보장합니다. 또한 클라이언트와 서버 간의 데이터 교환을 간소화하고 상태를 서버 측에서 저장하지 않기 때문에 확장성과 유지보수가 편리하다. 따라서 JWT는 현대적인 웹 애플리케이션과 API에서 많이 사용되며, 보안적인 요구사항을 충족하면서도 효율적인 인증 방법으로 인정받고 있다.      

## Theory: Refresh and Access Tokens

[JWT.io](https://jwt.io/)

![](https://i.imgur.com/KPUi819.png)


[JWT](https://www.freecodecamp.org/news/how-to-sign-and-validate-json-web-tokens/)

![JWT](https://www.freecodecamp.org/news/content/images/2023/01/token-based-authentication.jpg)


[Api manager Refresh Token Grant](https://apim.docs.wso2.com/en/latest/design/api-security/oauth2/grant-types/refresh-token-grant/)
![Refresh Token Grant](https://apim.docs.wso2.com/en/4.2.0/assets/img/learn/oauth-refresh-token-diagram.png)

(복습) 기본적인 JWT의 작동 방식은 다음과 같다:

1. 클라이언트(React 애플리케이션 등)가 사용자의 로그인 정보(사용자명과 비밀번호)를 서버로 전송한다. (보통 local storage에 저장)
2. 서버는 해당 정보를 검증하고, 유효한 경우, 액세스 토큰과 리프레시 토큰을 클라이언트에게 전달한다.
3. 클라이언트는 액세스 토큰을 로컬 저장소 등에 저장하여 보관한다.
4. 이후 클라이언트가 서버에 요청을 보낼 때마다 액세스 토큰을 요청과 함께 보내게 된다.
5. 서버는 클라이언트가 보낸 토큰의 유효성을 검증하고, 유효한 경우 해당 요청에 대한 접근 권한을 부여한다.

JWT는 상태를 저장하지 않고, 클라이언트와 서버 간의 통신에서 토큰을 사용하여 인증을 처리하는 상태 없는 방식이다. 액세스 토큰은 클라이언트가 인증된 상태로 서버에 요청을 보낼 때 함께 전달되며, 서버에서 이 토큰을 확인하여 사용자가 로그인한 상태인지 확인한다.       

액세스 토큰은 짧은 유효 기간을 가지며(예: 2분 또는 15분), 만료되면 서버에서 더 이상 유효하지 않다는 응답을 받게 된다.
토큰이 만료되면 서버에 다시 요청하여 리프레시 토큰을 사용하여 새로운 액세스 토큰을 발급받는다. 이렇게 함으로써 유저는 장기간 로그인 상태를 유지할 수 있다.     

리프레시 토큰은 액세스 토큰보다 더 오래 지속되는 특성을 가지므로 보안상의 이유로 액세스 토큰의 유효 기간을 짧게 설정하여 토큰이 노출되었을 때 더 작은 영향을 받도록 한다. 리프레시 토큰은 재사용을 피하기 위해 블랙리스트에 등록되거나 토큰 취소 기능이 구현될 수 있다.     


이렇게 JWT를 사용하여 클라이언트와 서버 간에 인증을 관리하면, 서버는 사용자의 세션 상태를 유지하지 않고도 유저가 로그인한 상태를 확인할 수 있다. 클라이언트는 액세스 토큰을 이용해 서버로 보내는 요청을 인증하고, 서버는 토큰의 유효성을 확인하여 액세스 권한을 부여한다.      


이는 보안에 대한 기본적인 개념이며 보다 심층적인 보안 메커니즘과 함께 사용해야 한다. 보안적인 측면을 고려하여 토큰 저장 방식, 안전한 전송, 토큰 폐기 메커니즘 등을 고려해야 한다. 보안은 매우 중요한 주제이며 토큰 기반 인증 외에도 다양한 인증 방식을 고려하여 토큰 탈취와 무단 액세스로부터 보호해야 한다.
## Build: Creating the Login Form

[FORMIK](https://formik.org/)

```
npm i formik
```

Formik은 React 애플리케이션에서 폼을 구축하는 데 사용되는 인기있는 라이브러리로, 폼 상태, 유효성 검사, 제출 등을 처리하는 데 도움이 되는 유틸리티와 컴포넌트를 제공한다. 이를 통해 코드 작성을 줄이고 코드의 재사용성을 높이며, 폼과 관련된 작업을 더욱 편리하게 처리할 수 있다.     

Formik 라이브러리를 설치하고, React 애플리케이션에 폼을 만들기 위해 새로운 컴포넌트를 생성하고, Formik을 이용해 폼 상태와 제출을 다루는 함수를 작성한다. 폼에는 유저명과 비밀번호를 입력할 수 있는 두 개의 입력 필드와 로그인 버튼이 포함된다.     

### Login.tsx

```typescript
import { useFormik } from "formik";
import { useNavigate } from "react-router-dom";

const Login = () => {
    const navigate = useNavigate();
    const formik = useFormik({
        initialValues: {
            username: "",
            password: "",
        },
        onSubmit: () => {},
    });
    
    return (
        <div>
            <h1>Login</h1>
            <form onSubmit={formik.handleSubmit}>
                <label>username</label>
                <input
                    id="username"
                    name="username"
                    type="text"
                    value={formik.values.username}
                    onChange={formik.handleChange}
                ></input>
                <label>password</label>
                <input
                    id="password"
                    name="password"
                    type="password"
                    value={formik.values.password}
                    onChange={formik.handleChange}
                ></input>
                <button type="submit">Submit</button>
            </form>
        </div>
    
    );
};

export default Login
```

## Build: Authentication Context and Authentication Services

### auth-service.d.ts

```typescript
export interface AuthServiceProps {

    login: (username: string, password: string) => any;


}
```


### AuthContext.tsx

```typescript
import React, { createContext, useContext } from "react";

import { AuthServiceProps } from "../@types/auth-service";

import { useAuthService } from "../services/AuthServices";

  

const AuthServiceContext = createContext<AuthServiceProps | null>(null);

  

export function AuthServicProvicer(props: React.PropsWithChildren<{}>) {

    const authServices = useAuthService();

  

    return(

    <AuthServiceContext.Provider value={authServices}>

        {props.children}

    </AuthServiceContext.Provider>

    );

}

  

export function useAuthServiceContext(): AuthServiceProps {

    const context = useContext(AuthServiceContext);

  

    if (context === null){

        throw new Error("Error - You have to use the AuthServiceProvider");

    }

    return context;

}
```

`AuthServiceContext` 는 `AuthServiceProps` 타입을 갖는데 초기값으로 null 을 설정한다.     

`AuthServicProvicer` 컴포넌트는 `props`로 `React.PropsWithChildren<{}>` 타입을 받는다. 이 컴포넌트는 `useAuthService` 훅을 사용하여 `authServices`를 생성하고, 이를 `AuthServiceContext`의 `Provider`로 제공한다. 이로써 이 컨텍스트 아래에 있는 모든 자식 컴포넌트들은 `authServices`에 접근할 수 있다.     

`useAuthServiceContext` 커스텀 훅은 `AuthServiceContext`를 사용하여 `authServices`를 얻어온다. 만약 컨텍스트가 `null`이라면, 에러를 발생시키고 그렇지 않다면 `authServices`를 반환한다.     

종합적으로 이 코드는 `AuthServiceContext`를 사용하여 인증 서비스를 관리하고, `AuthServicProvicer`로 감싸진 컴포넌트들은 `useAuthServiceContext` 훅을 사용하여 인증 서비스에 접근할 수 있게 된다. 이렇게 구현된 컨텍스트를 활용하여 인증 기능을 각 컴포넌트에서 공유하고 사용할 수 있다.

### Login.txs

```typescript
import { useFormik } from "formik";

import { useNavigate } from "react-router-dom";

import { useAuthServiceContext } from "../context/AuthContext";

  

const Login = () => {

    const { login } = useAuthServiceContext();

    const navigate = useNavigate();

    const formik = useFormik({

        initialValues: {

            username: "",

            password: "",

        },

        onSubmit: async (values) => {

            const { username, password } = values;

            const res = await login(username, password);

            if (res) {

                console.log(res);

            } else {

                navigate("/");

            }

        },

    });

    return (

        <div>

            <h1>Login</h1>

            <form onSubmit={formik.handleSubmit}>

                <label>username</label>

                <input

                    id="username"

                    name="username"

                    type="text"

                    value={formik.values.username}

                    onChange={formik.handleChange}

                ></input>

                <label>password</label>

                <input

                    id="password"

                    name="password"

                    type="password"

                    value={formik.values.password}

                    onChange={formik.handleChange}

                ></input>

                <button type="submit">Submit</button>

            </form>

        </div>

    );

};

  

export default Login
```

1. `useAuthServiceContext()`를 사용하여 인증 서비스의 `login` 함수를 가져온다.
    
2. `useNavigate()`를 사용하여 리액트 라우터의 `navigate` 함수를 가져온다.
    
3. `useFormik()` 훅을 사용하여 폼 상태와 제출 핸들러를 설정한다. `initialValues`로 초기값을 설정하고, `onSubmit`에서는 폼이 제출되었을 때 호출될 콜백 함수를 정의한다.
    
4. 폼의 `onSubmit` 핸들러에서는 `login` 함수를 호출하여 폼 데이터를 인증 서비스로 전달한다. 만약 인증에 성공하면 `res`에는 인증에 대한 응답 데이터가 있을 것이고, 이를 출력합니다. 그렇지 않으면 "/" 경로로 리다이렉트한다.
    
5. 리턴된 JSX에서는 폼 요소들을 렌더링하고, `formik` 객체를 사용하여 폼의 값과 핸들러들을 연결한다.
    

이렇게 구현된 `Login` 컴포넌트는 로그인 폼을 표시하고, 폼이 제출되면 인증 서비스로 데이터를 전달하여 로그인을 시도하고, 인증 결과에 따라 다른 동작을 수행한다.

### AuthServices.ts

```typescript
import axios from "axios";

import { AuthServiceProps } from "../@types/auth-service";

  
  

export function useAuthService(): AuthServiceProps {

    const login = async (username: string, password: string) => {

        try {

            const response = await axios.post(

                "http://127.0.0.1:8000/api/token/", {

                    username,

                    password,

                }

            );

            console.log(response)

        } catch (err: any) {

            return err;

        }

    }

    return {login}

}
```

1. `useAuthService` 함수 내부에서 `login` 함수를 정의한다.
    
2. `login` 함수는 `username`과 `password`를 인자로 받아서 API에 POST 요청을 보내는 역할을 한다.
    
3. POST 요청의 URL은 "[http://127.0.0.1:8000/api/token/"로](http://127.0.0.1:8000/api/token/%22%EB%A1%9C) 지정되어 있으며, `username`과 `password`를 데이터로 보낸다.
    
4. 요청이 성공할 경우, `response` 객체가 반환되고 이를 콘솔에 출력한다.
    
5. 요청이 실패할 경우, `catch` 블록이 실행되며, `err` 객체가 반환되어 컴포넌트에서 해당 에러를 처리할 수 있도록 한다.
    
6. `login` 함수를 포함하는 객체를 반환한다. 이 객체를 통해 컴포넌트에서 `login` 함수를 사용할 수 있다.
    

이렇게 정의된 `useAuthService` 커스텀 훅은 인증 서비스와 상호작용하고 로그인에 필요한 API 호출을 처리하는 역할을 한다. 컴포넌트에서 `useAuthService`를 호출하여 `login` 함수를 사용할 수 있게 된다. 이렇게 분리된 로직은 컴포넌트의 가독성과 재사용성을 높여준다.

### App.tsx

```typescript
  

import Home from "./pages/Home"

import Server from "./pages/Server"

import Explore from "./pages/Explore";

import { createBrowserRouter, createRoutesFromElements, Route, RouterProvider } from "react-router-dom"

import ToggleColorMode from "./components/ToggleColorMode";

import Login from "./pages/Login";

import { AuthServicProvicer } from "./context/AuthContext";

  

const router = createBrowserRouter(

  createRoutesFromElements(

    <Route>

      <Route path="/" element={<Home/>} />

      <Route path="/server/:serverId/:channelId?" element={<Server />} />

      <Route path="/explore/:categoryName" element={<Explore />} />

      <Route path="/login" element={<Login/>} />

    </Route>

  )

);

  

const App = () => {

  

  return (

    <AuthServicProvicer>

      <ToggleColorMode>

        <RouterProvider router={router} />

      </ToggleColorMode>

    </AuthServicProvicer>

  );

};

  

export default App;
```

`AuthServicProvicer` 태그로 감싸는 이유는 컨텍스트(Context)를 제공하기 위해서다.      

`AuthServicProvicer`는 `AuthContext`라는 컨텍스트를 생성하고 해당 컨텍스트에 `AuthServiceProps` 객체를 제공하는 역할을 한다. 이 컨텍스트를 사용하여 컴포넌트들이 인증 서비스와 상호작용하고 인증 상태를 관리할 수 있다.     

`AuthServicProvicer` 컴포넌트가 상위 컴포넌트인 `App` 컴포넌트 안에 위치하고 있으므로, `AuthServicProvicer` 컴포넌트로 감싸면 앱의 모든 하위 컴포넌트에서 `AuthContext`에 접근할 수 있게 된다.     

위 코드에서 `App` 컴포넌트 안에 `AuthServicProvicer`를 추가함으로써 `App` 컴포넌트와 하위의 모든 컴포넌트에서 `AuthContext`의 값을 사용할 수 있다. 예를 들어, `Login` 컴포넌트에서 `useAuthServiceContext` 훅을 사용하여 `AuthContext`의 값을 가져올 수 있다.     

이렇게 컨텍스트를 사용하는 이유는 앱의 여러 컴포넌트들이 상태(state)를 공유하고 데이터를 전역적으로 관리하기 위해서다. 컨텍스트를 사용하면 프론트엔드 앱의 복잡한 상태 관리를 단순화하고 컴포넌트 간에 데이터를 전달하는 작업을 효율적으로 수행할 수 있다.

![](https://i.imgur.com/WH0KYmy.png)

위와 같이 로그인에 성공하면 토큰이 발행된다.

## Build: Protecting API Endpoints

### urls.py

```python  

router = DefaultRouter()

router.register("api/messages", MessageViewSet, basename="message")

router.register("api/account", AccountViewSet, basename="message")

  

urlpatterns = [


    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),

    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),

] + router.urls

  

websocket_urlpatterns = [path("<str:serverId>/<str:channelId>", WebChatConsumer.as_asgi())]

  

if settings.DEBUG:

    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### views.py

```python
  

from rest_framework import viewsets

from rest_framework.response import Response

from drf_spectacular.utils import extend_schema

  

from .models import Account

from .serializers import AccountSerializer

from .schemas import user_list_docs

from rest_framework.permissions import IsAuthenticated

  

class AccountViewSet(viewsets.ViewSet):

    queryset = Account.objects.all()

    permission_classes = [IsAuthenticated]

    @user_list_docs

    def list(self, request):

        user_id = request.query_params.get("user_id")

        queryset = Account.objects.get(id=user_id)

        serializer = AccountSerializer(queryset)

        return Response(serializer.data)
```

1. `queryset = Account.objects.all()`: 이 부분에서 `Account` 모델의 모든 객체를 데이터베이스에서 가져와 `queryset`에 할당합니다.
    
2. `permission_classes = [IsAuthenticated]`: 이 뷰셋에는 `IsAuthenticated` 권한 클래스가 적용됩니다. 따라서 인증된 사용자만 이 뷰셋의 액션을 호출할 수 있습니다.
    
3. `@user_list_docs`: 이 데코레이터는 나중에 사용되는 `list` 메서드에 대한 API 문서를 확장(extend)하기 위한 것입니다.
    
4. `def list(self, request)`: `list` 메서드를 정의합니다. 이 메서드는 HTTP GET 요청에 응답합니다.
    
5. `user_id = request.query_params.get("user_id")`: 요청의 쿼리 파라미터 중에서 "user_id"를 가져옵니다.
    
6. `queryset = Account.objects.get(id=user_id)`: 가져온 "user_id"를 사용하여 해당 ID에 대한 `Account` 객체를 데이터베이스에서 가져옵니다. (`get` 메서드는 단일 객체를 반환합니다.)
    
7. `serializer = AccountSerializer(queryset)`: `AccountSerializer`를 사용하여 가져온 `Account` 객체를 직렬화합니다.
    
8. `return Response(serializer.data)`: 직렬화된 데이터를 HTTP 응답으로 반환합니다.
    

이 코드는 인증된 사용자에게만 허용되며, "user_id" 쿼리 파라미터를 사용하여 특정 사용자의 정보를 검색하는 API 엔드포인트를 정의합니다. 또한 `drf_spectacular`의 데코레이터를 사용하여 API 문서를 확장하고 있습니다.

### schemas.py

```python
from drf_spectacular.types import OpenApiTypes

from drf_spectacular.utils import OpenApiParameter, extend_schema

  

from .serializers import AccountSerializer

  

user_list_docs = extend_schema(

    responses=AccountSerializer(),

    parameters=[

        OpenApiParameter(

            name="user_id",

            type=OpenApiTypes.INT,

            location=OpenApiParameter.QUERY,

            description="User ID",

        ),

  

    ],

)
```

`drf_spectacular` 라이브러리를 사용하여 API 문서를 확장(extend)하기 위해  `@user_list_docs` 데코레이터를 정의하며, 해당 데코레이터를 이용하여 API에 대한 설명과 매개변수를 추가했다.

1. `user_list_docs` 데코레이터는 `extend_schema` 함수를 호출하여 API 문서를 확장한다.
2. `responses=AccountSerializer()`: 응답 스키마로서 `AccountSerializer`를 지정한다. 이는 API 응답에서 기대되는 데이터 형식을 나타낸다.
3. `parameters`: 이 파라미터는 API의 쿼리 파라미터를 정의한다. 
4. `OpenApiParameter`: 이 클래스는 `drf_spectacular`에서 제공하는 파라미터 정의 클래스다.

#### OpenApiParameter

`OpenApiParameter`는 `drf_spectacular` 라이브러리에서 제공하는 클래스로, API 문서를 생성하거나 확장할 때 매개변수에 대한 정보를 정의하는 데 사용된다. 이 클래스를 사용하여 API 엔드포인트에 대한 매개변수를 정의할 수 있다. 매개변수는 쿼리 파라미터, 경로 변수, 요청 바디 등과 같은 다양한 위치에 올 수 있다.

`OpenApiParameter` 클래스를 사용하는 방법은 다음과 같다:

```python
from drf_spectacular.utils import OpenApiParameter, OpenApiTypes

OpenApiParameter(
    name="parameter_name",           # 매개변수 이름
    type=OpenApiTypes.INT,           # 매개변수의 데이터 타입
    location=OpenApiParameter.QUERY, # 매개변수의 위치 (QUERY, PATH 등)
    description="Parameter description", # 매개변수 설명
)

```

여기서 `OpenApiTypes`는 `drf_spectacular`에서 제공하는 데이터 타입 상수다. 예를 들어, `OpenApiTypes.INT`, `OpenApiTypes.STRING`, `OpenApiTypes.BOOL` 등이 있다.

`location`은 매개변수의 위치를 나타내며, 가능한 값으로 `OpenApiParameter.QUERY`, `OpenApiParameter.PATH`, `OpenApiParameter.HEADER`, `OpenApiParameter.COOKIE` 등이 있다.

따라서 `user_list_docs` 데코레이터를 사용하면 해당 API 엔드포인트에 대한 API 문서에 `AccountSerializer` 응답 스키마와 "user_id"라는 쿼리 파라미터에 대한 설명이 추가된다. 이를 통해 개발자들이 API를 사용하는 데 도움을 얻을 수 있다.

#### extend_schema 와 다른 점

extend_schema는 API 엔드포인트의 전체 문서를 확장하고 수정할 때 사용도며 커스텀으로 사용할 때는 부분적인 문서추가나 확장할 때 주로 사용한다.

### serializers.py

```python
from .models import Account

from rest_framework import serializers

  

class AccountSerializer(serializers.ModelSerializer):

    class Meta:

        model = Account

        fields = ("username",)
```

#### 테스트

![](https://i.imgur.com/9oe8Nz3.png)


### jwt 구조

![](https://i.imgur.com/hdNIx2v.png)

다음과 같이 헤더와 페이로드등 데이터 구분이 . 으로 나눠진다
따라서 데이터 전처리를 할 때 해당 부분을 token.split('.') 와 같이 .을 구분해서 처리 해줘야 한다.
### AuthServices.ts

```typescript
import axios from "axios";

import { AuthServiceProps } from "../@types/auth-service";

  
  

export function useAuthService(): AuthServiceProps {

  

    const getUserDetails = async () => {

        try {

            const userId = localStorage.getItem("userId")

            const accessToken = localStorage.getItem("access_token")

            const response = await axios.get(

                `http://127.0.0.1:8000/api/account/?user_id=${userId}`,{

                    headers:{

                        Authorization: `Bearer ${accessToken}`

                    }

                }

            );

            const userDetails = response.data

            localStorage.setItem( "username", userDetails.username );

  

        } catch (err: any) {

            return err;

        }

    }

    const getUserIdFromToken = (access : string) => {

        const token = access

        const tokenParts = token.split('.')

        const encodedPayLoad = tokenParts[1]

        const decodedPayLoad = atob(encodedPayLoad)

        const payLoadData = JSON.parse(decodedPayLoad)

        const userId = payLoadData.user_id

  

        return userId

    }

    const login = async (username: string, password: string) => {

        try {

            const response = await axios.post(

                "http://127.0.0.1:8000/api/token/", {

                    username,

                    password,

                }

            );

  

            const { access, refresh } = response.data;

  

            // Save the tokens to local storage

            localStorage.setItem( "access_token", access );

            localStorage.setItem( "refresh_token", refresh );

            localStorage.setItem( "userId", getUserIdFromToken(access))

  

            getUserDetails()

        } catch (err: any) {

            return err;

        }

    }

    return {login}

}
```

1. `getUserDetails`: 로그인된 사용자의 세부 정보를 가져와서 로컬 스토리지에 저장하는 함수다. 해당 함수는 API를 호출하여 사용자 정보를 가져오고, 가져온 정보를 로컬 스토리지에 저장한다.
    
2. `getUserIdFromToken`: 액세스 토큰에서 사용자 아이디를 추출하는 함수다. JWT 토큰의 페이로드를 디코딩하여 사용자 아이디를 추출한다.
    
3. `login`: 사용자 로그인을 처리하는 함수다. 사용자명과 비밀번호를 사용하여 백엔드 API에 로그인 요청을 보내고, 응답에서 얻은 액세스 토큰을 로컬 스토리지에 저장한다. 또한 `getUserDetails` 함수를 호출하여 사용자 정보를 가져와 저장한다.
    

`useAuthService` 커스텀 훅은 `login` 함수만을 반환하며, 프론트엔드 애플리케이션에서 이를 사용하여 로그인 기능을 구현할 수 있다. 이 코드는 React나 다른 프론트엔드 프레임워크/라이브러리와 함께 사용되어야 한다.

1. **API 호출과 백엔드 통신**:
    
    - `axios` 라이브러리를 사용하여 서버와 HTTP 요청을 통신한다.
    - `axios.get()`와 `axios.post()` 메서드를 사용하여 백엔드 API에 GET 및 POST 요청을 보낸다.
2. **JWT(JSON Web Token) 인증**:
    
    - JWT는 사용자 인증을 위한 토큰 기반 인증 방식이다.
    - 사용자가 로그인하면 백엔드 서버에서 액세스 토큰과 리프레시 토큰을 발급한다.
    - 액세스 토큰은 사용자 인증을 확인하고, 리프레시 토큰은 액세스 토큰이 만료되었을 때 새로운 액세스 토큰을 발급하는 데 사용된다.
    - 액세스 토큰은 페이로드에 사용자 정보를 포함하며, 토큰을 디코딩하면 사용자 아이디 등의 정보를 얻을 수 있다.
3. **커스텀 훅 `useAuthService`의 역할**:
    
    - `useAuthService`는 사용자 로그인 기능과 사용자 세부 정보를 관리한다.
    - `login` 함수: 사용자가 제공한 사용자명과 비밀번호로 로그인 요청을 보내고, 응답에서 액세스 토큰을 받아 로컬 스토리지에 저장한다. 또한 `getUserDetails` 함수를 호출하여 사용자 세부 정보를 가져와 로컬 스토리지에 저장한다.
    - `getUserDetails` 함수: 로컬 스토리지에서 액세스 토큰을 가져와 백엔드 API에 사용자 정보 요청을 보내고, 응답에서 사용자 정보를 가져와 로컬 스토리지에 저장한다.
    - `getUserIdFromToken` 함수: 액세스 토큰에서 사용자 아이디를 추출하여 반환한다.

커스텀 훅을 사용하는 프론트엔드 애플리케이션은 이 `useAuthService`를 호출하여 로그인 기능을 구현하고, 사용자 세부 정보를 관리할 수 있다. 이러한 코드 구조를 사용하면 프론트엔드와 백엔드 간의 통신, 사용자 인증, 정보 관리 등을 보다 효율적으로 처리할 수 있다.

### 로그인 성공 후 

![](https://i.imgur.com/Ldy6g0g.png)

![](https://i.imgur.com/oKjZfnt.png)

## Build: Implement Protected Routes

지금까지 애플리케이션에서 사용자가 인증되었는지 확인할 수 있는 기능이 있다,  따라서 사용자가 백엔드 API에 요청을 보낼 경우 먼저 로그인하고 액세스 토큰을 요청과 함께 전송하여 백엔드에서 사용자가 인증되었는지 확인할 수 있도록 해야 한다.  

보호 경로 구현이란 특정 경로 또는 페이지에 대한 액세스를 제한하는 작업을 말한다. 또는 웹 애플리케이션의 경로에 대한 액세스를 제한하는 것을 말한다. 그리고 이는 사용자가 다시 인증되었는지 여부에 따라 결정된다.    

### 프론트엔드에서 사용자가 실제로 로그인했는지 어떻게 확인할 수 있을까?  

JWT 인증을 사용하면 상태 비저장 방식이다. 이 접근 방식을 사용하면 서버는 인증된 사용자에 대한 세션 상태를 유지하지 않는다. 따라서 서버에 '이 사용자가 로그인했는지'만 물어볼 수는 없다.      
  
따라서 사용자가 상태 비저장 JWT 인증으로 프런트엔드에서 로그인했는지 확인하려면 토큰을 확인해야 한다.
토큰이 실제로 존재하고 만료되지 않았다면 사용자가 로그인한 것으로 간주할 수 있다.         

훅에서 이 상태를 여러 컴포넌트에 전달하여 사용자가 인증되었는지 여부를 추적할 수 있으며 로그인했는지 여부를 추적하거나, 적어도 다른 컴포넌트에 해당 표시를 제공할 수 있다. 또한 다른 컴포넌트로 전달하거나 다른 컴포넌트를 사용하고 여기서 업데이트할 수도 있다.  

### auth-service.d.ts

```typescript
export interface AuthServiceProps {

    login: (username: string, password: string) => any;

    isLoggedIn: boolean;

}
```

### AuthServices.ts

```typescript
import axios from "axios";

import { AuthServiceProps } from "../@types/auth-service";

import { useState } from "react";

  
  

export function useAuthService(): AuthServiceProps {

    const [isLoggedIn, setIsLoggedIn] = useState<boolean>(()=> {

        const loggedIn = localStorage.getItem("isLoggedId")

        if (loggedIn != null){

            return Boolean(loggedIn)

        } else {

            return false

        }

    })

  

    const getUserDetails = async () => {

        try {

            const userId = localStorage.getItem("userId")

            const accessToken = localStorage.getItem("access_token")

            const response = await axios.get(

                `http://127.0.0.1:8000/api/account/?user_id=${userId}`,{

                    headers:{

                        Authorization: `Bearer ${accessToken}`

                    }

                }

            );

            const userDetails = response.data

            localStorage.setItem( "username", userDetails.username );

            setIsLoggedIn(true);

            localStorage.setItem("isLoggedIn", "true")

        } catch (err: any) {

            setIsLoggedIn(false)

            localStorage.setItem("isLoggedIn", "false")

            return err;

        }

    }

    const getUserIdFromToken = (access : string) => {

        const token = access

        const tokenParts = token.split('.')

        const encodedPayLoad = tokenParts[1]

        const decodedPayLoad = atob(encodedPayLoad)

        const payLoadData = JSON.parse(decodedPayLoad)

        const userId = payLoadData.user_id

  

        return userId

    }

    const login = async (username: string, password: string) => {

        try {

            const response = await axios.post(

                "http://127.0.0.1:8000/api/token/", {

                    username,

                    password,

                }

            );

  

            const { access, refresh } = response.data;

  

            // Save the tokens to local storage

            localStorage.setItem( "access_token", access );

            localStorage.setItem( "refresh_token", refresh );

            localStorage.setItem( "userId", getUserIdFromToken(access))

            localStorage.setItem("isLoggedIn", "true")

            setIsLoggedIn(true)

  

            getUserDetails()

        } catch (err: any) {

            return err.response.status;

        }

    }

    return {login, isLoggedIn}

}
```

`useAuthService` 함수가 정의되어 있고 이 함수가 `AuthServiceProps` 타입을 반환하는 것을 확인할 수 있다. 이 함수는 React의 커스텀 훅(custom hook)으로, 인증 및 로그인 관련 기능을 제공하고 상태를 관리하는 역할을 한다. 이 커스텀 훅은 아마도 컴포넌트에서 사용되어 로그인 기능과 로그인 상태를 관리할 것으로 예상된다.

1. `useAuthService` 커스텀 훅은 `login` 함수와 `isLoggedIn` 상태를 반환한다.
2. `login` 함수는 사용자의 `username`과 `password`를 받아 서버로 로그인 요청을 보내고, 성공 시 토큰을 저장하고 사용자 정보를 가져오는 등의 처리를 수행한다.
3. `isLoggedIn` 상태는 사용자가 로그인되어 있는지를 나타내는 불리언 값이다.
4. `getUserDetails` 함수는 로그인 후 사용자 정보를 가져오는 역할을 한다.
5. `getUserIdFromToken` 함수는 토큰에서 유저 아이디를 추출하여 반환한다.

### useAuthService 커스텀 훅

1. **useState**: `useState` 훅을 사용하여 `isLoggedIn` 상태와 해당 상태를 변경하는 `setIsLoggedIn` 함수를 생성한다. `isLoggedIn`은 현재 사용자가 로그인되어 있는지 여부를 나타내는 불리언 값입니다. 초기 상태는 로컬 스토리지에서 `isLoggedId` 값을 읽어와 결정된다.
    
2. **getUserDetails 함수**: 사용자 정보를 가져오는 역할을 한다. 로컬 스토리지에서 `userId`와 `access_token`을 읽어와 API 요청을 보내고, 성공 시 응답에서 사용자 정보를 추출하여 로컬 스토리지에 저장하고 `isLoggedIn` 상태를 `true`로 설정한다. 실패할 경우 `isLoggedIn` 상태를 `false`로 설정하고 오류를 반환한다.
    
3. **getUserIdFromToken 함수**: 토큰에서 유저 아이디를 추출하는 함수다. 토큰을 디코딩하여 내부 페이로드(payload)에서 유저 아이디를 추출한다.
    
4. **login 함수**: 사용자 로그인을 처리하는 역할을 한다. 제공된 `username`과 `password`를 사용하여 로그인 API 요청을 보내고, 응답에서 받은 `access` 토큰을 로컬 스토리지에 저장한다. 또한 `refresh_token`과 `userId`도 저장하고, `isLoggedIn` 상태를 `true`로 설정한다. 이후 `getUserDetails` 함수를 호출하여 사용자 정보를 가져온다. 로그인이 실패할 경우 오류 상태를 반환한다.
    
5. **커스텀 훅 반환값**: `login` 함수와 `isLoggedIn` 상태를 반환한다. 이렇게 반환된 값은 다른 React 컴포넌트에서 사용된다.
    

이 커스텀 훅은 React 애플리케이션에서 다음과 같이 사용될 수 있다:

```jsx
import React from 'react';
import { useAuthService } from './path-to/useAuthService';

function App() {
  const { login, isLoggedIn } = useAuthService();

  const handleLogin = async () => {
    const username = 'yourUsername';
    const password = 'yourPassword';

    try {
      await login(username, password);
    } catch (error) {
      console.error('Login failed:', error);
    }
  };

  return (
    <div>
      {isLoggedIn ? (
        <p>Welcome! You are logged in.</p>
      ) : (
        <button onClick={handleLogin}>Login</button>
      )}
    </div>
  );
}

export default App;

```

이렇게 사용자의 로그인 상태 및 인증을 관리하고 필요한 기능을 제공하는데 사용된다.

### ProtectedRoute.tsx

```typescript
import { Navigate } from "react-router-dom";
import { useAuthServiceContext } from "../context/AuthContext";

const ProtectedRoute = ({ children }: { children: React.ReactNode }) => {
    const { isLoggedIn } = useAuthServiceContext();
    if (!isLoggedIn) {
        return <Navigate to="/login" replace={true} />;
    }
    return <>{children}</>; // Wrapping children in a React fragment
};

export default ProtectedRoute;
```

 React Router를 사용하여 보호된(인증이 필요한) 라우트를 구현하는 컴포넌트인 `ProtectedRoute`를 정의했다. 이 컴포넌트의 역할은 사용자가 로그인되어 있는 경우에만 접근을 허용하고, 로그인되어 있지 않은 경우에는 로그인 페이지로 이동시키는 역할을 한다. 

1. `Navigate` 컴포넌트: `react-router-dom` 패키지에서 제공하는 컴포넌트로, 브라우저의 경로를 변경하는 데 사용된다. `to` 속성에 새 경로를 지정하고, `replace` 속성을 `true`로 설정하면 이전 기록을 대체할 수 있다.
    
2. `useAuthServiceContext`: `AuthContext`에서 제공되는 커스텀 훅으로, `isLoggedIn`과 같은 인증 관련 정보를 가져오는 데 사용된다. 이 커스텀 훅은 `AuthContext`의 상태를 사용하여 현재 로그인 상태를 확인한다.
    
3. `ProtectedRoute` 컴포넌트: 사용자가 로그인되어 있는지 확인하고, 로그인되어 있지 않은 경우 로그인 페이지로 이동시키는 역할을 한다. 만약 사용자가 로그인되어 있다면 `children`으로 전달된 컴포넌트들을 표시한다.
    

컴포넌트의 사용 방법:

1. `ProtectedRoute` 컴포넌트를 사용하려면, 해당 컴포넌트로 보호되어야 하는 컴포넌트들을 감싸면 된다.

```jsx
<ProtectedRoute>
  {/* 이 안에 있는 컴포넌트들은 인증되지 않은 경우 보호됩니다 */}
  <Dashboard />
</ProtectedRoute>

```

2. `useAuthServiceContext`를 사용하여 `isLoggedIn` 상태를 가져온다. 이를 통해 현재 사용자의 로그인 상태를 확인한다.
    
3. `isLoggedIn` 상태가 `false`인 경우, 사용자를 로그인 페이지로 리디렉션한다.
    

이를 통해 `ProtectedRoute` 컴포넌트를 사용하여 로그인이 필요한 페이지를 쉽게 구현하고, 보호된 페이지 접근을 관리할 수 있다.

## Build : Logging Out Users

```typescript
export function useAuthService(): AuthServiceProps {

  

    const getInitialLoggedInValue = () => {

        const loggedIn = localStorage.getItem("isLoggedIn");

        return loggedIn !== null && loggedIn === "true";

      };
      .....
         const logout = () => {

        localStorage.removeItem("access_token");

        localStorage.removeItem("refresh_token");

        localStorage.removeItem("userId")

        localStorage.removeItem("username")

        localStorage.setItem("isLoggedIn", "false")

        setIsLoggedIn(false);

  

    }

  
  

    return {login, isLoggedIn, logout}
```

![](https://i.imgur.com/yRazO0v.png)

![](https://i.imgur.com/e6VyLcx.png)

## Build: JWT Interceptor - Using Refresh Token

### settings.py

```python

from datetime import timedelta

SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(seconds=5),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=1),
}
```

### jwtinterceptor.ts

```typescript
import axios, { AxiosInstance } from 'axios';

import { useNavigate } from 'react-router-dom';

import { BASE_URL } from '../config';

  

const API_BASE_URL = BASE_URL

  

const useAxiosWithInterceptor = (): AxiosInstance => {

    const jwtAxios = axios.create({ baseURL: API_BASE_URL})

    const navigate = useNavigate()

  

    jwtAxios.interceptors.response.use(

        (response) => {

            return response;

        },

    async (error) => {

        const originalRequest = error.config;

        if (error.response?.status === 401 || 403) {

            const refreshToken = localStorage.getItem("refresh_token")

            if (refreshToken) {

                try {

                    const refreshResponse = await axios.post(

                        "http://127.0.0.1:8000/api/token/refresh/",

                        {

                            refresh: refreshToken

                        }

                    )

                const newAccessToken = refreshResponse.data.access

                console.log("1")

                localStorage.setItem("access_token", newAccessToken);

                originalRequest.headers['Authorization'] = `Bearer ${newAccessToken}`

                return jwtAxios(originalRequest)

                } catch (refreshError) {

                    navigate('/login')

                    throw refreshError

                }

            } else {

                navigate('/login')

            }

        }

        throw error;

    }

    )

    return jwtAxios;

}

  

export default useAxiosWithInterceptor
```

axios를 사용하여 API 요청을 보낼 때, JWT (JSON Web Token) 인증 및 토큰 갱신을 관리하기 위한 인터셉터를 구현한다. 

1. 모듈 및 상수 가져오기:
    
    - axios와 AxiosInstance는 axios 라이브러리로부터 가져온 모듈이다. AxiosInstance는 axios의 인스턴스를 만들기 위한 타입이다.
    - useNavigate는 'react-router-dom'에서 제공하는 후크 함수로, 라우터 내에서 페이지 전환을 담당한다.
    - BASE_URL 및 API_BASE_URL은 API 엔드포인트의 기본 URL을 나타내는 상수다.
2. useAxiosWithInterceptor 함수 정의:
    
    - jwtAxios: axios 인스턴스를 생성하고, 기본 URL을 API_BASE_URL로 설정한다.
3. 인터셉터 설정:
    
    - jwtAxios.interceptors.response.use(): axios의 응답 인터셉터를 설정한다. 이 인터셉터는 API 요청에 대한 응답을 처리한다.
    - (response) => {...}: 성공적인 응답의 경우, 해당 응답을 그대로 반환한다.
4. 오류 처리:
    
    - async (error) => {...}: 응답이 오류인 경우, 이 부분이 실행된다.
    - originalRequest: 처음 요청의 설정(config)을 저장한다.
5. 토큰 갱신 및 재시도:
    
    - if (error.response?.status === 401 || 403): 응답이 401 (Unauthorized) 또는 403 (Forbidden)일 때,
    - refreshToken: localStorage에서 "refresh_token"을 가져온다.
    - if (refreshToken): refresh token이 있다면,
        - 새로운 액세스 토큰을 얻기 위해 refresh token을 사용하여 서버에 POST 요청을 보낸다.
        - 얻은 새로운 액세스 토큰을 localStorage에 저장하고, originalRequest의 Authorization 헤더를 갱신한다.
        - 새로운 액세스 토큰을 사용하여 원래 요청을 다시 시도한다.
        - 갱신 요청이 실패하면 로그인 페이지로 이동한다.
    - refresh token이 없거나, 갱신 요청에 실패하면 로그인 페이지로 이동한다.
6. 마무리:
    
    - 함수 마지막에서 jwtAxios를 반환하며, 이제 이 인스턴스를 사용하여 API 요청을 보낼 수 있다.

이 코드는 사용자의 액세스 토큰이 만료되었을 때, 리프레시 토큰을 사용하여 새로운 액세스 토큰을 얻고, 요청을 재시도하여 사용자가 로그인 상태를 유지할 수 있도록 한다.

#### jwt 인터셉터가 뭐지?

JWT 인터셉터는 웹 애플리케이션에서 JWT(JSON Web Token)을 사용할 때, axios와 같은 HTTP 클라이언트 라이브러리를 이용하여 요청과 응답을 가로채서 JWT 관련 작업을 처리하는 역할을 합니다.

일반적으로 JWT 인터셉터는 다음과 같은 작업을 수행합니다:

1. **응답 에러 처리와 토큰 재발급**:
    
    - 서버 응답이 401 (Unauthorized) 또는 403 (Forbidden) 상태 코드를 반환하는 경우, Access Token이 유효하지 않거나 만료되었을 가능성이 있습니다.
    - JWT 인터셉터는 이러한 응답 에러를 가로채서 Refresh Token을 사용하여 새로운 Access Token을 발급받는 작업을 수행합니다.
    - Refresh Token을 사용하여 새로운 Access Token을 발급받은 후, 원래 요청을 수정하여 새로운 Access Token을 포함하여 다시 보내게 됩니다.
2. **모든 응답에 대한 처리**:
    
    - JWT 인터셉터는 모든 서버 응답에 대해 처리할 수 있는 기능을 가집니다. 위 코드에서는 주로 에러 처리와 토큰 갱신을 다루고 있습니다.
    - 하지만 인터셉터를 통해 요청 헤더에 일관된 설정을 추가하거나, 응답을 처리하여 공통된 데이터 추출 등 다양한 작업을 수행할 수도 있습니다.

JWT 인터셉터는 JWT 관련 로직을 클라이언트 측에서 중복해서 처리하지 않고, 중앙에서 효율적으로 관리하며 보안과 관련된 작업을 수행할 수 있도록 도와줍니다. 이를 통해 애플리케이션의 코드 중복을 줄이고 효율적인 토큰 관리를 실현할 수 있습니다.


### TestLogin.tsx

```typescript
import { useState } from "react";

import { useAuthServiceContext } from "../context/AuthContext";

import axios from "axios";

import useAxiosWithInterceptor from "../helpers/jwtinterceptor";

  

const TestLogin = () => {

  const { isLoggedIn, logout } = useAuthServiceContext();

  const [ username, setUsername ] = useState("");

  const jwtAxios = useAxiosWithInterceptor();

  

  const getUserDetails = async () =>{

    try {

        const userId = localStorage.getItem("userId")

        const accessToken = localStorage.getItem("access_token")

  

        const response = await jwtAxios.get(

            `http://127.0.0.1:8000/api/account/?user_id=${userId}`,{

            headers:{

                Authorization: `Bearer ${accessToken}`

            },

          }

        );

        const userDetails = response.data;

        setUsername(userDetails.username);

  
  

    } catch (err: any) {

  

        return err;

    }

}

  
  

  return (

    <>

      <div>{isLoggedIn.toString()}</div>

      <div>

        <button onClick={logout}>Logout</button>

        <button onClick={getUserDetails}>Get User Details</button>

      </div>

      <div>Username: {username} </div>

    </>

  );

};

export default TestLogin;
```

위 코드는 React 애플리케이션에서 로그인 상태 및 사용자 정보를 관리하고, JWT 인증을 사용하여 API 엔드포인트에서 사용자 정보를 가져오는 기능을 구현한다.

1. 모듈 및 상태 가져오기:
    
    - useState: React 후크 함수로, 컴포넌트의 상태를 관리하는 데 사용된다.
    - useAuthServiceContext: AuthContext로부터 로그인 상태 및 로그아웃 함수를 가져온다.
    - axios: HTTP 요청을 보내기 위한 라이브러리.
    - useAxiosWithInterceptor: 앞서 설명한대로 JWT 인터셉터를 사용한 axios 인스턴스를 가져온다.
2. TestLogin 컴포넌트 정의:
    
    - isLoggedIn 및 logout은 useAuthServiceContext 후크를 통해 가져온 로그인 상태와 로그아웃 함수다.
    - username 상태를 useState 후크를 사용하여 관리한다.
    - jwtAxios는 인증된 요청을 위해 JWT 인터셉터를 사용하는 axios 인스턴스다.
3. getUserDetails 함수:
    
    - 사용자의 아이디와 액세스 토큰을 localStorage에서 가져온다.
    - jwtAxios.get()을 사용하여 API 엔드포인트에 GET 요청을 보낸다. 이 때, 액세스 토큰을 Authorization 헤더에 포함하여 보낸다.
    - 응답이 성공하면 사용자 정보를 가져와서 setUsername을 통해 상태를 업데이트한다.
4. 반환:
    
    - isLoggedIn 값을 화면에 출력한다.
    - 로그아웃 버튼은 클릭하면 logout 함수가 호출되어 로그아웃 상태로 변경된다.
    - "Get User Details" 버튼을 클릭하면 getUserDetails 함수가 호출되어 사용자 정보를 가져오고, 이를 화면에 출력한다.
    - username 상태를 통해 사용자 이름을 표시한다.

이 코드는 사용자가 로그인한 상태에서 "Get User Details" 버튼을 클릭하면 JWT 토큰을 사용하여 API로부터 사용자 정보를 가져와 화면에 표시한다. 이를 통해 JWT 토큰과 API 인터셉터를 활용하여 보안적인 로그인 및 사용자 정보 조회를 구현한 예시다.

### jwtAxios

`jwtAxios`라는 변수는 `useAxiosWithInterceptor` 함수를 통해 생성되는 axios 인스턴스다. 이 axios 인스턴스에는 JWT 인터셉터가 포함되어 있다. JWT 인터셉터는 axios의 요청과 응답을 가로채서 특정 동작을 수행하는 역할을 한다.     

`useAxiosWithInterceptor` 함수에 정의된 JWT 인터셉터는 이전에 설명했던 것과 마찬가지의 기능을 수행한다 (복습) 주로 두 가지 주요 작업을 수행한다:     

1. **응답 에러 처리와 토큰 재발급**:
    
    - 서버 응답이 401 (Unauthorized) 또는 403 (Forbidden) 상태 코드를 반환하는 경우, 이는 사용자의 Access Token이 유효하지 않거나 만료되었을 가능성이 있다.
    - JWT 인터셉터는 이러한 응답 에러를 가로채서 Refresh Token을 사용하여 새로운 Access Token을 발급받는 작업을 수행한다.
    - Refresh Token을 사용하여 새로운 Access Token을 발급받은 후, 원래 요청을 수정하여 새로운 Access Token을 포함하여 다시 보내게 된다.
2. **모든 응답에 대한 처리**:
    
    - JWT 인터셉터는 모든 서버 응답에 대해 처리할 수 있는 기능을 가진다. 위 코드에서는 주로 에러 처리와 토큰 갱신을 다루고 있다.
    - 하지만 인터셉터를 통해 요청 헤더에 일관된 설정을 추가하거나, 응답을 처리하여 공통된 데이터 추출 등 다양한 작업을 수행할 수도 있다.

이러한 JWT 인터셉터를 사용함으로써, 애플리케이션에서는 토큰 관리 및 보안 상의 이슈에 대한 중복 코드를 피하고, 토큰 갱신과 관련된 작업을 효율적으로 처리할 수 있다.


![](https://i.imgur.com/h1xhbns.png)

처음 셋팅에 정의해둔 5초가 지나면 토큰이 만료된다.
#### refresh_token

![](https://i.imgur.com/r36zvd2.png)

refresh_token 과 access_token을 같이 발급 한 후 백엔드에서 제대로 동작하는지 endpoint에서 확인해본다.

![](https://i.imgur.com/cNzfJy4.png)



![](https://i.imgur.com/swtUpVt.png)


사용자가 매분 또는 매시간 로그인하지 않도록 리프레쉬 토큰을 백엔드로 전송하여 새 액세스 토큰을 반환하는 데 사용한다.    
JWT 인터셉터를 활용하여 리프레쉬 토큰으로 서버에 전송하고 사용자가 다시 로그인 할 필요 없이 새 액세스 토큰을 반환하는 프로세스를 자동화했다.

#### 근데 왜 Access token과 Refresh token을 굳이 나눠서 발급할까? 그냥 Acess token 의 유지기간을 길게 하면 안될까?

Access Token을 길게 유지하는 것도 가능하지만, Access Token을 길게 유지하면 보안에 대한 이슈가 발생할 수 있다. 그래서 Refresh Token을 사용하는 것이 좋은 이유가 있다.

**Access Token을 길게 유지하는 경우의 문제점:**

- Access Token을 길게 설정하면 토큰이 유출되었을 때의 보안 위험이 높아집니다. 해커가 유출된 토큰을 장기간 동안 사용할 수 있기 때문이다.
- 만약 악의적인 공격자가 Access Token을 획득하면 그 동안 사용자의 모든 권한을 남용할 수 있다.
- Access Token을 길게 유지하면 악성 사용자가 더 많은 시간 동안 액세스를 유지하고 권한을 남용할 수 있는 가능성이 커진다.

**Refresh Token의 역할과 장점:**

- Refresh Token은 Access Token의 유효 기간이 짧아도, 사용자가 로그인 상태를 유지하는 동안 지속적으로 새로운 Access Token을 발급받을 수 있도록 한다.
- Refresh Token은 일반적으로 HttpOnly 쿠키에 저장되어 클라이언트에서 직접 접근할 수 없다. 이로써 해커가 Refresh Token을 훔쳐가는 것이 어려워진다.
- Refresh Token은 Access Token보다 더 긴 유효 기간을 가질 수 있지만, 사용자의 실제 인증 정보는 아니기 때문에 보안적으로 덜 민감하다.

**Access Token 유출 vs. Refresh Token 유출:**

- Access Token이 유출된다면, 해당 토큰을 가로채 사용자의 권한을 남용할 수 있다.
- Refresh Token이 유출된다면, 해커는 Refresh Token을 사용하여 새로운 Access Token을 발급받을 수 있지만, 클라이언트에서 직접 Refresh Token을 사용하여 사용자의 권한을 남용할 수는 없다. Refresh Token 자체는 유출이 더 안전한 토큰이다.

#### Refresh Token이 보안적으로 덜 민감하며 왜 유출로부터 더 안전한 토큰일까?

1. **저장 위치와 접근성:**
    
    - Refresh Token은 일반적으로 HttpOnly 쿠키에 저장된다. 이는 클라이언트 측 JavaScript 코드에서 직접 접근할 수 없도록 보호된다.
    - 반면, Access Token은 클라이언트의 JavaScript 코드로부터 접근 가능한 곳에 저장될 수 있다.
2. **유효 기간:**
    
    - Refresh Token은 일반적으로 더 긴 유효 기간을 가지며, 보통 몇 주에서 몇 개월까지 지속된다.
    - 반면, Access Token은 짧은 유효 기간을 가지며, 몇 분에서 몇 시간까지 지속된다.
3. **역할의 차이:**
    
    - Refresh Token은 액세스 토큰을 얻기 위한 역할을 가지며, 보통 인증 서버와 교류할 때 사용된다. 클라이언트는 Refresh Token을 사용하여 새로운 액세스 토큰을 받아온다.
    - Access Token은 실제로 API 엔드포인트에 접근하거나 보호된 리소스를 사용하기 위한 역할을 한다. API 호출에 사용되어 보안 검증 및 권한 확인에 사용된다.
4. **권한 및 스코프 관리:**
    
    - Refresh Token은 일반적으로 사용자의 인증 정보를 포함하지 않고, 주로 액세스 토큰 발급을 위한 인증 수단으로 사용된다.
    - Access Token에는 사용자의 인증 및 권한 정보가 포함되므로, 이 토큰이 유출된다면 해커가 해당 사용자의 권한을 남용할 수 있다.

위와 같은 이유로 Refresh Token은 일종의 "보안 게이트웨이" 역할을 하며, Access Token이 유출되는 상황에서도 더 안전한 보안 레이어 역할을 한다. 반면에 Access Token은 실제로 리소스에 접근하고 사용자의 권한을 확인하는 데 사용되기 때문에 유출될 경우에는 보안 위험성이 높아진다.     

결국 Refresh Token을 사용하는 이유는 Access Token을 짧게 유지하면서도 사용자의 편의성과 보안을 동시에 고려할 수 있어서다. Refresh Token을 사용하여 Access Token의 유효 기간을 짧게 설정하더라도, 사용자는 계속해서 로그인할 필요 없이 자연스럽게 서비스를 이용할 수 있다.

### Preflight

Preflight 요청이 발생하는 이유는 CORS(Cross-Origin Resource Sharing) 정책을 준수하기 위한 과정으로, 브라우저가 보안상의 이유로 다른 도메인의 서버에 요청을 보내기 전에 사전에 검사용 요청을 보낸다.

Refresh Token을 사용하는 절차

1. **Access Token 만료 및 재발급 요청**:
    
    - 사용자가 로그인한 후, 서버로부터 Access Token과 Refresh Token을 발급받는다.
    - Access Token의 유효 기간이 만료되면, 브라우저에서는 API를 호출하기 전에 Preflight 요청을 보냅니다.
2. **Preflight 요청 과정**:
    
    - 브라우저는 API 엔드포인트에 실제 데이터 요청을 보내기 전에, OPTIONS 메서드를 사용하여 Preflight 요청을 보낸다.
    - Preflight 요청은 Access Token과 함께 Refresh Token도 헤더에 포함하여 보낸다.
    - 서버는 Preflight 요청을 받으면, CORS 정책 및 보안 검사를 수행한다.
    - 서버는 해당 도메인에서의 CORS 정책과 Refresh Token의 유효성을 확인하고, 요청을 허용할지 거부할지 결정한다.
3. **서버 응답**:
    
    - 서버는 Preflight 요청에 대한 응답을 보내면, 브라우저는 실제 데이터 요청을 보낼지 여부를 결정한다.
    - 서버가 Refresh Token을 허용하고, CORS 정책을 준수한다면, 브라우저는 Access Token과 함께 실제 데이터 요청을 보내게 된다.
    - 서버가 요청을 거부하거나, CORS 정책을 위반한다면, 브라우저는 해당 요청을 막는다.

이렇게 Refresh Token을 사용하는 경우, 브라우저는 Preflight 요청을 통해 서버의 허용 여부와 CORS 정책을 확인하고, 실제 데이터 요청을 보내는지 결정한다. 이 과정은 보안과 CORS 정책 준수를 위한 중요한 단계로, 사용자의 정보와 리소스를 안전하게 관리하기 위한 메커니즘이다.

## Theory: LocalStorage vs HttpOnly Cookie

LocalStorage에 토큰을 저장할 경우 위와 같이 보안성에 있어서 취약한 부분이 많다.     
자바스크립트로 컨트롤 및 접근이 용이한 부분도 있고 cross-site scripting 공격을 당하기 쉽다. cross-site scripting은 해커가 웹페이지에 악성코드를 주입하는 것을 말한다. 이 악성 스크립트는 로컬 스토리지를 읽을 수 있다.     

이러한 공격으로부터 보호하기 위해 Http 전용 쿠키를 사용할 수 있다. Http 전용 쿠키가 이러한 공격으로 부터 보호가 되는 이유는 클라이언트 측 자바스크립트 코드에서 엑세스하는 것을 방지하기 때문이다.     

![](https://i.imgur.com/KPUi819.png)

하지만 http 전용 쿠키로 이동하면 JWT 토큰 페이로드에 엑세스 할 수 없다.      
JWT 토큰의 내용 중 일부인 "페이로드(payload)"에 접근할 수 없다는 의미다.     

JWT(JSON Web Token)는 세 부분으로 이루어진 토큰입니다: 헤더(header), 페이로드(payload) 및 서명(signature). 헤더와 페이로드는 Base64로 인코딩되어 있으며, 일반적으로 클라이언트(브라우저)에서 디코딩할 수 있다. 그러나 JWT를 HttpOnly 쿠키로 저장할 경우, 클라이언트 측 JavaScript에서는 해당 쿠키에 접근할 수 없다.     

이는 보안상의 이유로 사용되며, 토큰의 내용을 브라우저 측에서 직접 접근할 수 없게 하여 보안을 강화하는 역할을 한다.     

HttpOnly 쿠키로 저장된 JWT 토큰은 서버로 전송되는 요청에만 포함되며, 클라이언트 측에서는 쿠키에 접근할 수 없기 때문에 보안상 이점이 있다. 이렇게 함으로써 JWT의 내용이 노출되는 것을 방지하고, 토큰을 안전하게 관리할 수 있다.


### 그럼 페이로드는 정확히 무슨 역할을 하지?

JWT(JSON Web Token)의 페이로드(payload)는 토큰의 중요한 정보를 포함하는 부분이다. 토큰의 페이로드는 JSON 형식으로 인코딩되어 있으며, 토큰을 생성하는 쪽에서 원하는 정보를 포함시킬 수 있다. 페이로드에 포함될 수 있는 정보는 사용자 정보, 권한, 기타 부가적인 데이터 등이 있을 수 있다.

페이로드의 주요 역할은 다음과 같다:

1. **사용자 정보 및 권한 부여**: 토큰의 페이로드에는 사용자 정보를 포함하여 해당 사용자의 식별자, 역할, 권한 등을 나타낼 수 있다. 이를 통해 클라이언트와 서버 간의 인증 및 권한 부여가 가능해진다.     
    
2. **컨텍스트 정보 제공**: 페이로드에는 토큰이 발급된 컨텍스트에 관련된 정보를 포함시킬 수 있다. 예를 들어, 토큰이 어떤 애플리케이션에서 사용되는지, 어떤 클라이언트가 발급한 것인지 등의 정보를 담을 수 있다.     
    
3. **클레임(claims) 정의**: 페이로드는 클레임(claims)을 포함할 수 있다. 클레임은 JWT 토큰의 내용을 설명하는 이름-값 쌍이다. 예를 들어, "sub" 클레임은 토큰이 소유한 주체(subject)를 지정하며, "exp" 클레임은 토큰의 만료 시간을 지정한다.
    
4. **맞춤 정보 제공**: 애플리케이션의 요구에 따라 페이로드에 추가적인 맞춤 정보를 제공할 수 있다. 예를 들어, 토큰 발급 시간, 토큰 유효 기간 등의 정보를 담을 수 있다. 
    

페이로드는 Base64로 인코딩되어 있어서 누구나 디코딩할 수 있지만, JWT의 서명(signature)과 함께 사용되어 변조를 방지한다. 따라서 페이로드에 포함된 정보를 신뢰할 수 있고 변조되지 않았다고 판단하려면 서명을 검증해야 한다.     

페이로드에 포함된 정보는 클라이언트 측에서 활용할 수 있는 추가적인 데이터를 제공할 수 있다. 하지만 이는 특정 상황에서만 적용되는 장점이며, 보안과 관련된 고려 사항도 고려해야 한다.     

다음은 페이로드에 접근할 수 있는 경우의 장점 몇 가지를 설명한다:     

1. **추가 정보 제공**: 페이로드에는 사용자 정보, 권한, 사용자의 역할 등의 추가 정보를 포함시킬 수 있다. 이를 활용하여 클라이언트 측에서 사용자에게 맞춤형 환경을 제공하거나, 권한 관리 등을 수행할 수 있다.
    
2. **클라이언트 측 로직 처리**: 클라이언트 측 JavaScript 코드에서 토큰의 페이로드를 읽을 수 있다면, 클라이언트 측에서 특정 로직을 처리하거나 사용자 경험을 개선하는 데 활용할 수 있다.
    
3. **요청 파라미터 대체**: 페이로드에 필요한 정보를 포함시키면, API 요청 시 별도의 요청 파라미터를 추가하지 않아도 될 수 있다.
    
4. **속도 향상**: 토큰 디코딩을 서버에서 처리하는 것보다 클라이언트에서 처리하는 것이 더 빠를 수 있다.
    

그러나 이러한 장점은 보안적인 측면에서 고려되어야 흔다. 페이로드에 민감한 정보가 포함된다면, 클라이언트 측에서 해당 정보를 읽을 수 있으므로 보안 위험이 발생할 수 있다. 따라서 민감한 정보는 토큰의 페이로드에 포함시키지 않는 것이 일반적인 보안 권장 사항이다. 대신, 토큰의 페이로드에는 사용자 식별자 등 필요한 최소한의 정보만 포함시키고, 나머지는 서버에서 안전하게 관리하도록 설계하는 것이 좋다.

## Build: Customizing Simple JWT - HTTP Only Authentication

![](https://i.imgur.com/hqlqK26.png)

### settings.py

```python
  

CORS_ALLOW_ALL_ORIGINS = True

CORS_ALLOW_CREDENTIALS = True

  

# CORS_ALLOWED_ORIGINS = [

#     "http://localhost:5173",

# ]

  
  

CHANNEL_LAYERS = {

    "default": {"BACKEND": "channels.layers.InMemoryChannelLayer"},

}

  

SIMPLE_JWT = {

    "ACCESS_TOKEN_LIFETIME": timedelta(seconds=5),

    "REFRESH_TOKEN_LIFETIME": timedelta(days=1),

    # JWTCookie

    "ACCESS_TOKEN_NAME": "access_token",

    "REFRESH_TOKEN_NAME": "refresh_token",

    "JWT_COOKIE_SAMESITE": "Lax",

}
```


![](https://i.imgur.com/Rjukkn3.png)


### views.py

```python
from django.conf import settings

from rest_framework import viewsets

from rest_framework.response import Response

from drf_spectacular.utils import extend_schema

  

from .models import Account

from .serializers import AccountSerializer

from .schemas import user_list_docs

from rest_framework.permissions import IsAuthenticated

from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

  

class AccountViewSet(viewsets.ViewSet):

    queryset = Account.objects.all()

    permission_classes = [IsAuthenticated]

    @user_list_docs

    def list(self, request):

        user_id = request.query_params.get("user_id")

        queryset = Account.objects.get(id=user_id)

        serializer = AccountSerializer(queryset)

        return Response(serializer.data)

  

class JWTSetCookieMixin:

    def finalize_response(self, request, response, *args, **kwargs):

        if response.data.get("refresh"):

            response.set_cookie(

                settings.SIMPLE_JWT["REFRESH_TOKEN_NAME"],

                response.data["refresh"],

                max_age=settings.SIMPLE_JWT["REFRESH_TOKEN_LIFETIME"],

                httponly=True,

                samesite=settings.SIMPLE_JWT["JWT_COOKIE_SAMESITE"],

            )

        if response.data.get("access"):

            response.set_cookie(

                settings.SIMPLE_JWT["ACCESS_TOKEN_NAME"],

                response.data["access"],

                max_age=settings.SIMPLE_JWT["ACCESS_TOKEN_LIFETIME"],

                httponly=True,

                samesite=settings.SIMPLE_JWT["JWT_COOKIE_SAMESITE"],

            )

        return super().finalize_response(request, response, *args, **kwargs)

class JWTCookieTokenObtainPairView(JWTSetCookieMixin, TokenObtainPairView):

    pass
```

**`JWTSetCookieMixin`:**

이 클래스는 JSON Web Token(JWT)의 쿠키 설정을 담당하는 Django Mixin 다. Mixin은 Django에서 클래스를 조합하여 재사용 가능한 기능을 제공하는 방법 중 하나다. `JWTSetCookieMixin`은 `finalize_response` 메서드를 오버라이드하여 JWT를 쿠키로 설정하는 기능을 추가한다.     

클래스 이름에 "Mixin"이 들어가는 이유는, 이 클래스가 다른 클래스와 함께 조합하여 사용될 수 있기 때문이다. Mixin은 기존의 클래스에 기능을 추가하거나 확장하기 위해 사용되며, 재사용 가능한 코드를 만들 때 유용하다.

`JWTSetCookieMixin` 클래스는 `finalize_response` 메서드를 오버라이드(덮어쓰기)하여 사용자가 로그인 또는 토큰 갱신을 요청할 때, 해당 요청의 응답에 Access Token과 Refresh Token을 쿠키로 설정하는 기능을 추가한다. 이렇게 함으로써 클라이언트(브라우저)에서 쿠키로 토큰을 저장하고 사용할 수 있게 된다.       

`finalize_response` 메서드는 Django REST framework의 View 클래스에서 응답이 최종적으로 만들어질 때 호출되는 메서드다. `JWTSetCookieMixin`에서는 `finalize_response`를 재정의하여 Access Token과 Refresh Token을 쿠키로 설정하는 동작을 수행한다. 이 클래스를 상속받은 뷰 클래스에서 이 기능을 사용할 수 있다.     

**`JWTCookieTokenObtainPairView`:**

이 클래스는 JSON Web Token(JWT)의 Access Token과 Refresh Token을 쿠키로 설정하는 뷰 클래스다. 이 클래스는 `JWTSetCookieMixin`을 상속받아서 `finalize_response` 메서드를 사용하며, `TokenObtainPairView`의 기능을 그대로 사용하면서 JWT 토큰을 쿠키로 설정하는 추가 동작을 수행한다.     

`JWTCookieTokenObtainPairView`는 `TokenObtainPairView`를 상속받았기 때문에, `TokenObtainPairView`의 모든 동작과 기능을 포함하면서 쿠키 설정 기능이 추가된 형태로 동작한다. 즉, 로그인 요청을 받아 유효한 사용자라면 Access Token과 Refresh Token을 생성하고, 이를 쿠키로 설정하여 응답을 반환한다.     

이렇게 `JWTCookieTokenObtainPairView` 클래스는 상속을 통해 기존 기능을 확장하고 새로운 기능을 추가한 뷰 클래스다. 따라서 별도의 내용이 없어도, 상속받은 클래스의 동작과 기능을 모두 사용할 수 있다.      

----- 
`set_cookie`는 Django의 HTTP 응답(Response) 객체에서 제공하는 메서드 중 하나로, 클라이언트(브라우저)에게 쿠키를 설정하는 데 사용됩니다. 이 메서드를 사용하여 쿠키를 설정하면, 서버에서 클라이언트로 쿠키 데이터를 전송하게 됩니다. 
```python
response.set_cookie(
    key,                  # 쿠키의 이름
    value,                # 쿠키의 값
    max_age=None,         # 쿠키의 최대 수명 (초 단위)
    expires=None,         # 쿠키의 만료 시간 (datetime 객체)
    path='/',             # 쿠키의 유효 경로
    domain=None,          # 쿠키의 도메인
    secure=None,          # HTTPS 연결에서만 전송할지 여부
    httponly=False,       # JavaScript에서 접근할 수 없는 쿠키인지 여부
    samesite=None         # SameSite 속성 설정 (Lax, Strict 등)
)

```


위의 코드에서 사용된 `response.set_cookie`는 쿠키를 설정하는 메서드다. 코드의 목적은 JSON Web Token(JWT)의 Access Token을 쿠키로 설정한다. 각 매개변수의 역할은 다음과 같다:

- `key`: 쿠키의 이름, 여기서는 `settings.SIMPLE_JWT["ACCESS_TOKEN_NAME"]`로 설정되어 있을 것으로 추정된다.
- `value`: 쿠키의 값으로, JWT의 Access Token을 사용한다.
- `max_age`: 쿠키의 최대 수명을 설정합니다. 여기서는 `settings.SIMPLE_JWT["ACCESS_TOKEN_LIFETIME"]`로 설정되어 있을 것으로 추정된다.
- `httponly`: `True`로 설정하면, JavaScript에서 접근할 수 없는 쿠키로 설정된다.
- `samesite`: 쿠키의 SameSite 속성을 설정한다. 이는 CSRF(Cross-Site Request Forgery) 공격을 방지하기 위한 설정으로, `settings.SIMPLE_JWT["JWT_COOKIE_SAMESITE"]`로 설정되어 있을 것으로 추정된다.
- `response.data["access"]`는 Django REST framework의 HTTP 응답(Response) 객체에서 "access"라는 이름의 데이터를 가져오는 것을 나타낸다. API 요청에 대한 응답 데이터에서 "access"라는 키(Key)를 가진 값을 추출한다.

일반적으로 Django REST framework에서 API 요청에 대한 응답은 JSON 형식으로 반환된다. JSON은 JavaScript Object Notation의 약자로, 데이터를 표현하고 교환하기 위한 경량의 데이터 형식이다.

예를 들어 다음과 같은 서버응답이 있다고 한다면

```python
{
  "access": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"

}

```

위의 JSON 응답에서 "access"라는 키에 해당하는 값인 JWT(JSON Web Token)이 포함되어 있다. `response.data["access"]`를 사용하면 이 JWT 값을 추출하여 변수에 할당하거나 다른 곳에 사용할 수 있다. 이 JWT는 클라이언트(브라우저)에서 저장하고, 이후 요청 시 인증 및 권한 부여에 사용될 수 있다.     

따라서 `response.data["access"]`는 Django REST framework에서 특정 API 요청에 대한 응답 데이터 중 "access" 키에 해당하는 값을 가져오는 것을 나타낸다.    

이러한 방식으로 `response.set_cookie`를 사용하여 쿠키를 설정하면, 해당 응답이 클라이언트에게 전송될 때 쿠키 데이터도 함께 전송되며, 클라이언트(브라우저)에 저장된다.


## Build: JWTAauthentication Class Customizations

### settings.py
```python
REST_FRAMEWORK = {

    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',

    'DEFAULT_AUTHENTICATION_CLASSES': [

        # 'rest_framework.authentication.SessionAuthentication',

        # 'rest_framework_simplejwt.authentication.JWTAuthentication',

        'account.authenticate.JWTCookieAuthentication',

    ],

}
```

### authenticate.py
```python
from rest_framework_simplejwt.authentication import JWTAuthentication

from django.conf import settings

  

class JWTCookieAuthentication(JWTAuthentication):

    def authenticate(self, request):

        raw_token = request.COOKIES.get(settings.SIMPLE_JWT["ACCESS_TOKEN_NAME"]) or None

        if raw_token is None:

            return None

        validate_token = self.get_validated_token(raw_token)

        return self.get_user(validate_token), validate_token
```

**기존의 JWTAuthentication과의 차이점:**

기본적으로 `rest_framework_simplejwt.authentication.JWTAuthentication` 클래스를 사용하여 JWT 기반의 인증을 구현할 수 있다. 그러나 지금 코드에서는 이 클래스를 상속받아서 커스터마이즈한 `JWTCookieAuthentication` 클래스를 사용하고 있다.

주요 차이점은 다음과 같다:

1. **쿠키 기반 JWT**: `JWTCookieAuthentication` 클래스는 JWT 토큰을 쿠키에 저장하여 인증을 처리합니다. 이로써 JWT 토큰이 HTTP 헤더에 직접 노출되지 않고, `settings.SIMPLE_JWT["ACCESS_TOKEN_NAME"]`으로 설정된 쿠키에 저장되어 브라우저에서 관리한다. 이렇게 함으로써 쿠키와 관련된 보안 설정을 활용할 수 있다.
    
2. **사용자 정의 메서드**: `authenticate` 메서드 내에서 JWT 토큰을 추출하고 검증하는 과정을 직접 구현하고 있다. `get_validated_token` 메서드와 `get_user` 메서드를 사용하여 토큰 검증과 사용자 정보 추출을 처리한다.
    

**사용 이유:**

1. **보안 강화**: JWT 토큰을 쿠키에 저장함으로써, HTTP 헤더를 통해 토큰이 노출되는 위험을 감소시킬 수 있다.
    
2. **동일 출처 정책 (Same-Origin Policy) 준수**: 쿠키는 동일 출처 정책을 준수하면서 다른 도메인에서도 사용할 수 있다. 이를 활용하여 여러 도메인 간에도 쉽게 인증 정보를 공유할 수 있다.
    
3. **특화된 요구 사항**: 기존의 JWTAuthentication을 커스터마이즈하여 특정 요구 사항에 맞게 동작하도록 조정하는 것이 목적일 수 있다.
    
4. **보안 설정 활용**: 쿠키 설정 관련 보안 옵션을 활용하여 더욱 안전한 인증을 구현할 수 있다.
    

즉, 이러한 커스터마이즈를 통해 보안과 사용성을 모두 고려한 효율적인 인증 시스템을 구현할 수 있다. 

### TestLogin.tsx

```typescript
const getUserDetails = async () =>{
    try {
        const response = await jwtAxios.get(
            `http://127.0.0.1:8000/api/account/?user_id=1`,
            {
                withCredentials: true
            }
        );

        const userDetails = response.data;
        setUsername(userDetails.username);

    } catch (err: any) {
        return err;
    }
}

```

1. `Authorization` 헤더: 기존 코드에서는 `localStorage`에서 가져온 Access Token을 `Authorization` 헤더에 추가하여 요청을 보내고 있었다. 하지만 변경된 코드에서는 `JWTCookieAuthentication` 클래스를 사용하므로, 토큰은 쿠키에 저장되어 있어 별도의 `Authorization` 헤더가 더 이상 필요하지 않다.
    
2. `withCredentials`: 변경된 코드에서는 `withCredentials: true` 옵션을 사용하여 CORS(Cross-Origin Resource Sharing) 요청 시 쿠키를 함께 전송하도록 설정하고 있다. 이는 쿠키 기반의 인증 시스템에서 필요한 설정으로, 서버로부터 쿠키를 받아올 수 있도록 해준다.
    

즉, `JWTCookieAuthentication` 클래스를 사용하여 JWT 인증을 쿠키 기반으로 커스터마이즈했기 때문에, 인증 토큰을 헤더에 직접 추가하지 않고도 쿠키를 통해 인증을 처리할 수 있게 되었다. 이로써 코드가 간결해지고 보안성도 높아질 수 있다.

#### withCredentials 가 뭐지?

`withCredentials`는 XMLHttpRequest 또는 Fetch API를 사용하여 웹 브라우저에서 HTTP 요청을 보낼 때, 해당 요청에 쿠키와 같은 인증 정보를 포함시키도록 지정하는 옵션이다. 이 옵션을 사용하면 동일 출처 정책(Same-Origin Policy)을 준수하면서도 다른 도메인 간에 인증 정보를 전달할 수 있다.     

동일 출처 정책은 웹 보안을 위해 도입된 정책으로, 다른 출처(도메인)에서 온 리소스 요청을 차단하여 보안 상의 이슈를 방지하는 역할을 한다. 그러나 일부 상황에서는 다른 도메인 간에도 인증 정보를 공유해야 하는 경우가 있다. 이때 `withCredentials` 옵션을 사용하면, 웹 브라우저는 요청을 보낼 때 쿠키와 같은 인증 정보를 함께 전송할 수 있게 된다.     

간단한 예를 들어 설명해보겠습니다. 브라우저에서 `http://example.com` 도메인에서 실행 중인 웹 페이지가 있다고 가정해보자. 이 웹 페이지에서 `http://api.example.com` 도메인에 AJAX 요청을 보내려면, 기본적으로는 동일 출처 정책에 의해 요청이 차단될 수 있다. 그러나 `withCredentials` 옵션을 사용하면 아래와 같이 요청을 보낼 수 있다:     

```javascript
fetch('http://api.example.com/data', {
    method: 'GET',
    credentials: 'include'  // withCredentials 옵션
})

```

`credentials: 'include'`는 `withCredentials` 옵션을 활성화하는 것을 의미한다. 이렇게 하면 브라우저는 요청을 보낼 때 현재 페이지의 쿠키를 함께 전송하여 인증 정보를 공유한다. 서버는 이 인증 정보를 사용하여 요청을 처리하고 응답을 반환한다.     

요약하면, `withCredentials` 옵션은 CORS 요청에서 쿠키와 같은 인증 정보를 사용하고자 할 때 사용되며, 서로 다른 도메인 간에 인증 정보를 안전하게 전송할 수 있는 방법을 제공한다.

## Build: Returning the User ID - Subclassing JWT Serializer

### authenticate.py

```python
from django.conf import settings

from rest_framework_simplejwt.authentication import JWTAuthentication

  
  

class JWTCookieAuthentication(JWTAuthentication):

    def authenticate(self, request):

        raw_token = request.COOKIES.get(settings.SIMPLE_JWT["ACCESS_TOKEN_NAME"]) or None

  

        if raw_token is None:

            return None

  

        validated_token = self.get_validated_token(raw_token)

        return self.get_user(validated_token), validated_token
```

 Django의 인증 시스템을 확장하여 JWT(Json Web Token)을 사용한 커스텀 인증 방식을 구현했다. 코드 내부에 있는 `JWTCookieAuthentication` 클래스는 `JWTAuthentication` 클래스를 상속하고 있다. 이 클래스는 JWT 토큰을 사용하여 사용자 인증을 처리하는 데 도움이 되는 메서드를 제공한다.

주요 메서드인 `authenticate` 메서드는 사용자의 요청(request)을 매개변수로 받아와서 다음과 같은 작업을 수행한다:

1. `request.COOKIES.get(settings.SIMPLE_JWT["ACCESS_TOKEN_NAME"])` 코드는 설정 파일(`settings`)에서 정의된 JWT 액세스 토큰의 이름을 이용하여 클라이언트의 쿠키에서 해당 토큰 값을 가져온다.
    
2. 가져온 토큰 값을 `raw_token` 변수에 할당한다. 만약 쿠키에 해당 토큰이 없다면 `None`을 할당한다.
    
3. `if raw_token is None:` 코드는 `raw_token`이 `None`인 경우, 즉 쿠키에서 토큰을 찾지 못한 경우에는 `None`을 반환하여 인증을 실패로 처리한다.
    
4. 그렇지 않은 경우, `self.get_validated_token(raw_token)`을 사용하여 토큰을 유효성 검사하고, `validated_token` 변수에 유효한 토큰을 할당한다.
    
5. 마지막으로 `self.get_user(validated_token)`을 사용하여 유효한 토큰에 대한 사용자를 가져오고, `validated_token`과 함께 반환하여 인증된 사용자와 토큰 정보를 함께 반환한다.
    

이 커스텀 인증 클래스는 JWT 토큰을 사용하여 사용자 인증을 처리하는 역할을 한다.

#### http 쿠키 조작이 안된다고 했는데 그럼 이렇게 접근하는 건 뭘까?

쿠키에서 토큰 값을 가져오는 것은 가능하다. 토큰 값을 변수에 할당하여 사용하는 것은 올바른 접근 방법이다. 이 부분이 쿠키 조작과는 별개의 개념이다.     

쿠키는 클라이언트(웹 브라우저)에 저장되는 작은 데이터 조각이며, 서버에서 클라이언트로 전송하여 저장하고 나중에 서버로 다시 보낼 수 있다. 따라서 서버는 클라이언트에게 토큰을 쿠키에 저장하도록 요청하고, 클라이언트는 해당 토큰을 쿠키에 저장하여 서버에 보낼 수 있다.     

따라서 코드에서 토큰 값을 쿠키에서 가져오고 변수에 할당하는 부분은 제대로 작동하는 방식이다. 이는 토큰 값을 추출하여 사용하는 과정이며, 쿠키 조작과는 아무런 관련이 없다. 쿠키 조작은 클라이언트가 의도적으로 쿠키의 값을 변경하거나 조작하는 것을 의미한다. 하지만 코드에서는 단지 쿠키에 저장된 토큰 값을 가져와서 사용하는 것뿐이다.

### serializers.py

```python
from rest_framework import serializers

from rest_framework_simplejwt.serializers import TokenObtainPairSerializer, TokenRefreshSerializer

from .models import Account

from django.conf import settings

from rest_framework_simplejwt.exceptions import InvalidToken

  

class AccountSerializer(serializers.ModelSerializer):

    class Meta:

        model = Account

        fields = ("username",)

  

class CustomTokenObtainPairSerializer(TokenObtainPairSerializer):

    def get_token(cls, user):

        token = super().get_token(user)

        token["example"] = "example"

        return token

    def validate(self, attrs):

        data = super().validate(attrs)

        data["user_id"] = self.user.id

        return data

class JWTCookieTokenRefreshSerializer(TokenRefreshSerializer):

    refresh = None

  

    def validate(self, attrs):

        attrs["refresh"] = self.context["request"].COOKIES.get(settings.SIMPLE_JWT["REFRESH_TOKEN_NAME"])

  

        if attrs["refresh"]:

            return super().validate(attrs)

        else:

            raise InvalidToken("No valid refresh token found")
```

Django REST framework와 `djangorestframework-simplejwt` 라이브러리를 사용하여 JWT(Json Web Token) 인증에 관련된 시리얼라이저와 커스텀 시리얼라이저를 만들었다.

1. `AccountSerializer` 클래스: `Account` 모델을 기반으로 한 시리얼라이저로, `username` 필드만을 포함하여 사용자 정보를 시리얼라이즈하는 역할을 한다.
    
2. `CustomTokenObtainPairSerializer` 클래스: 기본 `TokenObtainPairSerializer`를 확장한 커스텀 시리얼라이저다. `get_token` 메서드를 오버라이드하여 토큰에 추가 정보를 넣고, `validate` 메서드를 오버라이드하여 사용자 ID를 반환한다.
    
3. `JWTCookieTokenRefreshSerializer` 클래스: 기본 `TokenRefreshSerializer`를 확장한 커스텀 시리얼라이저로, 쿠키에서 리프레시 토큰을 가져오는 역할을 한다. 리프레시 토큰이 없는 경우 `InvalidToken` 예외를 발생시킨다.
    

### account/views.py

```python
from django.conf import settings

from rest_framework import viewsets

from rest_framework.permissions import IsAuthenticated

from rest_framework.response import Response

from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

  

from .models import Account

from .schemas import user_list_docs

from .serializers import AccountSerializer, CustomTokenObtainPairSerializer, JWTCookieTokenRefreshSerializer

  
  

class AccountViewSet(viewsets.ViewSet):

    queryset = Account.objects.all()

    permission_classes = [IsAuthenticated]

  

    @user_list_docs

    def list(self, request):

        user_id = request.query_params.get("user_id")

        queryset = Account.objects.get(id=user_id)

        serializer = AccountSerializer(queryset)

        return Response(serializer.data)

  
  

class JWTSetCookieMixin:

    def finalize_response(self, request, response, *args, **kwargs):

        if response.data.get("refresh"):

            response.set_cookie(

                settings.SIMPLE_JWT["REFRESH_TOKEN_NAME"],

                response.data["refresh"],

                max_age=settings.SIMPLE_JWT["REFRESH_TOKEN_LIFETIME"],

                httponly=True,

                samesite=settings.SIMPLE_JWT["JWT_COOKIE_SAMESITE"],

            )

        if response.data.get("access"):

            response.set_cookie(

                settings.SIMPLE_JWT["ACCESS_TOKEN_NAME"],

                response.data["access"],

                max_age=settings.SIMPLE_JWT["ACCESS_TOKEN_LIFETIME"],

                httponly=True,

                samesite=settings.SIMPLE_JWT["JWT_COOKIE_SAMESITE"],

            )

  
  

            del response.data["access"]

  

        return super().finalize_response(request, response, *args, **kwargs)

  
  

class JWTCookieTokenObtainPairView(JWTSetCookieMixin, TokenObtainPairView):

    serializer_class = CustomTokenObtainPairSerializer

  

class JWTCookieTokenRefreshView(JWTSetCookieMixin, TokenRefreshView):

    serializer_class = JWTCookieTokenRefreshSerializer
```

1. `AccountViewSet` 클래스:
    
    - `viewsets.ViewSet`를 상속하여 사용자 정보를 조회하는 뷰셋을 정의한다.
    - `list` 메서드는 `@user_list_docs` 데코레이터로 장식되어 있으며, 사용자 ID를 쿼리 파라미터로 받아와 해당 ID의 사용자 정보를 시리얼라이즈하여 응답한다. 
2. `JWTSetCookieMixin` 클래스:
    
    - 토큰 발급 및 갱신 시 토큰 값을 쿠키에 저장하는 기능을 담은 믹스인 클래스다.
    - `finalize_response` 메서드를 오버라이드하여 토큰 값을 쿠키에 설정하고, 응답을 반환하기 전에 토큰 값을 응답 데이터에서 제거한다.
3. `JWTCookieTokenObtainPairView` 클래스:
    
    - `TokenObtainPairView`를 상속한 커스텀 뷰 클래스로, `JWTSetCookieMixin`을 사용하여 토큰 값을 쿠키에 설정하는 기능을 추가한다.
    - `CustomTokenObtainPairSerializer` 시리얼라이저를 사용하여 토큰을 발급한다.
4. `JWTCookieTokenRefreshView` 클래스:
    
    - `TokenRefreshView`를 상속한 커스텀 뷰 클래스로, `JWTSetCookieMixin`을 사용하여 토큰 값을 쿠키에 설정하는 기능을 추가한다.
    - `JWTCookieTokenRefreshSerializer` 시리얼라이저를 사용하여 토큰을 갱신한다.

JWT 토큰을 발급하고 관리하는 동작을 커스터마이징하여 토큰 값을 쿠키에 저장하는 기능을 추가했다. 

#### `JWTCookieTokenObtainPairView`와 `JWTCookieTokenRefreshView`

`JWTCookieTokenObtainPairView`와 `JWTCookieTokenRefreshView` 클래스는 Django REST framework의 뷰 클래스인 `TokenObtainPairView`와 `TokenRefreshView`를 커스터마이징하여 JWT 토큰을 발급 및 갱신하는 과정에서 쿠키에 토큰 값을 저장하도록 확장한다.

1. `JWTCookieTokenObtainPairView`:
    
    - 이 클래스는 `TokenObtainPairView`를 상속받아서 만들었다. 따라서 기본적으로 `TokenObtainPairView`의 동작을 포함하면서 그 기능을 확장한다.
    - `JWTSetCookieMixin`을 상속하여 해당 믹스인 클래스에 정의된 `finalize_response` 메서드가 사용된다.
    - `serializer_class`는 `CustomTokenObtainPairSerializer`로 설정되어 있으며, 이 커스텀 시리얼라이저를 사용하여 토큰을 발급한다.
2. `JWTCookieTokenRefreshView`:
    
    - 이 클래스도 `TokenRefreshView`를 상속받아서 작성되었으며, 기능 확장과 쿠키에 토큰 값을 저장하는 역할을 한다.
    - 마찬가지로 `JWTSetCookieMixin`을 상속하고, `serializer_class`는 `JWTCookieTokenRefreshSerializer`로 설정되어 있다.

동작 원리:

1. 클라이언트(웹 브라우저)가 JWT 토큰을 발급하거나 갱신하는 요청을 서버에 보낸다.
    
2. 서버에서는 해당 요청에 대한 뷰 클래스(`JWTCookieTokenObtainPairView` 또는 `JWTCookieTokenRefreshView`)가 실행된다.
    
3. 해당 뷰 클래스 내에서는 먼저 상속받은 `JWTSetCookieMixin`의 `finalize_response` 메서드가 호출된다. 이 메서드는 응답 데이터에 "refresh" 및 "access" 토큰 값이 있으면 이를 쿠키에 설정한다. 그리고 "access" 토큰 값을 응답 데이터에서 제거한다.
    
4. 그 후, 요청에 따라 토큰을 발급하거나 갱신하는 기본 동작이 수행된다. 이 때, 커스텀 시리얼라이저(`CustomTokenObtainPairSerializer` 또는 `JWTCookieTokenRefreshSerializer`)가 사용된다.
    
5. 서버는 클라이언트에게 응답을 반환하면서 쿠키에 토큰 값을 설정하여 전송한다.
    

이러한 방식으로, JWT 토큰 발급 및 갱신 요청에 대한 응답에 토큰 값을 쿠키로 전달하여 토큰을 더 편리하게 관리하고 활용할 수 있도록 만들었다.

### urls.py

```python
 urlpatterns = [
    path("api/token/refresh/", JWTCookieTokenRefreshView.as_view(), name="token_refresh"),
] + router.urls
```

`TokenRefreshView` 에서 새로 커스텀으로 만든 `JWTCookieTokenRefreshView` 로 갈아끼워준다

### jwinterceptor.ts

```typescript
import axios from "axios";

import { useNavigate } from "react-router-dom";

import { useAuthService } from "../services/AuthServices";

  

const useAxiosWithJwtInterceptor = () => {

  const jwtAxios = axios.create({});

  const navigate = useNavigate();

  const { logout } = useAuthService()

  

  jwtAxios.interceptors.response.use(

    (response) => {

      return response;

    },

    async (error) => {

      const originalRequest = error.config;

      if (error.response.status === 401 || 403) {

        axios.defaults.withCredentials = true;

          try {

            const response = await axios.post(

              "http://127.0.0.1:8000/api/token/refresh/"

            );

            if (response["status"] == 200) {

              return jwtAxios(originalRequest);

            }

          } catch (refreshError) {

            logout()

            const goLogin = () => navigate("/login");

            goLogin();

            return Promise.reject(refreshError);

          }

      }

    }

  );

  return jwtAxios;

};

export default useAxiosWithJwtInterceptor;
```
JWT 토큰을 사용하여 Axios 요청에 대한 응답을 관리한다. JWT 토큰이 만료되었을 때 자동으로 토큰을 갱신하고, 갱신에 실패하면 로그아웃을 수행하고 로그인 페이지로 이동하는 등의 작업을 수행한다.


1. `useAxiosWithJwtInterceptor` 함수:
    
    - `axios.create({})`를 사용하여 새로운 Axios 인스턴스를 생성한다. 이 인스턴스는 토큰 인터셉터와 함께 사용된다.
    - `useNavigate` 훅을 사용하여 리액트 라우터의 `navigate` 함수를 가져온다.
    - `useAuthService` 훅을 사용하여 인증 서비스의 `logout` 함수를 가져온다.
    - `jwtAxios` 인스턴스의 `response` 인터셉터를 설정한다. 이 인터셉터는 요청에 대한 응답을 처리하며, 성공적인 응답은 그대로 반환하고, 토큰 관련 오류가 발생한 경우에 대한 처리를 수행한다.
2. `jwtAxios.interceptors.response.use` 함수:
    
    - `response`와 `error`를 처리하는 콜백 함수가 제공된다.
    - `error.response.status`로부터 HTTP 응답 상태 코드를 가져와서, 토큰 관련 오류인지(401 - Unauthorized 또는 403 - Forbidden) 확인한다.
    - 토큰 관련 오류가 확인되면, 토큰을 갱신하는 요청을 수행한다. (`axios.post`를 사용하여 토큰을 갱신하는 API 엔드포인트를 호출)
    - 토큰 갱신에 성공하면 원래 요청을 다시 보내고, 실패하면 로그아웃을 수행하고 로그인 페이지로 이동한다.

JWT 토큰 관련 오류를 처리하여 토큰을 갱신하고 세션을 관리하는데 사용된다. 오류 처리 및 갱신 로직은 비동기적으로 처리되며, 실패 시 로그인 페이지로 리디렉션된다. 이를 통해 사용자 경험을 향상시키고 토큰 관리를 자동화할 수 있다.

### AuthServices.ts
```typescript
import axios from "axios";

import { AuthServiceProps } from "../@types/auth-service";

import { useState } from "react";

  
  

export function useAuthService(): AuthServiceProps {

  

    const getInitialLoggedInValue = () => {

        const loggedIn = localStorage.getItem("isLoggedIn");

        return loggedIn !== null && loggedIn === "true";

      };

  

    const [isLoggedIn, setIsLoggedIn] = useState<boolean>((getInitialLoggedInValue))

    const getUserDetails = async () =>{

        try {

            const userId = localStorage.getItem("user_id")

            const response = await axios.get(

                `http://127.0.0.1:8000/api/account/?user_id=${userId}`,

                {

                    withCredentials: true

                }

            );

            const userDetails = response.data

            localStorage.setItem("username", userDetails.username);

            setIsLoggedIn(true);

            localStorage.setItem("isLoggedIn", "true")

        } catch (err: any) {

            setIsLoggedIn(false)

            localStorage.setItem("isLoggedIn", "false")

            return err;

        }

    }

  
  

    const login = async (username: string, password: string) =>{

        try {

            const response = await axios.post(

                "http://127.0.0.1:8000/api/token/", {

                    username,

                    password,

            }, { withCredentials: true }

            );

  

            // console.log(response.data)

            const user_id = response.data.user_id

            localStorage.setItem("isLoggedIn", "true")

            localStorage.setItem("user_id", user_id)

            setIsLoggedIn(true)

            getUserDetails()

  

        } catch (err: any) {

            return err.response.status;

        }

    }

  

    const logout = () => {

        localStorage.setItem("isLoggedIn", "false")

        localStorage.removeItem("user_id")

        localStorage.removeItem("username");

        setIsLoggedIn(false);

  

    }

  

    return {login, isLoggedIn, logout}

}
```


1. `getInitialLoggedInValue` 함수:
    
    - `localStorage`에서 "isLoggedIn" 키의 값을 가져와서 사용자의 로그인 상태를 확인하는 함수다.
2. `useAuthService` 훅:
    
    - `useState` 훅을 사용하여 `isLoggedIn` 상태와 이를 변경하는 `setIsLoggedIn` 함수를 관리한다.
    - `getUserDetails` 함수는 로그인된 사용자의 상세 정보를 가져오는 역할을 한다. 이 함수는 서버에 GET 요청을 보내고, 응답으로 받은 사용자 정보를 `localStorage`에 저장하고 로그인 상태를 설정한다.
3. `login` 함수:
    
    - 사용자 로그인을 처리하는 함수입니다. 서버에 POST 요청을 보내고 토큰을 받아온다. 토큰을 `localStorage`에 저장하고, `getUserDetails` 함수를 호출하여 사용자 정보를 가져온다.
4. `logout` 함수:
    
    - 사용자 로그아웃을 처리하는 함수로, `localStorage`에서 로그인 정보를 초기화하고 상태를 업데이트한다.
5. `return` 문:
    
    - `login`, `isLoggedIn`, `logout` 함수를 반환하여, 이 훅을 사용하는 컴포넌트에서 각각의 인증 관련 기능을 활용할 수 있도록 한다.


![](https://i.imgur.com/iJ3R1jo.png)

처음 access_token 시간이 만료되면 자동으로 refresh_token이 발급된다.

![](https://i.imgur.com/O9NWtUF.png)

refresh_token 시간이 만료되면 자동으로 로그아웃이 되어 로그인 페이지로 이동한다.

## Build: WebSocket Authentication

웹소켓은 기본적으로 일반적으로 보호되거나 암호화되지 않는다.
채팅 서비스에서 사용하는 것처럼 웹소켓을 사용할 때는 클라이언트와 서버 간의 통신을 보호하기 위한 보안 조치를 고려하는 것이 중요하다.     
첫 번째로는 HTTPS 를 사용한다.       

HTTPS는 HTTP의 보안 버전이다.     
Https는 클라이언트와 서버 간의 통신을 암호화하기 위해 SSL(보안 소켓 레이어) 또는 그 후속 버전인 TLS(전송 계층 보안)을 사용한다.


### HTTPS 

HTTPS(Hypertext Transfer Protocol Secure)는 월드 와이드 웹(WWW)에서 정보를 안전하게 전송하기 위한 프로토콜이다. HTTP의 보안 버전으로서, 데이터의 암호화와 인증을 통해 사용자의 개인 정보와 웹 사이트 간의 통신을 보호한다.     

HTTPS의 주요 특징과 작동 방식은 다음과 같다:

1. **암호화된 통신**: HTTPS는 데이터를 암호화하여 제3자가 데이터를 열어보거나 변조할 수 없도록 한다. 이를 위해 SSL 또는 TLS 프로토콜을 사용하여 데이터를 암호화하고 복호화한다.
    
2. **인증 및 서버 신원 검증**: HTTPS는 인증서를 사용하여 웹 서버의 신원을 검증한다. 웹 브라우저는 서버로부터 받은 인증서를 확인하고, 신뢰할 수 있는 인증 기관(Certificate Authority)에 의해 서명된 것인지 확인한다.
    
3. **데이터 무결성**: HTTPS는 데이터가 전송 중에 변경되지 않도록 보장한다. 데이터의 무결성을 보호하여 중간에서 데이터가 변조되거나 손상되는 것을 방지한다.
    
4. **보안 연결 설정**: 사용자가 HTTPS로 보호된 웹 사이트에 접속할 때, 웹 브라우저와 웹 서버 간에 보안 연결이 설정된다. 이를 통해 사용자와 웹 사이트 간의 모든 통신이 암호화되어 보호된다.
    

HTTPS의 사용은 웹 사이트의 보안과 개인 정보 보호에 중요한 역할을 한다. 사용자의 개인 정보, 로그인 정보, 결제 정보 등 민감한 데이터가 웹을 통해 전송되는 경우, HTTPS를 통해 데이터 보안을 강화할 수 있다.      

HTTPS를 구현하려면 다음 단계를 따를 수 있습니다:

1. **SSL/TLS 인증서 획득**: 웹 호스팅 공급자 또는 인증 기관을 통해 SSL/TLS 인증서를 획득한다.
    
2. **웹 서버 설정**: 웹 서버 설정을 수정하여 SSL/TLS 인증서를 설치하고 HTTPS를 활성화한다.
    
3. **링크 및 리소스 수정**: 웹 사이트 내의 모든 링크와 리소스를 HTTPS로 수정하여 보안 연결을 유지한다.
    
4. **301 리다이렉션 설정**: 웹 사이트에 HTTP 요청이 오면 HTTPS로 리다이렉션되도록 설정한다.
    
5. **사용자 경험 최적화**: HTTPS로 전환 시 웹 사이트의 속도와 성능을 최적화하여 사용자 경험을 향상시킨다.
    

HTTPS를 사용하면 웹 사이트가 안전하게 통신하고 사용자의 개인 정보를 보호할 수 있으므로, 모든 웹 사이트 운영자에게 권장되는 보안 조치 중 하나다.     
### SSL, TLS

SSL(Secure Sockets Layer)과 TLS(Transport Layer Security)은 네트워크 통신에서 보안 연결을 제공하기 위한 프로토콜이다. 이들은 데이터의 기밀성과 무결성을 보호하여 정보가 안전하게 전송되도록 한다.     

1. **SSL (Secure Sockets Layer)**: SSL은 초기에 개발된 보안 프로토콜로, 웹 통신에서 주로 사용되었다. SSL은 서버와 클라이언트 간의 통신을 암호화하고, 인증서를 사용하여 서버의 신원을 검증하며, 데이터 무결성을 보호한다. 그러나 SSL은 보안 결함과 취약점이 발견되어 더 강력하고 안전한 프로토콜인 TLS로 대체되었다.
    
2. **TLS (Transport Layer Security)**: TLS는 SSL의 후속 버전으로, SSL 3.0을 기반으로 개발되었다. TLS는 SSL과 유사한 목적을 가지며, 웹, 이메일, VPN 등 다양한 프로토콜에서 보안 통신을 제공한다. TLS는 버전이 업그레이드되면서 보안 결함을 보완하고, 다양한 강력한 암호화 알고리즘을 지원하며, 보안 프로토콜을 계속 발전시키고 있다.
    

TLS는 주로 다음과 같은 보안 기능을 제공합니다:

- **암호화 (Encryption)**: 데이터를 암호화하여 제3자가 내용을 열어보거나 변조할 수 없도록 한다.
- **인증 (Authentication)**: 서버의 신원을 확인하고, 클라이언트와 서버 간의 신뢰성을 보장한다.
- **데이터 무결성 (Data Integrity)**: 데이터가 전송 중에 변조되지 않도록 보장하며, 중간에서 데이터가 수정되는 것을 방지한다.

TLS는 일반적으로 HTTPS 프로토콜과 함께 사용되며, 사용자가 웹 사이트에 접속할 때 브라우저와 웹 서버 간의 통신을 보호한다. TLS는 공개 키 기반의 암호화를 사용하며, 대부분의 웹 사이트에서는 SSL/TLS 인증서를 통해 웹 사이트의 신원을 검증하고 보안 연결을 설정한다.     

-------
하지면 로컬로 개발할 예정이기 때문에 인증과 권한을 활용해서 보안을 강화하는 방법을 사용해보려고 했다.     

웹소켓 연결에 대한 인증 및 권한 부여 메커니즘을 구현하면서 WebSocket과 함께 전송되는 데이터를 유효성 검사하고 검증하는 다른 유형의 접근 방식도 고려해야 한다.     

실제로 들어오는 데이터를 필터링하고 해당 데이터가 WebSocket을 통해 예상대로 전송되는 것인지 확인하고 적합한지 여부를 확인하는 것은 물론, 실제 전송되는 데이터를 유효성 검사하거나 예상대로 특정 데이터 유형을 사용하고 있는지 확인한다.    

보안을 강화하는 데 고려해야 할 다른 사항은 요청 제한과 쓰로틀링이다.     

웹소켓 연결의 남용이나 과도한 사용을 방지하기 위해 일종의 요청 제한 및 쓰로틀링 메커니즘을 실제로 구현한다.     

물론 여기에는 방화벽, 네트워크 보안과 같은 다른 계층도 있으며, 라이브러리와 프레임워크를 최신 상태로 유지하는 것도 중요하다.       

### WebSocket 을 더 안전하게 사용하기 위한 접근 방식

사용자가 WebSocket에 접근하고 사용하려고 할 때 사용자를 인증하는 방법에 대해 생각해보자     

누군가 WebSocket을 통해 연결을 시도하면, 물론 이것은 이 컨슈러로 라우팅된다.      
그래서 채널 사용자를 초기화하여 세부 정보를 채우고 채널 레이어를 설정할 수 있도록 시작한다.     

그런 다음 연결(connect)을 시작하고 채널 ID를 가져와 사용자를 해당 채널에 추가한다.      
이제 여기서 랜덤한 쿼리를 가지고 있는데, 사용자 ID를 기반으로 사용자를 추출하려고 한다. 현재로서는 고정된 상태 id=1 이다.     
웹소켓 연결 시에 이미 갖고 있는 토큰을 전달한다. 그래서 그 토큰을 가져오고,  그 토큰은 액세스 토큰(access token)이다. 
여기에서 확인할 수 있는 데이터는 사용자 ID 다

우선적으로 해당 토큰이 유효성이 검사되면, 사용자를 인증하고 사용자가 서비스에 성공적으로 액세스할 수 있도록 허용한다.     

요약하자면 토큰을 가져와서 토큰을 인증하거나 토큰을 검증한다.

### Django 에서 JWT 토큰을 검증하는 미드루에어 구현 과정

1. **미들웨어 생성 및 등록**: "JWTAuthMiddleware"라는 이름의 미들웨어를 생성하고 Django 애플리케이션에 등록.
    
2. **미들웨어 동작**: 클라이언트로부터의 요청이 서버로 전달되면, 미들웨어가 초기화된다. 이 때 JWTAuthMiddleware의 초기화 메서드가 호출되고, 해당 미들웨어 객체에 "app"이라는 애플리케이션 객체를 할당한다.
    
3. **토큰 추출**: JWTAuthMiddleware는 클라이언트로부터의 요청에서 JWT 토큰을 추출한다. 이 토큰은 클라이언트의 인증 정보를 포함하고 있다.
    
4. **토큰 유효성 검사**: 추출한 JWT 토큰을 사용하여 토큰의 유효성을 검사한다. 유효하지 않은 토큰은 거부된다.
    
5. **통과 혹은 차단**: 토큰이 유효하다면, 해당 요청은 미들웨어를 통과하고 뷰(view)로 이동하게 된다. 그렇지 않다면, 클라이언트의 요청은 거부된다.
    
6. **요청 처리 및 응답**: 뷰에서 요청을 처리하고 적절한 응답을 생성한다. 이후 응답은 다시 미들웨어를 통과하며 클라이언트로 전송된다.
    
7. **미들웨어 순서 설정**: 이 미들웨어가 어느 시점에 동작할지, 즉 다른 미들웨어들 사이에서 어떤 위치에 배치할지를 결정한다.
------------
### webchat/middleware.py

```python
import jwt

from channels.db import database_sync_to_async

from django.conf import settings

from django.contrib.auth import get_user_model

from django.contrib.auth.models import AnonymousUser

  
  

@database_sync_to_async

def get_user(scope):

    token = scope["token"]

    model = get_user_model()

  

    try:

        if token:

            user_id = jwt.decode(token, settings.SECRET_KEY, algorithms=["HS256"])["user_id"]

            return model.objects.get(id=user_id)

        else:

            return AnonymousUser()

    except (jwt.exceptions.DecodeError, model.DoesNotExist):

        return AnonymousUser()

  
  

class JWTAuthMiddleWare:

    def __init__(self, app):

        self.app = app

  

    async def __call__(self, scope, recieve, send):

        headers_dict = dict(scope["headers"])

        cookies_str = headers_dict.get(b"cookie", b"").decode()

        cookies = {cookie.split("=")[0]: cookie.split("=")[1] for cookie in cookies_str.split("; ")}

        access_token = cookies.get("access_token")

  

        scope["token"] = access_token

        scope["user"] = await get_user(scope)

  

        return await self.app(scope, recieve, send)
```

1. `get_user(scope)` 함수:
    
    - `database_sync_to_async` 데코레이터로 비동기 함수로 만들어진 함수다.
    - 웹 소켓 스코프(scope)를 인자로 받아 해당 스코프의 JWT 토큰을 디코드하여 유저 정보를 가져오는 함수다.
    - JWT 토큰을 디코드하여 user_id를 추출한 후, 해당 user_id로 사용자 정보를 데이터베이스에서 찾아 반환한다.
    - 토큰이 없거나 디코드 불가능하거나 해당 사용자가 없는 경우 `AnonymousUser()`를 반환한다.
2. `JWTAuthMiddleWare` 클래스:
    
    - Django Channels 미들웨어 클래스다.
    - `__init__(self, app)` 메서드: 미들웨어 객체가 생성될 때 호출되며, 앱(app)을 인자로 받아 인스턴스 변수 `self.app`에 저장한다.
    - `__call__(self, scope, receive, send)` 메서드: 웹 소켓 연결 시 호출되는 메서드로, 클라이언트의 요청을 처리한다.
        - 클라이언트의 헤더에서 쿠키를 추출하여 `access_token`을 가져온다.
        - 웹 소켓 스코프에 `token` 키로 `access_token`을 저장한다.
        - `get_user(scope)` 함수를 사용하여 JWT 토큰을 디코드하고, 해당 사용자 정보를 스코프에 `user` 키로 저장한다.
        - `self.app`에 원래의 앱(app)을 호출하여 요청을 계속 처리한다.

`database_sync_to_async`는 Django Channels에서 사용되는 데코레이터(decorator)로, 동기 함수를 비동기 함수로 변환하는 역할을 한다. Django Channels는 비동기(Asynchronous) 코드를 사용하여 웹 소켓과 같은 실시간 통신을 처리하는데, 이 때 데이터베이스와 같은 동기 코드와의 상호작용이 필요한 경우가 있다.     

`database_sync_to_async` 데코레이터는 이러한 상황에서 동기 코드를 비동기 코드와 조화롭게 사용할 수 있도록 도와준다. 이 데코레이터를 사용하면 동기 함수를 비동기로 변환하여, 해당 함수 내에서 데이터베이스와 같은 동기 작업을 수행할 수 있다.     

예를 들어, 위의 코드에서 `get_user(scope)` 함수가 `database_sync_to_async` 데코레이터로 감싸져 있다. 이는 해당 함수가 데이터베이스 조회와 같은 동기 작업을 수행하기 때문이다. 따라서 해당 함수를 웹 소켓 연결과 같은 비동기 환경에서 사용하기 위해 이 데코레이터를 사용하여 비동기 함수로 변환했다.

간단한 예제를 통해 설명하면 다음과 같다:

```python
from channels.db import database_sync_to_async

@database_sync_to_async
def sync_function():
    result = perform_sync_operation()
    return result

async def async_function():
    result = await sync_function()
    # 비동기 코드 작성

```

위의 코드에서 `sync_function()`은 동기 함수이지만, `database_sync_to_async` 데코레이터를 사용하여 `async_function()` 내에서 비동기로 사용될 수 있도록 변환했다.     

요약하면, `database_sync_to_async` 데코레이터는 Channels에서 동기 코드와 비동기 코드를 함께 사용하기 위한 도구로 활용된다.


----

사실 channel 의 공식문서에서는 조금 더 자세한 내용을 원한다면, 인증(authentication)으로 가서 사용자 정의 인증(custom authentication)을 확인해보면 된다. 여기서는 기본적으로 우리가 자체적으로 작성한 커스텀 미들웨어를 사용하여 세부 정보를 구문 분석하거나 데이터를 처리하는 방식을 따르고 있다. 이렇게 해서 URL과 컨슈머에 도달하기 전에 전송된 데이터를 처리한다. 완벽히는 아니여도 지금 과정은 유사한 패턴을 따르고 있다.

간단히 말하면, scope, receive, send는 현재 미들웨어에서 처리되고 있는 요청 및 응답을 나타내는 매개변수다. 우리는 예를 들어 scope 내부에서 쿠키에 접근하여 쿠키를 찾을 수 있다.     

### 웹 소켓 스코프

웹 소켓 스코프(Websocket Scope)는 Django Channels 프레임워크에서 사용되는 개념으로, 웹 소켓 연결 및 통신을 관리하는 데 필요한 정보와 데이터를 담고 있는 딕셔너리다. 웹 소켓 스코프는 웹 소켓 연결이 생성될 때마다 생성되며, 해당 연결의 상태와 요청, 응답 등을 관리하는 역할을 한다. 웹 소켓 스코프는 비동기 환경에서 웹 소켓과 상호작용하기 위해 사용된다.     

웹 소켓 스코프는 다양한 정보를 포함하고 있는데, 주요한 내용은 아래와 같다:

1. `type`: 이벤트의 종류를 나타냅니다. "websocket.connect", "websocket.disconnect", "websocket.receive" 등이 있다.
2. `path`: 연결된 웹 소켓의 경로를 나타낸다.
3. `headers`: 웹 소켓 연결에 대한 헤더 정보를 담고 있는 딕셔너리다.
4. `query_string`: 웹 소켓 경로에서 추출한 쿼리 문자열(query string) 정보를 나타낸다.
5. 기타 다양한 연결 정보와 메타데이터 등이 포함될 수 있
6. 다.

웹 소켓 스코프는 Channels의 미들웨어와 라우팅을 통해 클라이언트의 요청을 처리하고 응답을 생성하는 데 사용된다. 미들웨어는 웹 소켓 연결이 생성될 때마다 또는 이벤트 발생 시마다 작동하며, 라우팅은 클라이언트의 요청을 처리할 적절한 핸들러로 라우팅한다.     

**웹 소켓(Websocket)**은 HTTP와 비슷한 프로토콜로, 양방향 실시간 통신을 가능하게 해주는 기술이다. 웹 소켓은 클라이언트와 서버 간에 지속적인 연결을 유지하고, 양방향으로 데이터를 교환할 수 있도록 한다. 이전의 일반적인 HTTP 요청과 달리, 웹 소켓은 서버가 클라이언트에게 데이터를 프롬프트 없이 보낼 수 있다.

("서버가 클라이언트에게 데이터를 프롬프트 없이 보낼 수 있다"는 말은 웹 소켓의 핵심적인 특징 중 하나인 실시간 양방향 통신 능력을 나타낸다.     

일반적인 HTTP 요청과 응답 방식에서는 클라이언트가 서버에게 요청을 보내면, 서버는 그에 대한 응답을 반환한다. 이 과정에서 클라이언트가 서버에게 어떤 동작을 요청하고, 서버가 그에 대한 응답을 하기까지는 시간이 소요된다. 클라이언트가 서버로부터 데이터를 받으려면 주기적으로 요청을 보내고 응답을 기다려야 한다.     

하지만 웹 소켓을 사용하는 경우, 한 번의 연결을 통해 클라이언트와 서버 간에 지속적인 연결이 유지된다. 이 연결은 양방향으로 데이터를 전송할 수 있는 채널을 열어둔 상태다. 따라서 서버는 언제든지 클라이언트에게 데이터를 보낼 수 있으며, 클라이언트도 언제든지 서버에게 데이터를 보낼 수 있다. 이 때문에 "프롬프트 없이" 데이터를 주고받을 수 있다고 말한다.     

예를 들어, 채팅 애플리케이션을 생각해보면, 일반적인 HTTP 요청 방식에서는 새로운 메시지를 보내거나 받으려면 주기적으로 서버에 요청을 보내야 한다. 그러나 웹 소켓을 사용하면 서버에서 새로운 메시지가 도착하면 클라이언트에게 즉시 전달되어 화면에 메시지가 표시된다. 이런 실시간 통신 능력은 웹 소켓의 큰 장점 중 하나로, 실시간 업데이트나 알림, 게임 상태 변경 등 다양한 용도에서 활용된다.)

요약하면, 웹 소켓 스코프는 Django Channels에서 웹 소켓 연결과 관련된 정보와 데이터를 담고 있는 딕셔너리이며, 웹 소켓은 실시간 양방향 통신을 위한 프로토콜로 사용된다.

### webchat/consumer.py

```python
class WebChatConsumer(JsonWebsocketConsumer):
    # ...

    def connect(self):
        self.user = self.scope["user"]  # 클라이언트의 인증된 사용자 정보를 가져온다.
        self.accept()  # 웹 소켓 연결을 수락한다.

        # 인증되지 않은 사용자는 연결을 종료한다.
        if not self.user.is_authenticated:
            self.close(code=4001)

        # URL 경로에서 채널 ID를 가져온다.
        self.channel_id = self.scope["url_route"]["kwargs"]["channelId"]

        # 예시로 사용자 정보를 강제로 가져오는 코드다.
        self.user = User.objects.get(id=1)

        # 채널 그룹에 현재 컨슈머를 추가한다.
        async_to_sync(self.channel_layer.group_add)(self.channel_id, self.channel_name)

    # ...


```

1. `self.scope["user"]`: 현재 웹 소켓 연결의 사용자 정보다. Django Channels는 스코프(scope)를 통해 여러 정보를 전달하며, 여기서는 `user` 키를 사용하여 인증된 사용자 정보를 가져온다.
    
2. `self.accept()`: 웹 소켓 연결을 수락한다. 연결이 수락되면 클라이언트와 서버 간의 실시간 통신이 가능해진다.
    
3. `if not self.user.is_authenticated`: 클라이언트가 인증되지 않은 경우, `self.close(code=4001)`을 사용하여 연결을 종료한다. 이 코드는 클라이언트에게 4001 상태 코드와 함께 연결 종료를 알리는 역할을 한다.
    
4. `self.channel_id`: URL 경로에서 추출한 채널 ID다. 채널 ID는 클라이언트와 서버 간의 특정 주제(채팅방 또는 채널)를 식별하는 역할을 한다.
    
5. `async_to_sync(self.channel_layer.group_add)`: 현재 컨슈머를 채널 그룹에 추가하는 메서드다. 이를 통해 해당 컨슈머는 해당 채널에 속하는 다른 연결과 메시지를 주고받을 수 있다.
    

Django Channels의 `scope`는 웹 소켓 연결과 관련된 정보를 담고 있는 딕셔너리다. 이 정보에는 클라이언트의 헤더, 경로, 인증 정보, URL 매개변수 등이 포함된다. 컨슈머에서 `self.scope`를 통해 이러한 정보를 사용할 수 있다.     

### asgi.py

```python
import os

  

from channels.routing import ProtocolTypeRouter, URLRouter

from django.core.asgi import get_asgi_application

  

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "djchat.settings")

  

django_application = get_asgi_application()

  

from . import urls  # noqa isort:skip

from webchat.middleware import JWTAuthMiddleWare  # noqa isort:skip

  

application = ProtocolTypeRouter(

    {

        "http": get_asgi_application(),

        "websocket": JWTAuthMiddleWare(URLRouter(urls.websocket_urlpatterns)),

    }

)
```

1. `ProtocolTypeRouter`: 프로토콜별로 다른 핸들러를 사용할 수 있도록 해주는 라우터다. `http` 프로토콜과 `websocket` 프로토콜에 대한 처리를 설정하고 있다.
    
2. `"http": get_asgi_application()`: HTTP 연결을 처리하는 핸들러로, 기존의 Django WSGI 애플리케이션을 ASGI로 변환하여 사용한다.
    
3. `"websocket": JWTAuthMiddleWare(URLRouter(urls.websocket_urlpatterns))`: 웹 소켓 연결을 처리하는 핸들러로, `JWTAuthMiddleWare`라는 미들웨어로 감싸고 있다. 웹 소켓 연결 시에 먼저 해당 미들웨어가 실행되며, 미들웨어 내에서 JWT 토큰의 유효성을 검사하고 사용자를 인증한다. 그리고 URL 라우팅을 위해 `URLRouter`로 경로를 설정한다.
    
4. `JWTAuthMiddleWare`: 이 미들웨어는 웹 소켓 연결 시에 JWT(Json Web Token) 토큰의 유효성을 검사하고 사용자를 인증한다. 이를 통해 웹 소켓 연결 시 보안과 인증을 담당하게 된다.
    

라우팅 설정과 미들웨어 설정의 목적은 다음과 같다:

- **라우팅 설정**: `ProtocolTypeRouter`를 사용하여 어떤 프로토콜에 대한 처리를 어떤 핸들러로 연결할 것인지 설정한다. HTTP 요청은 기존의 Django WSGI 애플리케이션으로 처리되며, 웹 소켓 연결은 `JWTAuthMiddleWare`를 통해 처리하고 해당 URL 라우팅을 설정한다.
    
- **미들웨어 설정**: `JWTAuthMiddleWare`를 웹 소켓 핸들러에 적용하여, 웹 소켓 연결 시에 JWT 토큰의 유효성을 검사하고 사용자를 인증한다. 이는 보안과 사용자 인증을 담당하여 안전한 웹 소켓 통신을 보장한다.
     

## Build: WebSocket Refresh Access Token

### auth-service.d.ts

```typescript
export interface AuthServiceProps {

    login: (username: string, password: string) => any;

    isLoggedIn: boolean;

    logout: () => void;

    refreshAccessToken: () => Promise<void>

}
```

`refreshAccessToken` 함수는 보통 웹 애플리케이션에서 사용자의 인증 정보를 갱신하고, 액세스 토큰(access token)의 만료를 관리하는 데 사용된다. 이 함수는 액세스 토큰이 만료되기 전에 새로운 액세스 토큰을 서버로부터 가져오는 역할을 한다.     

일반적인 웹 애플리케이션의 인증 흐름은 다음과 같다:

1. 사용자가 로그인하면 서버는 액세스 토큰과 리프레시 토큰(refresh token)을 발급한다.
2. 액세스 토큰은 일정 기간(예: 몇 시간) 동안 유효하며, 리프레시 토큰은 더 오래 유지된다.
3. 액세스 토큰이 만료되면, 클라이언트는 리프레시 토큰을 사용하여 서버로 새로운 액세스 토큰을 요청한다.
4. 서버는 리프레시 토큰을 검증하고, 유효하다면 새로운 액세스 토큰을 발급한다.
5. 클라이언트는 새로운 액세스 토큰을 사용하여 보호된 리소스에 접근한다.

따라서 `refreshAccessToken` 함수는 리프레시 토큰을 사용하여 새로운 액세스 토큰을 가져오는 역할을 하며, 이를 통해 사용자가 로그인 상태를 유지하면서 액세스 토큰의 만료를 관리할 수 있다. 이 함수는 주로 사용자 경험을 향상시키기 위해 인증 흐름을 자동화하는 데 사용된다.

### AuthServices.ts

```typescript
import axios from "axios";

import { AuthServiceProps } from "../@types/auth-service";

import { useState } from "react";

import { BASE_URL } from "../config";

  
  

export function useAuthService(): AuthServiceProps {

  

    const getInitialLoggedInValue = () => {

        const loggedIn = localStorage.getItem("isLoggedIn");

        return loggedIn !== null && loggedIn === "true";

      };

  

    const [isLoggedIn, setIsLoggedIn] = useState<boolean>((getInitialLoggedInValue))

    const getUserDetails = async () =>{

        try {

            const userId = localStorage.getItem("user_id")

            const response = await axios.get(

                `http://127.0.0.1:8000/api/account/?user_id=${userId}`,

                {

                    withCredentials: true

                }

            );

            const userDetails = response.data

            localStorage.setItem("username", userDetails.username);

            setIsLoggedIn(true);

            localStorage.setItem("isLoggedIn", "true")

        } catch (err: any) {

            setIsLoggedIn(false)

            localStorage.setItem("isLoggedIn", "false")

            return err;

        }

    }

  
  

    const login = async (username: string, password: string) =>{

        try {

            const response = await axios.post(

                "http://127.0.0.1:8000/api/token/", {

                    username,

                    password,

            }, { withCredentials: true }

            );

  

            // console.log(response.data)

            const user_id = response.data.user_id

            localStorage.setItem("isLoggedIn", "true")

            localStorage.setItem("user_id", user_id)

            setIsLoggedIn(true)

            getUserDetails()

  

        } catch (err: any) {

            return err.response.status;

        }

    }

  

    const refreshAccessToken = async () => {

        try {

            await axios.post(

                `${BASE_URL}/token/refresh/` , {} , {withCredentials:true}

            )

        } catch (refreshError) {

            return Promise.reject(refreshError)

        }

    }

    const logout = () => {

        localStorage.setItem("isLoggedIn", "false")

        localStorage.removeItem("user_id")

        localStorage.removeItem("username");

        setIsLoggedIn(false);

  

    }

  

    return {login, isLoggedIn, logout, refreshAccessToken}

}
```

`useAuthService` 함수는 React 컴포넌트에서 사용자 인증과 관련된 작업을 수행하기 위한 `AuthServiceProps` 객체를 반환하는 커스텀 훅이다. 해당 코드에서 `refreshAccessToken` 함수는 토큰을 갱신하는 역할을 하며, 그 이유와 `useAuthService` 함수의 반환 객체에 포함된 `refreshAccessToken`의 역할은 다음과 같다.

1. `refreshAccessToken` 함수: 이 함수는 서버로부터 액세스 토큰을 갱신하기 위해 호출된다. 기존의 액세스 토큰이 만료되었을 때, 리프레시 토큰을 사용하여 새로운 액세스 토큰을 발급받는 역할을 한다. 코드에서는 `axios.post` 메서드를 사용하여 서버에 리프레시 토큰을 보내고 새로운 액세스 토큰을 얻어온다. 만약 리프레시 토큰이 만료되거나 오류가 발생할 경우, `Promise.reject`를 호출하여 오류를 전달한다. 이렇게 함으로써, 인증 토큰을 갱신하려는 시도가 실패한 경우에 대비할 수 있다.
    
2. `return {login, isLoggedIn, logout, refreshAccessToken}`: `useAuthService` 함수가 반환하는 객체는 다양한 인증 관련 작업을 수행하는 함수와 상태값들을 포함하고 있다. 이 중에서 `refreshAccessToken` 함수는 다음과 같은 역할을 한다:
    
    - `login`: 사용자가 로그인하면서 인증 토큰을 발급받을 때, `refreshAccessToken` 함수를 사용하여 새로운 액세스 토큰을 받아올 수 있다. 이로써 로그인 후에도 액세스 토큰이 만료되지 않도록 보장한다.
        
    - `isLoggedIn`: 사용자가 현재 로그인 상태인지 여부를 나타내는 상태값이다. 로그인 여부를 확인하기 위해 사용되며, 로그인 후에도 유지될 수 있도록 인증 토큰을 관리하는 역할을 한다.
        
    - `logout`: 사용자 로그아웃 시에 호출되며, 로그아웃 과정에서 필요한 작업을 수행한다. 로그아웃 후에는 `isLoggedIn` 상태값이 `false`로 변경된다.
        
    - `refreshAccessToken`: 액세스 토큰을 갱신하고 유효 기간을 연장하는 역할을 한다. 이 함수를 통해 사용자 경험을 향상시킬 수 있으며, 인증 토큰의 유효성을 유지하는 데 도움을 준다.
        

요약하면, `refreshAccessToken` 함수는 액세스 토큰의 유효 기간을 관리하고 만료되지 않도록 갱신해주는 중요한 역할을 한다.

### MessageInterface.tsx

```typescript
import { useState } from "react";

import { useParams } from "react-router-dom";

import useWebSocket from "react-use-websocket";

import useCrud from "../../hooks/useCrud";

import { Server } from "../../@types/server.d";

import { useAuthService } from "../../services/AuthServices";

import {

  Avatar,

  Box,

  List,

  ListItem,

  ListItemAvatar,

  ListItemText,

  TextField,

  Typography,

  useTheme,

} from "@mui/material";

import MessageInterfaceChannels from "./MessageInterfaceChannels";

import Scroll from "./Scroll";

import React from "react";

  

interface SendMessageData {

  type: string;

  message: string;

  [key: string]: any;

}

  

interface ServerChannelProps {

  data: Server[];

}

  

interface Message {

  sender: string;

  content: string;

  timestamp: string;

}

  

const messageInterface = (props: ServerChannelProps) => {

  const { data } = props;

  const theme = useTheme();

  const [newMessage, setNewMessage] = useState<Message[]>([]);

  const [message, setMessage] = useState("");

  const { serverId, channelId } = useParams();

  const server_name = data?.[0]?.name ?? "Server";

  const { logout, refreshAccessToken } = useAuthService();

  const { fetchData } = useCrud<Server>(

    [],

    `/messages/?channel_id=${channelId}`

  );

  

  const socketUrl = channelId

    ? `ws://127.0.0.1:8000/${serverId}/${channelId}`

    : null;

  

  const [reconnectionAttempt, setReconnectionAttempt] = useState(0);

  const maxConnectionAttempts = 4;

  

  const { sendJsonMessage } = useWebSocket(socketUrl, {

    onOpen: async () => {

      try {

        const data = await fetchData();

        setNewMessage([]);

        setNewMessage(Array.isArray(data) ? data : []);

        console.log("Connected!!!");

      } catch (error) {

        console.log(error);

      }

    },

    onClose: (event: CloseEvent) => {

      if (event.code == 4001) {

        console.log("Authentication Error");

        refreshAccessToken().catch((error) => {

          if(error.response && error.response.status === 401){

            logout();

          }

        });

      }

      console.log("Close");

      setReconnectionAttempt((prevAttempt) => prevAttempt + 1);

    },

    onError: () => {

      console.log("Error!");

    },

    onMessage: (msg) => {

      const data = JSON.parse(msg.data);

      setNewMessage((prev_msg) => [...prev_msg, data.new_message]);

      setMessage("");

    },

    shouldReconnect: (closeEvent) => {

      if (

        closeEvent.code === 4001 &&

        reconnectionAttempt >= maxConnectionAttempts

        ) {

          setReconnectionAttempt(0);

          return false;

      }

      return true;

    },

    reconnectInterval: 1000,

  });

  

  const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {

    if (e.key === "Enter") {

      e.preventDefault();

      sendJsonMessage({

        type: "message",

        message,

      } as SendMessageData);

    }

  };

  

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {

    e.preventDefault();

    sendJsonMessage({

      type: "message",

      message,

    } as SendMessageData);

  };

  

  function formatTimeStamp(timestamp: string): string {

    const date = new Date(Date.parse(timestamp));

    const formattedDate = `${

      date.getMonth() + 1

    }/${date.getDate()}/${date.getFullYear()}`;

  

    const formattedTime = date.toLocaleTimeString([], {

      hour: "2-digit",

      minute: "2-digit",

      hour12: true,

    });

  

    return `${formattedDate} at ${formattedTime}`;

  }

  

  return (

    <>

      <MessageInterfaceChannels data={data} />

      {channelId == undefined ? (

        <Box

          sx={{

            overflow: "hidden",

            p: { xs: 0 },

            height: `calc(80vh)`,

            display: "flex",

            justifyContent: "center",

            alignItems: "center",

          }}

        >

          <Box sx={{ textAlign: "center" }}>

            <Typography

              variant="h4"

              fontWeight={700}

              letterSpacing={"-0.5px"}

              sx={{ px: 5, maxWidth: "600px" }}

            >

              Welcome to {server_name}

            </Typography>

            <Typography>

              {data?.[0]?.description ?? "This is our home"}

            </Typography>

          </Box>

        </Box>

      ) : (

        <>

          <Box

            sx={{

              overflow: "hidden",

              p: 0,

              height: `calc(100vh - 100px)`,

            }}

          >

            <Scroll>

              <List sx={{ width: "100%", bgcolor: "background.paper" }}>

                {newMessage.map((msg: Message, index: number) => {

                  return (

                    <ListItem key={index} alignItems="flex-start">

                      <ListItemAvatar>

                        <Avatar alt="user image" />

                      </ListItemAvatar>

                      <ListItemText

                        primaryTypographyProps={{

                          fontSize: "12px",

                          variant: "body2",

                        }}

                        primary={

                          <>

                            <Typography

                              component="span"

                              variant="body1"

                              color="text.primary"

                              sx={{ display: "inline", fontW: 600 }}

                            >

                              {msg.sender}

                            </Typography>

                            <Typography

                              component="span"

                              variant="caption"

                              color="textSecondary"

                            >

                              {" at "}

                              {formatTimeStamp(msg.timestamp)}

                            </Typography>

                          </>

                        }

                        secondary={

                          <>

                            <Typography

                              variant="body1"

                              style={{

                                overflow: "visible",

                                whiteSpace: "normal",

                                textOverflow: "clip",

                              }}

                              sx={{

                                display: "inline",

                                lineHeight: 1.2,

                                fontWeight: 400,

                                letterSpacing: "-0.2px",

                              }}

                              component="span"

                              color="text.primary"

                            >

                              {msg.content}

                            </Typography>

                          </>

                        }

                      />

                    </ListItem>

                  );

                })}

              </List>

            </Scroll>

          </Box>

          <Box sx={{ position: "sticky", bottom: 0, width: "100%" }}>

            <form

              onSubmit={handleSubmit}

              style={{

                bottom: 0,

                right: 0,

                padding: "1rem",

                backgroundColor: theme.palette.background.default,

                zIndex: 1,

              }}

            >

              <Box sx={{ display: "flex" }}>

                <TextField

                  fullWidth

                  multiline

                  value={message}

                  minRows={1}

                  maxRows={4}

                  onKeyDown={handleKeyDown}

                  onChange={(e) => setMessage(e.target.value)}

                  sx={{ flexGrow: 1 }}

                />

              </Box>

            </form>

          </Box>

        </>

      )}

    </>

  );

};

export default messageInterface;
```

전체적인 웹소켓 연결 관련 로직이다. 웹소켓이 연결되었을 때와 연결이 끊어졌을 때의 동작을 제어하며, 특히 토큰 갱신 및 재연결 시도를 한다.

1. `refreshAccessToken` 및 토큰 갱신 로직: 이 부분에서 `refreshAccessToken` 함수를 호출하여 액세스 토큰을 갱신하려고 시도한다. 웹소켓 연결이 만료되거나 재연결을 시도할 때마다 호출되며, 만약 토큰 갱신에 실패하거나 응답 상태 코드가 401 (Unauthorized)인 경우, 사용자를 로그아웃 처리한다. 이를 통해 토큰 갱신에 실패한 경우에도 로그아웃을 통해 사용자의 보안을 유지한다.
    
2. `onClose` 이벤트: 웹소켓 연결이 닫혔을 때 호출되는 이벤트 핸들러다. 만약 `closeEvent`의 상태 코드가 4001 (Custom code, 예를 들어 인증 오류)이고, `reconnectionAttempt` 값이 `maxConnectionAttempts` 보다 크거나 같으면 재연결 시도를 중단하고 웹소켓 연결을 종료한다. 이렇게 함으로써 특정 조건에서의 재연결 시도를 제한할 수 있다.
    
3. `shouldReconnect` 함수: 웹소켓 재연결 여부를 결정하는 함수다. 만약 `closeEvent`의 상태 코드가 4001이면서 `reconnectionAttempt` 값이 `maxConnectionAttempts` 보다 작으면 재연결을 시도한다. 그렇지 않은 경우에는 재연결을 중단한다. 이를 통해 재연결 횟수를 제한하고, 일부 특정 오류 상황에서의 재연결을 제어할 수 있다.
    
4. `reconnectInterval`: 웹소켓 재연결 시도 간격을 지정한다. 이 값은 재연결 시도 사이의 대기 시간을 제어한다. 예제에서는 1000ms(1초)로 설정되어 있으므로, 웹소켓 연결이 끊어졌을 때 1초마다 재연결을 시도하게 된다.
    

이러한 로직을 통해 웹소켓 연결 관련 문제 상황을 다양한 상황에서 대응할 수 있으며, 사용자의 보안과 사용자 경험을 개선할 수 있다.

![](https://i.imgur.com/iJxkKAQ.png)

토큰을 삭제하면 자동으로 재연결을 시도한다.
## Refactoring: useChatServices Custom Hook

이전에 MessageInterface 부분에서 채팅기능에 대한 코드 유지보수 하기 편하도록 리펙토링 해서 분리했다.
### chatService.ts

```typescript
  

import useWebSocket from "react-use-websocket";

import { useState } from "react";

import { useAuthService } from "../services/AuthServices";

import useCrud from "../hooks/useCrud";

import { WS_ROOT } from "../config";

import { Server } from "../@types/server"

  

interface Message {

    sender: string;

    content: string;

    timestamp: string;

  }

  

const useChatWebSocket = (channelId: string, serverId: string) => {

    const [newMessage, setNewMessage] = useState<Message[]>([]);

    const [message, setMessage] = useState("");

    const { logout, refreshAccessToken } = useAuthService();

    const { fetchData } = useCrud<Server>(

      [],

      `/messages/?channel_id=${channelId}`

    );

  

    const socketUrl = channelId

    ? `${WS_ROOT}/${serverId}/${channelId}`

    : null;

  

    const [reconnectionAttempt, setReconnectionAttempt] = useState(0);

    const maxConnectionAttempts = 4;

  

    const { sendJsonMessage } = useWebSocket(socketUrl, {

    onOpen: async () => {

        try {

        const data = await fetchData();

        setNewMessage([]);

        setNewMessage(Array.isArray(data) ? data : []);

        console.log("Connected!!!");

        } catch (error) {

        console.log(error);

        }

    },

    onClose: (event: CloseEvent) => {

        if (event.code == 4001) {

        console.log("Authentication Error");

        refreshAccessToken().catch((error) => {

            if(error.response && error.response.status === 401){

            logout();

            }

        });

        }

        console.log("Close");

        setReconnectionAttempt((prevAttempt) => prevAttempt + 1);

    },

    onError: () => {

        console.log("Error!");

    },

    onMessage: (msg) => {

        const data = JSON.parse(msg.data);

        setNewMessage((prev_msg) => [...prev_msg, data.new_message]);

        setMessage("");

    },

    shouldReconnect: (closeEvent) => {

        if (

        closeEvent.code === 4001 &&

        reconnectionAttempt >= maxConnectionAttempts

        ) {

            setReconnectionAttempt(0);

            return false;

        }

        return true;

    },

    reconnectInterval: 1000,

    });

  

    return {

        newMessage,

        message,

        setMessage,

        sendJsonMessage

    }

}

export default useChatWebSocket
```

이 훅을 사용하여 웹소켓 관련 로직을 추상화하고, 채팅 기능을 구현할 때 활용할 수 있다.      

1. `newMessage`: 상태 변수로, 새로운 메시지를 저장하는 배열이다.
2. `message`: 상태 변수로, 사용자가 입력한 메시지를 저장한다.
3. `setMessage`: `message` 상태를 업데이트하는 함수다.
4. `sendJsonMessage`: 웹소켓을 통해 JSON 형식의 메시지를 보내는 함수다.

이 훅은 다음과 같은 기능을 수행한다:     

- `onOpen`: 웹소켓 연결이 열렸을 때 호출되며, 서버로부터 메시지를 가져와 `newMessage` 상태를 업데이트한다.
- `onClose`: 웹소켓 연결이 닫혔을 때 호출되며, 인증 오류가 발생한 경우 `refreshAccessToken`을 호출하여 토큰을 갱신하고, 만약 401 상태 코드가 반환된다면 로그아웃한다.
- `onError`: 웹소켓 연결 중 오류가 발생한 경우 호출된다.
- `onMessage`: 웹소켓으로부터 메시지를 수신한 경우 호출되며, 새로운 메시지를 `newMessage` 상태에 추가하고, 입력된 메시지를 초기화한다.
- `shouldReconnect`: 웹소켓 재연결 여부를 결정하는 함수로, 인증 오류가 발생한 경우와 재연결 시도 횟수가 `maxConnectionAttempts`보다 작은 경우 재연결을 시도한다.
- `reconnectInterval`: 웹소켓 재연결 시도 간격을 지정한다.

이 커스텀 훅을 사용하면 채팅 기능을 구현할 때 웹소켓 연결 및 관련된 로직을 간편하게 처리할 수 있다. 이를 활용하여 사용자와 서버 간의 실시간 채팅을 구현할 수 있다.      

### MessageInterface.tsx

```typescript
  

import {

  Avatar,

  Box,

  List,

  ListItem,

  ListItemAvatar,

  ListItemText,

  TextField,

  Typography,

  useTheme,

} from "@mui/material";

import MessageInterfaceChannels from "./MessageInterfaceChannels";

import Scroll from "./Scroll";

import { useParams } from "react-router-dom";

import { Server } from "../../@types/server.d";

import useChatWebSocket from "../../services/chatService";

  

interface SendMessageData {

  type: string;

  message: string;

  [key: string]: any;

}

  

interface ServerChannelProps {

  data: Server[];

}

  

interface Message {

  sender: string;

  content: string;

  timestamp: string;

}

  
  

const messageInterface = (props: ServerChannelProps) => {

  const { data } = props;

  const theme = useTheme();

  const { serverId, channelId } = useParams();

  

  const { newMessage, message, setMessage, sendJsonMessage } = useChatWebSocket(

    channelId || "",

    serverId || ""

  );

  

  const server_name = data?.[0]?.name ?? "Server";

  
  
  

  const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {

    if (e.key === "Enter") {

      e.preventDefault();

      sendJsonMessage({

        type: "message",

        message,

      } as SendMessageData);

    }

  };

  

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {

    e.preventDefault();

    sendJsonMessage({

      type: "message",

      message,

    } as SendMessageData);

  };

  

  function formatTimeStamp(timestamp: string): string {

    const date = new Date(Date.parse(timestamp));

    const formattedDate = `${

      date.getMonth() + 1

    }/${date.getDate()}/${date.getFullYear()}`;

  

    const formattedTime = date.toLocaleTimeString([], {

      hour: "2-digit",

      minute: "2-digit",

      hour12: true,

    });

  

    return `${formattedDate} at ${formattedTime}`;

  }

  

  return (

    <>

      <MessageInterfaceChannels data={data} />

      {channelId == undefined ? (

        <Box

          sx={{

            overflow: "hidden",

            p: { xs: 0 },

            height: `calc(80vh)`,

            display: "flex",

            justifyContent: "center",

            alignItems: "center",

          }}

        >

          <Box sx={{ textAlign: "center" }}>

            <Typography

              variant="h4"

              fontWeight={700}

              letterSpacing={"-0.5px"}

              sx={{ px: 5, maxWidth: "600px" }}

            >

              Welcome to {server_name}

            </Typography>

            <Typography>

              {data?.[0]?.description ?? "This is our home"}

            </Typography>

          </Box>

        </Box>

      ) : (

        <>

          <Box

            sx={{

              overflow: "hidden",

              p: 0,

              height: `calc(100vh - 100px)`,

            }}

          >

            <Scroll>

              <List sx={{ width: "100%", bgcolor: "background.paper" }}>

                {newMessage.map((msg: Message, index: number) => {

                  return (

                    <ListItem key={index} alignItems="flex-start">

                      <ListItemAvatar>

                        <Avatar alt="user image" />

                      </ListItemAvatar>

                      <ListItemText

                        primaryTypographyProps={{

                          fontSize: "12px",

                          variant: "body2",

                        }}

                        primary={

                          <>

                            <Typography

                              component="span"

                              variant="body1"

                              color="text.primary"

                              sx={{ display: "inline", fontW: 600 }}

                            >

                              {msg.sender}

                            </Typography>

                            <Typography

                              component="span"

                              variant="caption"

                              color="textSecondary"

                            >

                              {" at "}

                              {formatTimeStamp(msg.timestamp)}

                            </Typography>

                          </>

                        }

                        secondary={

                          <>

                            <Typography

                              variant="body1"

                              style={{

                                overflow: "visible",

                                whiteSpace: "normal",

                                textOverflow: "clip",

                              }}

                              sx={{

                                display: "inline",

                                lineHeight: 1.2,

                                fontWeight: 400,

                                letterSpacing: "-0.2px",

                              }}

                              component="span"

                              color="text.primary"

                            >

                              {msg.content}

                            </Typography>

                          </>

                        }

                      />

                    </ListItem>

                  );

                })}

              </List>

            </Scroll>

          </Box>

          <Box sx={{ position: "sticky", bottom: 0, width: "100%" }}>

            <form

              onSubmit={handleSubmit}

              style={{

                bottom: 0,

                right: 0,

                padding: "1rem",

                backgroundColor: theme.palette.background.default,

                zIndex: 1,

              }}

            >

              <Box sx={{ display: "flex" }}>

                <TextField

                  fullWidth

                  multiline

                  value={message}

                  minRows={1}

                  maxRows={4}

                  onKeyDown={handleKeyDown}

                  onChange={(e) => setMessage(e.target.value)}

                  sx={{ flexGrow: 1 }}

                />

              </Box>

            </form>

          </Box>

        </>

      )}

    </>

  );

};

export default messageInterface;
```

`useChatWebSocket` 부분을 분리해서 관리하고 필요한 기능들을 모듈화 했다.

## Refactoring: Incorporating Custom Hooks to allow access to the Router

### App.tsx

```typescript
import Home from "./pages/Home";

import Server from "./pages/Server";

import Explore from "./pages/Explore";

import {

  Route,

  Routes,

  BrowserRouter

} from "react-router-dom";

import ToggleColorMode from "./components/ToggleColorMode";

import Login from "./pages/Login";

import { AuthServiceProvider } from "./context/AuthContext";

import TestLogin from "./pages/TestLogin";

import ProtectedRoute from "./services/ProtectedRoute";

  
  

const App = () => {

  return (

    <BrowserRouter>

      <AuthServiceProvider>

        <ToggleColorMode>

          <Routes>

            <Route path="/" element={<Home />} />

            <Route

              path="/server/:serverId/:channelId?"

              element={

                <ProtectedRoute>

                  <Server />

                </ProtectedRoute>

              }

            />

            <Route path="/explore/:categoryName" element={<Explore />} />

            <Route path="/login" element={<Login />} />

            <Route

              path="/testlogin"

              element={

                <ProtectedRoute>

                  <TestLogin />

                </ProtectedRoute>

              }

            />

          </Routes>

        </ToggleColorMode>

      </AuthServiceProvider>

    </BrowserRouter>

  );

};

  

export default App;
```

`Routes`와 `Route`를 사용하여 라우팅을 구성했다.     

1. **더 명확한 구조**: `Routes`와 `Route`를 사용하면 라우팅 경로와 컴포넌트의 매칭이 더 명확하게 이루어진다. 예를 들어, 각 경로와 해당 컴포넌트를 분리하여 정의할 수 있어 코드의 가독성을 향상시킨다.
    
2. **중첩된 라우팅 지원**: `Routes`를 사용하면 중첩된 라우팅을 더 쉽게 구현할 수 있다. 중첩된 라우팅이란, 하위 경로에 따라 다른 컴포넌트를 렌더링하는 것을 의미한다. 예를 들어, 특정 경로의 하위 경로에 따라 다른 레이아웃 또는 컴포넌트를 표시하려고 할 때 유용하다.
    
3. **동적 경로 정의의 향상**: `Route` 컴포넌트를 사용하여 동적 경로 매개변수를 더 간단하게 정의할 수 있다. 이로 인해 경로의 동적 매개변수를 더욱 직관적으로 처리할 수 있다.
    
4. **코드 스플리팅과 최적화**: 새로운 `Routes`와 `Route` 패턴은 코드 스플리팅과 라우트 기반의 비동기 로딩을 더욱 간단하게 할 수 있도록 지원한다. 이를 통해 초기 번들 크기를 줄이고 앱의 성능을 최적화할 수 있다.
    
5. **코드의 모듈화**: `Routes`와 `Route` 패턴을 사용하면 각 경로에 대한 라우팅 로직과 컴포넌트가 더 모듈화되어 코드의 재사용성과 유지보수성이 향상된다.
    

이러한 장점들로 인해 React Router v6에서 도입된 `Routes`와 `Route` 패턴은 더욱 효율적이고 유연한 라우팅을 구현할 수 있도록 도와준다.

## Build: Removing HTTP Only Cookies on Logout

로그아웃 되었을 때 쿠키를 삭제해주자
### account/views.py

```python
  

class LogOutAPIView(APIView):

    def post(self, request, format=None):

        response = Response("Logged out successfully")

        response.set_cookie("refresh_token", "", expires=0)

        response.set_cookie("access_token", "", expires=0)

        return response
```

DRF 의 APIView를 사용해 로그아웃을 처리한다. 클리이언트에서 POST 요청을 보내면 이 뷰는 쿠키를 설정해서 로그아웃 작업을 수행한다.     

1. `post` 메서드: 이 메서드는 POST 요청을 처리하는 역할을 한다. 로그아웃을 위해 사용된다.
    
2. `response`: `Response` 객체를 생성하여 반환할 준비를 한다. 이 객체에는 응답 메시지와 함께 쿠키 설정이 포함된다.
    
3. `response.set_cookie("refresh_token", "", expires=0)`: "refresh_token" 쿠키를 만료시키는 작업을 수행한다. 쿠키를 만료시키면 해당 쿠키는 더 이상 유효하지 않으며, 클라이언트에서 해당 쿠키를 사용할 수 없게 된다.
    
4. `response.set_cookie("access_token", "", expires=0)`: "access_token" 쿠키를 만료시키는 작업을 수행한다. 마찬가지로, 이로써 클라이언트의 엑세스 토큰이 무효화된다.
    
5. `return response`: 설정된 쿠키와 함께 로그아웃 성공 메시지를 담은 응답을 반환한다.
    

이 코드를 사용하면 클라이언트가 로그아웃을 요청하면 해당 클라이언트의 refresh 토큰과 access 토큰을 만료시키는 역할을 한다. 이로써 클라이언트는 더 이상 유효한 토큰을 사용할 수 없게 된다.

### AuthServices.ts

```typescript
    const logout = async () => {

        localStorage.setItem("isLoggedIn", "false")

        localStorage.removeItem("user_id")

        localStorage.removeItem("username");

        setIsLoggedIn(false);

        navigate("/login")

  

        try {

            await axios.post(

                `${BASE_URL}/logout/` , {} , {withCredentials:true}

            )

        } catch (refreshError) {

            return Promise.reject(refreshError)

        }

  

    }

  

    return {login, isLoggedIn, logout, refreshAccessToken}
```

`async` 키워드는 함수 내에서 비동기적인 작업을 수행할 때 사용된다. 비동기적인 작업은 일반적으로 시간이 걸리는 작업이나 네트워크 요청과 같이 완료되기까지 시간이 걸리는 작업을 의미한다.     

위의 코드에서 `logout` 함수가 사용하는 `axios.post` 메서드는 서버로 요청을 보내고 응답을 기다려야 하는 작업이다. 이때 `axios.post` 메서드는 비동기 함수이므로, 함수 앞에 `async` 키워드를 붙여서 비동기 함수로 만들어준다. 이렇게 함으로써 함수 내에서 `await` 키워드를 사용하여 비동기 작업의 완료를 기다릴 수 있다.     

따라서 `async` 키워드를 사용하여 `logout` 함수를 비동기 함수로 만든 후에 `axios.post` 요청을 보내고 그 응답을 기다리는 것이 이 함수에서 사용되는 목적으로 사용했다.

## Build: Handling Login Form Validation

### Login.tsx

```typescript
import { useFormik } from "formik";

import { useNavigate } from "react-router-dom";

import { useAuthServiceContext } from "../context/AuthContext";

import { Box, Button, Container, TextField, Typography } from "@mui/material";

  

const Login = () => {

  const { login } = useAuthServiceContext();

  const navigate = useNavigate();

  const formik = useFormik({

    initialValues: {

      username: "",

      password: "",

    },

    validate: (values) => {

      const errors: Partial<typeof values> = {};

      if (!values.username) {

        errors.username = "Required"

      }

      if (!values.password) {

        errors.password = "Required"

      }

      return errors;

    },

    onSubmit: async (values) => {

      const { username, password } = values;

      const status = await login(username, password);

      if (status === 401) {

        console.log("Unauthorised");

        formik.setErrors({

          username: "Invalid username or password",

          password: "Invalid username or password",

        });

      } else {

        navigate("/");

      }

    },

  });

  return (

    <Container

      component="main"

      maxWidth="xs"

    >

      <Box

        sx={{

          marginTop:8,

          display: "flex",

          alignItems: "center",

          flexDirection: "column"

        }}

      >

        <Typography

          variant="h5"

          noWrap

          component="h1"

          sx={{

            fontWeight: 500,

            pb: 2,

          }}

        >

          Sign in

        </Typography>

      </Box>

        <div>

          <h1>Login</h1>

          <Box component="form" onSubmit={formik.handleSubmit}

            sx={{ mt:1 }}

          >

            <TextField

              autoFocus

              fullWidth

              id="username"

              name="username"

              label="Username"

              value={formik.values.username}

              onChange={formik.handleChange}

              error={!!formik.touched.username && !! formik.errors.username}

              helperText={formik.touched.username && formik.errors.username}

            ></TextField>

  

            <TextField

              margin="normal"

              fullWidth

              id="password"

              name="password"

              label="Password"

              type="password"

              value={formik.values.password}

              onChange={formik.handleChange}

              error={!!formik.touched.password && !! formik.errors.password}

              helperText={formik.touched.password && formik.errors.password}

            ></TextField>

            <Button

              variant="contained"

              disableElevation

              type="submit"

              sx = {{

                mt: 1,

                mb: 2

              }}

            >

              Next

            </Button>

          </Box>

        </div>

  

    </Container>

  

  );

};

  

export default Login;
```

1. `useAuthServiceContext`를 사용하여 `login` 함수와 `useNavigate` 훅을 가져온다. 이는 커스텀 컨텍스트 `AuthContext`에서 제공되는 인증 서비스와 라우팅 기능을 사용하기 위해서다.
    
2. `formik` 객체를 생성하고 초기값, 유효성 검증, 제출 핸들러를 설정한다. `formik`은 폼 상태와 유효성 검증을 관리하는 유용한 라이브러리다.
    
3. `onSubmit` 핸들러는 양식이 제출될 때 호출된다. 제출이 발생하면 입력된 사용자명과 비밀번호를 가져와 `login` 함수를 호출한다. `login` 함수의 반환 상태 코드에 따라 적절한 조치를 취한다. 만약 상태 코드가 401(Unauthorized)인 경우, 에러를 설정하고 잘못된 사용자명이나 비밀번호임을 알리며, 그렇지 않은 경우에는 홈으로 이동한다.

4. `Button` 컴포넌트를 사용하여 "Next" 버튼을 생성하고, 버튼이 클릭되었을 때 `formik.handleSubmit` 함수를 호출합니다.
    
![](https://i.imgur.com/lHZWhB2.png)


## Build: Registration Form

### account/views.py

```python
class RegisterView(APIView):
    def post(self, request):
        serializer = RegisterSerializer(data=request.data)
        if serializer.is_valid():
            username = serializer.validated_data["username"]
        
            forbidden_usernames = ["admin", "root", "superuser"]
            if username is forbidden_usernames:
                return Response(
                    {"error": "Username not allowed"},
                    status=status.HTTP_409_CONFLICT
                )
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        
        errors = serializer.errors
        if "username" in errors and "non_field_errors" not in errors:
            return Response(
                {"error": "Username already exists"},
                status=status.HTTP_409_CONFLICT
            )
            
        return Response(errors, status=status.HTTP_400_BAD_REQUEST)
```

1. `RegisterView` 클래스는 `APIView` 클래스를 상속받는다. 이는 Django에서 제공하는 뷰 클래스의 기본 클래스로, HTTP 요청에 대한 처리를 담당한다.
    
2. `post` 메서드는 HTTP POST 요청을 처리하는 로직을 구현하고 있다. `request` 객체를 받아서 회원가입에 필요한 데이터를 처리한다.
    
3. `RegisterSerializer` 클래스를 사용하여 `serializer` 객체를 생성하고, `serializer.is_valid()`를 호출하여 입력 데이터가 유효한지 확인한다.
    
4. 만약 `serializer`가 유효하다면, `validated_data`에서 `username`을 가져온다.
    
5. `forbidden_usernames` 리스트에는 금지된 사용자명들이 포함되어 있다. 만약 입력한 사용자명이 금지된 사용자명 중 하나와 일치한다면, `"Username not allowed"`라는 에러 메시지와 함께 `status.HTTP_409_CONFLICT` 상태 코드를 반환한다.
    
6. 그렇지 않다면, `serializer.save()`를 호출하여 데이터베이스에 새로운 회원 정보를 저장한다.
    
7. `Response` 객체를 생성하여 성공적으로 생성된 회원 정보를 반환하고, 상태 코드로 `status.HTTP_201_CREATED`을 사용한다.
    
8. 만약 `serializer`가 유효하지 않다면, `serializer.errors`에서 에러 정보를 가져온다.
    
9. "username" 에러가 존재하고, "non_field_errors"가 없다면 이미 존재하는 사용자명에 대한 충돌 에러를 반환한다.
    
10. 그 외의 경우에는 모든 에러 정보를 반환하고, 상태 코드로 `status.HTTP_400_BAD_REQUEST`을 사용한다.
    

Django REST framework를 사용하여 회원가입을 처리하고, 입력 데이터의 유효성을 확인하며, 이미 존재하는 사용자명과 충돌 여부를 확인하는 등의 기능을 수행한다.

### account/serializers.py

```python
class RegisterSerializer(serializers.ModelSerializer):
    class Meta:
        model  = Account
        fields = ("username", "password",)

    def is_valid(self, raise_exception=False):
        valid = super().is_valid(raise_exception=raise_exception)
        
        if valid:
            username = self.validated_data["username"]
            if Account.objects.filter(username=username).exists():
                self._errors["username"] = ["username already exists"]
                valid = False
        
        return valid
    
    def create(self, validated_data):
        user = Account.objects.create_user(**validated_data)
        return user
```

1. `RegisterSerializer` 클래스는 `serializers.ModelSerializer`를 상속받는다. 이는 Django REST framework에서 모델 기반의 직렬화를 지원하는 클래스로, 모델 필드를 기반으로 직렬화 및 역직렬화를 수행한다.
    
2. `Meta` 클래스 내에서 `model` 속성을 `Account` 모델로 설정하고, 직렬화할 필드를 `fields` 속성에 지정한다. 이 경우에는 "username"과 "password" 필드만 직렬화한다.
    
3. `is_valid` 메서드는 유효성 검사를 수행한다. 부모 클래스인 `super().is_valid(raise_exception=raise_exception)`를 호출하여 기본 유효성 검사를 수행하고, 그 결과를 `valid` 변수에 저장한다.
    
4. `valid` 변수가 `True`인 경우, `validated_data`에서 "username"을 가져와 해당 사용자명이 이미 데이터베이스에 존재하는지 확인한다.
    
5. 이미 존재하는 경우, `_errors` 속성을 활용하여 "username" 필드에 대한 에러를 설정하고, `valid`를 `False`로 설정한다.
    
6. `create` 메서드는 실제로 데이터베이스에 새로운 사용자를 생성한다. `Account.objects.create_user(**validated_data)`를 호출하여 `Account` 모델에 새로운 사용자를 생성하고 반환한다.
    

이 코드는 회원 가입 시에 입력한 사용자명이 이미 존재하는지 확인하고, 존재하지 않으면 새로운 사용자를 생성하는 `RegisterSerializer` 클래스를 정의했다.

### auth-service.d.ts

```typescript
export interface AuthServiceProps {
    login: (username: string, password: string) => any;
    isLoggedIn: boolean;
    logout: () => void;
    refreshAccessToken: () => Promise<void>
    register: (username: string, password: string) => Promise<any>;
}
```

여기서 `register`는 인증 관련 서비스(`AuthService`)에서 제공되는 메서드 중 하나입니다. 이 메서드는 사용자의 회원 가입을 처리한다.     

`register` 메서드는 두 개의 인자, 즉 `username` (사용자명)과 `password` (비밀번호)를 받는다. 이 메서드를 호출하면, 해당 사용자명과 비밀번호를 사용하여 새로운 계정을 생성하고, 이를 서버 또는 데이터베이스에 저장한다.     

회원 가입 과정에서 입력받은 사용자 정보를 바탕으로 새로운 계정을 생성하는데, 이러한 작업은 비동기적으로 이루어진다. 그래서 `register` 메서드는 `Promise<any>`를 반환하게 되어 있다.     

이 `Promise`는 회원 가입 작업이 성공적으로 완료되었을 경우에는 성공 상태에 관한 정보를, 실패했을 경우에는 오류에 관한 정보를 반환할 수 있다. 반환되는 정보에 따라 애플리케이션에서 적절한 조치를 취할 수 있게 된다.     

따라서, `register` 메서드는 사용자의 회원 가입을 처리하고, 그 결과에 따른 상태 정보를 `Promise`를 통해 반환하는 역할을 한다.     

### Register.tsx

```typescript
+import { useFormik } from "formik";
import { useNavigate } from "react-router-dom";
import { useAuthServiceContext } from "../context/AuthContext";
import { Box, Button, Container, TextField, Typography } from "@mui/material";

const Register = () => {
  const { register } = useAuthServiceContext();
  const navigate = useNavigate();
  const formik = useFormik({
    initialValues: {
      username: "",
      password: "",
    },
    validate: (values) => {
      const errors: Partial<typeof values> = {};
      if (!values.username) {
        errors.username = "Required";
      }
      if (!values.password) {
        errors.password = "Required";
      }
      return errors;
    },
    onSubmit: async (values) => {
      const { username, password } = values;
      const status = await register(username, password);
      if (status === 409) {
        formik.setErrors({
          username: "The username already exists",
        });
      } else if (status === 401) {
        console.log("Unauthorised");
        formik.setErrors({
          username: "Invalid username or password",
          password: "Invalid username or password",
        });
      } else {
        navigate("/login");
      }
    },
  });
  return (
    <Container component="main" maxWidth="xs">
      <Box
        sx={{
          marginTop: 8,
          display: "flex",
          alignItems: "center",
          flexDirection: "column",
        }}
      >
        <Typography
          variant="h5"
          noWrap
          component="h1"
          sx={{
            fontWeight: 500,
            pb: 2,
          }}
        >
          Register
        </Typography>
        <Box component="form" onSubmit={formik.handleSubmit} sx={{ mt: 1 }}>
          <TextField
            autoFocus
            fullWidth
            id="username"
            name="username"
            label="username"
            value={formik.values.username}
            onChange={formik.handleChange}
            error={!!formik.touched.username && !!formik.errors.username}
            helperText={formik.touched.username && formik.errors.username}
          ></TextField>
          <TextField
            margin="normal"
            fullWidth
            id="password"
            name="password"
            type="password"
            label="password"
            value={formik.values.password}
            onChange={formik.handleChange}
            error={!!formik.touched.password && !!formik.errors.password}
            helperText={formik.touched.password && formik.errors.password}
          ></TextField>
          <Button
            variant="contained"
            disableElevation
            type="submit"
            sx={{ mt: 1, mb: 2 }}
          >
            Next
          </Button>
        </Box>
      </Box>
    </Container>
  );
};

export default Register;
```

주어진 코드는 React 애플리케이션에서 회원 가입을 처리하는 `Register` 컴포넌트를 보여준다. 이 컴포넌트는 회원 가입 폼을 렌더링하고, 사용자 입력을 처리하여 회원 가입을 진행한다.


1. `Register` 컴포넌트는 `useAuthServiceContext` 훅을 사용하여 `register` 함수와 `useNavigate` 훅을 가져온다. 이를 통해 사용자 등록 함수와 페이지 이동 함수를 사용할 수 있다.
    
2. `formik` 객체를 생성하고 초기값과 유효성 검증, 제출 핸들러를 설정한다. 이 객체는 회원 가입 폼의 상태 및 로직을 관리한다.
    
3. `onSubmit` 핸들러는 폼이 제출될 때 호출된다. 제출이 발생하면 입력된 사용자명과 비밀번호를 가져와 `register` 함수를 호출한다.
    
4. `register` 함수의 반환 상태 코드에 따라 적절한 조치를 취한다. 409 상태 코드는 이미 존재하는 사용자명인 경우를 나타내며, 해당 경우에는 에러를 표시한다. 401 상태 코드는 권한이 없는 경우를 나타내며, 잘못된 사용자명 또는 비밀번호를 입력한 경우 에러를 표시한다. 그 외의 경우에는 로그인 페이지로 이동한다.
    
5. `Container` 컴포넌트를 사용하여 폼을 전체 화면 크기에 맞게 배치하고, `Typography` 컴포넌트를 사용하여 "Register" 제목을 렌더링한다.
    
6. `TextField` 컴포넌트를 사용하여 사용자명과 비밀번호 입력 필드를 생성하고, `formik` 객체의 값 및 이벤트 핸들러를 연결하여 폼 상태를 관리한다.
    
7. `Button` 컴포넌트를 사용하여 "Next" 버튼을 생성하고, 버튼이 클릭되었을 때 `formik.handleSubmit` 함수를 호출한다.
    

### AuthServices.ts

```typescript
   const register = async (username: string, password: string) =>{
        try {
            const response = await axios.post(
                "http://127.0.0.1:8000/api/register/", {
                    username,
                    password,
            }, { withCredentials: true }
            );

            return response.status

        } catch (err: any) {
            return err.response.status;
        }
    }
```

Axios를 사용하여 회원 가입을 처리하는 `register` 함수를 만든다. 입력된 사용자명과 비밀번호를 서버로 전송하여 회원 가입을 시도하고, 그 결과에 따른 상태 코드를 반환한다.

1. `register` 함수는 두 개의 인자인 `username`과 `password`를 받습니다. 이 함수는 입력된 사용자명과 비밀번호를 가지고 서버에 회원 가입 요청을 보내고, 그에 따른 응답 상태 코드를 반환한다.
    
2. `axios.post`를 사용하여 POST 요청을 보낸다. 요청 주소는 `"http://127.0.0.1:8000/api/register/"`이며, `withCredentials: true` 옵션을 사용하여 요청에 인증 정보를 포함한다.
    
3. 요청 데이터로 사용자명과 비밀번호를 객체로 전달한다.
    
4. `try` 블록 내에서 서버 응답을 대기하고, 응답이 성공적으로 완료되었을 경우 `response.status` 값을 반환한다. 이 값은 서버의 응답 상태 코드를 나타낸다.
    
5. `catch` 블록에서는 요청이 실패한 경우(`err` 객체에 의해 나타남) `err.response.status` 값을 반환한다. 이 값은 서버의 응답 상태 코드를 나타낸다.
    

이렇게 `register` 함수는 입력된 사용자명과 비밀번호를 사용하여 회원 가입 요청을 보내고, 서버 응답에 따른 상태 코드를 반환하는 역할을 한다. 반환된 상태 코드는 회원 가입 상태에 대한 정보를 나타내며, 이 정보를 기반으로 클라이언트 측에서 적절한 조치를 취할 수 있다.

{% endraw %}