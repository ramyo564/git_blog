---
layout: single
title: " [Django DRF] React DjangoDRF project (3) "
categories: Django_Chat_Service
tags:
  - Python
  - "[Django"
  - DRF]
  - DjangoDRF
  - +
  - React
  - chat
  - project
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

JWT Interceptor는 JWT(JSON Web Token)를 사용하여 인증된 사용자의 HTTP 요청을 가로채고 처리하는 기능을 제공하는 인터셉터(interceptor)다.     

JWT는 웹 애플리케이션에서 사용자 인증에 자주 사용되는 토큰 기반의 인증 방식이다. 사용자가 로그인하면 서버에서 JWT를 발급하고, 이 토큰을 클라이언트(예: 브라우저)에 저장한다. 그 후, 사용자가 다른 요청을 보낼 때마다 JWT를 함께 전송하여 서버에서 해당 사용자를 인증하고 접근 권한을 확인할 수 있다.     

JWT Interceptor는 클라이언트에서 HTTP 요청을 보낼 때마다 요청 헤더에 JWT를 자동으로 포함시키는 역할을 한다. 이를 통해 사용자가 로그인한 상태에서 인증이 필요한 요청을 보낼 때 매번 JWT를 직접 설정하는 번거로움을 피할 수 있다. JWT Interceptor는 보통 웹 애플리케이션의 HTTP 클라이언트에 통합되어 사용된다.     

간단한 사용 예시:
{% raw %}
```javascript
import axios from 'axios';

const jwtToken = localStorage.getItem('jwtToken');

const axiosInstance = axios.create({
  baseURL: 'https://api.example.com',
  headers: {
    Authorization: `Bearer ${jwtToken}`, // JWT를 요청 헤더에 자동으로 포함
  },
});

axiosInstance.get('/user/profile'); // 서버로 인증된 요청을 보낼 때 JWT가 자동으로 포함됨

```

위 코드에서 `axiosInstance`는 JWT를 인증 헤더에 자동으로 포함시켜 서버로 요청을 보내는 데 사용된다. 이렇게 JWT Interceptor를 사용하면 매번 수동으로 JWT를 설정하지 않아도 된다. 이는 보안성을 높이고 클라이언트에서 JWT를 효과적으로 관리할 수 있도록 도와준다.     


### jwtinterceptor.ts

```typescript
import axios, { AxiosInstance } from 'axios';

import { useNavigate  } from 'react-router-dom';

import { BASE_URL } from '../config';

  

const API_BASE_URL = BASE_URL

  

const useAxiosWithInterceptor = (): AxiosInstance => {

    const jwtAxios = axios.create({ baseURL: API_BASE_URL })

    const navigate = useNavigate()

  

    jwtAxios.interceptors.response.use(

        (response) => {

            return response;

        },

    async (error) => {

        const originalRequest = error.config;

        if (error.response?.status === 401) {

            const goRoot = () => navigate("/test")

            goRoot();

        }

        throw error;

    }

    )

    return jwtAxios;

}

  

export default useAxiosWithInterceptor
```

위 코드는 React 애플리케이션에서 Axios를 사용하여 HTTP 요청을 보내고, JWT 인증 토큰을 인터셉트하여 응답 오류를 처리하는 커스텀 훅(hook)인 `useAxiosWithInterceptor`를 정의한다.     

1. `axios`와 `AxiosInstance`를 `axios` 라이브러리에서 가져온다.
2. `useNavigate` 훅을 `react-router-dom`에서 가져온다.
3. `BASE_URL`이 정의된 `config` 파일로부터 `API_BASE_URL`을 설정한다.
4. `useAxiosWithInterceptor`라는 커스텀 훅을 정의한다.
5. `jwtAxios`라는 Axios 인스턴스를 생성하고, `baseURL`을 `API_BASE_URL`로 설정한다.
6. `useNavigate` 훅을 사용하여 네비게이션 기능을 가져온다.
7. `jwtAxios`에 응답 인터셉터를 추가하여 오류 처리를 설정. 401(Unauthorized) 오류가 발생한 경우, `/test` 경로로 이동하도록 설정한다.
8. 커스텀 훅의 반환값으로 `jwtAxios`를 반환한다.

이렇게 구성된 커스텀 훅은 애플리케이션에서 Axios를 사용할 때 JWT 토큰 인증을 처리하고, 401 오류가 발생할 경우 자동으로 `/test` 경로로 이동하도록 한다. 이렇게 인터셉터를 사용하여 요청과 응답을 처리하면, 전역적으로 인증과 관련된 로직을 쉽게 관리할 수 있으며, 보안성을 강화할 수 있다.

### views.py
```python
from rest_framework import viewsets
from rest_framework.exceptions import AuthenticationFailed, ValidationError
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated
from django.db.models import Count
from .models import Server
from .serializer import ServerSerializer
from .schema import server_list_docs
class ServerListViewSet(viewsets.ViewSet):

    queryset = Server.objects.all()
    permission_classes = [IsAuthenticated]
    
    @server_list_docs
    def list(self, request):
```

`permission_classes`는 Django REST framework에서 제공하는 클래스 기반 뷰(view)에서 인증과 권한을 설정하기 위해 사용되는 속성이다. 이 속성을 사용하여 특정 뷰에 접근할 때 필요한 인증과 권한을 지정할 수 있다.     

위 코드에서 `ServerListViewSet`는 `viewsets.ViewSet`을 상속받은 클래스로, Django REST framework에서 제공하는 기본적인 뷰셋(viewset)이다. `permission_classes` 속성은 이 뷰셋의 list 메서드에 접근할 때 필요한 인증과 권한을 설정하는 데 사용된다.     

`IsAuthenticated` 클래스는 Django REST framework에서 제공하는 기본 권한 클래스 중 하나로, 인증된 사용자만 해당 뷰에 접근할 수 있도록 해줍니다. 즉, 인증된 사용자만 list 메서드에 접근할 수 있고, 인증되지 않은 사용자는 해당 뷰에 접근할 수 없다.     

따라서 위 코드에서 `ServerListViewSet`의 `list` 메서드는 `IsAuthenticated` 권한을 가져야만 접근할 수 있으며, 인증되지 않은 사용자가 이 뷰에 접근하려고 하면 인증 실패(`AuthenticationFailed`) 예외가 발생하게 된다.     

이렇게 `permission_classes` 속성을 사용하여 뷰에 필요한 인증과 권한을 지정하면, Django REST framework는 요청이 해당 조건을 만족하는지 확인하고, 요청이 유효하지 않은 경우 적절한 예외를 발생시킨다. 이를 통해 뷰에 접근하는 사용자의 인증과 권한을 효과적으로 관리할 수 있다.

### SecondaryDraw.tsx
```typescript
import { Box, Typography } from "@mui/material";
import { useTheme } from "@mui/material/styles";

import axios from "axios";
///
import useAxiosWithInterceptor from "../../helpers/jwtinterceptor";
///

const SecondaryDraw = () => {
    const theme = useTheme();
    const jwtAxios = useAxiosWithInterceptor();

    axios
    jwtAxios
        .get('http://127.0.0.1:8000/api/server/select/?category=cat1')
        .then(response => {
            console.log(response.data);
```

위 코드는 `@mui/material` 라이브러리에서 `Box`와 `Typography`를 가져오고, `useTheme` 훅을 사용하여 현재 테마를 가져오는 것으로 시작한다. 또한 `axios`와 커스텀 훅인 `useAxiosWithInterceptor`를 임포트한다.     

`SecondaryDraw` 컴포넌트에서는 `useAxiosWithInterceptor`로부터 반환된 `jwtAxios`를 사용하여 HTTP GET 요청을 보낸다. 요청은 `http://127.0.0.1:8000/api/server/select/?category=cat1` URL로 보내지며, 해당 응답 데이터는 콘솔에 출력된다.     

`useAxiosWithInterceptor` 커스텀 훅은 앞서 제공한 코드를 사용하여 HTTP 요청에 JWT 인증 토큰을 자동으로 추가하는 기능을 제공한다. 이를 통해 JWT 토큰을 사용하여 인증된 요청을 보낼 수 있게 된다.     


## Build : Create a CRUD Hook

리엑트에서 훅은 컴포넌트 상태와 다른 React 기능을 추가하는 특별한 한수다.     
훅을 사용하면 클래스 컴포넌트 없이 상태를 재사용할 수 있다. 이를 통해 클래스 컴포넌트가 필요하지 않은 상태의 로직을 재사용할 수 있다.    
따라서 훅은 재사용 가능한 상태 로직을 제공하는 클래스와 같이 생각할 수 있다.     

Hooks 는 보통 use 로 시작하는 이름을 가진다. 그래서 이 훅의 이름은 useCrud로 정했다.      
물론 생성한 백엔드 필터를 활요앻 보낼 요청의 유형에 따라 이를 확장한다.    
기본 값을 설정하고 어떤 종류의 반환을 해야 할지 생각해야하는데 우선 JWT InterCeptor를 가져와야 한다. 나중에 컴포넌트에서 사용되기 때문이다.     

또한 다른 컴포넌트에서 실제로 사용할 수 있도록 이를 전달하고 반환 하려면 이 기능들을 컴포넌트에 전달하고 필요할 때마다 재사용할 수 있도록 만드는게 좋다.     

Axios.get 메서드는 기본적으로 비동기는 아니지만 비동기 함수 내에서 사용하고 await 키워드를 이용해서 서버의 응답을 기다릴 수 있다. 따라서 Axios 라이브러리 자체에 async 키워드를 사용하는 것은 필수가 아니다.      
Axios 라이브러리는 프로미스 기반 API 를 제공하므로 비동기/대기 문법을 사용하든지 말든지 본인 스타일과 접근 방법에 따라 결정하면된다.     


### useCrud.ts
```typescript
import useAxiosWithInterceptor from "../helpers/jwtinterceptor";
import { BASE_URL } from "../config";
import { useState } from 'react';

interface IuseCrud<T> {
    dataCRUD: T[];
    fetchData: () => Promise<void>;
    error : Error | null;
    isLoading : boolean
}

const useCrud = <T>(initialData: T[], apiURL: string): IuseCrud<T> => {
    const jwtAxios = useAxiosWithInterceptor();
    const [dataCRUD, setDataCRUD] = useState<T[]>(initialData)
    const [error, setError] = useState<Error | null>(null)
    const [isLoading, setIsLoading] = useState(false)

    const fetchData = async () => {
        setIsLoading(true)
        try{
            const response = await jwtAxios.get(`${BASE_URL}${apiURL}`, {})
            const data = response.data
            setDataCRUD(data)
            setError(null)
            setIsLoading(false)
            return data;
        } catch (error: any){
            if (error.response && error.response.status === 400) {
                setError(new Error("400"))
            }
            setIsLoading(false)
            throw error;
        }
    };
    return {
        fetchData, dataCRUD, error, isLoading
    }
}
export default useCrud
```


위 코드는 React 컴포넌트에서 CRUD (Create, Read, Update, Delete) 작업을 수행하는 커스텀 훅인 `useCrud`를 정의한다. `useCrud` 훅은 제네릭을 사용하여 임의의 데이터 타입(`T`)에 대해 CRUD 작업을 수행하고, 해당 데이터의 상태를 관리하는 기능을 제공한다.


1. `useAxiosWithInterceptor` 훅을 import하여 axios 인스턴스를 생성한다.
2. `IuseCrud`라는 제네릭 인터페이스를 정의합니다. 이 인터페이스는 컴포넌트에서 사용할 데이터와 함수의 타입을 지정한다.
3. `useCrud` 함수는 제네릭 타입 T와 초기 데이터 `initialData` 그리고 API URL `apiURL`을 인자로 받는다.
4. `useState` 훅을 이용하여 데이터 `dataCRUD`, 에러 `error`, 로딩 상태 `isLoading`을 관리한다.
5. `fetchData` 함수는 데이터를 가져오는 비동기 함수다. 요청 전에 `isLoading` 상태를 true로 설정하고, `jwtAxios.get`으로 데이터를 가져와서 `dataCRUD` 상태에 저장한다. 요청이 성공하면 `error`를 null로 초기화하고, 요청이 실패하면 에러 상태를 설정한다.
6. `useCrud` 함수는 `fetchData`, `dataCRUD`, `error`, `isLoading`을 객체로 반환한다. 컴포넌트에서 이 훅을 사용하면 이러한 기능과 데이터를 활용할 수 있다.

이 훅을 사용하는 컴포넌트에서는 `fetchData` 함수를 호출하여 데이터를 가져오고, `dataCRUD` 상태를 통해 가져온 데이터에 접근할 수 있으며, `error` 상태를 통해 발생한 에러를 처리할 수 있다. 또한 `isLoading` 상태를 통해 로딩 화면을 표시할 수 있다.

## Build : Primary Draw Component - Popular Servers

[mui avator 공문](https://mui.com/material-ui/react-avatar/)
[mui list 공문](https://mui.com/material-ui/react-list/)


### PopularChannels.tsx
```typescript
import { 
    List,
    ListItem,
    ListItemButton,
    ListItemIcon,
    ListItemText,
    Box,
    Typography,
} from "@mui/material";
import useCrud from "../../hooks/useCrud";
import { useEffect } from "react";
import ListItemAvatar from "@mui/material/ListItemAvatar";
import Avatar from "@mui/material/Avatar";
import { MEDIA_URL  } from "../../config";
import { Link } from "react-router-dom";

interface Server {
    id : number;
    name : string;
    category : string;
    icon: string;
}

type Props = {
    open: boolean;
};

const PopularChannels: React.FC<Props> = ({ open }) => {
    const { dataCRUD, error, isLoading, fetchData } = useCrud<Server>(
        [],
        "/server/select/"
    );

    useEffect(() => {
        fetchData();
    }, []);

    return (
    <>
    <Box 
        sx={{
            height: 50,
            p: 2,
            display: "flex",
            alignItems: "center",
            flex: "1 1 100%",
            backgroundColor: "blue"
        }}
    >
        <Typography
            sx={{
                display: open ? "block" : "none"
            }}
        >
            Popular
        </Typography>
    </Box>
    <List>
        {dataCRUD.map((item) => (
            <ListItem
                key={item.id}
                disablePadding
                sx = {{ display: "block" }}
                dense = {true}
            >
                <Link
                    to={`/server/${item.id}`}
                    style = {{ 
                        textDecoration: "none",
                        color: "inherit"
                    }}
                >
                    <ListItemButton
                        sx = {{minHeight:0,}}
                    >
                        <ListItemIcon
                            sx = {{
                                minWidth:0,
                                justifyContent:"center"
                            }}
                        >
                            <ListItemAvatar sx = {{minWidth: "50px"}}>
                                <Avatar 
                                    alt="Server Icon" 
                                    src={`${MEDIA_URL}${item.icon}`}
                                />

                            </ListItemAvatar>
                        </ListItemIcon>
                        <ListItemText
                            primary={
                                <Typography
                                    varient="body2"
                                    sx={{
                                        fontWeight: 700,
                                        lineWeight: 1.2,
                                        textOverflow: "ellipsis",
                                        overflow: "hidden",
                                        whiteSpace: "nowrap",
                                    }}
                                >
                                    {item.name}
                                </Typography>
                            }
                            secondary={
                                <Typography 
                                    variant="body2" 
                                    sx={{ 
                                        fontWeight: 500,
                                        lineHeight: 1.2,
                                        color: "testSecondary",
                                     }}
                                >
                                    {item.category}
                                </Typography>
                                }
                                sx = {{
                                    opacity: open ? 1 : 0
                                }}
                                primaryTypographyProps={{
                                    sx: {
                                        textOverflow: "ellipsis",
                                        overflow: "hidden",
                                        whitespave: "nowrap",
                                    }
                                }}
                        />
                    </ListItemButton>
                </Link>
            </ListItem>
        ))}
    </List>
    
    </>
    );
};


export default PopularChannels;

```

위의 코드는 `PopularChannels`라는 React 함수형 컴포넌트를 정의하고 있다. 이 컴포넌트는 `open`이라는 프롭스를 받아와서 사용하며, `useCrud` 훅을 이용하여 서버로부터 데이터를 불러온다.

1. `useCrud` 훅을 이용하여 데이터를 불러온다. `dataCRUD`에는 서버에서 받아온 데이터 배열이 저장된다.
    
2. `useEffect` 훅을 이용하여 컴포넌트가 마운트되면(`fetchData` 함수 실행 후) 데이터를 불러온다. `useEffect` 훅을 사용하면 컴포넌트가 렌더링된 후에 비동기적으로 데이터를 불러올 수 있다.
    
3. `<Box>`와 `<Typography>` 컴포넌트를 사용하여 "Popular"이라는 텍스트를 화면에 표시한다. `open` 프롭스에 따라 화면에 텍스트가 보이거나 감추어진다.
    
4. `<List>`와 `<ListItem>` 컴포넌트를 사용하여 데이터 배열인 `dataCRUD`를 매핑하여 리스트 아이템을 생성한다.
    
5. 각 리스트 아이템은 `<Link>` 컴포넌트를 사용하여 해당 서버의 상세 페이지로 이동할 수 있도록 링크로 감싸져 있다.
    
6. 리스트 아이템에는 `item`의 `name`과 `category`를 보여주는 `<ListItemText>` 컴포넌트와 `item.icon` 값을 이미지 URL로 설정하는 `<Avatar>` 컴포넌트가 있다.
    

위 컴포넌트는 서버로부터 데이터를 불러와서 인기 있는 서버의 리스트를 표시하는 역할을 한다. 이 코드 자체적으로는 오류가 없어 보이며, 컴포넌트가 예상대로 작동하기 위해서는 `MEDIA_URL`이 올바른 이미지 URL 경로를 가리키도록 설정되어야 한다. 또한 `dataCRUD` 배열에 올바른 데이터가 담겨져 있어야 리스트 아이템이 올바르게 생성된다.


### Home.tsx
```typescript
import PrimaryDraw from "./templates/PrimaryDraw";
import SecondaryDraw from "./templates/SecondaryDraw";
import Main from "./templates/Main"
import PopularChannels from "../components/PrimaryDraw/PopularChannels";

const Home = () => {
        <Box sx={{ display: "flex" }}>
            <CssBaseline />
            <PrimaryAppBar />
            <PrimaryDraw></PrimaryDraw>
            <PrimaryDraw>
                <PopularChannels/>
            </PrimaryDraw>
            <SecondaryDraw/>
            <Main/>
        </Box>

```

`<PopularChannels />` 컴포넌트가 `<PrimaryDraw>` 컴포넌트의 내부에 있기 때문에, `<PopularChannels />` 컴포넌트는 `<PrimaryDraw>` 컴포넌트의 자식(child) 컴포넌트로 취급된다.

`<PrimaryDraw>` 컴포넌트의 내부에 `<PopularChannels />` 컴포넌트를 넣었을 때의 영향은 다음과 같다:

1. 컴포넌트의 렌더링 구조: `<PopularChannels />`는 `<PrimaryDraw>` 컴포넌트의 내부에 있으므로, `<PopularChannels />` 컴포넌트는 `<PrimaryDraw>` 컴포넌트와 함께 렌더링된다.
    
2. 컴포넌트 간 데이터 흐름: `<PopularChannels />`는 `<PrimaryDraw>` 컴포넌트의 자식 컴포넌트로 취급되므로, 필요에 따라 `<PrimaryDraw>` 컴포넌트가 데이터를 `<PopularChannels />`에게 전달하거나, `<PopularChannels />`가 `<PrimaryDraw>` 컴포넌트의 상태를 변경하는 것이 가능하다.
    
3. 스타일링: `<PrimaryDraw>` 컴포넌트의 스타일링에 영향을 줄 수 있다. 예를 들어, `<PrimaryDraw>` 컴포넌트의 스타일링이 `<PopularChannels />`에 상속되어 스타일이 더 이해하기 쉬운 방식으로 구성될 수 있다.
    

이렇게 중첩된 구조는 컴포넌트 간의 분리와 재사용성을 향상시키며, 복잡한 UI 레이아웃을 보다 쉽게 관리할 수 있도록 도와준다. 따라서 코드에서 `<PopularChannels />`가 `<PrimaryDraw>` 안에 있는 것은 일반적으로 컴포넌트 간 구조와 데이터 흐름을 잘 조정하기 위한 설계다.

## Build : Secondary Draw Component - Explore Categories


### ExploreCategories.tsx

```typescript
import { 
    List,
    ListItem,
    ListItemButton,
    ListItemIcon,
    ListItemText,
    Box,
    useTheme,
    Typography,
} from "@mui/material";
import useCrud from "../../hooks/useCrud";
import { useEffect } from "react";
import ListItemAvatar from "@mui/material/ListItemAvatar";
import Avatar from "@mui/material/Avatar";
import { MEDIA_URL  } from "../../config";
import { Link } from "react-router-dom";

interface Category {
    id : number;
    name : string;
    description : string;
    icon: string;
}

const ExploreCategories = () => {
    const theme = useTheme();
    const { dataCRUD, error, isLoading, fetchData } = useCrud<Category>(
        [],
        "/server/category/" 
    );

    useEffect(() => {
        fetchData();
    }, []);

    return <>
    <Box 
        sx={{ 
            height: "50px",
            display: "flex",
            alignItems: "center",
            px: 2,
            borderBottom: `1px solid ${theme.palette.divider}`,
            position: "sticky",
            top: 0,
            backgroundColor: theme.palette.background.default, 
        }}
    >
        Explore
    </Box>
        <List sx = {{ py: 0}}>
            {dataCRUD.map((item) => (
                <ListItem
                    disablePadding
                    key = {item.id}
                    sx = {{ display: "block "}}
                    dense={true}
                >
                    <Link 
                        to={`/explore/${item.name}`}
                        style={{ 
                            textDecoration: "none",
                            color: "inherit",
                        }}    
                    >
                    <ListItemButton sx={{ minHeight: 48 }}>
                        <ListItemIcon sx={{ minWidth: 0, justifyContent: "center"}}>
                            <ListItemAvatar sx = {{ minWidth:"0px" }}>
                                <img 
                                    alt="server Icon"
                                    src={`${MEDIA_URL}${item.icon}`}
                                    style={{
                                        width: "25px",
                                        height: "25px",
                                        display: "block",
                                        margin: "auto"
                                    }}
                                />
                            </ListItemAvatar>
                        </ListItemIcon>
                        <ListItemText
                            primary={
                                <Typography
                                    variant="body1"
                                    textAlign="start"
                                    paddingLeft={1}
                                >
                                    {item.name}
                                </Typography>
                            }
                        />
                    </ListItemButton>
                    </Link>
                </ListItem>
            ))}
        </List>
    </>;
};

export default ExploreCategories;
```

이 컴포넌트는 서버에서 가져온 카테고리 데이터를 기반으로 탐색(Explore) 카테고리 리스트를 보여주는 역할을 한다.

컴포넌트의 주요 동작은 다음과 같다:

1. `useTheme` 훅을 사용하여 현재 테마를 가져온다. 이를 통해 앱의 테마 스타일에 맞추어 카테고리 리스트의 배경색과 스타일을 설정할 수 있다.
    
2. `useCrud` 훅을 이용하여 서버에서 카테고리 데이터를 가져온다. `dataCRUD`에는 서버에서 받아온 카테고리 데이터 배열이 저장된다.
    
3. `useEffect` 훅을 사용하여 컴포넌트가 마운트되면(`fetchData` 함수 실행 후) 카테고리 데이터를 불러온다. `useEffect` 훅을 사용하면 컴포넌트가 렌더링된 후에 비동기적으로 데이터를 불러올 수 있다.
    
4. `<Box>` 컴포넌트를 사용하여 Explore 카테고리의 제목 부분을 구성한다.
    
5. `<List>`와 `<ListItem>` 컴포넌트를 사용하여 데이터 배열인 `dataCRUD`를 매핑하여 Explore 카테고리 리스트 아이템을 생성한다.
    
6. 각 리스트 아이템은 `<Link>` 컴포넌트를 사용하여 해당 카테고리의 탐색 페이지로 이동할 수 있도록 링크로 감싸져 있다.
    
7. 리스트 아이템에는 카테고리 아이콘과 이름이 포함되어 있다. 아이콘은 `<img>` 요소를 사용하여 `item.icon`을 이미지 URL로 설정한다. `<Typography>`를 사용하여 카테고리 이름을 표시한다.
    


### SecondaryDraw.tsx
```typescript
import { Box } from "@mui/material";

import { useTheme } from "@mui/material/styles";

  

type SecondaryDrawProps = {

    children: React.ReactNode;

}

  

const SecondaryDraw = ({ children }: SecondaryDrawProps ) => {

    const theme = useTheme();

  

    return(

        <Box

            sx={{

                minWidth: `${theme.secondaryDraw.width}px`,

                height: `calc(100vh - ${theme.primaryAppBar.height}px )`,

                mt: `${theme.primaryAppBar.height}px`,

                borderRight: `1px solid ${theme.palette.divider}`,

                display: { xs: "none", sm: "block"},

                overflow: "auto",

            }}

        >

            {children}

  

        </Box>

    );

};

export default SecondaryDraw;
```

위의 코드는 "SecondaryDraw"라는 React 함수형 컴포넌트를 정의하고 있다. 이 컴포넌트는 자식 요소(Children)를 받아와서 특정 스타일과 함께 `<Box>` 컴포넌트로 렌더링하는 역할을 한다.

컴포넌트의 주요 동작은 다음과 같다:

1. `useTheme` 훅을 사용하여 현재 MUI(Material-UI) 테마를 가져온다. MUI 테마를 통해 특정 스타일 속성들을 동적으로 계산하여 적용할 수 있다.
    
2. `SecondaryDrawProps`라는 타입을 정의하여 `children`이라는 이름으로 React 노드(ReactNode)를 받을 수 있도록 한다.
    
3. `SecondaryDraw` 컴포넌트는 `children`을 받아와서 `<Box>` 컴포넌트 안에 렌더링한다. `Box`는 MUI에서 제공하는 스타일링을 지원하는 컴포넌트로, 여기서는 특정 스타일을 적용하기 위해 사용된다.
    
4. `<Box>` 컴포넌트의 `sx` 속성을 사용하여 스타일을 정의한다. 여기서는 주로 MUI 테마에서 가져온 값과 `children`을 이용하여 스타일을 계산한다.
    
5. `minWidth`, `height`, `mt` 등의 속성을 사용하여 박스의 크기와 여백을 설정한다.
    
6. `display: { xs: "none", sm: "block" }`를 사용하여 반응형 스타일을 적용한다. 이를 통해 화면 크기에 따라 박스의 표시 여부가 조정된다. 작은 화면에서는 보이지 않고, 중간 이상의 화면 크기에서는 블록 형태로 보이도록 설정한다.
    
7. `<Box>` 컴포넌트 안에 `{children}`을 렌더링하여 컴포넌트로 전달받은 자식 요소를 포함시킨다. `children`은 컴포넌트 태그의 내용을 나타내는 프롭스로, 해당 컴포넌트를 사용할 때 다양한 형태의 자식 요소를 넣을 수 있다.
    

위 코드에서 `SecondaryDraw` 컴포넌트는 MUI 테마를 활용하여 미리 정의된 스타일과 자식 요소를 함께 렌더링하는 컴포넌트다. `children`을 사용하면 `SecondaryDraw` 컴포넌트로 다양한 내용을 전달할 수 있으며, 해당 내용은 `<Box>` 컴포넌트의 내부에서 스타일이 적용된 상태로 렌더링된다.



`React.ReactNode`는 TypeScript에서 React 컴포넌트의 자식(children) 요소를 타입으로 지정하기 위해 사용되는 타입이다.

React 컴포넌트의 `children`은 해당 컴포넌트 태그의 내용을 나타내는 프롭스로, `React.ReactNode` 타입은 이러한 자식 요소들을 타입으로 나타내기 위해 사용된다.

예를 들어, 아래와 같이 컴포넌트를 정의하고 `children` 프롭스를 `React.ReactNode` 타입으로 선언할 수 있다:
```typescript
import React, { ReactNode } from 'react';

type MyComponentProps = {
  children: ReactNode;
};

const MyComponent: React.FC<MyComponentProps> = ({ children }) => {
  return <div>{children}</div>;
};

```

위의 코드에서 `MyComponentProps` 타입은 `children` 프롭스를 `ReactNode` 타입으로 지정한다. 이제 `MyComponent` 컴포넌트를 사용할 때 다양한 타입의 자식 요소를 전달할 수 있다.

예를 들어:

```typescript
<MyComponent>
  <h1>Hello, world!</h1>
</MyComponent>


또는

<MyComponent>
  <p>This is some content.</p>
  <button>Click Me</button>
</MyComponent>

```

이렇게 `children`의 타입을 `React.ReactNode`로 선언하면, 컴포넌트에 전달할 수 있는 자식 요소들이 자유로워지며, 다양한 형태의 내용을 컴포넌트로 전달할 수 있다. `React.ReactNode`는 이러한 자식 요소들의 타입을 표현하기 위해 사용되는 편리한 TypeScript 타입이다.


## Build : Main Component Exploring Servers

### App.tsx
```typescript
import { ThemeProvider } from "@emotion/react";
import Home from "./pages/Home"
import Explore from "./pages/Explore";
import { createBrowserRouter, createRoutesFromElements, Route, RouterProvider } from "react-router-dom"
import { createMuiTheme } from "./theme/theme";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route>
      <Route path="/" element={<Home/>} />
      <Route path="/explore/:categoryName" element={<Explore />} />
    </Route>
  )
);
```

1. `import { ThemeProvider } from "@emotion/react";`
    
    - `@emotion/react`에서 `ThemeProvider`를 가져온다. 이는 Emotion 라이브러리를 사용하여 React 컴포넌트에 테마를 적용하는 데 사용된다.
2. `import Home from "./pages/Home"`
    
    - `./pages/Home` 파일에서 `Home` 컴포넌트를 가져온다. 아마도 홈 페이지의 컴포넌트다.
3. `import Explore from "./pages/Explore";`
    
    - `./pages/Explore` 파일에서 `Explore` 컴포넌트를 가져온다. 아마도 카테고리를 탐색하는 페이지의 컴포넌트다.
4. `import { createBrowserRouter, createRoutesFromElements, Route, RouterProvider } from "react-router-dom"`
    
    - `react-router-dom`에서 `createBrowserRouter`, `createRoutesFromElements`, `Route`, `RouterProvider` 등의 여러 함수와 컴포넌트를 가져온다. 이 라이브러리는 React 애플리케이션에서 라우팅을 처리하는 데 사용된다.
5. `import { createMuiTheme } from "./theme/theme";`
    
    - `./theme/theme` 파일에서 `createMuiTheme` 함수를 가져온다. 이 함수는 Material-UI 라이브러리의 테마를 생성하는 데 사용된다.
6. `const router = createBrowserRouter(...)`
    
    - `createBrowserRouter` 함수를 사용하여 라우터를 생성한다. 이 함수는 라우팅 구성을 설정하기 위해 사용된다.
7. `createRoutesFromElements(...)`
    
    - JSX 요소로부터 라우팅 구성을 생성하는 함수다. 이 경우 `<Route>` 컴포넌트를 사용하여 두 개의 경로를 설정하고, 해당 경로에 대해 렌더링할 컴포넌트들을 지정하고 있다.
8. `<Route path="/" element={<Home/>} />`
    
    - 경로가 "/"인 경우, `Home` 컴포넌트를 렌더링한다.
9. `<Route path="/explore/:categoryName" element={<Explore />} />`
    
    - 경로가 "/explore/:categoryName"인 경우, `Explore` 컴포넌트를 렌더링한다. `:categoryName`은 해당 위치의 동적인 카테고리 이름을 나타낸다.

위의 코드를 통해 React 애플리케이션에서 Emotion 테마를 사용하고, 라우팅을 설정하며, Material-UI 테마를 생성한다.     

`<Route path="/explore/:categoryName" element={<Explore />} />`은 React 애플리케이션에서 사용되는 라우팅 구성을 나타낸다. 이 코드는 `react-router-dom` 라이브러리를 사용하여 경로와 해당 경로에 대해 렌더링할 컴포넌트를 설정하는 역할을 한다.

- `path="/explore/:categoryName"`: 이 부분은 라우팅 경로를 정의하는 부분이다. 여기서 `/explore/` 다음에 오는 값은 동적인 값을 나타내며, 콜론(`:`)으로 시작하는 `:categoryName`은 URL에서 사용되는 매개변수입니다. 이렇게 설정하면 `/explore/` 다음에 오는 값이 `categoryName` 변수에 동적으로 할당된다.

예를 들어, URL이 `/explore/sports`인 경우, `categoryName` 변수는 `"sports"`라는 문자열로 할당된다. 마찬가지로 `/explore/technology`인 경우 `categoryName` 변수는 `"technology"`이 된다.

- `element={<Explore />}`: 이 부분은 해당 경로로 이동했을 때 렌더링될 React 컴포넌트를 지정하는 부분이다. 위의 코드에서는 `Explore` 컴포넌트가 해당 경로로 이동했을 때 렌더링된다.

따라서 이 라우팅 구성은 동적인 카테고리 이름을 가진 Explore 페이지를 설정하고, 해당 URL에 따라 다른 카테고리를 보여주는 데 사용될 수 있다. 예를 들어, `/explore/sports` 경로로 이동하면 Explore 페이지가 렌더링되고, `categoryName` 변수에는 `"sports"`가 할당된다. 이를 활용하여 해당 카테고리의 내용을 동적으로 표시할 수 있다.

### Explore.tsx
```typescript
import { Box, CssBaseline } from "@mui/material";
import PrimaryAppBar from "./templates/PrimaryAppBar";
import PrimaryDraw from "./templates/PrimaryDraw";
import SecondaryDraw from "./templates/SecondaryDraw";
import Main from "./templates/Main";
import PopularChannels from "../components/PrimaryDraw/PopularChannels";
import ExploreCategories from "../components/SecondaryDraw/ExploreCategories";
import ExploreServers from "../components/Main/ExploreServers";

const Home = () => {
  return (
    <Box sx={{ display: "flex" }}>
      <CssBaseline />
      <PrimaryAppBar />
      <PrimaryDraw>
        <PopularChannels open={false} />
      </PrimaryDraw>
      <SecondaryDraw>
        <ExploreCategories />
      </SecondaryDraw>
      <Main>
        <ExploreServers />
      </Main>
    </Box>
  );
};
export default Home;
```
이전 Home.tsx 와 내용은 똑같다.

![](https://i.imgur.com/1B6rtbt.png)



![](https://i.imgur.com/NXWevsG.png)


### ExploreServers.tsx
```typescript
import {
    List,
    ListItem,
    ListItemIcon,
    ListItemText,
    Box,
    Typography,
  } from "@mui/material";
  import Card from "@mui/material/Card";
  import CardContent from "@mui/material/CardContent";
  import CardMedia from "@mui/material/CardMedia";
  import Grid from "@mui/material/Grid";
  import Container from "@mui/material/Container";
  import ListItemAvatar from "@mui/material/ListItemAvatar";
  import Avatar from "@mui/material/Avatar";
  import { useParams } from "react-router-dom";
  import useCrud from "../../hooks/useCrud";
  import { useEffect } from "react";
  import { MEDIA_URL } from "../../config";
  import { Link } from "react-router-dom";
  
  interface Server {
    id: number;
    name: string;
    description: string;
    icon: string;
    category: string;
    banner: string;
  }

  const ExploreServers = () => {
    const { categoryName } = useParams();
    const url = categoryName
      ? `/server/select/?category=${categoryName}`
      : "/server/select/";
    const { dataCRUD, fetchData } = useCrud<Server>([], url);

    useEffect(() => {
        fetchData();
      }, [categoryName]);
    
    return (
        <>
        <Container maxWidth="lg">
            <Box sx={{ pt: 6 }}>
                <Typography
                    variant="h3"
                    noWrap
                    component="h1"
                    sx={{
                    display: {
                        sm: "block",
                        fontWeight: 700,
                        letterSpacing: "-2px",
                        textTransform: "capitalize",
                    },
                    textAlign: { xs: "center", sm: "left" },
                    }}
                >
                    {categoryName ? categoryName : "Popular Channels"}
                </Typography>
            </Box>
            <Box>
                <Typography
                    variant="h6"
                    noWrap
                    component="h2"
                    color="textSecondary"
                    sx={{
                    display: {
                        sm: "block",
                        fontWeight: 700,
                        letterSpacing: "-1px",
                    },
                    textAlign: { xs: "center", sm: "left" },
                    }}
                >
                    {categoryName
                    ? `Channels talking about ${categoryName}`
                    : "Check out some of our popular channels"}
                </Typography>
            </Box>
            
            <Typography
                variant="h6"
                sx={{ pt: 6, pb: 1, fontWeight: 700, letterSpacing: "-1px" }}
                >
                Recommended Channels
            </Typography>
            <Grid container spacing={{ xs: 0, sm: 2 }}>
                {dataCRUD.map((item) => (
                    <Grid item key={item.id} xs={12} sm={12} md={6} lg={3}>
                    <Card
                        sx={{
                        height: "100%",
                        display: "flex",
                        flexDirection: "column",
                        boxShadow: "none",
                        backgroundImage: "none",
                        borderRadius: 0,
                        }}
                    >
                        <Link
                        to={`/server/${item.id}`}
                        style={{ textDecoration: "none", color: "inherit" }}
                        >
                        <CardMedia
                            component="img"
                            image={
                            item.banner
                                ? `${MEDIA_URL}${item.banner}`
                                : "https://source.unsplash.com/random/"
                            }
                            alt="random"
                            sx={{ display: { xs: "none", sm: "block" } }}
                        />
                        <CardContent
                            sx={{
                            flexGrow: 1,
                            p: 0,
                            "&:last-child": { paddingBottom: 0 },
                            }}
                        >
                            <List>
                            <ListItem disablePadding>
                                <ListItemIcon sx={{ minWidth: 0 }}>
                                <ListItemAvatar sx={{ minWidth: "50px" }}>
                                    <Avatar
                                        alt="server Icon"
                                        src={`${MEDIA_URL}${item.icon}`}
                                    />
                                </ListItemAvatar>
                                </ListItemIcon>
                                <ListItemText
                                    primary={
                                        <Typography
                                            variant="body2"
                                            textAlign="start"
                                            sx={{
                                                textOverflow: "ellipsis",
                                                overflow: "hidden",
                                                whiteSpace: "nowrap",
                                                fontWeight: 700,
                                            }}
                                        >
                                            {item.name}
                                        </Typography>
                                    }
                                    secondary={
                                        <Typography variant="body2">
                                            {item.category}
                                        </Typography>
                                    }
                                />
                            </ListItem>
                            </List>
                        </CardContent>
                        </Link>
                    </Card>
                    </Grid>
                ))}
            </Grid>
        </Container>
        
        </>
    );
  };

  export default ExploreServers;
```

  
위 코드는 ExploreServers 컴포넌트를 정의하는 TypeScript와 Material-UI를 사용한 React 코드다. 이 컴포넌트는 서버 정보를 동적으로 표시하는 Explore 페이지를 구성한다.

1. `interface Server`는 서버 정보를 정의하는 TypeScript 인터페이스다.
2. `const ExploreServers = () => { ... }`: ExploreServers 컴포넌트를 정의한다. 함수형 컴포넌트다.
3. `useParams()`를 사용하여 현재 경로의 매개변수를 가져온다. `categoryName`은 현재 경로의 동적인 매개변수다. 예를 들어, "/explore/sports" 경로에서 `categoryName`은 "sports"가 된다.
4. `useCrud<Server>([], url)`를 사용하여 서버 정보를 비동기적으로 가져오는 데 사용되는 custom hook인 `useCrud`를 호출한다. `dataCRUD` 배열에 서버 정보가 저장되고, `fetchData()` 함수를 통해 서버 정보를 가져온다.
5. `useEffect()`를 사용하여 `categoryName`이 변경될 때마다 `fetchData()`를 호출하여 해당 카테고리에 대한 서버 정보를 업데이트한다.
6. JSX를 사용하여 화면을 렌더링한다.
    - `Typography` 컴포넌트로 페이지 제목과 부제목을 표시한다.
    - `Grid` 컴포넌트와 `Card` 컴포넌트를 사용하여 서버 카드를 레이아웃한다.
    - 서버 카드에는 서버 이름, 카테고리, 아이콘, 배너 이미지 등이 포함된다. 해당 정보는 `dataCRUD` 배열에서 가져온 서버 정보로 채워진다.
    - `Link` 컴포넌트를 사용하여 서버 카드를 클릭하면 해당 서버의 상세 페이지로 이동할 수 있도록 설정한다.

위 코드는 Explore 페이지에서 추천 서버들을 표시하는데 사용되며, 해당 페이지에 동적으로 카테고리를 전달하여 해당 카테고리의 서버들만 표시할 수 있다. 이를 통해 사용자는 카테고리에 따라 다양한 서버들을 쉽게 찾아볼 수 있다.


[그리드 Grid v2](https://mui.com/material-ui/react-grid2/)

## Build : Primary App Menu - Explore Categories


![](https://i.imgur.com/8DGkWGI.png)

### PrimaryAppBar.tsx
```typescript
 import { useTheme } from "@mui/material/styles";
import MenuIcon from "@mui/icons-material/Menu";
import React, { useEffect, useState } from "react";
import ExploreCategories from "../../components/SecondaryDraw/ExploreCategories";


const PrimaryAppBar = () => {
    const [sideMenu, setSideMenu] = useState(false);
    const theme = useTheme();

    const isSmallScreen = useMediaQuery(theme.breakpoints.up("sm"));

    useEffect(() => {
        if (isSmallScreen && sideMenu){
            setSideMenu(false);
        }
    }, [isSmallScreen]);

    const toggleDrawer = 
        (open: boolean) => (event: React.KeyboardEvent | React.MouseEvent)=> {
            if(
                event.type === "keydown" &&
                ((event as React.KeyboardEvent).key === "Tab" ||
                    (event as React.KeyboardEvent).key === "Shift")
            )    {
                return;
            }
            setSideMenu(open);
        };
    
        const list = () => (
            <Box
                sx = {{
                    paddingTop: `${theme.primaryAppBar.height}px`, minWidth: 200 
                }}
                role = "presentation"
                onClick={toggleDrawer(false)}
                onKeyDown={toggleDrawer(false)}
            >
                <ExploreCategories />
            </Box>
        );

    return (
        <AppBar sx={{
            zIndex: (theme) => theme.zIndex.drawer + 2,
            backgroundColor: theme.palette.background.default,
            borderBottom: `1px solid ${theme.palette.divider}`,
            }}
        >
            <Toolbar variant="dense" sx={{ 
                height: theme.primaryAppBar.height,
                minHeight: theme.primaryAppBar.height 
                }}
            >
                <Box sx={{ display: { xs: "block", sm: "none" } }}>
                    <IconButton
                        color="inherit"
                        aria-label="open drawer"
                        edge="start"
                        onClick={toggleDrawer(true)}
                        sx={{mr:2}}
                    >
                        <MenuIcon />
                    </IconButton>
                </Box>

                <Drawer anchor="left" open={sideMenu} onClose={toggleDrawer(false)}>
                                    {list()}
                </Drawer>

                <Link href="/" underline="none" color="inherit">
```

위 코드는 `PrimaryAppBar`라는 React 함수형 컴포넌트를 정의하는데, 이는 앱 상단에 위치한 네비게이션 바를 나타낸다. 네비게이션 바에는 좌측에 메뉴 아이콘을 클릭하면 나타나는 측면 메뉴(drawer)와 앱의 로고인 "DJCHAT" 링크가 있다. 해당 컴포넌트는 Material-UI의 컴포넌트들과 기능들을 사용하여 구현했다.

1. `useState(false)`와 `setSideMenu`를 사용하여 `sideMenu`라는 상태 변수를 선언하고 초기값을 `false`로 설정한다. 이 변수는 측면 메뉴의 열림/닫힘 상태를 관리하는 데 사용된다.
    
2. `useTheme()`를 사용하여 현재 Material-UI 테마를 가져온다.
    
3. `useMediaQuery(theme.breakpoints.up("sm"))`를 사용하여 현재 미디어 쿼리 상태를 가져온다. `isSmallScreen`이라는 변수에 저장되며, `sm` 브레이크포인트보다 큰 화면인지 확인한다.
    
4. `useEffect()`를 사용하여 미디어 쿼리 상태(`isSmallScreen`)가 변경될 때마다 실행되는 콜백을 설정한다. 이 콜백은 화면이 `sm` 브레이크포인트보다 큰 경우에 측면 메뉴를 닫도록 한다.
    
5. `toggleDrawer(open: boolean) => (event: React.KeyboardEvent | React.MouseEvent)`는 측면 메뉴를 열고 닫는 함수를 정의한다. 해당 함수는 메뉴 아이콘을 클릭하거나 키보드 이벤트를 통해 호출된다.
    
6. `list()` 함수는 측면 메뉴에 표시될 내용을 정의한다. 이 함수는 `ExploreCategories` 컴포넌트를 렌더링한다.
    
7. `PrimaryAppBar` 컴포넌트에서는 `AppBar` 컴포넌트를 사용하여 네비게이션 바를 생성한다. 해당 바에는 툴바와 로고, 그리고 메뉴 아이콘이 위치한다.
    
8. `IconButton` 컴포넌트를 사용하여 메뉴 아이콘을 생성하고, 이를 클릭하면 `toggleDrawer(true)`를 호출하여 측면 메뉴를 열도록 설정한다.
    
9. `Drawer` 컴포넌트를 사용하여 측면 메뉴를 생성하고, 이를 `sideMenu` 상태에 따라 열거나 닫을 수 있도록 설정한다.
    
10. `Typography` 컴포넌트를 사용하여 "DJCHAT" 로고를 표시하고, 이를 클릭하면 홈페이지로 이동하도록 설정한다.
    

![](https://i.imgur.com/0DfN3iD.png)

## Build : Dark Mode

### AccountButton.tsx
```typescript
import { Box, IconButton, Menu, MenuItem } from "@mui/material";
import { AccountCircle } from "@mui/icons-material";
import DarkModeSwitch from "./DarkMode/DarkModeSwitch";
import { useState } from "react";

const AccountButton = () => {
  const [anchorEl, setAnchorEl] = useState<null | HTMLElement>(null);

  const isMenuOpen = Boolean(anchorEl);

  const handleProfileMenuOpen = (event: React.MouseEvent<HTMLElement>) => {
    setAnchorEl(event.currentTarget);
  };

  const handleMenuClose = () => {
    setAnchorEl(null);
  };

  const renderMenu = (
    <Menu
      anchorEl={anchorEl}
      anchorOrigin={{ vertical: "bottom", horizontal: "right" }}
      open={isMenuOpen}
      keepMounted
      onClose={handleMenuClose}
    >
      <MenuItem>
        <DarkModeSwitch />
      </MenuItem>
    </Menu>
  );

  return (
    <Box sx={{ display: { xs: "flex" } }}>
      <IconButton edge="end" color="inherit" onClick={handleProfileMenuOpen}>
        <AccountCircle />
      </IconButton>
      {renderMenu}
    </Box>
  );
};
export default AccountButton;
```

위 코드는 MUI(Material-UI) 컴포넌트를 사용하여 계정 관련 버튼을 구현한 `AccountButton` 컴포넌트다. 이 컴포넌트는 프로필 아이콘(계정 아이콘)을 클릭하면 메뉴가 나타나며, 메뉴에는 다크 모드를 전환하는 기능을 포함하고 있다.

1. `useState<null | HTMLElement>(null)`을 사용하여 `anchorEl` 상태 변수를 선언하고 초기값을 `null`로 설정한다. 이 변수는 메뉴를 열 때 사용되며, 열려진 메뉴가 없는 상태에서는 `null`로 초기화된다.
    
2. `isMenuOpen = Boolean(anchorEl)`을 사용하여 `anchorEl`의 값이 `null`인지 여부를 확인하는 변수를 설정한다. `anchorEl`이 `null`이 아닌 경우 `isMenuOpen`은 `true`가 된다.
    
3. `handleProfileMenuOpen()` 함수는 프로필 아이콘(계정 아이콘)을 클릭했을 때 메뉴를 열도록 `anchorEl`의 값을 설정하는 함수다.
    
4. `handleMenuClose()` 함수는 메뉴를 닫기 위해 `anchorEl`의 값을 `null`로 설정하는 함수다.
    
5. `renderMenu`은 메뉴를 렌더링하는 JSX 코드다. `Menu` 컴포넌트를 사용하여 메뉴를 구성하고, 메뉴에는 `DarkModeSwitch` 컴포넌트를 포함한 `MenuItem`이 있다.
    
6. `DarkModeSwitch` 컴포넌트는 다크 모드를 전환하는 스위치를 나타내는 컴포넌트다.
    
7. `return` 부분에서는 `IconButton` 컴포넌트를 사용하여 프로필 아이콘(계정 아이콘)을 생성한다. 이 아이콘을 클릭하면 `handleProfileMenuOpen` 함수가 호출되어 메뉴가 열리도록 설정한다. 그리고 `renderMenu`를 렌더링하여 메뉴를 표시한다.

### DarkModeSwitch.tsx
```typescript
import { useContext } from "react";
import { ColorModeContext } from "../../../context/DarkModeContext";
import { useTheme } from "@mui/material/styles";
import { IconButton, Typography } from "@mui/material";
import ToggleOffIcon from "@mui/icons-material/ToggleOff";
import ToggleOnIcon from "@mui/icons-material/ToggleOn";
import Brightness4Icon from "@mui/icons-material/Brightness4";

const DarkModeSwitch = () => {
  const theme = useTheme();
  const colorMode = useContext(ColorModeContext);
  return (
    <>
      <Brightness4Icon sx={{ marginRight: "6px", fontSize: "20px" }} />
      <Typography variant="body2" sx={{ textTransform: "capitalize" }}>
        {theme.palette.mode} mode
      </Typography>
      <IconButton
        sx={{ m: 0, p: 0, pl: 2 }}
        onClick={colorMode.toggleColorMode}
        color="inherit"
      >
        {theme.palette.mode === "dark" ? (
          <ToggleOffIcon sx={{ fontSize: "2.5rem", p: 0 }} />
        ) : (
          <ToggleOnIcon sx={{ fontSize: "2.5rem" }} />
        )}
      </IconButton>
    </>
  );
};
export default DarkModeSwitch;
```

위 코드는 다크 모드를 전환하는 스위치를 구현하는 `DarkModeSwitch` 컴포넌트다. 이 컴포넌트는 MUI(Material-UI)의 아이콘과 텍스트를 사용하여 다크 모드를 토글하는 스위치를 렌더링한다.

1. `useContext(ColorModeContext)`를 사용하여 `DarkModeContext`에서 현재 다크 모드 상태와 다크 모드를 전환하는 함수를 가져온다.
    
2. `useTheme()`를 사용하여 현재 MUI(Material-UI) 테마를 가져온다.
    
3. 컴포넌트에서는 `Brightness4Icon` 아이콘, 텍스트, 그리고 다크 모드를 토글하는 버튼을 렌더링한다.
    
4. `Brightness4Icon` 아이콘은 다크 모드 아이콘으로서 밝은/어두운 아이콘을 나타낸다.
    
5. `Typography` 컴포넌트를 사용하여 `theme.palette.mode` 값을 보여줍니다. 이 값은 현재 테마 모드(밝은 모드인지 어두운 모드인지)를 나타낸다.
    
6. `IconButton` 컴포넌트는 다크 모드를 전환하는 버튼을 나타낸다. 이 버튼을 클릭하면 `colorMode.toggleColorMode` 함수가 호출되어 다크 모드와 밝은 모드를 전환한다.
    
7. 버튼 내부에는 현재 다크 모드 상태에 따라 다른 아이콘이 표시된다.
    
    - 만약 `theme.palette.mode`가 "dark"인 경우, `<ToggleOffIcon>` 아이콘을 표시하고, 그렇지 않은 경우에는 `<ToggleOnIcon>` 아이콘을 표시한다.
    - 따라서 다크 모드가 활성화되어 있을 때는 `ToggleOffIcon`이 나타나며, 비활성화되어 있을 때는 `ToggleOnIcon`이 나타난다.
    - 각 아이콘에는 해당 모드를 토글하는 역할을 한다.

위 코드는 MUI(Material-UI)의 아이콘과 컨텍스트를 사용하여 다크 모드를 토글하는 스위치를 구현하는데 활용된다. 이 컴포넌트를 사용하면 사용자는 테마 모드를 편리하게 전환할 수 있다.

### ExploreCategories.tsx
```typescript
import {

    List,

    ListItem,

    ListItemButton,

    ListItemIcon,

    ListItemText,

    Box,

    useTheme,

    Typography,

} from "@mui/material";

import useCrud from "../../hooks/useCrud";

import { useEffect } from "react";

import ListItemAvatar from "@mui/material/ListItemAvatar";

import { MEDIA_URL  } from "../../config";

import { Link } from "react-router-dom";

  

interface Category {

    id : number;

    name : string;

    description : string;

    icon: string;

}

  

const ExploreCategories = () => {

    const theme = useTheme();

    const isDarkMode = theme.palette.mode === "dark";

    const { dataCRUD, error, isLoading, fetchData } = useCrud<Category>(

        [],

        "/server/category/"

    );

  

    useEffect(() => {

        fetchData();

    }, []);

  

    return <>

    <Box

        sx={{

            height: "50px",

            display: "flex",

            alignItems: "center",

            px: 2,

            borderBottom: `1px solid ${theme.palette.divider}`,

            position: "sticky",

            top: 0,

            backgroundColor: theme.palette.background.default,

        }}

    >

        Explore

    </Box>

        <List sx = {{ py: 0}}>

            {dataCRUD.map((item) => (

                <ListItem

                    disablePadding

                    key = {item.id}

                    sx = {{ display: "block "}}

                    dense={true}

                >

                    <Link

                        to={`/explore/${item.name}`}

                        style={{

                            textDecoration: "none",

                            color: "inherit",

                        }}    

                    >

                    <ListItemButton sx={{ minHeight: 48 }}>

                        <ListItemIcon sx={{ minWidth: 0, justifyContent: "center"}}>

                            <ListItemAvatar sx = {{ minWidth:"0px" }}>

                                <img

                                    alt="server Icon"

                                    src={`${MEDIA_URL}${item.icon}`}

                                    style={{

                                        width: "25px",

                                        height: "25px",

                                        display: "block",

                                        margin: "auto",

                                        filter: isDarkMode ? "invert(100%)" : "none",

                                    }}

                                />

                            </ListItemAvatar>

                        </ListItemIcon>

                        <ListItemText

                            primary={

                                <Typography

                                    variant="body1"

                                    textAlign="start"

                                    paddingLeft={1}

                                >

                                    {item.name}

                                </Typography>

                            }

                        />

                    </ListItemButton>

                    </Link>

                </ListItem>

            ))}

        </List>

    </>;

};

  

export default ExploreCategories;
```


위 코드는 `ExploreCategories` 컴포넌트로, 카테고리 목록을 보여주는 기능을 구현한 컴포넌트다. 해당 컴포넌트는 MUI(Material-UI)의 컴포넌트들과 컨텍스트를 사용하여 구성되어 있다.

1. `useTheme()`를 사용하여 현재 MUI(Material-UI) 테마를 가져온다.
    
2. `theme.palette.mode === "dark"`를 통해 현재 다크 모드인지 아닌지를 판별하여 `isDarkMode` 변수에 저장.
    
3. `useCrud<Category>([], "/server/category/")`를 통해 `useCrud` 커스텀 훅을 사용하여 서버로부터 카테고리 데이터를 가져온다. 해당 데이터는 `dataCRUD`에 저장.
    
4. `useEffect()`를 사용하여 컴포넌트가 처음 렌더링될 때 한 번만 `fetchData()`를 호출하여 카테고리 데이터를 가져오도록 설정.
    
5. `return` 부분에서는 카테고리 목록을 보여주는 UI를 구성.
    
6. `<Box>` 컴포넌트를 사용하여 "Explore"라는 텍스트를 보여주는 타이틀 바를 생성. 이 타이틀 바는 상단에 고정되어 있으며, 상단의 위치에 고정하기 위해 `position: "sticky"` 속성을 사용
    
7. `<List>` 컴포넌트를 사용하여 카테고리 목록을 표시
    
8. `dataCRUD.map()`을 사용하여 서버로부터 가져온 카테고리 데이터를 순회하며 각각의 카테고리 항목을 생성
    
9. `<ListItem>` 컴포넌트를 사용하여 각 카테고리 항목을 표시
    
10. `<Link>` 컴포넌트를 사용하여 각 카테고리 항목을 클릭하면 해당 카테고리의 상세 페이지로 이동하도록 설정
    
11. `<ListItemButton>` 컴포넌트를 사용하여 리스트 아이템을 클릭 가능한 버튼으로 만듬
    
12. `<ListItemIcon>` 컴포넌트를 사용하여 카테고리 아이콘을 표시
    
13. `<ListItemAvatar>` 컴포넌트를 사용하여 아이콘 이미지를 표시, 이미지의 `src` 속성은 카테고리 아이콘의 URL을 가져온다.
    
14. `filter: isDarkMode ? "invert(100%)" : "none"`는 이미지에 스타일을 적용하는 부분이다. `isDarkMode`가 `true`인 경우 이미지에 `invert(100%)` 필터를 적용하여 이미지를 반전시키는 효과를 준다. 즉, 다크 모드인 경우에만 카테고리 아이콘 이미지의 색상을 반전시킨다. 이로써 다크 모드에서 아이콘 이미지가 밝은 색상으로 표시된다.
    
15. `<ListItemText>` 컴포넌트를 사용하여 카테고리 이름을 표시한다.
    

`ExploreCategories` 컴포넌트는 카테고리 목록을 렌더링하는데 사용되며, 각 카테고리 항목은 아이콘과 이름으로 구성되어 있다. 또한 다크 모드에서는 아이콘 이미지가 반전되어 표시된다.

### ToggleColorMode.tsx
```typescript
import { CssBaseline, ThemeProvider, useMediaQuery } from "@mui/material";

import { useEffect, useMemo, useState } from "react";

import React from "react";

import createMuiTheme from "../theme/theme";

import { ColorModeContext } from "../context/DarkModeContext";

import Cookies from "js-cookie";

  

interface ToggleColorModeProps {

  children: React.ReactNode;

}

  

const ToggleColorMode: React.FC<ToggleColorModeProps> = ({ children }) => {

  const storedMode = Cookies.get("colorMode") as "light" | "dark";

  const preferedDarkMode = useMediaQuery("([prefers-color-scheme: dark])");

  const defaultMode = storedMode || (preferedDarkMode ? "dark" : "light");

  

  const [mode, setMode] = useState<"light" | "dark">(defaultMode);

  

  const toggleColorMode = React.useCallback(() => {

    setMode((prevMode) => (prevMode === "light" ? "dark" : "light"));

  }, []);

  

  useEffect(() => {

    Cookies.set("colorMode", mode);

  }, [mode]);

  

  const colorMode = useMemo(() => ({ toggleColorMode }), [toggleColorMode]);

  

  const theme = React.useMemo(() => createMuiTheme(mode), [mode]);

  

  console.log("Retrieved mode:", mode);

  

  return (

    <ColorModeContext.Provider value={colorMode}>

      <ThemeProvider theme={theme}>

        <CssBaseline />

        {children}

      </ThemeProvider>

    </ColorModeContext.Provider>

  );

};

  

export default ToggleColorMode;
```


```
npm install js-cookie
```


위 코드는 다크 모드를 토글할 수 있는 기능을 제공하는 컴포넌트인 `ToggleColorMode` 컴포넌트다. 이 컴포넌트는 MUI(Material-UI)의 `ThemeProvider`와 `CssBaseline`을 사용하여 전체 애플리케이션의 테마를 설정하고, `ColorModeContext`를 사용하여 다크 모드를 관리한다.

1. `interface ToggleColorModeProps`를 사용하여 `children` prop을 정의
    
2. `ToggleColorMode` 컴포넌트는 `children` prop을 받아서 해당 prop으로 렌더링
    
3. `Cookies.get("colorMode")`를 사용하여 이전에 저장된 사용자의 테마 모드를 가져오고 가져온 값은 `storedMode` 변수에 저장
    
4. `useMediaQuery("([prefers-color-scheme: dark])")`를 사용하여 사용자의 시스템 설정에 따라 다크 모드를 지원하는지 여부를 확인. 이 값은 `preferedDarkMode` 변수에 저장.
    
5. `defaultMode` 변수는 이전에 저장된 사용자의 테마 모드가 있으면 해당 값을 사용하고, 그렇지 않으면 사용자의 시스템 설정에 따라 다크 모드를 지원하는지 여부를 확인하여 기본값을 설정
    
6. `useState<"light" | "dark">(defaultMode)`를 사용하여 `mode` 상태 변수를 설정하고, 기본값은 `defaultMode`로 초기화
    
7. `toggleColorMode` 함수는 현재 테마 모드를 토글하는 함수다. `mode` 상태 변수를 업데이트하여 테마 모드를 전환
    
8. `useEffect()`를 사용하여 `mode`가 변경될 때마다 `Cookies.set("colorMode", mode)`를 호출하여 사용자의 테마 모드를 쿠키에 저장
    
9. `useMemo(() => ({ toggleColorMode }), [toggleColorMode])`를 사용하여 `colorMode` 변수를 생성. 이 변수는 `toggleColorMode` 함수를 포함한 객체를 담고 있다.
    
10. `React.useMemo(() => createMuiTheme(mode), [mode])`를 사용하여 `mode` 값에 따라 해당 테마 모드를 생성하는 `theme` 변수를 생성. `createMuiTheme()` 함수는 `mode` 값을 받아와 해당 모드에 맞는 MUI(Material-UI) 테마를 생성.
    
11. `ColorModeContext.Provider`를 사용하여 `colorMode` 값을 만듬. 이렇게 함으로써 하위 컴포넌트에서 `colorMode` 객체를 사용하여 다크 모드를 토글할 수 있다.
    
12. `ThemeProvider`를 사용하여 애플리케이션의 전체 테마를 설정. `theme` 변수에 저장된 테마를 사용.
    
13. `CssBaseline`을 사용하여 초기 CSS 설정을 적용.
    
14. `{children}`을 렌더링하여 하위 컴포넌트를 표시.
    

`ToggleColorMode` 컴포넌트는 전체 애플리케이션의 테마를 설정하고, 다크 모드를 토글할 수 있는 기능을 제공. `ColorModeContext`를 사용하여 다크 모드를 관리하며, 테마 모드에 따라 적절한 MUI(Material-UI) 테마를 생성하여 적용.

### DarkModeContext.tsx

```typescript
import React from "react";

interface ColorModeContextProps {
  toggleColorMode: () => void;
}

export const ColorModeContext = React.createContext<ColorModeContextProps>({
  toggleColorMode: () => {},
});
```

위 코드는 React의 Context API를 사용하여 다크 모드를 관리하기 위한 `ColorModeContext`라는 컨텍스트를 생성하는 부분이다. Context API를 사용하면 상위 컴포넌트에서 하위 컴포넌트로 데이터를 전달할 수 있으며, 이를 활용하여 전역 상태를 관리하거나 여러 컴포넌트 간에 데이터를 공유할 수 있다.

1. `interface ColorModeContextProps`는 `toggleColorMode`라는 함수를 포함하는 인터페이스를 정의. 이 함수는 다크 모드를 토글하는데 사용된다.
    
2. `React.createContext<ColorModeContextProps>({ toggleColorMode: () => {} })`를 사용하여 `ColorModeContext`라는 컨텍스트를 생성. 이 컨텍스트는 기본값으로 `{ toggleColorMode: () => {} }`를 가지며, `toggleColorMode` 함수가 아무 동작을 하지 않도록 빈 함수로 초기화된다.
    

`ColorModeContext`를 이용하면 `toggleColorMode` 함수를 포함한 객체를 생성하여 하위 컴포넌트들이 해당 컨텍스트를 이용하여 다크 모드를 토글할 수 있다. 하위 컴포넌트들은 `useContext`를 사용하여 `ColorModeContext`의 값을 읽거나 변경할 수 있다. 이를 통해 전역적으로 다크 모드를 관리하고 적용하는데 용이하게 사용할 수 있다.


### PrimaryAppBar.tsx 수정
```typescript
import MenuIcon from "@mui/icons-material/Menu";
import React, { useEffect, useState } from "react";
import ExploreCategories from "../../components/SecondaryDraw/ExploreCategories";

import AccountButton from "../../components/PrimaryAppBar/AccountButton";

const PrimaryAppBar = () => {
    const [sideMenu, setSideMenu] = useState(false);
	const PrimaryAppBar = () => {
                        DJCHAT
                    </Typography>
                </Link>
                <Box sx={{ flexGrow: 1}}></Box>
                <AccountButton/>
            </Toolbar>
        </AppBar>
    );
```

### theme.tsx
```typescript
+export const createMuiTheme = (mode: "light" | "dark") => {
    let theme = createTheme({

        typography: {
            fontFamily: [
                'IBM Plex Mono', 'monospace',
                'IBM Plex Sans KR', 'sans-serif'
            ].join(","),
            body1:{
                fontWeight: 500,
                letterSpacing: "-0.5px",
            },
            body2: {
                fontWeight: 500,
                fontSize: "15px",
                letterSpacing: "-0.5px"
            }
        },

        primaryAppBar: {
            height: 50,
        },
        primaryDraw: {
            width: 240,
            closed: 70,
        },
        secondaryDraw: {
            width: 240,
        },
        palette: {
            mode,
        },
        components: {
            MuiAppBar: {
                defaultProps: {
                    color: "default",
                    elevation: 0,
                }
            }
        }
    });
    theme = responsiveFontSizes(theme);
    return theme;
};

export default createMuiTheme;

```

`palette`는 Material-UI에서 테마를 구성하는 중요한 속성 중 하나다. 이 속성은 색상 관련 설정을 담당하여 애플리케이션의 모양과 느낌을 결정하는데 사용된다.

Material-UI의 `createTheme` 함수를 사용하여 테마를 생성할 때, `palette` 속성을 설정하여 기본 색상과 테마 모드를 지정할 수 있다. `palette` 객체는 다음과 같은 속성을 가질 수 있다:

1. `mode`: 테마 모드를 설정하는 속성으로, "light" 또는 "dark" 값을 가진다. "light" 모드는 밝은 테마를, "dark" 모드는 어두운 테마를 나타낸다. 이 값을 통해 사용자가 테마 모드를 전환할 때 해당 모드로 테마가 변경된다.
    
2. `primary`: 기본(primary) 색상으로 사용될 색상을 지정한다. 예를 들어, 앱 바(상단 바)의 배경색이나 버튼의 배경색에 사용된다.
    
3. `secondary`: 보조(secondary) 색상으로 사용될 색상을 지정한다. 예를 들어, 강조된 요소나 액션 버튼에 사용된다.
    
4. `error`: 오류(error) 색상으로 사용될 색상을 지정한다. 예를 들어, 유효성 검사가 실패한 경우 입력 필드의 하이라이트 색상으로 사용될 수 있다.
    
5. `text`: 텍스트의 기본 색상을 지정한다. 앱 내에서 대부분의 텍스트는 이 색상을 기본으로 사용한다.
    
6. `background`: 배경색을 지정합니다. 앱의 전체 배경색으로 사용된다.
    
7. `action`: 액션 요소(버튼, 아이콘 등)에 사용될 색상을 지정한다.
    
8. `divider`: 구분선의 색상을 지정한다.
    

`palette` 속성을 사용하여 각 요소에 사용될 기본 색상을 정의하면 Material-UI가 해당 색상을 요소들에 자동으로 적용한다. 테마 모드가 변경될 때, `mode` 속성에 따라 자동으로 밝은 테마와 어두운 테마가 적절하게 전환된다. 이를 통해 일관성 있고 사용자 정의 가능한 테마를 쉽게 만들 수 있다.















{% endraw %}