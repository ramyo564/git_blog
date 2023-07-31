---

layout: single
title: " [Django DRF] React DjangoDRF project (4) "
categories: Django
tag: [Python,"[Django DRF] DjangoDRF + React chat project",]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---

# Building Chat Services
{% raw %}
## 비동기 웹 어플리캐이션 

Django Channels는 Django의 비동기 기능을 확장하는 서드파티 라이브러리다. 기존의 Django는 동기식 요청-응답 모델을 따르는데, 클라이언트 요청마다 Django 뷰에서 처리를 하고 서버가 해당하는 HTTP 응답을 반환한다. 그러나 Channels는 WebSocket과 같은 비동기 통신, 백그라운드 작업 등을 처리할 수 있도록 Django의 기능을 확장해준다.     

Django Channels는 클라이언트가 계속해서 요청을 보내지 않아도 실시간으로 데이터를 클라이언트에게 푸시하는 것을 가능하게 해준다. 이러한 기능은 채팅 애플리케이션과 같은 기능을 구현하는 데 특히 유용하다.     

Channels를 사용하기 위해 기본 Django 서버가 아닌 다른 서버를 사용해야 한다. 일반적으로 Django 서버는 WSGI(웹 서버 게이트웨이 인터페이스)를 사용하여 동작한다. 이는 웹 서버와 Python 웹 애플리케이션 또는 프레임워크 간의 표준 인터페이스를 정의하는 규격과 프로토콜이다. 이는 Python 웹 개발에서 널리 사용되고 있다. 그러나 WebSocket과 같은 비동기 처리를 위해서는 기본 서버를 사용할 수 없다.     

Channels의 설치 문서에 따르면 ASGI(비동기 서버 게이트웨이 인터페이스) 서버를 사용해야 한다. ASGI는 WSGI와 비슷하지만 비동기 통신이나 WebSocket과 같은 비동기 통신을 다룰 수 있다. 즉, ASGI 서버는 전통적인 Http 트래픽 뿐만 아니라 WebSocket과 같은 비동기 통신을 처리할 수 있다.     
### WSGI vs ASGI

ASGI(Asynchronous Server Gateway Interface)와 WSGI(Web Server Gateway Interface)는 웹 서버와 웹 애플리케이션 혹은 프레임워크 간의 표준 인터페이스를 정의하는 프로토콜이다. 이들은 파이썬 웹 개발에서 웹 서버와 애플리케이션 사이의 상호작용을 담당한다.

1. WSGI(Web Server Gateway Interface):
    
    - WSGI는 기존에 사용되던 웹 서버와 웹 애플리케이션 간의 인터페이스 표준입니다.
    - Synchronous(동기적) 방식으로 동작하며, 요청이 올 때마다 해당 요청에 대한 응답을 반환한다.
    - Django, Flask 등의 대부분의 파이썬 웹 프레임워크는 WSGI를 지원한다.
    - 하지만 WebSocket과 같이 비동기 통신을 처리하기에는 제약이 있어서 비동기 처리를 위해서는 다른 방식의 서버가 필요하다.
2. ASGI(Asynchronous Server Gateway Interface):
    
    - ASGI는 WSGI의 한계를 극복하고 비동기 통신을 지원하기 위해 등장한 새로운 표준이다.
    - Asynchronous(비동기적) 방식으로 동작하여 WebSocket과 같은 실시간 통신을 처리할 수 있다.
    - Django Channels와 같은 비동기 웹 프레임워크에서 사용된다.
    - ASGI 서버는 비동기 처리를 지원하는 서버를 사용하며, Daphne, Uvicorn 등이 ASGI를 지원하는 서버 중 일부다.


간단히 말하면, WSGI는 동기적으로 요청을 처리하는 표준이고, ASGI는 비동기적으로 요청을 처리하면서 WebSocket과 같은 실시간 통신을 지원하는 표준이다. Channels를 사용하여 Django 애플리케이션을 WebSocket과 같은 비동기 기능과 함께 사용하려면 ASGI 서버를 사용해야 한다.


종합적으로, Channels를 사용하여 WebSocket과 같은 실시간 통신을 구현할 수 있으며, Http 트래픽과 WebSocket 트래픽을 구분하여 다룰 수 있다. 채팅 애플리케이션을 구축하기 위해 Http 트래픽과 WebSocket 트래픽을 각각 다른 방식으로 처리하여 웹 애플리케이션의 성능을 개선할 수 있다.     

일반적인 Http 트래픽이 애플리케이션으로 들어오면, 이는 기존의 Django 루트 URL과 뷰를 통해 관리된다. 반면 WebSocket 트래픽이 애플리케이션으로 들어오면, 이는 다른 방식으로 처리되고 컨슈머(consumer)로 라우팅된다.      

처음으로 Channels를 설정할 때 목표는 일반 Http 트래픽을 기존의 Django 경로(URL) 및 뷰를 통해 라우팅하는 것이며, 채팅 통신을 위해 생성할 WebSocket 트래픽을 새로운 방식으로 처리하고 컨슈머로 라우팅하는 것이다.     

설치 문서에는 Channels를 설치하는 것 외에도 애플리케이션 서버로 Daphne가 필요하다고 명시되어 있다. Daphne는 인기 있는 애플리케이션 서버 중 하나이며, 여러 개의 서버 중 선택할 수 있다. 물론 Channels를 설치하고 해당 서버를 사용해도 문제는 없다. 이 프로젝트에서는 Uvicorn을 웹 서버로 사용할 것이다.     

 "Daphne vs Uvicorn, 어떤 게 더 좋을까?" Daphne와 Uvicorn은 각기 다른 엔진에서 파생되었다. 예를 들어 Daphne는 twisted 네트워크 엔진을 기반으로 하고, Uvicorn은 async 프레임워크를 기반으로 한다. 따라서 다소 다른 엔진을 기반으로 한다.     

Daphne는 Django 애플리케이션에 대해 SGI 서버를 위해 권장되며, Django Channels와 잘 통합된다. 반면 Uvicorn은 fastAPI와 함께 사용되는 것이 일반적이며 뛰어난 성능과 속도를 자랑한다.      

요약하면, Daphne은 주로 Django 애플리케이션을 위해 설계되었고 Django Channels와 잘 어울립니다. 반면 Uvicorn은 높은 성능을 강조하는 서버로, Django와도 잘 작동한다. 만약 Django와 WebSocket 지원을 주로 사용한다면 Daphne을 선택하는 것이 좋다. 하지만 성능을 우선시하고 높은 동시성을 다룰 수 있는 서버를 원한다면 Uvicorn을 고려해보는 것이 좋다.        


## Daphne vs uvicorn
  
Daphne과 uvicorn은 모두 Python으로 작성된 ASGI(Asynchronous Server Gateway Interface) 서버다. ASGI는 비동기 웹 애플리케이션을 처리하기 위한 Python의 표준 인터페이스로, 기존의 WSGI(Web Server Gateway Interface)를 확장하여 비동기 요청과 응답을 지원한다. ASGI를 사용하면 비동기 웹 애플리케이션을 더 효율적으로 처리할 수 있다.     

하지만 Daphne과 uvicorn은 서로 다른 ASGI 서버다. 각각의 특징과 사용되는 목적이 조금 다르다:

1. Daphne:
    
    - Daphne은 Django 웹 프레임워크와 함께 사용되는 ASGI 서버다.
    - Django의 비동기 기능을 지원하고, Django Channels를 사용하여 WebSocket과 같은 프로토콜을 처리하는데 적합하다.
    - Django 애플리케이션의 ASGI 호환 서버로서 Django의 개발 생태계와 잘 통합되어 있다.
2. uvicorn:
    
    - uvicorn은 Starlette와 FastAPI 같은 비동기 웹 프레임워크와 함께 사용되는 ASGI 서버다.
    - Starlette와 FastAPI는 비동기 처리에 특화되어 있으며, uvicorn은 이러한 프레임워크와 잘 맞아서 높은 성능을 제공한다.
    - FastAPI는 특히 빠른 API 개발을 위해 디자인된 프레임워크로, uvicorn과 함께 사용되면 높은 성능과 비동기 기능을 제공한다.

따라서 Daphne과 uvicorn은 모두 ASGI 서버이지만, 주로 사용되는 프레임워크와의 통합, 성능, 그리고 비동기 처리에 따라 선택되는 경우가 다를 수 있다.       



## Build : Installing Django Channels

```
pip install channels
```
[장고 채널 공문](https://channels.readthedocs.io/en/latest/)
[장고 채널 소개 글](https://testdriven.io/blog/django-channels/)


## Build : Installing and Managing Uvicorn

```
pip install 'uvicorn[standard]'
```

```
uvicorn {루트앱폴더}.asgi:application --port 8000 --workers 4 --log-level debug --reload
```
[유비콘 공문](https://www.uvicorn.org/)


기본적으로 Django 애플리케이션을 실행할 때는 `manage.py runserver` 명령을 사용하여 WSGI(Web Server Gateway Interface) 서버를 실행한다. WSGI는 웹 서버와 파이썬 웹 애플리케이션 간의 통신을 정의하는 명세로, Django 애플리케이션을 WSGI 서버를 사용하여 실행한다.     

그러나 Django Channels를 사용하기 위해서는 WSGI 서버가 아닌 ASGI(Asynchronous Server Gateway Interface) 서버가 필요하다. ASGI는 웹 서버와 Python 웹 애플리케이션 또는 프레임워크 간의 비동기 통신을 정의하는 명세로, WebSocket과 같은 비동기 통신을 지원한다.

Uvicorn은 ASGI 서버로, Django Channels와 함께 사용하면 WebSocket과 같은 비동기 기능을 지원하는 빠르고 성능이 좋은 서버를 구축할 수 있다. Uvicorn은 FastAPI와 같이 높은 성능을 요구하는 애플리케이션에 적합하며, Django 애플리케이션과 WebSocket을 함께 사용해야 할 때에도 좋은 선택이 될 수 있다.

따라서 Uvicorn은 Django Channels와 함께 사용하여 Django 애플리케이션에서 WebSocket과 같은 비동기 기능을 구현하는 데 사용된다.


여기서 `chat.asgi`는 ASGI 파일의 경로를 지정하고, `application`은 해당 파일 내의 애플리케이션 객체를 지정한다. `--port` 옵션으로 포트를 설정하고, `--workers` 옵션으로 생성할 워커 프로세스의 수를 지정한다. 워커 프로세스는 들어오는 요청을 처리하기 위해 생성되며, 동시에 여러 요청을 처리할 수 있어 서버의 처리 능력을 높여준다.

`--reload` 옵션은 코드 변경 시 서버를 자동으로 리로드한다. 개발 환경에서는 코드 수정 후 자동 리로드 기능을 사용하면 편리하게 작업할 수 있다.

마지막으로, `--log-level debug` 옵션은 디버그 정보를 로그로 출력한다. 디버깅이나 문제 해결을 위해 자세한 정보가 필요한 경우 유용하다.

명령어를 입력하면 서버가 실행되며, 루프백 주소(localhost)와 8000 포트로 서버가 동작함을 확인할 수 있다.

그러나 이제 WebSockets와 같은 비동기 요청을 처리해야 하므로, Http 요청과 WebSockets 요청을 분리하여 다르게 처리해야 한다. 

## Build : Implimenting WebSockets - Routing

[ProtocolTypeRouter](https://channels.readthedocs.io/en/stable/topics/routing.html#protocoltyperouter)

Django Channels의 라우팅을 설정하는 작업을 해야한다. HTTP와 웹소켓 트래픽을 분리하고 해당 트래픽을 각각 다른 리소스로 보낼 수 있어야 한다.

우선, ASGI 파일(chat/asgi.py)에 필요한 리소스를 임포트해야 한다. channels.routing에서 ProtocolTypeRouter와 URLRouter를 임포트한 후 ProtocolTypeRouter는 HTTP와 웹소켓 트래픽을 분리하는 데 사용되며, URLRouter는 URL 패턴을 설정하는 데 사용된다.

그리고 두 개의 라우터를 생성한다. 하나는 HTTP 데이터를 처리하기 위한 것으로 기존의 Django URL과 뷰를 사용하고. 다른 하나는 웹소켓 데이터를 처리하기 위한 것으로 WebSocket의 URL을 설정해야 한다.

urls.py
```python
from django.conf import settings

from django.conf.urls.static import static

from django.contrib import admin

from django.urls import path

from drf_spectacular.views import (SpectacularAPIView,

                                   SpectacularSwaggerView)

from rest_framework.routers import DefaultRouter

  

from server.views import ServerListViewSet, CategoryListViewSet

  

router = DefaultRouter()

router.register("api/server/select", ServerListViewSet)

router.register("api/server/category", CategoryListViewSet)

  

urlpatterns = [

    path('admin/', admin.site.urls),

    path('api/docs/schema/', SpectacularAPIView.as_view(), name='schema'),

    path('api/docs/schema/ui/', SpectacularSwaggerView.as_view()),

] + router.urls

  

websocket_urlpatterns = [path()]

  

if settings.DEBUG:

    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

```

이제 WebSocket용 URL 패턴을 추가해야 한다. 해당 URL 패턴에 웹소켓으로 처리할 뷰를 연결하여 WebSocket 트래픽을 처리할 준비를 해야 한다. 현재 코드에는 WebSocket에 대한 URL 패턴이 비어 있으므로 나중에 해당 URL 패턴에 뷰를 연결하는 작업을 해주어야 한다.     

이전에 ASGI 파일(chat/asgi.py)에 ProtocolTypeRouter와 URLRouter를 임포트하고, 두 개의 라우터를 생성했다. 이제 웹소켓 트래픽을 처리할 WebSocket용 URL 패턴을 설정해야 한다. 이는 URLs 파일(chat/urls.py)에서 이루어진다.

웹소켓 트래픽을 처리할 WebSocket용 URL 패턴을 WebSocket URL patterns라는 이름으로 설정한다. 이제 WebSocket용 URL 패턴에 웹소켓으로 처리할 뷰를 연결해야 한다. 뷰는 웹소켓 트래픽을 처리하는 콘슈머(consumer)로 구현된다. 하지만 아직 뷰를 생성하지 않았으므로, 일단 WebSocket URL patterns를 비워둔다.

Django 프로젝트를 초기화하기 전에 URL 임포트를 시도하고 있기 때문에 오류가 있다. Django 프로젝트를 초기화해야 URLs 파일을 임포트할 수 있다. 따라서 Django 프로젝트를 초기화하고 나서야 URLs를 임포트하도록 코드를 수정해야 한다.

asgi.py
```python
# urls를 Django 프로젝트 초기화 후에 임포트하도록 코드를 수정합니다.
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from django.urls import path

# Django 프로젝트를 초기화합니다.
django_application = get_asgi_application()

# 라우터 생성
application = ProtocolTypeRouter(
    {
        # HTTP 요청은 기존 방식대로 처리
        "http": django_application,
        # WebSocket 요청은 URLRouter에 설정된 URL 패턴을 따라 처리
        "websocket": URLRouter([
            # 여기에 WebSocket에 대한 URL 패턴을 추가해야 합니다.
            # 나중에 웹소켓으로 처리할 뷰를 연결합니다.
        ]),
    }
)

# 라우터와 함께 설정될 WebSocket용 URL 패턴
websocket_urlpatterns = [
    # 여기에 WebSocket에 대한 URL 패턴을 추가해야 합니다.
    # 나중에 웹소켓으로 처리할 뷰를 연결합니다.
]

```

이제 라우터 설정과 WebSocket URL 패턴이 올바르게 초기화되었다. 웹소켓 트래픽을 처리할 뷰를 생성하기 전까지는 WebSocket URL patterns를 비워두면 된다. 뷰를 생성한 후에는 해당 URL 패턴에 뷰를 연결하여 웹소켓 트래픽을 처리할 수 있게 된다.

#### 웹소켓이란?

웹소켓(WebSocket)은 실시간 양방향 통신을 지원하는 프로토콜이다. 기존의 HTTP 프로토콜은 클라이언트가 서버에 요청을 보내면 서버가 그에 대한 응답을 보내는 단방향 통신 방식이었다. 즉, 클라이언트는 서버에게 요청을 보내기 위해 반드시 HTTP 요청을 보내야 했고, 서버도 클라이언트에게 응답을 보내기 위해 HTTP 응답을 전송해야 했다.

하지만 웹소켓은 이와 달리, 한 번의 연결을 통해 서버와 클라이언트 간에 양방향 통신을 지원한다. 클라이언트와 서버가 한 번 연결되면 그 후에는 계속해서 실시간으로 데이터를 주고받을 수 있다. 이를 통해 서버에서 데이터를 액티브하게 푸시(push)하고, 클라이언트는 요청 없이도 실시간으로 업데이트를 받아볼 수 있다.

웹소켓은 실시간 채팅 애플리케이션, 온라인 게임, 주식 시장 모니터링, 알림 기능 등 실시간 데이터를 다루는 애플리케이션에서 주로 활용된다. 기존의 HTTP 요청-응답 방식으로는 불가능했던 실시간 통신을 웹소켓을 이용하여 구현할 수 있다.


## Build : Channel Consumer


컨슈머는 채팅 애플리케이션에서 메시지를 보내고 받는 데 필수적인 로직을 구축하는 데  필요하고 또한 통신 서비스와 관련된 모든 로직을 담당한다.     
"consumer.py"라는 새로운 파일을 만들어서 첫 번째 컨슈머를 구축했다.     
컨슈머는 Django Channels에서 들어오는 메시지를 처리하고 WebSocket 연결이나 다른 지원하는 프로토콜에서 나가는 메시지를 생성하는 Python 클래스로서, 서버와 클라이언트 간 통신의 주요 로직 핸들러 역할을 한다.    

우선적으로 동기식과 비동기식 컨슈머로 나누는데, 동기식 컨슈머를 주로 사용할 예정이며, 필요할 때 비동기식으로 전환할 수 있다.     
"JsonWebsocketConsumer" 클래스를 활용하여 JSON 데이터와 웹소켓 연결을 쉽게 처리할 수 있다.     

![](https://i.imgur.com/YhlOPpS.png)
[컨슈머 공문](https://channels.readthedocs.io/en/stable/topics/consumers.html)

이 컨슈머는 웹소켓 프레임으로 전송된 JSON 데이터를 자동으로 인코딩하고 디코딩하여 JSON 페이로드를 쉽게 다룰 수 있도록 지원한다.
컨슈머가 제공하는 메서드들에 대한 내부 동작 방식에 대해서다.
이 메서드들은 연결 관리, 데이터 수신 및 웹소켓 연결 해제와 관련된 기능을 담당합니다.
튜토리얼에서는 먼저 기본적인 컨슈머를 만든 후, 프론트엔드로 이동하여 클라이언트와 서버 간의 웹소켓 연결을 설정할려고 한다.     

튜토리얼에서는 "JsonWebsocketConsumer" 클래스를 최종적으로 활용할 예정이지만, 현재는 설정 과정을 배우기 위해 일단은 "WebsocketConsumer" 클래스를 사용할 것이다.

disconnect 메서드가 있는 이유는 연결을 닫기 전에 추가 작업이 필요한 경우가 있기 때문이다. 예를 들어, 데이터베이스 또는 다른 자원을 정리해야 할 수도 있다.     

[리엑트 웹소캣](https://www.npmjs.com/package/react-use-websocket)

"npm install use-websockets" 명령을 실행하여 이 라이브러리를 설치한 후, WebSocket 연결을 만든다. 연결 URL과 콜백 함수들을 제공하여 연결 상태 및 에러 등을 모니터링할 수 있다.     
그런 다음, 프론트엔드에서 웹소켓 연결을 설정할 페이지를 만들어서 서버에 연결한다. 이로써 백엔드와 프론트엔드 간의 웹소켓 연결이 성립되며, 채팅 서비스를 구축하기 위한 기반이 마련된다.     

요약하자면 현재 상태에서는 웹소켓 연결이 성공적으로 이루어지고, 서버와 프론트엔드 간에 상태가 유지되며, 서버로부터 메시지를 보내고 그에 대한 응답 메시지를 받을 수 있도록 한다.     

### consumer.py
```python

from channels.generic.websocket import WebsocketConsumer

  
  

class MyConsumer(WebsocketConsumer):

    def connect(self):

        self.accept()

  

    def receive(self, text_data):

        self.send(text_data=text_data)

  

    def disconnect(self, close_code):

        pass
```

이 코드는 Django Channels에서 사용되는 WebsocketConsumer의 기본 예제다. WebsocketConsumer는 WebSocket 연결을 처리하기 위해 Django Channels에서 제공하는 기본 클래스다.

1. MyConsumer 클래스는 WebsocketConsumer 클래스를 상속한다.
    
2. connect(self) 메서드는 클라이언트가 WebSocket 연결을 시도할 때 호출된다. 이 예제에서는 self.accept()를 호출하여 클라이언트의 연결을 수락한다.
    
3. receive(self, text_data) 메서드는 클라이언트로부터 메시지를 받을 때 호출된다. 이 예제에서는 클라이언트로부터 받은 메시지를 그대로 다시 클라이언트로 보내는 self.send(text_data=text_data)를 호출한다.
    
4. disconnect(self, close_code) 메서드는 클라이언트가 연결을 종료할 때 호출된다. 이 예제에서는 아무 작업도 수행하지 않고, pass 문을 사용하여 빈 메서드로 남겨둔다.
    

이러한 형태의 Consumer를 이용하여 Django Channels를 사용하면, 클라이언트와 실시간으로 양방향 통신을 할 수 있다. 클라이언트가 서버로 메시지를 보내면 해당 메시지를 처리하고, 서버가 클라이언트로 메시지를 보내면 클라이언트는 이를 처리하는 방식으로 실시간 채팅이나 실시간 데이터 전송 기능을 구현할 수 있다.

### urls.py
```python
from django.conf import settings

from django.conf.urls.static import static

from django.contrib import admin

from django.urls import path

from drf_spectacular.views import (SpectacularAPIView,

                                   SpectacularSwaggerView)

from rest_framework.routers import DefaultRouter

  

from server.views import ServerListViewSet, CategoryListViewSet

from webchat.consumer import MyConsumer

  

router = DefaultRouter()

router.register("api/server/select", ServerListViewSet)

router.register("api/server/category", CategoryListViewSet)

  

urlpatterns = [

    path('admin/', admin.site.urls),

    path('api/docs/schema/', SpectacularAPIView.as_view(), name='schema'),

    path('api/docs/schema/ui/', SpectacularSwaggerView.as_view()),

] + router.urls

  

websocket_urlpatterns = [path("ws/test", MyConsumer.as_asgi())] <- 추가

  

if settings.DEBUG:

    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

websocket_urlpatterns는 Django Channels를 사용하여 WebSocket 연결을 처리하는 URL 패턴을 정의하는 부분이다. Django에서 일반적으로 사용되는 URL 패턴은 HTTP 요청을 처리하는 데 사용되지만, Django Channels는 WebSocket 연결을 다루기 위해 별도의 URL 패턴을 정의해야 한다.

WebSocket 연결은 HTTP 요청과는 다른 프로토콜을 사용하며, 웹소켓 연결은 웹소켓 URL에 대한 HTTP 요청을 통해 이루어진다. 웹소켓 연결이 성공하면 HTTP 연결이 열리고, 이후 실시간 양방향 통신이 웹소켓을 통해 이루어진다.

websocket_urlpatterns 변수는 WebSocket 연결을 처리할 URL 패턴을 정의한다. 이 변수에는 as_asgi() 메서드를 사용하여 MyConsumer 클래스를 ASGI(Asynchronous Server Gateway Interface) 프로토콜을 준수하는 객체로 변환하여 등록한다.

즉, path("ws/test", MyConsumer.as_asgi())는 "/ws/test" 경로로 들어오는 WebSocket 연결 요청을 MyConsumer 클래스로 처리하도록 매핑한다. 따라서 "/ws/test"로 들어오는 모든 WebSocket 연결 요청은 MyConsumer의 connect(), receive(), disconnect() 메서드를 통해 처리되게 된다. 이를 통해 클라이언트와 서버 사이의 실시간 양방향 통신이 가능해진다.

### asgi.py
```python

import os

from channels.routing import ProtocolTypeRouter, URLRouter

  

# from django.core.wsgi import get_wsgi_application

from django.core.asgi import get_asgi_application

  

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'djchat.settings')

django_application = get_asgi_application()

  

from . import urls # noqa isort: skip

  
# application = get_wsgi_application()

application = ProtocolTypeRouter(

    {

        "http": get_asgi_application(),

        "websocket": URLRouter(urls.websocket_urlpatterns),

    }

)
```

이 코드는 Django Channels의 ASGI(Application Server Gateway Interface) 설정을 구성하는 부분이다. ASGI는 Django 애플리케이션이 웹소켓과 같은 비동기 프로토콜을 지원하도록 해주는 Python 웹 서버와 웹 애플리케이션 간의 표준 인터페이스다.     

`ProtocolTypeRouter`는 ASGI 애플리케이션의 프로토콜 라우팅을 처리하는 클래스다. 이 클래스를 사용하여 HTTP 요청과 웹소켓 연결을 각각 다른 핸들러로 라우팅할 수 있다.              

`get_asgi_application()` 함수는 Django의 기본 ASGI 애플리케이션 객체를 반환합니다. 이 함수를 사용하여 Django 애플리케이션을 ASGI 애플리케이션으로 변환한다.     

`django_application` 변수는 Django 애플리케이션을 ASGI 애플리케이션으로 변환한 객체다.     

`URLRouter`는 URL 패턴을 처리하는 클래스로, WebSocket 연결을 다루기 위해 사용된다. `urls.websocket_urlpatterns`는 이전 코드에서 정의한 `websocket_urlpatterns` 변수로 WebSocket 연결을 처리하는 URL 패턴들이 저장되어 있다.     

마지막으로, `ProtocolTypeRouter`를 사용하여 HTTP 요청과 웹소켓 연결을 각각 다른 핸들러로 라우팅한다. HTTP 요청은 `get_asgi_application()`으로 처리하고, 웹소켓 연결은 `URLRouter(urls.websocket_urlpatterns)`로 처리한다. 따라서 이 애플리케이션은 HTTP 요청과 웹소켓 연결을 모두 처리할 수 있는 Django Channels ASGI 애플리케이션이 된다.     

### Server.tsx
```typescript
import { useState } from "react";

import useWebSocket from "react-use-websocket";

  

const socketUrl = "ws://127.0.0.1:8000/ws/test";

  

const Server = () => {

  const [message, setMessage] = useState("");

  const [inputValue, setInputValue] = useState("");

  const { sendJsonMessage } = useWebSocket(socketUrl, {

    onOpen: () => {

      console.log("Connected!");

    },

    onClose: () => {

      console.log("Closed!");

    },

    onError: () => {

      console.log("Error!");

    },

    onMessage: (msg) => {

      setMessage(msg.data);

    },

  });

  

  const sendInputValue = () => {

    const message = { text: inputValue };

    sendJsonMessage(message);

    setInputValue("");

  };

  

  return (

    <div>

      <input

        type="text"

        value={inputValue}

        onChange={(e) => setInputValue(e.target.value)}

      />

      <button onClick={sendInputValue}>Send</button>

      <div>Recieved Data: {message}</div>

    </div>

  );

};

export default Server;
```

위 코드는 React 컴포넌트인 Server를 정의하는 부분이다. 이 컴포넌트는 WebSocket을 사용하여 서버와 통신하고, 사용자가 입력한 값을 서버로 전송하고, 서버로부터 받은 데이터를 화면에 표시한다.     

- `useState` 훅을 사용하여 `message`와 `inputValue` 상태를 정의한다. `message`는 서버로부터 받은 데이터를 저장하고, `inputValue`는 사용자가 입력한 값을 저장한다.
- `useWebSocket` 커스텀 훅을 사용하여 WebSocket을 관리한다. `socketUrl`은 서버의 WebSocket 엔드포인트를 나타낸다.
- `onOpen`, `onClose`, `onError`, `onMessage`는 WebSocket의 상태 변화나 메시지 수신에 대한 이벤트 핸들러를 정의한다. `onOpen`은 WebSocket 연결이 열렸을 때 호출되며, `onClose`는 연결이 닫혔을 때 호출된다. `onError`는 오류가 발생했을 때 호출되며, `onMessage`는 서버로부터 메시지를 수신했을 때 호출된다.
- `sendInputValue` 함수는 사용자가 입력한 값을 서버로 보내고, 입력 필드를 초기화한다.
- JSX를 사용하여 화면에 입력 필드와 버튼을 렌더링하고, 받은 데이터를 표시한다.

이 컴포넌트를 사용하면 사용자가 입력한 메시지를 서버로 전송하고, 서버에서 받은 메시지를 브라우저 화면에 표시할 수 있다.

### App.tsx
```typescript
  

import Home from "./pages/Home"

import Server from "./pages/Server"

import Explore from "./pages/Explore";

import { createBrowserRouter, createRoutesFromElements, Route, RouterProvider } from "react-router-dom"

import ToggleColorMode from "./components/ToggleColorMode";

  

const router = createBrowserRouter(

  createRoutesFromElements(

    <Route>

      <Route path="/" element={<Home/>} />

      <Route path="/server" element={<Server/>} />

      <Route path="/explore/:categoryName" element={<Explore />} />

    </Route>

  )

);

  

const App = () => {

  

  return (

    <ToggleColorMode>

      <RouterProvider router={router} />

    </ToggleColorMode>

  );

};

  

export default App;
```

위 코드는 React 앱의 라우팅과 페이지 구성을 담당하는 부분이다. `react-router-dom` 라이브러리를 사용하여 라우팅을 설정하고, 각 경로에 대응하는 페이지 컴포넌트를 매핑한다.     

- `Home`, `Server`, `Explore` 컴포넌트는 각각 "Home", "Server", "Explore" 페이지를 나타내는 컴포넌트다.
- `createBrowserRouter` 함수를 사용하여 브라우저 기반의 라우터를 생성한다.
- `createRoutesFromElements` 함수를 사용하여 JSX 엘리먼트를 기반으로 라우트 구성을 생성한다. `Route` 컴포넌트를 중첩하여 경로와 해당 페이지 컴포넌트를 매핑한다.
- `router` 변수에 생성한 라우터를 저장한다.
- `ToggleColorMode` 컴포넌트를 사용하여 앱의 컬러 모드를 토글할 수 있도록 래핑한다.
- `<RouterProvider>` 컴포넌트를 사용하여 라우터를 앱에 제공합니다. 이를 통해 페이지 간의 이동이 가능해진다.

이 코드는 `react-router-dom`을 사용하여 각 경로에 대응하는 페이지를 렌더링하는 방식으로 React 앱의 라우팅을 구성한다. `Home`, `Server`, `Explore` 페이지에 접근하고자 할 때, 라우터를 통해 해당 페이지 컴포넌트가 렌더링되어 사용자에게 표시된다.



![](https://i.imgur.com/XuzOH1c.png)

hi 라고 보내면 hi 라고 출력

## Build : Towards Multiple User Chat Rooms

[채널 레이어](https://channels.readthedocs.io/en/stable/topics/channel_layers.html)

![](https://i.imgur.com/HvHJZd0.png)

Redis 를 이용해 개발할 수 있지만 복잡하기도 하고 도커를 사용하고 있지 않은점등을 고려해 메모리 채널을 이용해 구현을 하는 게 지금 상황에 맞는 것 같다.     
(Redis 에 대해서 아직 잘 모른다.ㅜ)

![](https://i.imgur.com/MEhZIZe.png)

실제 배포단계에서는 데이터 손실 및 성능 저하가 일어날 수 있으므로 메모리 채널은 로컬로 찍먹용으로 적당한 것 같다.      
지금 토이프로젝트를 완성하면 Redis 를 배울 생각인데 Redis 한 번 배우고나서 응용겸 해당 부분 다시 개발하면 딱일 듯 하다.     

### settings.py
```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels.layers.InMemoryChannelLayer"
    }
}
```



한 사용자가 다른 사용자에게 메시지를 보낼 수 있는 채팅방을 만들 때 이를 위해 처음 연결되는 순간 사용자를 그룹에 넣는다. 그러면 그 그룹에 속한 모든 사용자에게 메시지를 보낼 수 있다.      

이를 channels 용어로 말하면 채널 레이어(Channel Layer)를 이용한 통신이다. 채널 레이어는 서로 다른 애플리케이션 인스턴스간에 데이터를 주고받을 수 있도록 해준다. 사용자가 웹소켓을 통해 서버에 연결할 때마다 새로운 애플리케이션 인스턴스가 생성되며, 이러한 인스턴스들끼리 데이터를 전달할 수 있도록 채널 레이어를 활용한다.     
Redis 기술을 이용해서 채널 레이어를 구현할 수 있지만, 아직 잘 모르기도 하고 배우는 단계라서 단순화하기 위해 인메모리 채널 레이어를 사용하고 이건 로컬 개발 용도로 딱이다.     

채팅 컨슈머를 수정하여 사용자들 간에 메시지를 주고받을 수 있는 방법은 첫 번째로 연결된 사용자를 그룹에 할당하고 해당 그룹에 속한 사용자들에게 메시지를 보낼 수 있도록 구현하는 것이다. 이를 위해 비동기 코드로 변환해야 하므로 `async to sync`를 이용하여 동기 코드로 변환시킨다.     

또한 데이터베이스에서 채팅방과 그룹에 속한 사용자들을 저장하기 위해 방 이름과 채널 이름을 변수로 추가해야한다.      
그리고 메시지를 어디로 보낼지를 식별하기 위해 Django channels에게 해당 메시지가 방 이름으로 보내져야 한다고 알려주면 된다.      

### consumer.py
```python
from channels.generic.websocket import JsonWebsocketConsumer

from asgiref.sync import async_to_sync

  
  

class WebChatConsumer(JsonWebsocketConsumer):

    def __init__(self, *args, **kwargs):

        super().__init__(*args, **kwargs)

        self.room_name = "testserver"

    def connect(self):

        self.accept()

        async_to_sync(self.channel_layer.group_add)(

            self.room_name,

            self.channel_name,

        )

  

    def receive_json(self, content):

        async_to_sync(self.channel_layer.group_send)(

            self.room_name,

            {

                "type": "chat.message",

                "new_message": content["message"],

            }

        )

  

    def chat_message(self, event):

        self.send_json(event)

  

    def disconnect(self, close_code):

        pass
```

위 코드는 Django Channels를 사용하여 WebSocket 통신을 구현하는 `WebChatConsumer` 클래스다. 

1. `WebChatConsumer` 클래스는 `JsonWebsocketConsumer`를 상속한다. 이는 WebSocket을 사용하여 JSON 형식의 데이터를 주고받기 위한 기본 클래스다.
    
2. `__init__` 메서드에서는 부모 클래스의 생성자를 호출하여 초기화하고, `self.room_name` 변수를 "testserver"로 설정한다. 이는 웹소켓 연결 시 사용할 채팅방 이름을 지정하는 부분이다.
    
3. `connect` 메서드는 WebSocket 연결이 이루어질 때 호출된다. 여기서는 연결을 수락하고, `async_to_sync`를 사용하여 채널 레이어의 `group_add` 메서드를 호출한다. 이를 통해 해당 클라이언트를 "testserver"라는 그룹에 추가합니다. 이 그룹은 채팅방 역할을 한다.
    
4. `receive_json` 메서드는 클라이언트로부터 JSON 형식의 데이터를 수신할 때 호출된다. 이 메서드에서는 받은 메시지를 `async_to_sync`를 사용하여 채널 레이어의 `group_send` 메서드를 호출하여 모든 그룹 멤버에게 메시지를 보낸다.
    
5. `chat_message` 메서드는 채널 레이어의 `group_send` 메서드로부터 메시지를 수신할 때 호출된다. 이 메서드에서는 받은 메시지를 클라이언트로 다시 전송한다. 이를 통해 모든 그룹 멤버들에게 메시지를 보여줄 수 있다.

이렇게 구현된 `WebChatConsumer` 클래스를 사용하면 클라이언트들이 "testserver"라는 채팅방에 연결되어 메시지를 주고받을 수 있게 된다. 클라이언트로부터 받은 메시지는 해당 채팅방의 모든 사용자들에게 전달되며, 모든 사용자들의 메시지는 클라이언트들에게 보여진다.


### Server.tsx
```typescript
import { useState } from "react";

import useWebSocket from "react-use-websocket";

  

const socketUrl = "ws://127.0.0.1:8000/ws/test";

  

const Server = () => {

  const [newMessage, setNewMessage] = useState<string[]>([]);

  const [message, setMessage] = useState("");

  

  const { sendJsonMessage } = useWebSocket(socketUrl, {

    onOpen: () => {

      console.log("Connected!");

    },

    onClose: () => {

      console.log("Closed!");

    },

    onError: () => {

      console.log("Error!");

    },

    onMessage: (msg) => {

      const data = JSON.parse(msg.data);

      setNewMessage((prev_msg) => [...prev_msg, data.new_message]);

    },

  });

  

  return (

    <div>

      {newMessage.map((msg, index) => {

        return(

          <div key={index}>

            <p>{msg}</p>

          </div>

        );

      })}

      <form>

        <label>

          Enter Message:

          <input

            type="text"

            value={message}

            onChange={(e) => setMessage(e.target.value)}

          />

  

        </label>

      </form>

      <button onClick={() => {

          sendJsonMessage({type: "message", message});

        }}

      >

        Send Message

      </button>

    </div>

  );

};

export default Server;
```

위 코드는 React 애플리케이션에서 WebSocket을 사용하여 채팅 기능을 구현한 코드다.

1. 코드의 시작 부분에서 `useState` 훅을 사용하여 상태를 관리한다. `newMessage` 상태는 채팅방에 온 메시지들을 배열로 저장하고, `message` 상태는 입력 필드에 입력된 메시지를 저장한다.
    
2. `useWebSocket` 훅을 사용하여 WebSocket 연결을 설정한다. `socketUrl`은 WebSocket 서버의 URL 주소다. `sendJsonMessage` 함수를 이용하여 서버로 JSON 형식의 메시지를 보낼 수 있다. `onOpen`, `onClose`, `onError`, `onMessage` 콜백 함수를 정의하여 WebSocket의 상태 변화에 대한 처리를 설정한다.
    
3. `onMessage` 콜백 함수에서 새로운 메시지가 도착하면 `setNewMessage` 함수를 사용하여 `newMessage` 상태에 새로운 메시지를 추가한다.
    
4. 렌더링 부분에서는 `newMessage` 상태를 사용하여 채팅 메시지들을 표시한다. `map` 함수를 사용하여 배열에 있는 모든 메시지를 순회하며 화면에 표시한다.
    
5. 아래에는 입력 폼과 전송 버튼이 있다. `message` 상태와 입력 필드를 바인딩하여 사용자가 메시지를 입력할 수 있게 하고, 전송 버튼을 누르면 `sendJsonMessage` 함수를 호출하여 서버로 메시지를 전송한다.
    

이렇게 구현된 `Server` 컴포넌트를 사용하면, 사용자는 입력 폼을 통해 메시지를 입력하고 전송 버튼을 클릭하여 다른 사용자들과 채팅할 수 있다. 새로운 메시지가 도착할 때마다 화면에 추가되어 모든 사용자들이 실시간으로 채팅을 할 수 있다.


시크릿 모드로 같은 포트에 접속 했을 때 채팅처럼 구현이된다.

![](https://i.imgur.com/sA4bOYW.png)



## Build : Server Page Templating


템플릿에는 다음과 같은 섹션이 포함된다:

1. **서버 섹션**: 이 섹션은 서버의 이름과 아이콘을 표시한다. 이 서버에 대한 링크가 있으며 현재 사용자가 접속한 서버를 보여준다.
    
2. **채널 섹션**: 이 섹션은 각 서버에 대해 여러 채팅 방을 표시한다. 각 채널을 클릭하여 해당 채널의 메시지를 볼 수 있다.
    
3. **환영 화면**: 사용자가 채널을 선택하지 않은 경우 기본적으로 표시되는 화면이다. 기본 채널을 설정하는 것도 가능하다.
    

`MessagingInterface` 컴포넌트를 만들고, `Server` 컴포넌트를 업데이트하여 `MessagingInterface` 컴포넌트를 포함하도록 한다. 그리고 `ServerChannels` 컴포넌트를 만들어 채널 목록을 표시하도록 한다.      
이렇게 구현한 다음, 사용자의 선택에 따라 실제 서버와 채널 데이터를 가져와서 표시할 수 있다.     

 `MessagingInterface` 컴포넌트를 `Server` 컴포넌트에 추가하고 `ServerChannels` 컴포넌트를 생성하여 채널 목록을 표시하도록 한다.     

먼저, `MessagingInterface` 컴포넌트를 만들고 `Server` 컴포넌트에서 `MessagingInterface` 컴포넌트를 포함시킨다. 그리고 `ServerChannels` 컴포넌트를 생성하여 채널 목록을 표시한다.     

데이터베이스에서 서버와 채널 데이터를 가져올 때, 현재 사용자가 접속한 서버를 파악하기 위해 URL에서 서버 ID를 추출해야 한다. 이를 위해 라우터의 경로를 수정하여 `/server/:serverId`와 같은 형식으로 파라미터를 전달할 수 있도록 한다.      

또한, 채널 선택 시 해당 채널의 ID를 추출하여 해당 채널의 채팅에 접속할 수 있도록 구현한다. 이러한 작업을 통해 서버와 채널 데이터를 효율적으로 관리하고, 재사용 가능한 컴포넌트를 만들어 전체 애플리케이션을 보다 구조화된 형태로 개발할 수 있다.      

현재 코드에서는 서버 페이지(`Server`)가 여러 개의 하위 컴포넌트들을 가지고 있다. 이 컴포넌트들이 모두 서버 데이터를 필요로 하기 때문에, 여러 번 같은 데이터를 불러오는 상황을 피하고자 한다. 이러한 중복을 줄이기 위해, 상위 컴포넌트인 `Server` 컴포넌트에서 데이터를 가져와 하위 컴포넌트들로 전달하는 방식으로 변경해야한다.     

`Server` 컴포넌트를 보다 강화시켜서, 서버 페이지의 주요 진입점이 되도록 만들어야 한다. 또한, 로딩 상태를 표시하는 `isLoading` 변수도 활용하면 된다. 이를 위해 `isLoading`을 import하고, 서버 정보와 채널 정보를 함께 가져오기 위해 `fetchData` 함수를 이용한다.     

서버 데이터와 채널 데이터를 가져올 때, 해당 데이터를 필요로 하는 하위 컴포넌트들에게 전달하는 작업을 한다. 이를 위해 서버 페이지(`Server`) 컴포넌트에서 서버 정보를 가져오고, 그 정보를 하위 컴포넌트들로 전달한다. 이를 통해 코드를 재사용하고, 애플리케이션을 보다 구조적으로 개발할 수 있다.      

1. `app/types` 폴더를 생성하여 타입 정의 파일들을 모아두는 방식으로 변경. 이렇게 하면 중복된 타입 정의를 방지하고 코드를 보다 구조적으로 관리할 수 있다.
    
2. `Server` 컴포넌트를 강화하여, 서버 페이지의 주요 진입점으로 만든다. `useEffect` 훅을 이용하여 서버 정보를 가져오는 `fetchData` 함수를 호출하도록 설정하고, 가져온 데이터를 하위 컴포넌트들로 전달한다.
    
3. 서버 페이지에 접근할 때, 잘못된 서버 ID나 채널 ID를 입력했을 경우, 홈 페이지로 리디렉션하도록 설정한다.
    
4. `UserServers` 컴포넌트에 데이터를 전달하기 위해, `data`와 `open` props를 추가하고, 이를 이용하여 서버 정보를 렌더링하도록 변경한다.
    
5. 채널 정보를 렌더링하는 부분은 아직 구현하지 않았으며, 이후에 채널 정보를 추가로 구현한다.
    

이로써 `Server` 컴포넌트가 주요한 역할을 하게 된다. 다른 하위 컴포넌트들은 이 컴포넌트로부터 전달받은 데이터를 활용하여 서버 정보를 렌더링하게 된다. 추가적으로 채널 정보를 렌더링하는 기능을 구현한다.

### server.d.ts
```typescript
export interface Server {
    id: number;
    name: string;
    server: string;
    description: string;
    icon: string;
    category: string;
    channel_server: {
      id: number;
      name: string;
      server: number;
      topic: string;
      owner: number;
    }[];
}
```

### App.tsx
```typescript
import Home from "./pages/Home"
import Server from "./pages/Server"
import Explore from "./pages/Explore";
import { createBrowserRouter, createRoutesFromElements, Route, RouterProvider } from "react-router-dom"
import ToggleColorMode from "./components/ToggleColorMode";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route>
      <Route path="/" element={<Home/>} />
      <Route path="/server" element={<Server/>} />
      <Route path="/server/:serverId/:channelId?" element={<Server />} />
      <Route path="/explore/:categoryName" element={<Explore />} />
    </Route>
  )
```

위 코드는 React 애플리케이션에서 라우팅을 설정하는 부분이다. `react-router-dom` 라이브러리를 사용하여 라우팅을 처리한다. 

1. `Home`, `Server`, `Explore` 컴포넌트를 import합니다. 각각은 애플리케이션의 다른 페이지를 나타낸다.
    
2. `createBrowserRouter` 함수를 사용하여 브라우저 라우터를 생성한다. 이것은 브라우저의 주소창의 URL을 기반으로 라우팅을 처리한다.
    
3. `createRoutesFromElements` 함수를 사용하여 `Route` 컴포넌트를 구성한다. 이 함수는 JSX 엘리먼트로부터 `Route` 컴포넌트들을 생성한다.
    
4. `Route` 컴포넌트들은 URL 경로와 매칭되는 페이지 컴포넌트를 설정한다. `element` 속성에 해당 페이지 컴포넌트를 JSX로 전달한다.
    
5. `/` 경로는 `Home` 컴포넌트와 매칭되어 홈 페이지를 렌더링한다.
    
6. `/server` 경로는 `Server` 컴포넌트와 매칭되어 서버 페이지를 렌더링한다.
    
7. `/server/:serverId/:channelId?` 경로는 `Server` 컴포넌트와 매칭됩니다. 여기서 `:serverId`와 `:channelId`는 동적 라우트 매개변수다. 즉, 실제 서버 ID와 채널 ID를 URL에 넣어 해당 정보를 `Server` 컴포넌트로 전달한다.
    
8. `/explore/:categoryName` 경로는 `Explore` 컴포넌트와 매칭되어 탐색 페이지를 렌더링한다. 이때 `:categoryName`은 동적 라우트 매개변수로서 탐색 카테고리 이름을 URL에 넣어 해당 정보를 `Explore` 컴포넌트로 전달한다.
    
9. 설정한 라우트들을 `Route` 컴포넌트로 감싸주어 최상위 라우트를 생성한다.
    
10. 라우터 컴포넌트(`router`)를 `RouterProvider` 컴포넌트로 감싸서 애플리케이션에 라우터를 제공한다.
    

위의 코드는 React Router의 라우팅을 설정하는 부분으로, 페이지에 따라 다른 컴포넌트를 렌더링하고, 동적 라우트 매개변수를 이용하여 URL로부터 데이터를 추출하여 페이지 컴포넌트로 전달하는 기능을 구현한다.      

### MessageInterface.tsx
```typescript
import { useState } from "react";
import useWebSocket from "react-use-websocket";

const socketUrl = "ws://127.0.0.1:8000/ws/test";

const messageInterface = () => {
  const [newMessage, setNewMessage] = useState<string[]>([]);
  const [message, setMessage] = useState("");

  const { sendJsonMessage } = useWebSocket(socketUrl, {
    onOpen: () => {
      console.log("Connected!");
    },
    onClose: () => {
      console.log("Closed!");
    },
    onError: () => {
      console.log("Error!");
    },
    onMessage: (msg) => {
      const data = JSON.parse(msg.data);
      setNewMessage((prev_msg) => [...prev_msg, data.new_message]);
    },
  });

  return (
    <div>
      {newMessage.map((msg, index) => {
        return(
          <div key={index}>
            <p>{msg}</p>
          </div>
        );
      })}
      <form>
        <label>
          Enter Message:
          <input
            type="text"
            value={message}
            onChange={(e) => setMessage(e.target.value)}
          />

        </label>
      </form>
      <button onClick={() => {
          sendJsonMessage({type: "message", message});
        }}
      >
        Send Message
      </button>
    </div>
  );
};
export default messageInterface;
```

위 코드는 React 컴포넌트인 `messageInterface`를 정의하는 부분이다. 이 컴포넌트는 웹 소켓을 사용하여 메시지를 주고받을 수 있도록 구현되어 있다.

1. `useState`를 사용하여 상태를 정의한다.
    
    - `newMessage`: 새로운 메시지를 저장하는 문자열 배열이다.
    - `message`: 사용자가 입력하는 메시지를 저장하는 문자열이다.
2. `useWebSocket` 훅을 사용하여 웹 소켓 연결을 설정한다. `socketUrl` 변수에 정의된 주소로 웹 소켓 연결을 시도한다.
    
    - `onOpen`: 웹 소켓 연결이 성공하면 호출되는 콜백 함수다.
    - `onClose`: 웹 소켓 연결이 닫히면 호출되는 콜백 함수다.
    - `onError`: 웹 소켓 연결에서 오류가 발생하면 호출되는 콜백 함수다.
    - `onMessage`: 웹 소켓으로부터 메시지를 받으면 호출되는 콜백 함수다. 메시지를 파싱하여 `newMessage` 상태를 업데이트한다.
3. 컴포넌트가 렌더링되면, `newMessage` 상태를 사용하여 받은 메시지를 화면에 출력한다.
    
4. 사용자가 메시지를 입력하는 폼을 만들어 `message` 상태를 업데이트할 수 있도록 한다.
    
5. "Send Message" 버튼을 클릭하면, `sendJsonMessage` 함수를 사용하여 웹 소켓을 통해 JSON 형태의 메시지를 서버로 전송한다. 이때 메시지의 타입과 내용을 JSON 형태로 보낸다.
    

이렇게 작성된 `messageInterface` 컴포넌트는 웹 소켓을 통해 메시지를 주고받을 수 있는 간단한 인터페이스를 제공한다. 사용자가 입력한 메시지를 서버로 전송하고, 서버로부터 온 메시지를 화면에 출력하는 기능이 포함되어 있다.      


### UserServers.tsx
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


interface ServerChannelsProps {
    data:Server[];
}

type Props = {
    open: boolean;
};


const UserServers: React.FC<Props & ServerChannelsProps> = ({ open, data }) => {


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
            Servers
        </Typography>
    </Box>
    <List>
        {data.map((item) => (
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
                                    variant="body2"
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


export default UserServers;
```

위 코드는 `UserServers`라는 React 컴포넌트를 정의하는 부분이다. 이 컴포넌트는 사용자가 속한 서버 목록을 보여주는 기능을 가지고 있다.

컴포넌트에서 사용되는 주요 요소들:

1. `List`, `ListItem`, `ListItemButton`, `ListItemIcon`, `ListItemText`, `Box`, `Typography` : Material-UI 라이브러리에서 제공하는 UI 컴포넌트들이다.
    
2. `ListItemAvatar`, `Avatar` : Material-UI에서 제공하는 아바타(Avatar) 관련 컴포넌트들이다.
    
3. `MEDIA_URL` : 외부 파일 또는 이미지의 URL 주소를 저장하는 상수다.
    
4. `Link` : react-router-dom에서 제공하는 링크 컴포넌트로, 페이지 이동을 처리한다.
    
5. `Server` : `ServerChannelsProps`에서 사용하는 서버 데이터의 타입을 정의한 인터페이스다.
    
6. `ServerChannelsProps` : `data` prop으로 받아오는 서버 데이터의 타입을 정의한 인터페이스다.
    
7. `Props` : `open` prop으로 받아오는 불리언 타입의 `Props`다.
    
8. `UserServers` : `Props`와 `ServerChannelsProps`를 모두 활용하는 함수형 컴포넌트다. `open` prop과 `data` prop을 받아와 사용한다.
    

컴포넌트의 동작:

- `open` prop은 사용자가 서버 목록을 열었는지 닫았는지를 나타내는 불리언 값이다.
    
- `data` prop은 `Server` 타입의 배열로, 사용자가 속한 서버들의 정보가 들어 있다.
    
- 컴포넌트가 렌더링되면, 서버 목록의 제목을 포함한 `Box` 컴포넌트가 먼저 보여진다.
    
- 이후 `List` 컴포넌트가 사용자가 속한 서버들의 정보를 매핑하여 목록 형태로 보여준다. 각 서버 정보는 `ListItem`과 `ListItemButton`을 사용하여 클릭 가능한 목록으로 구성되어 있다.
    
- `Avatar` 컴포넌트를 사용하여 각 서버의 아이콘을 보여준다.
    
- `ListItemText` 컴포넌트를 사용하여 각 서버의 이름과 카테고리를 보여준다. 이름은 글꼴 굵기가 높은 본문(`Typography`)으로, 카테고리는 글꼴 굵기가 중간 정도인 본문으로 표시된다.
    
- `Link` 컴포넌트를 사용하여 각 서버를 클릭하면 해당 서버의 상세 페이지로 이동한다.
    

컴포넌트를 통해 사용자가 속한 서버 목록을 시각적으로 보여주고, 각 서버를 클릭하여 상세 페이지로 이동할 수 있도록 구현되어 있다.

### ServerChannels.tsx
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
import { MEDIA_URL  } from "../../config";
import { Link } from "react-router-dom";

interface Category {
    id : number;
    name : string;
    description : string;
    icon: string;
}

const ServerChannels = () => {
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

export default ServerChannels;
```

위 코드는 `ServerChannels`라는 React 컴포넌트를 정의하는 부분이다. 이 컴포넌트는 서버 카테고리 목록을 보여주는 기능을 가지고 있다.

컴포넌트에서 사용되는 주요 요소들:

1. `List`, `ListItem`, `ListItemButton`, `ListItemIcon`, `ListItemText`, `Box`, `useTheme`, `Typography` : Material-UI 라이브러리에서 제공하는 UI 컴포넌트들이다.
    
2. `useCrud` : 커스텀 훅으로, 서버와 상호작용하기 위한 CRUD(create, read, update, delete) 기능을 제공한다. `useCrud` 훅을 사용하여 서버 카테고리 데이터를 가져온다.
    
3. `useEffect` : React의 훅으로, 컴포넌트가 렌더링 된 후에 비동기로 실행되는 코드를 작성하기 위해 사용된다.
    
4. `ListItemAvatar`, `img` : Material-UI에서 제공하는 아바타(Avatar) 관련 컴포넌트들이다.
    
5. `MEDIA_URL` : 외부 파일 또는 이미지의 URL 주소를 저장하는 상수다.
    
6. `Category` : 서버 카테고리 데이터의 타입을 정의한 인터페이스다.
    

컴포넌트의 동작:

- `useCrud` 훅을 사용하여 서버 카테고리 데이터를 가져온다. `dataCRUD` 변수에 데이터, `error` 변수에 오류 정보, `isLoading` 변수에 로딩 상태를 저장한다.
    
- 컴포넌트가 렌더링되면, "Explore"라는 제목을 가진 `Box` 컴포넌트가 상단에 고정되어 보여진다.
    
- `List` 컴포넌트를 사용하여 서버 카테고리 목록을 보여준다. `dataCRUD` 배열을 매핑하여 각 카테고리 정보를 목록 형태로 보여준다.
    
- 각 카테고리 정보는 `ListItem`, `ListItemButton`, `ListItemIcon`, `ListItemText`를 사용하여 클릭 가능한 목록으로 구성되어 있다.
    
- `img` 컴포넌트를 사용하여 각 카테고리의 아이콘을 보여준다. `isDarkMode` 변수를 사용하여 다크 모드인 경우 아이콘의 필터를 변경하여 이미지를 반전시킨다.
    
- `Typography` 컴포넌트를 사용하여 각 카테고리의 이름을 보여준다.
    
- `Link` 컴포넌트를 사용하여 각 카테고리를 클릭하면 해당 카테고리의 상세 페이지로 이동한다.
    

컴포넌트를 통해 서버 카테고리 목록을 시각적으로 보여주고, 각 카테고리를 클릭하여 상세 페이지로 이동할 수 있도록 구현되어 있다.

### Server.tsx
```typescript
import { Box, CssBaseline } from "@mui/material";

import PrimaryAppBar from "./templates/PrimaryAppBar";

import PrimaryDraw from "./templates/PrimaryDraw";

import SecondaryDraw from "./templates/SecondaryDraw";

import Main from "./templates/Main"

import MessageInterface from "../components/Main/MessageInterface";

import ServerChannels from "../components/SecondaryDraw/ServerChannels";

import UserServers from "../components/PrimaryDraw/UserServers";

import { useNavigate, useParams } from "react-router-dom";

import useCrud from "../hooks/useCrud";

import { Server } from "../@types/server.d";

import { useEffect } from "react";

  

const Server = () => {

  const navigate = useNavigate()

  const { serverId, channelId } = useParams();

  const { dataCRUD, error, isLoading, fetchData } = useCrud<Server>(

    [],

    `/server/select/?by_serverid=${serverId}`

  )

  if (error !== null && error.message === "400") {

    navigate("/");

    return null;

  }

  

  useEffect(() => {

    fetchData();

  },[]);


  return(

      <Box sx={{ display: "flex" }}>

          <CssBaseline />

          <PrimaryAppBar />

          <PrimaryDraw>

            <UserServers open={false} data={dataCRUD} />

          </PrimaryDraw>

          <SecondaryDraw>

            <ServerChannels/>

          </SecondaryDraw>

          <Main>

            <MessageInterface/>

          </Main>

      </Box>

  );

};

export default Server;
```

위 코드는 `Server`라는 React 컴포넌트를 정의하는 부분다. 이 컴포넌트는 서버 페이지를 구성하는데 사용된다.

컴포넌트에서 사용되는 주요 요소들:

1. `Box`, `CssBaseline` : Material-UI에서 제공하는 UI 컴포넌트다.
    
2. `PrimaryAppBar`, `PrimaryDraw`, `SecondaryDraw`, `Main`, `MessageInterface`, `ServerChannels`, `UserServers` : 다른 컴포넌트들을 임포트하여 사용한다.
    
3. `useNavigate`, `useParams` : React Router의 훅으로, 네비게이션과 URL 파라미터를 다루기 위해 사용된다.
    
4. `useCrud` : 커스텀 훅으로, 서버와 상호작용하기 위한 CRUD(create, read, update, delete) 기능을 제공한다. `useCrud` 훅을 사용하여 서버 정보를 가져온다.
    
5. `Server` : `@types/server.d`에서 정의된 `Server` 타입을 참조한다.
    

컴포넌트의 동작:

- `useParams` 훅을 사용하여 현재 URL에서 `serverId`와 `channelId`를 가져온다.
    
- `useCrud` 훅을 사용하여 서버 정보를 가져옵니다. `dataCRUD` 변수에 서버 정보가 저장된다. 만약 오류가 발생하면, 오류 메시지가 400이라면 홈 페이지(`/`)로 리다이렉트한다.
    
- `useEffect` 훅을 사용하여 컴포넌트가 렌더링 된 후에 서버 정보를 가져오도록 한다.
    
- `Box` 컴포넌트를 사용하여 여러 컴포넌트를 가로로 나열한다.
    
- `PrimaryAppBar` 컴포넌트를 상단에 배치하여 주요 앱 바를 보여준다.
    
- `PrimaryDraw` 컴포넌트를 사용하여 서버 목록을 보여준다. `UserServers` 컴포넌트를 이용하여 사용자의 서버 목록을 보여준다.
    
- `SecondaryDraw` 컴포넌트를 사용하여 서버 채널 목록을 보여준다. `ServerChannels` 컴포넌트를 이용하여 서버 채널 목록을 보여준다.
    
- `Main` 컴포넌트를 사용하여 메시지 인터페이스를 보여준다. `MessageInterface` 컴포넌트를 이용하여 메시지 인터페이스를 보여준다.
    

컴포넌트를 통해 서버 정보를 가져와서 서버 페이지를 구성하고, 서버와 서버 채널 목록, 메시지 인터페이스를 보여주는 기능을 구현하고 있다.

## Build : Implementing Server Channels 


채널 필터링을 위해서는 일단 `UserServers` 컴포넌트와 유사한 방식으로 데이터를 전달해야 한다. `data`를 `ServerChannels` 컴포넌트로 전달한다.      

다음으로 인터페이스를 만들어야 한다. 이전에 사용자 서버 컴포넌트에서 복사해서 더 이상 카테고리를 사용하지 않으므로 관련 코드를 삭제하고, `ServerChannelsProps` 인터페이스를 추가한다. 또한, `useCrud` 훅으로 데이터를 가져오는 작업을 더 이상 사용하지 않으므로 삭제한다. `useEffect` 훅도 필요없으므로 삭제. 이미 서버 정보를 가져왔기 때문에 그냥 사용하면 된다.

서버 채널 컴포넌트에 `ServerChannelsProps`를 인자로 받도록 설정하고, `dataCRUD`를 `data` 변수로 변경한다.

이렇게 구현하면 서버 채널 목록이 보이는 서버의 페이지를 완성하게 된다. 사용자가 서버 채널을 선택하면 해당 채널로 이동할 수 있다. 또한, 올바르지 않은 채널 이름을 입력하면 다시 서버 페이지로 돌아간다. 

### ServerChannels.tsx
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

  
  

import { Link, useParams } from "react-router-dom";

import { Server } from "../../@types/server";

  
  

interface ServerChannelsProps {

    data:Server[];

}

  

const ServerChannels = (props: ServerChannelsProps) => {

    const { data } = props;

    const theme = useTheme();

    const {serverId} = useParams();

    const server_name = data?.[0]?.name ?? "Server";

  
  

    return (

    <>

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

        <Typography

          variant="body1"

          style={{

            textOverflow: "ellipsis",

            overflow: "hidden",

            whiteSpace: "nowrap",

          }}

        >

          {server_name}

        </Typography>

    </Box>

        <List sx = {{ py: 0}}>

            {data.flatMap((obj) =>

                obj.channel_server.map((item) =>

                (

                    <ListItem

                    disablePadding

                    key = {item.id}

                    sx = {{ display: "block ", maxHeight:"40px"}}

                    dense={true}

                    >

                    <Link

                        to={`/server/${serverId}/${item.id}`}

                        style={{

                            textDecoration: "none",

                            color: "inherit",

                        }}    

                    >

                    <ListItemButton sx={{ minHeight: 48 }}>

  

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

            ))

            )}

        </List>

    </>

    );

};

  

export default ServerChannels;
```

`ServerChannels` 컴포넌트는 서버 페이지에서 해당 서버의 채널 목록을 보여주는 역할을 한다. 이 컴포넌트는 `ServerChannelsProps` 인터페이스를 통해 서버 정보를 받아온다.

1. 먼저, `useTheme` 훅을 사용하여 테마를 가져온다.
    
2. `useParams` 훅을 사용하여 URL 파라미터에서 `serverId`를 가져온다.
    
3. `data`를 비구조화 할당하여 서버 정보를 가져온다.
    
4. `server_name`은 서버 이름을 저장한다. 데이터가 있을 경우 첫 번째 서버의 이름을, 데이터가 없을 경우 "Server"라는 기본 이름을 보여준다.
    
5. `<Box>` 요소를 사용하여 서버 이름이 보이는 상단 부분을 만든다. 이 부분은 화면 위쪽에 고정되도록 `position: "sticky"` 스타일을 설정한다.
    
6. `<Typography>`을 사용하여 서버 이름을 출력한다. 이름이 너무 길 경우, `textOverflow`, `overflow`, `whiteSpace` 속성을 사용하여 줄임표로 표시하도록 설정한다.
    
7. `<List>` 요소를 사용하여 채널 목록을 만든다.
    
8. `data.flatMap`을 사용하여 모든 서버의 채널 목록을 하나의 배열로 평탄화한다.
    
9. `obj.channel_server.map`을 사용하여 각 서버의 채널들을 `<ListItem>`으로 매핑한다.
    
10. `<Link>`를 사용하여 채널을 클릭하면 해당 채널로 이동할 수 있도록 설정한다. 링크는 `/server/${serverId}/${item.id}`로 구성된다. `${serverId}`와 `${item.id}`는 URL 파라미터에서 가져온 값이다.
    
11. 채널 이름을 `<ListItemText>` 안에서 출력한다.
    

이렇게 구현된 `ServerChannels` 컴포넌트는 서버 페이지에서 해당 서버의 채널 목록을 출력하고, 채널을 클릭하면 해당 채널로 이동할 수 있다.

### Server.tsx

```typescript
import { Box, CssBaseline } from "@mui/material";

import PrimaryAppBar from "./templates/PrimaryAppBar";

import PrimaryDraw from "./templates/PrimaryDraw";

import SecondaryDraw from "./templates/SecondaryDraw";

import Main from "./templates/Main"

import MessageInterface from "../components/Main/MessageInterface";

import ServerChannels from "../components/SecondaryDraw/ServerChannels";

import UserServers from "../components/PrimaryDraw/UserServers";

import { useNavigate, useParams } from "react-router-dom";

import useCrud from "../hooks/useCrud";

import { Server } from "../@types/server.d";

import { useEffect } from "react";

  

const Server = () => {

  const navigate = useNavigate()

  const { serverId, channelId } = useParams();

  const { dataCRUD, error, isLoading, fetchData } = useCrud<Server>(

    [],

    `/server/select/?by_serverid=${serverId}`

  )

  if (error !== null && error.message === "400") {

    navigate("/");

    return null;

  }

  

  useEffect(() => {

    fetchData();

  },[]);

  

  // Check if the channelId is valid by searching for it in the data fetched from the API

  const isChannel = (): Boolean => {

    if (!channelId) {

      return true;

    }

  

    return dataCRUD.some((server) =>

      server.channel_server.some(

        (channel) => channel.id === parseInt(channelId)

      )

    );

  };

  

  useEffect(() => {

    if (!isChannel()) {

      navigate(`/server/${serverId}`);

    }

  }, [isChannel, channelId]);

  

  return(

      <Box sx={{ display: "flex" }}>

          <CssBaseline />

          <PrimaryAppBar />

          <PrimaryDraw>

            <UserServers open={false} data={dataCRUD} />

          </PrimaryDraw>

          <SecondaryDraw>

            <ServerChannels data={dataCRUD}/>

          </SecondaryDraw>

          <Main>

            <MessageInterface/>

          </Main>

      </Box>

  );

};

export default Server;
```

이 코드는 서버 페이지를 구성하는 `Server` 컴포넌트다. 이 컴포넌트는 해당 서버의 채널 목록을 보여주고, 채널을 클릭하면 해당 채널로 이동할 수 있도록 구현하고 있다.

1. `useNavigate` 훅을 사용하여 라우터를 이용하여 페이지 간 이동을 처리한다.
    
2. `useParams` 훅을 사용하여 URL 파라미터에서 `serverId`와 `channelId`를 가져온다.
    
3. `useCrud` 훅을 사용하여 서버 정보를 가져온다. `dataCRUD`는 서버 정보를 담고 있는 배열이며, `fetchData` 함수를 호출하여 서버 정보를 가져온다.
    
4. `error`가 발생하고 `error.message`가 "400"일 경우에는 홈 페이지로 이동시킨다.
    
5. `useEffect` 훅을 사용하여 컴포넌트가 마운트될 때 서버 정보를 가져온다.
    
6. `isChannel` 함수를 정의하여 현재 `channelId`가 유효한지 확인한다. 만약 `channelId`가 없다면 항상 유효하다고 판단한다. 그렇지 않을 경우, `dataCRUD` 배열 안에서 `channelId`와 일치하는 채널을 찾아본다. 만약 유효한 채널이 존재한다면 `true`를 반환하고, 그렇지 않으면 `false`를 반환한다.
    
7. 두 번째 `useEffect` 훅을 사용하여 현재 채널이 유효하지 않을 경우, 서버 페이지로 이동시킨다. 이때 `isChannel`과 `channelId`를 의존성 배열로 지정하여 `channelId`가 변경될 때마다 체크하도록 한다.
    
8. 페이지의 레이아웃을 구성한다. `PrimaryAppBar`는 상단의 앱 바를, `PrimaryDraw`는 왼쪽의 유저 서버 목록을, `SecondaryDraw`는 오른쪽의 채널 목록을, `Main`은 중앙의 메인 컨텐츠를, `MessageInterface`는 채팅 메시지 인터페이스를 각각 담당한다.
    

이렇게 구현된 `Server` 컴포넌트는 서버 페이지를 완성한다. 해당 서버의 채널 목록을 출력하고, 채널을 클릭하면 해당 채널로 이동할 수 있도록 한다. 또한, 잘못된 채널 ID를 입력할 경우 서버 페이지로 리디렉션된다.


![](https://i.imgur.com/5vId5vJ.png)

![](https://i.imgur.com/mbCgAfP.png)

잘못된 서버 아이디여도 원래 채널로 돌아간다.

![](https://i.imgur.com/ttSFjyn.png)

## Build : Switching Chat Rooms (Channels) in a Server

다음 단계는 채널을 연결하여 개별 채널을 선택하면 해당 채널에 대해서 자유롭게 웹소켓을 사용할 수 있도록 하는 것이다. 선택한 채널을 통해 해당 채널의 채팅방에 있는 누구든지와 대화를 시작할 수 있다.     

지금까지 한 일은 능동적인 룸(Active Room)을 만들었습니다. 이것은 채널 레이어를 사용하여 우리의 서비스에 연결한 사용자들을 그룹화하는 것을 의미한다. 이 룸 이름인 "Test Server"에 현재 연결된 모든 사용자들에게 메시지를 방송할 수 있다. 현재 연결된 사용자들의 목록을 룸 이름에 대한 뒷단에 유지하고 있다.     

이제 우리는 채널들, 즉 서버 채널들을 특정 룸(Room)에 연결했다. 사용자들이 특정 룸, 즉 채널과 연결되도록 선택할 수 있게 된다. 이러한 특정 룸(채널)들과 사용자들을 그룹화하여 사용자들이 해당 채널의 채팅방 안에서 대화를 시작할 수 있도록 한다.

`roomName`을 `channelId`로 변경한다. 이것은 각 채널에 연결된 채널 ID가 된다. 우리가 새로운 채널을 뒤에서 데이터베이스에 생성할 때, 채널과 연결된 고유 번호로서의 ID가 있다. 이 ID를 추출하고 채널과 연결된 사용자들을 그룹으로 묶어주기 위해서 채널 ID를 사용한다.      

### consumer.py
```python
from channels.generic.websocket import JsonWebsocketConsumer

from asgiref.sync import async_to_sync

  
  

class WebChatConsumer(JsonWebsocketConsumer):

    def __init__(self, *args, **kwargs):

        super().__init__(*args, **kwargs)

        self.channel_id = None

        self.user = None

    def connect(self):

        self.accept()

        print(self.scope)

        self.channel_id = self.scope["url_route"]["kwargs"]["channelId"]

        async_to_sync(self.channel_layer.group_add)(

            self.channel_id,

            self.channel_name,

        )

  

    def receive_json(self, content):

        async_to_sync(self.channel_layer.group_send)(

            self.channel_id,

            {

                "type": "chat.message",

                "new_message": content["message"],

            }

        )

  

    def chat_message(self, event):

        self.send_json(event)

  

    def disconnect(self, close_code):

        pass
```

만약 이 범위에 있는 다른 정보들이 무엇인지 궁금하다면, 방금 말한대로 `self.scope`를 프린트하여 확인할 수 있다. 여기에서는 라우트(route)를 볼 수 있고 키워드 인수(keyword arguments)에 접근할 수 있다. 그리고 키워드 인수에서 우리가 전달한 `channelId`를 찾을 수 있다. 즉, 우리가 여기서 채널 ID를 추출할 수 있도록 되어 있다.

![](https://i.imgur.com/KHadX1R.png)
좌측과 우측에서 새 사용자를 시뮬레이션하기 위해 다른 채널, Channel one과 Channel two를 선택한다.

이 사이트를 사용하면 이 채널들과 연결된 메시지만 볼 수 있다. 이 메시지들은 서버나 채팅방에 속하지 않은 경우에는 보이지 않는다. 이 경우 사용자들은 서로 다른 채널에 있기 때문이다.

그래서 "1"이라는 메시지를 입력하면, 오른쪽 측면에 있는 사용자는 여전히 Channel two에 있기 때문에 메시지를 보지 못한다. 그리고 왼쪽 측면에서도 Channel one에 있기 때문에 메시지를 볼 수 있다.

이제 "2"라는 메시지를 입력하면, 양 측 모두 Channel one에 있기 때문에 메시지를 주고받을 수 있다.

이렇게 간단한 변경으로 서로 다른 채널(채팅방)로 이동하여 서로 다른 메시지를 보거나 채팅을 할 수 있게 되었다. 다음 단계에서는 이러한 기록, 텍스트 기록, 즉 다른 채널로 이동할 때 해당 채널과 관련된 메시지만 표시할 수 있도록 수정한다. 또한 아직 기록 기능이 없어서 페이지를 새로고침하면 모든 메시지가 사라지는데, 이것도 추가해보자.

### urls.py
```python
urlpatterns = [

    path('admin/', admin.site.urls),

    path('api/docs/schema/', SpectacularAPIView.as_view(), name='schema'),

    path('api/docs/schema/ui/', SpectacularSwaggerView.as_view()),

] + router.urls

  

websocket_urlpatterns = [path("<str:serverId>/<str:channelId>", WebChatConsumer.as_asgi())]

  

if settings.DEBUG:

    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```




### MessageInterface.tsx
```typescript
import { useState } from "react";

import { useParams } from "react-router-dom";

  

import useWebSocket from "react-use-websocket";

  
  
  

const messageInterface = () => {

  const [newMessage, setNewMessage] = useState<string[]>([]);

  const [message, setMessage] = useState("");

  const { serverId, channelId } = useParams();

  const socketUrl = channelId

    ? `ws://127.0.0.1:8000/${serverId}/${channelId}`

    : null ;

  

  const { sendJsonMessage } = useWebSocket(socketUrl, {

    onOpen: () => {

      console.log("Connected!");

    },

    onClose: () => {

      console.log("Closed!");

    },

    onError: () => {

      console.log("Error!");

    },

    onMessage: (msg) => {

      const data = JSON.parse(msg.data);

      setNewMessage((prev_msg) => [...prev_msg, data.new_message]);

    },

  });

  

  return (

    <div>

      {newMessage.map((msg, index) => {

        return(

          <div key={index}>

            <p>{msg}</p>

          </div>

        );

      })}

      <form>

        <label>

          Enter Message:

          <input

            type="text"

            value={message}

            onChange={(e) => setMessage(e.target.value)}

          />

  

        </label>

      </form>

      <button onClick={() => {

          sendJsonMessage({type: "message", message});

        }}

      >

        Send Message

      </button>

    </div>

  );

};

export default messageInterface;
```

이 컴포넌트는 웹소켓을 사용하여 메시지를 주고받을 수 있도록 구현되어 있다.

해당 컴포넌트에서 사용하는 주요 상태 변수들은 다음과 같다:

- `newMessage`: 새로운 메시지를 저장하는 상태 변수로, 새로운 메시지가 도착할 때마다 업데이트된다.
- `message`: 입력 폼에서 사용자가 입력한 메시지를 저장하는 상태 변수다.

컴포넌트는 `useParams`를 사용하여 현재 URL에서 `serverId`와 `channelId`를 추출한다. 그리고 `socketUrl` 변수를 생성하는데, 이는 현재 `channelId`가 존재하는 경우 해당 채널에 대한 웹소켓 URL을 생성하고, 존재하지 않는 경우에는 `null`로 설정한다.

`useWebSocket` 훅을 사용하여 웹소켓을 관리한다. 이 훅은 주어진 URL로 웹소켓을 생성하고, 웹소켓의 상태 변화(예: 연결, 닫힘, 오류 등)와 메시지 수신을 처리하는 콜백 함수를 설정한다.

컴포넌트는 현재까지 수신된 새 메시지들을 `newMessage` 상태 변수를 사용하여 화면에 출력한다. 새 메시지가 도착할 때마다 `setNewMessage` 함수를 사용하여 이전 메시지들과 함께 새로운 메시지를 추가한다.

아래는 컴포넌트의 렌더링 부분이다:

1. `newMessage` 배열을 `map` 함수로 순회하여 화면에 출력한다. 각 메시지는 `<p>` 태그로 감싸져 출력된다.
    
2. 사용자가 메시지를 입력할 수 있는 폼을 생성한다. 입력된 메시지는 `message` 상태 변수에 저장된다.
    
3. "Send Message" 버튼을 클릭하면 `sendJsonMessage` 함수가 호출되며, 웹소켓을 통해 입력된 메시지가 전송된다.
    

컴포넌트는 새로운 메시지를 수신하거나 사용자가 메시지를 보낼 때마다 화면이 업데이트되고, 사용자가 입력한 메시지는 다른 사용자들에게 웹소켓을 통해 전송된다. 이를 통해 사용자들은 서로 채널(채팅방)에 연결되어 있는 동안 메시지를 주고받을 수 있다.

## Build : Implementing Channel Message History


현재 구현 중인 채팅 앱에 채널 메시지 히스토리가 저장이 안된다.      

그래서 이제 데이터베이스에 테이블을 만들어야 한다. 두 가지 엔티티(테이블)이 필요합니다. 먼저 'Conversation' 테이블을 만들고, 이 테이블에는 각 채널과 관련된 대화를 기록한다. 그리고 'Message' 테이블을 만들어 이 테이블에는 각 대화에 속하는 메시지들을 기록한다.     

데이터베이스 모델로 들어가서 'Conversation' 테이블과 'Message' 테이블을 정의합시다. 또한 우리는 'Message' 테이블에서 'Conversation' 테이블과의 관계를 설정해야 합니다.

models.py
```python
from django.contrib.auth import get_user_model

from django.db import models

  
  

class Conversation(models.Model):

    channel_id = models.CharField(max_length=255)

    created_at = models.DateTimeField(auto_now_add=True)

  
  

class Message(models.Model):

    conversation = models.ForeignKey(

        Conversation,

        on_delete=models.CASCADE,

        related_name="message"

    )

    sender = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)

    content = models.TextField()

    timestamp = models.DateTimeField(auto_now_add=True)
```

Conversation 테이블은 채널 ID와 대화가 시작된 시간을 저장하며, Message 테이블은 대화에 속하는 메시지의 내용과 발송자, 그리고 메시지가 생성된 시간을 저장한다.      


이제 웹소켓 연결이 이루어지는 곳인 consumer 코드를 수정해야 한다. 여기에서 메시지를 수신하고 데이터베이스에 저장하는 로직을 구현해야 합니다.

이것은 이전에 설명드린 웹소켓 연결 로직과 함께 구현해야 한다. 

consumer.py
```python
from channels.generic.websocket import JsonWebsocketConsumer

from asgiref.sync import async_to_sync

from .models import Conversation, Message

from django.contrib.auth import get_user_model

  

User = get_user_model()

  

class WebChatConsumer(JsonWebsocketConsumer):

    def __init__(self, *args, **kwargs):

        super().__init__(*args, **kwargs)

        self.channel_id = None

        self.user = None

    def connect(self):

        self.accept()

        print(self.scope)

        self.channel_id = self.scope["url_route"]["kwargs"]["channelId"]

        self.user = User.objects.get(id=1)

        async_to_sync(self.channel_layer.group_add)(

            self.channel_id,

            self.channel_name,

        )

  

    def receive_json(self, content):

        channel_id = self.channel_id

        sender = self.user

        message = content["message"]

  

        conversation, created = Conversation.objects.get_or_create(channel_id=channel_id)

  

        new_message = Message.objects.create(conversation=conversation, sender=sender, content=message)

        async_to_sync(self.channel_layer.group_send)(

            self.channel_id,

            {

                "type": "chat.message",

                "new_message": {

                    "id": new_message.id,

                    "sender": new_message.sender.username,

                    "content": new_message.content,

                    "timestamp": new_message.timestamp.isoformat(),

                },

            },

        )

  
  

    def chat_message(self, event):

        self.send_json(event)

  

    def disconnect(self, close_code):

        async_to_sync(self.channel_layer.group_discard)(self.channel_id, self.channel_name)

        super().disconnect(close_code)
```

이렇게 하면 채팅 메시지가 데이터베이스에 저장되어, 페이지를 새로고침해도 메시지가 보존될 것입니다. 또한 각 채널에서 보낸 메시지는 해당 채널에서만 볼 수 있게 된다.

1. 웹 소켓 연결 시 메시지 로드: connect() 메서드에서 채널 ID와 사용자 정보를 설정한 후, async_to_sync(self.channel_layer.group_add)를 사용하여 해당 채널의 그룹에 연결한다. 이렇게 하면 해당 채널의 모든 클라이언트들이 같은 그룹에 속하게 된다.
    
2. 메시지 수신 시 채팅 기록 저장: receive_json() 메서드에서 새로운 메시지를 수신하면, Conversation과 Message 모델을 사용하여 데이터베이스에 채팅 기록을 저장한다. 이때, 채널 ID를 기준으로 Conversation을 가져오고, 해당 Conversation에 새로운 Message를 생성하여 저장한다.
    
3. 그룹 메시지 전송: 그리고 그룹에 속한 모든 클라이언트들에게 메시지를 전송하도록 group_send()를 사용한다. 이때, "type": "chat.message"와 함께 새로운 메시지 정보를 함께 보내서 클라이언트 측에서 메시지를 화면에 표시할 수 있도록 한다.
    
4. chat_message() 메서드: chat_message() 메서드는 group_send()에서 전송한 메시지를 클라이언트로 전송하는 역할을 한다. 이를 통해 모든 클라이언트가 동일한 메시지를 받아서 화면에 표시할 수 있게 된다.
    

따라서, 메시지가 새로고침되어도 이전에 저장한 채팅 기록이 남아있게 된다. 웹 소켓 연결을 통해 서버와 클라이언트가 실시간으로 데이터를 주고받으므로, 새로고침해도 이전에 받은 메시지들이 그대로 남아 있게 된다. 이를 통해 채팅방에서 실시간으로 새로운 메시지가 도착할 때마다 화면에 즉시 반영할 수 있게 된다.     


현재 사용자 로그인 시스템은 구현되지 않은 상태이므로, 임시적으로 고정된 사용자를 이용하여 메시지를 저장하는 것으로 했다.      

우선, 데이터베이스 모델에서 Conversation과 Message 테이블을 정의하고, 해당 테이블들 간의 관계를 설정했다. 그 다음, WebSocket 연결 로직인 consumer 코드에서 메시지를 수신하고 데이터베이스에 저장하는 부분을 구현해야 한다.

데이터베이스에 저장할 메시지를 먼저 준비하고, Conversation 테이블에는 채널 ID와 생성 시간을, Message 테이블에는 발송자, 내용, 생성 시간을 저장한다. 그리고 새로운 메시지가 생성될 때 해당 정보를 웹소켓을 통해 프론트엔드로 전달하고, 메시지가 화면에 출력되도록 하려고 했다.

일단 로그인 시스템이 구현되지 않았으므로, 임시로 admin 사용자를 이용하여 메시지를 저장했다.     

### 요약 

1. 데이터베이스 모델에 Conversation과 Message 테이블을 추가하고, 각 테이블 간의 관계를 설정한다.
2. 웹소켓 연결 로직인 consumer 코드를 수정하여 메시지를 수신하고, Conversation과 Message 테이블에 저장하는 로직을 구현한다.
3. 로그인 시스템이 아직 구현되지 않았으므로 임시로 고정된 사용자를 이용하여 메시지를 저장한다.
4. 웹소켓 연결을 통해 프론트엔드로 메시지를 전달하고, 프론트엔드에서는 이를 데이터베이스에 저장하고 화면에 출력한다.
5. 처음에는 Conversation을 get_or_create 메서드로 생성하여 Conversation 인스턴스를 반환하도록 수정한다.
6. 데이터베이스에 메시지가 정상적으로 저장되는 것을 확인한다.

다음으로는 프론트엔드에서 채팅 메시지를 보여주는 인터페이스를 수정하고, 메시지 히스토리를 표시할 수 있도록 처리하기 위해 Message 인터페이스의 코드를 수정하고, 메시지의 발신자, 타임스탬프, 내용을 화면에 출력하도록 변경해야 한다. 이렇게 하면 채팅 메시지의 기록이 적절히 표시되어 사용자들이 채팅방의 이전 메시지를 확인할 수 있게 된다.

### MessageInterface.tsx

```typescript
import { useState } from "react";

import { useParams } from "react-router-dom";

import useWebSocket from "react-use-websocket";

import useCrud from "../../hooks/useCrud";

import { Server } from "../../@types/server.d";

  

interface Message {

  sender: string;

  content: string;

  timestamp: string;

}

  

const messageInterface = () => {

  const [newMessage, setNewMessage] = useState<Message[]>([]);

  const [message, setMessage] = useState("");

  const { serverId, channelId } = useParams();

  const { fetchData } = useCrud<Server>(

    [],

    `/messages/?channel_id=${channelId}`

  );

  const socketUrl = channelId

    ? `ws://127.0.0.1:8000/${serverId}/${channelId}`

    : null ;

  

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

    onClose: () => {

      console.log("Closed!");

    },

    onError: () => {

      console.log("Error!");

    },

    onMessage: (msg) => {

      const data = JSON.parse(msg.data);

      setNewMessage((prev_msg) => [...prev_msg, data.new_message]);

    },

  });

  

  return (

    <div>

      {newMessage.map((msg: Message, index: number) => {

        return(

          <div key={index}>

            <p>{msg.sender}</p>

            <p>{msg.content}</p>

          </div>

        );

      })}

      <form>

        <label>

          Enter Message:

          <input

            type="text"

            value={message}

            onChange={(e) => setMessage(e.target.value)}

          />

  

        </label>

      </form>

      <button onClick={() => {

          sendJsonMessage({type: "message", message});

        }}

      >

        Send Message

      </button>

    </div>

  );

};

export default messageInterface;
```

메시지 인터페이스를 구현하는 부분이다. 메시지를 보내고 받을 수 있는 채팅 인터페이스를 만들어낸다.

1. 컴포넌트 설정과 상태 관리: useState 훅을 사용하여 newMessage와 message라는 두 개의 상태를 설정한다. newMessage는 채팅방에서 보여지는 메시지들의 배열을 저장하고, message는 사용자가 입력한 새로운 메시지를 저장한다.
    
2. useParams 훅: react-router-dom의 useParams 훅을 사용하여 현재 URL에서 serverId와 channelId를 가져온다. 이를 통해 현재 접속한 채팅방의 서버 ID와 채널 ID를 얻을 수 있다.
    
3. useCrud 훅: 사용자 정의 훅인 useCrud를 사용하여 서버로부터 채팅 메시지 데이터를 가져온다. fetchData 함수를 사용하여 "/messages/?channel_id=${channelId}" API 엔드포인트로부터 데이터를 가져온다.
    
4. useWebSocket 훅: react-use-websocket 훅을 사용하여 웹 소켓을 설정한다. socketUrl은 현재 채널에 해당하는 웹 소켓 서버의 주소로 설정된다. 채널이 선택되지 않은 경우, socketUrl은 null이 된다.
    
5. 웹 소켓 이벤트 처리: onOpen, onClose, onError, onMessage 콜백 함수들을 설정하여 웹 소켓 이벤트를 처리한다. onOpen에서 fetchData 함수를 사용하여 서버로부터 채팅 기록을 가져오고, onMessage에서 새로운 메시지가 도착할 때마다 newMessage 상태를 업데이트한다.
    
6. 화면 출력: newMessage 배열을 사용하여 채팅방에 표시될 메시지들을 출력한다. 메시지 내용과 발신자(sender)를 순서대로 표시하고, 아래에는 메시지를 입력할 수 있는 폼과 전송 버튼을 제공한다.
    
7. 메시지 전송: 전송 버튼을 클릭하면 sendJsonMessage 함수를 사용하여 새로운 메시지를 서버로 보낸다. sendJsonMessage에는 메시지 타입과 내용이 포함된다.
    

이렇게 구현된 코드는 WebSocket을 사용하여 실시간으로 채팅 메시지를 주고받을 수 있도록 만들어주는 메시지 인터페이스다.

views.py
```python
from rest_framework import viewsets

from rest_framework.response import Response

  

from .models import Conversation

from .schemas import list_message_docs

from .serializers import MessageSerializer

  
  

class MessageViewSet(viewsets.ViewSet):

    @list_message_docs

    def list(self, request):

        channel_id = request.query_params.get("channel_id")

  

        try:

            conversation = Conversation.objects.get(channel_id=channel_id)

            message = conversation.message.all()

            serializer = MessageSerializer(message, many=True)

            return Response(serializer.data)

        except Conversation.DoesNotExist:

            return Response([])
```

1. MessageViewSet 클래스: MessageViewSet 클래스는 viewsets.ViewSet 클래스를 상속하여 만들어진 Django REST framework의 ViewSet 다. ViewSet은 Django 모델과 상호작용하는 API 뷰를 만들 수 있는 기본 클래스를 제공한다.
    
2. @list_message_docs 데코레이터: `@list_message_docs` 데코레이터는 custom decorator로 보이며, 해당 메서드의 API 문서를 제공한다. API 문서의 구성을 조정하거나 Swagger와 같은 도구에서 API 문서를 생성할 때 유용하게 사용될 수 있다.
    
3. list 메서드: list 메서드는 Django REST framework의 ListAPIView에 해당하는 메서드로, GET 요청을 처리하여 메시지 리스트를 반환한다.
    
4. request.query_params.get("channel_id"): GET 요청으로 전달된 쿼리 파라미터 중 "channel_id"를 가져온다. 이는 채널 ID를 나타낸다.
    
5. try-except 블록: Conversation.objects.get()을 사용하여 주어진 채널 ID를 가진 Conversation 객체를 데이터베이스에서 가져온다. 만약 해당 채널에 대한 Conversation이 없을 경우 `Conversation.DoesNotExist` 예외가 발생한다.
    
6. message.all(): conversation.message.all()을 사용하여 해당 Conversation에 연결된 모든 Message 객체들을 가져온다. `message`는 Conversation 모델에서 related_name으로 정의된 필드다.
    
7. MessageSerializer: MessageSerializer를 사용하여 가져온 Message 객체들을 직렬화한다. 이때 many=True 옵션을 사용하여 여러 개의 Message를 직렬화하도록 지정한다.
    
8. Response(serializer.data): 직렬화된 데이터를 Response 객체로 감싸서 반환한다. 클라이언트는 이 응답 데이터를 JSON 형식으로 받게 된다.
    
9. except 블록: 만약 주어진 채널 ID에 해당하는 Conversation이 데이터베이스에 없는 경우, 빈 리스트를 Response로 반환한다. 이를 통해 클라이언트에게 해당 채널에 대한 메시지가 없음을 알려준다.
    

이렇게 구현된 코드는 API 요청으로부터 "channel_id"를 받아 해당 채널에 대한 메시지들을 가져와서 직렬화하여 JSON 형식으로 반환하는 Django REST framework의 ViewSet 이다.

### 요약

1. 먼저, 프론트엔드에서는 Message 인터페이스에 메시지 데이터를 담을 수 있도록 인터페이스를 구성했다. 발신자(Sender), 내용(Content), 그리고 타임스탬프(Timestamp)를 표시하기 위한 인터페이스를 만들었다.
    
2. 그 다음으로, 데이터베이스에서 메시지를 조회하기 위한 API 엔드포인트를 생성했다. Django의 view set을 활용하여 채팅방의 메시지를 조회하는 기능을 구현했다. 조회한 데이터는 Message Serializer를 통해 직렬화하여 프론트엔드에 반환했다.
    
3. 마지막으로, 프론트엔드에서는 useEffect 훅을 사용하여 채팅방에 들어올 때마다 해당 채팅방의 메시지를 조회하도록 처리하했다. 조회한 데이터를 Message 인터페이스에 맞게 출력하여 이전에 주고받은 메시지들을 채팅방에 표시했다.
    

이렇게 해서 채팅방의 이전 메시지들을 조회하고, 프론트엔드에 표시할 수 있게 했다. 이제 이를 바탕으로 채팅 인터페이스를 더욱 다양하게 디자인하고, 사용자들이 편리하게 채팅을 주고받을 수 있도록 기능을 추가해 나갈 수 있다.

또한 웹 소켓으로 채팅방에 연결할 때 해당 채널과 관련된 이전 메시지들을 데이터베이스에서 가져오도록 설정했다.

1. 서버 쪽에서는 API 엔드포인트를 생성하여 채널에 관련된 모든 메시지를 조회할 수 있도록 구현했다. 이 엔드포인트는 채널의 ID를 파라미터로 받아와 해당 채널과 관련된 메시지들을 반환한다.
    
2. 프론트엔드 쪽에서는 useEffect 훅을 사용하여 웹 소켓으로 채팅방에 연결할 때마다 해당 채널의 메시지를 조회하고, 조회한 메시지들을 Message 인터페이스에 맞게 출력하여 이전 메시지들을 채팅방에 표시하도록 처리한다.
    

이제는 웹 소켓 연결 시 해당 채널의 이전 메시지를 데이터베이스에서 가져와서 채팅방에 표시할 수 있다. Open 함수에서는 async/await를 사용하여 데이터를 가져온 후, 해당 채널의 이전 메시지들을 newMessage 상태로 설정한다. 또한, 데이터를 가져오는 과정에서 발생할 수 있는 오류를 try/except 문으로 처리하여 에러가 발생해도 500 Internal Server Error가 나타나지 않도록 처리한다.

![](https://i.imgur.com/Rat3zBM.png)

이제 웹 소켓을 통해 채팅방에 연결할 때, 해당 채널의 이전 메시지들이 표시되며, 다른 채널로 이동할 때마다 이전 메시지들이 삭제되는 것을 확인할 수 있다.

현재는 메시지를 보낸 사용자의 이름 대신 ID가 표시되고, 서버에서 처음 생성된 채널에는 메시지가 표시되지 않는 문제가 있다. 

## Build : Server Landing Page


현재 애플리케이션은 서버로 이동할 때마다 실제 메시지 인터페이스가 표시된다. 
사용자가 서버로 이동할 때 첫 번째로 볼 내용에 대해 생각해볼 필요가 있다. 이것은 서버의 홈인지, 아니면 채널 안에 있는지를 결정하기 위해서 URL의 서버 ID와 채널 ID에 접근할 수 있어야 한다. 예를 들어, 채널이 존재하지 않는다면 우리는 서버의 홈 페이지에 있다고 판단할 수 있다.     


### MessageInterface.tsx
```typescript
import { useState } from "react";

import { useParams } from "react-router-dom";

import useWebSocket from "react-use-websocket";

import useCrud from "../../hooks/useCrud";

import { Server } from "../../@types/server.d";

import { Box, Typography } from "@mui/material";

  

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

  const [newMessage, setNewMessage] = useState<Message[]>([]);

  const [message, setMessage] = useState("");

  const { serverId, channelId } = useParams();

  const server_name = data?.[0]?.name ?? "Server";

  const { fetchData } = useCrud<Server>(

    [],

    `/messages/?channel_id=${channelId}`

  );

  const socketUrl = channelId

    ? `ws://127.0.0.1:8000/${serverId}/${channelId}`

    : null ;

  

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

    onClose: () => {

      console.log("Closed!");

    },

    onError: () => {

      console.log("Error!");

    },

    onMessage: (msg) => {

      const data = JSON.parse(msg.data);

      setNewMessage((prev_msg) => [...prev_msg, data.new_message]);

    },

  });

  

  return (

    <>

      {channelId == undefined ? (

        <Box

          sx = {{

            overflow: "hidden",

            p: { xs: 0},

            height: `calc(80vh)`,

            display: "flex",

            justifyContent: "center",

            alignItems: "center",

          }}

        >

          <Box

            sx= {{

              textAlign: "center"

            }}

          >

            <Typography

              variant="h4"

              fontWeight={700}

              letterSpacing={"-0.5px"}

              sx = {{

                px:5,

                maxWidth: "600px",

              }}

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

          <div>

            {newMessage.map((msg: Message, index: number) => {

              return(

                <div key={index}>

                  <p>{msg.sender}</p>

                  <p>{msg.content}</p>

                </div>

              );

            })}

            <form>

              <label>

                Enter Message:

                <input

                  type="text"

                  value={message}

                  onChange={(e) => setMessage(e.target.value)}

                />

  

              </label>

            </form>

            <button onClick={() => {

                sendJsonMessage({type: "message", message});

              }}

            >

              Send Message

              </button>

        </div>

      </>

    )}

  </>

);

}

  

export default messageInterface;
```

### Server.tsx
```typescript
          <Main>

            <MessageInterface data={dataCRUD}/> <- 추가

          </Main>
```

1. useState를 사용하여 상태를 관리한다. `newMessage`는 메시지 목록을 저장하고, `message`는 사용자가 입력하는 메시지를 저장한다.
2. `useParams`를 사용하여 URL의 파라미터(serverId, channelId)를 가져온다.
3. `useCrud`를 사용하여 메시지 데이터를 가져온다.
4. `useWebSocket`을 사용하여 WebSocket 연결을 설정하고, 새로운 메시지가 도착할 때마다 화면에 표시한다.
5. `return` 부분에서, channelId이 정의되어 있지 않은 경우 (undefined인 경우) 서버 홈 페이지를 보여준다. 그렇지 않은 경우, 메시지 목록과 메시지 입력 폼을 표시한다.

위 코드는 서버와 채널을 통해 메시지를 주고받을 수 있는 기능을 구현하는 React 컴포넌트다. 컴포넌트는 서버와 채널의 데이터를 받아와 메시지를 화면에 표시하고, 사용자가 메시지를 입력하여 전송할 수 있는 기능을 제공한다. 컴포넌트는 서버 홈 페이지와 채널 메시지 인터페이스를 나누어 보여주며, WebSocket을 사용하여 실시간으로 메시지가 업데이트 된다.

### 옵셔널 체이닝, 널 병합 연산자
이를 사용하여 서버의 이름을 가져오는데, 데이터가 존재하지 않을 때 기본값으로 "Server"라는 문자열을 사용한다.     

옵셔널 체이닝 (Optional Chaining)은 객체 내의 프로퍼티를 안전하게 접근하기 위해 도입된 문법이다. 객체 내의 중첩된 프로퍼티를 참조할 때, 중간에 존재하지 않는 프로퍼티가 있을 경우 `undefined`를 반환하는 대신에 에러를 발생시키지 않고 안전하게 처리할 수 있게 해준다.     

널 병합 연산자 (Nullish Coalescing Operator)는 `null` 또는 `undefined`인 경우에만 기본값을 반환하는 연산자다. 즉, 왼쪽 피연산자가 `null`이거나 `undefined`이면 오른쪽 피연산자로 대체된다.     

1. `data?.[0]?.name`: `data` 객체가 존재하는지 확인한 후, 그 안에 `[0]` 인덱스로 접근하여 그 객체가 존재하는지 확인하고, 다시 그 안의 `name` 프로퍼티가 존재하는지 확인합니다. 만약 중간에 어느 하나라도 존재하지 않는 프로퍼티가 있다면 `undefined`를 반환한다.
2. `?? "Server"`: 앞서 얻은 값이 `undefined`인 경우, 즉 데이터가 존재하지 않을 경우에는 "Server"라는 기본값으로 대체된다.

이를 통해 `server_name` 변수는 데이터가 존재하는 경우 해당 서버의 이름을 가지고 오고, 데이터가 존재하지 않는 경우 "Server"라는 기본값을 가지게 된다. 이렇게 하면 데이터가 없더라도 에러가 발생하지 않고 기본값을 사용하여 안전하게 처리할 수 있다.



## Build : Templating - Dynamic Channel Selection



### MessageInterfaceChannels.tsx
```typescript 
import {
    AppBar,
    Toolbar,
    Box,
    ListItemAvatar,
    Avatar,
    Typography,
    IconButton,
    Drawer,
    useTheme,
    useMediaQuery,
} from "@mui/material";
import { MEDIA_URL } from "../../config";
import { Server } from "../../@types/server";
import { useParams } from "react-router-dom";
import ServerChannels from "../SecondaryDraw/ServerChannels";
import { useEffect, useState } from "react";
import MoreVertIcon from "@mui/icons-material/MoreVert";



interface ServerChannelProps {
    data: Server[];
  }
  

const MessageInterfaceChannels = (props: ServerChannelProps) => {
    const { data } = props;
    const theme = useTheme();
    const { serverId, channelId } = useParams()
    const [sideMenu, setSideMenu] = useState(false);
    const channelName = 
        data
            ?.find((server) => server.id == Number(serverId))
            ?.channel_server?.find((channel) => channel.id === Number(channelId))
            ?.name || "home";
            
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
            <ServerChannels data={data}/>
        </Box>
    );
    return <>
        <AppBar
            sx = {{
                backgroundColor: theme.palette.background.default,
                borderBottom: `1px solid ${theme.palette.divider}`,
            }}
            color = "default"
            position = "sticky"
            elevation = {0}
        >
            <Toolbar 
                variant="dense"
                sx = {{
                    minHeight: theme.primaryAppBar.height,
                    height: theme.primaryAppBar.height,
                    display: "flex",
                    alignItems: "center",
                }}
            >
                <Box
                    sx = {{
                        display: { xs: "block", sm: "none"}
                    }}
                >
                    <ListItemAvatar
                        sx = {{ minWidth: "40px" }}
                    >
                        <Avatar
                            alt="Server Icon"
                            src={`${MEDIA_URL}${data?.[0]?.icon}`}
                            sx = {{ width: 30, height: 30 }}
                        />
                    </ListItemAvatar>
                </Box>
                <Typography noWrap component="div">
                    {channelName}
                </Typography>
                <Box sx={{ flexGrow: 1 }}></Box>
                <Box sx={{ display: { xs: "block", sm: "none" } }}>
                    <IconButton color="inherit" onClick={toggleDrawer(true)} edge="end">
                        <MoreVertIcon />
                    </IconButton>
                </Box>
                <Drawer anchor="left" open={sideMenu} onClose={toggleDrawer(false)}>
                    {list()}
                </Drawer>
            </Toolbar>

        </AppBar>
    </>;
};

export default MessageInterfaceChannels;
```

채널 메뉴를 작은 화면 크기에서도 사용할 수 있도록 개선하는 작업을 해야한다. 현재는 작은 화면에서 채널 목록을 표시하는 부분이 사라지기 때문에 다른 채널을 선택할 수 없다. 이를 해결하기 위해 현재 선택된 채널을 표시하는 섹션을 추가하고, 작은 화면에서도 채널을 변경할 수 있는 사이드 메뉴를 구현해야 한다.     

이를 위해 `MessageInterfaceChannels` 컴포넌트를 만들어서 `AppBar`과 채널 정보를 표시하고, 작은 화면에서는 드로어(Drawer)를 통해 채널 목록을 표시하는 기능을 구현한다. 또한, 여러 컴포넌트에 데이터를 전달하는 것이 복잡해질 수 있으므로 컨텍스트(Context)를 사용하여 전역적으로 서버 데이터에 접근할 수 있도록 리팩토링이 필요할 수 있다.      

`MessageInterfaceChannels` 컴포넌트를 만들어서 메인 섹션 상단에 서버 아이콘과 채널 이름을 표시하며, 작은 화면에서는 드로어 토글 버튼을 통해 사이드 메뉴를 열고 닫을 수 있게 만든다. 이로써 사용자는 작은 화면에서도 다른 채널을 선택할 수 있다.     

채널 메뉴와 관련하여 추가적인 기능을 구현하기 위해 `ChannelMessage` 컴포넌트를 만들고, `MessageInterfaceChannels` 컴포넌트에 데이터를 전달하여 사용한다. 이로 인해 컴포넌트 간 데이터 전달이 간소화되며, 전역 상태 관리를 위해 컨텍스트를 고려할 수 있다. 이런 식으로 컴포넌트 간의 계층 구조를 잘 설계하여 원활한 개발을 진행할 수 있다.     

1. 먼저 필요한 모듈들과 타입들을 import합니다. 그리고 ServerChannelProps라는 인터페이스를 선언한다.
    
2. MessageInterfaceChannels 함수 컴포넌트를 선언합니다. 이 함수 컴포넌트는 ServerChannelProps 타입의 props를 받는다.
    
3. props에서 data를 가져온다.
    
4. useTheme와 useMediaQuery 훅을 사용하여 현재 테마와 스크린 크기를 가져온다.
    
5. useParams 훅을 사용하여 URL의 serverId와 channelId를 가져온다.
    
6. state로 sideMenu와 channelName을 선언한다. sideMenu는 현재 드로어(사이드 메뉴)의 열림/닫힘 여부를 저장하는 상태고, channelName은 현재 채널의 이름을 저장하는 상태다.
    
7. useEffect를 사용하여 스크린 크기가 변경되면서 현재 sideMenu가 열려있다면, 스크린 크기가 작아지면 자동으로 sideMenu를 닫아주는 로직을 구현한다.
    
8. toggleDrawer 함수를 선언한다. 이 함수는 드로어의 열림/닫힘 상태를 변경하는 역할을 한다.
    
9. list 함수를 선언한다. 이 함수는 드로어 안에 표시될 컴포넌트를 반환한다. 현재는 ServerChannels 컴포넌트가 드로어 안에 표시되도록 구현되어 있다.
    
10. AppBar 컴포넌트를 구현한다. 이 컴포넌트는 앱 상단에 표시되는 네비게이션 바를 나타낸다. 테마와 props를 활용하여 스타일을 설정하고, 드로어와 드로어 토글 버튼을 포함하고 있다.
    
11. 컴포넌트를 반환하고, 이 컴포넌트를 export한다.
    

이렇게 구현된 MessageInterfaceChannels 컴포넌트는 채널 이름을 표시하고, 작은 스크린에서는 드로어 토글 버튼을 제공하여 채널 목록을 펼칠 수 있다. 또한, 스크린 크기가 작아지면 자동으로 드로어를 닫아주는 기능이 구현되어 있다. 이 컴포넌트를 이용하면 채팅 앱의 채널 관련 인터페이스를 구현할 수 있다.

![](https://i.imgur.com/TpcRTcp.png)


## Build : Templating - Message Template

이제 메시지 템플릿링을 추가하여 채팅 메시지를 화면에 표시하는 작업을 하면 된다. 먼저, 메시지를 표시할 박스를 만들고 해당 박스를 스크롤 가능하게 설정한다. 스크롤을 뒤집어서 최신 메시지가 먼저 표시되도록 한다.

- 메시지를 표시할 리스트 생성
- 각 메시지마다 아바타(사용자 이미지) 표시
- 메시지 보낸 사람의 이름 표시
- 메시지 내용 표시
- 메시지의 시간 정보 표시

기본적으로 제공되지 않는 사용자 아바타 이미지에 대한 처리도 고려해야 한다.     

### MessageInterface.tsx

```typescript
import { useState } from "react";

import { useParams } from "react-router-dom";

import useWebSocket from "react-use-websocket";

import useCrud from "../../hooks/useCrud";

import { Server } from "../../@types/server.d";

import { Avatar, Box, List, ListItem, ListItemAvatar, ListItemText, Typography } from "@mui/material";

import MessageInterfaceChannels from "./MessageInterfaceChannels";

  

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

  const [newMessage, setNewMessage] = useState<Message[]>([]);

  const [message, setMessage] = useState("");

  const { serverId, channelId } = useParams();

  const server_name = data?.[0]?.name ?? "Server";

  const { fetchData } = useCrud<Server>(

    [],

    `/messages/?channel_id=${channelId}`

  );

  const socketUrl = channelId

    ? `ws://127.0.0.1:8000/${serverId}/${channelId}`

    : null ;

  

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

    onClose: () => {

      console.log("Closed!");

    },

    onError: () => {

      console.log("Error!");

    },

    onMessage: (msg) => {

      const data = JSON.parse(msg.data);

      setNewMessage((prev_msg) => [...prev_msg, data.new_message]);

    },

  });

  

  return (

    <>

      <MessageInterfaceChannels data={data} />

      {channelId == undefined ? (

        <Box

          sx = {{

            overflow: "hidden",

            p: { xs: 0},

            height: `calc(80vh)`,

            display: "flex",

            justifyContent: "center",

            alignItems: "center",

          }}

        >

          <Box

            sx= {{

              textAlign: "center"

            }}

          >

            <Typography

              variant="h4"

              fontWeight={700}

              letterSpacing={"-0.5px"}

              sx = {{

                px:5,

                maxWidth: "600px",

              }}

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

  

          <Box sx={{ overflow: "hidden", p:0, height: `calc(100vh - 100px)` }}>

            <List sx={{ width: "100%", bgcolor: "background.paper "}}>

              {newMessage.map((msg: Message, index: number) => {

                return(

                  <ListItem key={index} alignItems="flex-start">

                    <ListItemAvatar>

                      <Avatar alt="user image"/>

                    </ListItemAvatar>

                    <ListItemText

                      primaryTypographyProps={{

                        fontSize: "12px",

                        variant: "body2",

                      }}

                      primary={

                        <Typography

                          component="span"

                          variant="body1"

                          color="text.primary"

                          sx = {{

                            display: "inline",

                            fontW: 600

                          }}

                        >

                          {msg.sender}

                        </Typography>

                      }

                      secondary= {

                        <Box>

                          <Typography

                            variant = "body1"

                            style={{

                              overflow: "visible",

                              whiteSpace: "normal",

                              textOverflow: "clip",

                            }}

                            sx = {{

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

                        </Box>

                      }

                    />

                  </ListItem>

                );

              })}

            </List>

  

          </Box>

  

          {/* <div>

            {newMessage.map((msg: Message, index: number) => {

              return(

                <div key={index}>

                  <p>{msg.sender}</p>

                  <p>{msg.content}</p>

                </div>

              );

            })}

            <form>

              <label>

                Enter Message:

                <input

                  type="text"

                  value={message}

                  onChange={(e) => setMessage(e.target.value)}

                />

  

              </label>

            </form>

            <button onClick={() => {

                sendJsonMessage({type: "message", message});

              }}

            >

              Send Message

              </button>

        </div> */}

      </>

    )}

  </>

);

}

  

export default messageInterface;
```

위의 코드는 `MessageInterfaceChannels` 컴포넌트와 함께 사용되는 `messageInterface`라는 함수형 컴포넌트다. 이 컴포넌트는 서버와 채널 관련 데이터를 받아와 채팅 메시지를 표시하고 WebSocket을 통해 메시지를 주고받을 수 있도록 구성되어 있다.

1. `useState`를 이용하여 state 변수들을 정의한다. `newMessage`는 채팅 메시지들을 담는 배열, `message`는 사용자가 입력한 새로운 메시지를 담는 문자열이다.
    
2. `useParams`를 사용하여 현재 라우터의 파라미터(serverId, channelId)를 받아온다.
    
3. `useCrud` 커스텀 훅을 사용하여 해당 채널에 대한 메시지 데이터를 가져온다.
    
4. WebSocket을 이용하여 서버와 실시간으로 통신하고 새로운 메시지가 도착할 때마다 state 변수 `newMessage`를 업데이트 한다.
    
5. 컴포넌트가 리렌더링될 때마다 채팅 메시지를 가져오기 위해 `fetchData` 함수를 호출한다.
    
6. 채팅 메시지를 표시하는 부분은 조건부 렌더링을 통해 구성되어 있다. `channelId`가 없는 경우, 채널이 선택되지 않은 상태이므로 환영 메시지를 표시한다. `channelId`가 있는 경우, 채팅 메시지를 리스트로 표시한다.
    
7. `newMessage` 배열을 `map` 함수를 이용하여 각각의 채팅 메시지를 리스트 아이템으로 표시한다. 메시지의 보낸 사람과 내용을 표시하고, `ListItemText` 컴포넌트를 사용하여 메시지의 텍스트를 스타일링하여 표시한다.
    

MUI(Materail-UI) 라이브러리의 컴포넌트들을 사용하여 UI를 구성하고, React Router 및 WebSocket을 사용하여 서버와의 통신과 동적 라우팅을 구현했다.      

![](https://i.imgur.com/H42iTbA.png)














{% endraw %}