---
layout: single
title: " [Django DRF] React DjangoDRF project (6) "
categories: Django_Chat_Service
tags:
  - Python
  - DjangoDRF
  - React
  - chat
toc: true
toc_sticky: true
author_profile: false
sidebar:
---

# Extending Chat Services

## Build: Server Membership

### server/views.py

```python
class ServerMembershipViewSet(viewsets.ViewSet):
    permission_classes = [IsAuthenticated]

    def create(self, request, server_id):
        server = get_object_or_404(Server, id=server_id)

        user = request.user

        if server.member.filter(id=user.id).exists():
            return Response({"error": "User is already a member"}, status=status.HTTP_409_CONFLICT)

        server.member.add(user)

        return Response({"message": "User joined server successfully"}, status=status.HTTP_200_OK)

    @action(detail=False, methods=["DELETE"])
    def remove_member(self, request, server_id):
        server = get_object_or_404(Server, id=server_id)
        user = request.user

        if not server.member.filter(id=user.id).exists():
            return Response({"error": "User is not a member"}, status=status.HTTP_404_NOT_FOUND)

        if server.owner == user:
            return Response({"error": "Owners cannot be removed as a member"}, status=status.HTTP_409_CONFLICT)

        server.member.remove(user)

        return Response({"message": "User removed from server..."}, status=status.HTTP_200_OK)

    @action(detail=False, methods=["GET"])
    def is_member(self, request, server_id=None):
        server = get_object_or_404(Server, id=server_id)
        user = request.user

        is_member = server.member.filter(id=user.id).exists()

        return Response({"is_member": is_member})

```


1. `create` 메서드:
    
    - 사용자의 서버 멤버십을 생성하는 기능을 처리한다.
    - 사용자는 `IsAuthenticated` 권한 클래스에 의해 인증(로그인)되어야 한다.
    - 제공된 `server_id`를 사용하여 `server` 객체를 가져온다.
    - 요청한 사용자가 이미 서버의 멤버인지 확인한다. 이미 멤버이면 409 Conflict 응답을 반환한다.
    - 사용자를 서버의 멤버 목록에 추가하고 성공 응답을 반환한다.
2. `remove_member` 메서드:
    
    - 사용자를 서버 멤버십에서 제거하는 기능을 처리한다.
    - 다시 한번, 사용자는 인증되어야 한다.
    - 제공된 `server_id`를 사용하여 `server` 객체를 가져온다.
    - 사용자가 서버의 멤버인지 확인한다. 멤버가 아니라면 404 Not Found 응답을 반환한다.
    - 사용자가 서버의 소유자인지 확인한다. 소유자는 멤버에서 제거될 수 없으므로, 사용자가 소유자인 경우 409 Conflict 응답을 반환한다.
    - 사용자를 서버의 멤버 목록에서 제거하고 성공 응답을 반환한다.
3. `is_member` 메서드:
    
    - 특정 서버의 사용자 멤버십 여부를 확인하는 방법을 제공한다.
    - 사용자는 인증되어야 한다.
    - 제공된 `server_id`를 사용하여 `server` 객체를 가져온다.
    - 요청한 사용자가 서버의 멤버인지 확인하고, 사용자가 멤버인지 여부를 나타내는 JSON 응답을 반환한다.

전반적으로 이 코드는 서버 멤버십을 관리하는 뷰셋(ViewSet)을 정의하며, 서버 가입 및 탈퇴와 멤버십 상태 확인 기능을 제공한다. Django의 내장 `ViewSets` 및 `actions`을 사용하여 이러한 기능을 제공한다. 

### urls.py

```python
router.register(
    r"api/membership/(?P<server_id>\d+)/membership", ServerMembershipViewSet, basename="server-membership"
) 
```

정규식을 사용하여 URL 패턴을 정의하는 것에는 몇 가지 장점이 있다. 주로 동적인 URL 경로에 대응하거나 특정 값을 추출해야 할 때 유용하게 사용된다.     

`router.register`의 경우, 기본적으로 뷰셋(ViewSet)의 이름을 기반으로 URL 패턴을 생성한다. 그러나 때로는 동적인 URL 경로를 사용해야 할 때가 있다. 이때 정규식을 사용하여 URL 경로에 변수를 넣고 이 변수를 뷰에서 활용할 수 있다.

`r"api/membership/(?P<server_id>\d+)/membership"`라는 정규식 패턴에서 `(?P<server_id>\d+)` 부분은 다음과 같은 기능을 한다:

1. `?P<server_id>`: 이 부분은 그룹 이름을 정의하는데, 여기서 `server_id` 라는 그룹 이름을 사용한다.
2. `\d+`: 이 부분은 1개 이상의 숫자(digit)에 대응한다.

따라서, URL 패턴에서 숫자로 된 서버 ID를 추출할 수 있게 된다. 이를 활용하여 뷰에서 `server_id` 변수를 받아와서 사용할 수 있다.

정규식을 사용하는 장점은 다음과 같다:

1. **동적인 URL 처리**: URL 패턴에 변수를 포함시켜 동적인 요청을 처리할 수 있다. 예를 들어, `api/membership/1/membership`와 같은 URL에 대응하여 서버 ID를 추출할 수 있다.
    
2. **유연성**: 정규식을 사용하면 뷰와 URL 간의 결합이 더 유연해진다. 다양한 요청을 동일한 뷰에서 처리하거나, 같은 URL 패턴을 다른 뷰에서 사용하는 등의 유연한 구성이 가능하다.
    
3. **파라미터 추출**: 정규식 그룹을 사용하면 URL에서 특정 값을 추출하여 뷰 함수로 전달할 수 있다. 이를 통해 필요한 정보를 추출하거나 처리할 수 있다.
    
4. **가독성 및 유지보수**: 정규식을 사용하여 명확하게 URL 패턴을 정의하면 가독성이 향상되며, 나중에 유지보수 및 변경이 용이해진다.
    

따라서, 동적인 URL 경로나 특정 값 추출이 필요한 경우, 정규식을 사용하여 URL 패턴을 구성하는 것은 유용한 방법이다.

### JoinServerButton.tsx

{% raw %}

```typescript
import { useMembershipContext } from "../../context/MemberContext";

import { useParams } from "react-router-dom";

import { useNavigate } from "react-router-dom";

  

const JoinServerButton = () => {

  const { serverId } = useParams();

  const navigate = useNavigate();

  const { joinServer, leaveServer, isLoading, error, isUserMember } =

    useMembershipContext();

  

  const handleJoinServer = async () => {

    try {

      await joinServer(Number(serverId));

      navigate(`/server/${serverId}/`);

      console.log("User has joined server");

    } catch (error) {

      console.log("Error joining", error);

    }

  };

  

  const handleLeaveServer = async () => {

    try {

      await leaveServer(Number(serverId));

      navigate(`/server/${serverId}/`);

      console.log("User has left the server successfully!");

    } catch (error) {

      console.error("Error leaving the server:");

    }

  };

  

  if (isLoading) {

    return <div>Loading...</div>;

  }
  

  return (

    <>

      ismember: {isUserMember.toString()}

      {isUserMember ? (

        <button onClick={handleLeaveServer}>Leave Server</button>

      ) : (

        <button onClick={handleJoinServer}>Join Server</button>

      )}

    </>

  );

};

export default JoinServerButton;
```

`JoinServerButton` 는, 서버 멤버십 상태를 확인하고 조인 또는 나가기 작업을 처리한다. 이 컴포넌트는 React Router와 멤버십 컨텍스트를 사용하여 서버 가입 및 탈퇴 기능을 구현한다.

간단히 설명하면, 이 코드는 다음과 같은 작업을 수행한다:

1. `useParams()` 훅을 사용하여 URL 파라미터에서 `serverId`를 추출한다.
2. `useNavigate()` 훅을 사용하여 React Router의 네비게이션 기능을 활용할 수 있다.
3. `useMembershipContext()` 훅을 사용하여 멤버십 컨텍스트로부터 필요한 함수와 상태를 가져온다.
4. `handleJoinServer()` 함수: 사용자가 서버에 가입하도록 시도하고, 성공 시 네비게이션을 수행하고 콘솔에 메시지를 출력한다.
5. `handleLeaveServer()` 함수: 사용자가 서버를 나가도록 시도하고, 성공 시 네비게이션을 수행하고 콘솔에 메시지를 출력한다.
6. 로딩 상태일 때는 "Loading..."을 반환한다.
7. `isUserMember` 상태를 기반으로, 사용자가 멤버인지 여부에 따라 "Leave Server" 또는 "Join Server" 버튼을 렌더링한다.

주석 처리된 부분은 에러 메시지를 처리하는 부분으로, 필요에 따라 주석 해제하여 에러를 보여줄 수 있다.

이 컴포넌트는 멤버십 상태와 사용자의 조인 또는 나가기 액션을 처리하며, 이를 통해 서버 가입 및 탈퇴 기능을 화면에 표시한다.


### MembershipCheck.tsx

```typescript
import { useEffect } from "react";
import { useMembershipContext } from "../../context/MemberContext";
import { useParams } from "react-router-dom";

interface MembershipCheckProps {
  children: any;
}

const MembershipCheck: React.FC<MembershipCheckProps> = ({ children }) => {
  const { serverId } = useParams();
  const { isMember } = useMembershipContext();

  useEffect(() => {
    const checkMembership = async () => {
      try {
        await isMember(Number(serverId));
      } catch (error) {
        console.log("Error checking membership status", error);
      }
    };
    checkMembership();
  }, [serverId]);

  return <>{children}</>;
};

export default MembershipCheck;
```

 `MembershipCheck`는 멤버십 상태를 확인하고, 해당 서버의 멤버인지 아닌지를 검사하는 역할을 한다. 특정 서버에 대한 멤버십 상태를 확인하고자 할 때 사용될 수 있다.


1. `MembershipCheckProps` 인터페이스: `children` 프로퍼티를 가진 `MembershipCheck` 컴포넌트의 props의 형식을 정의한다.
    
2. `MembershipCheck` 컴포넌트: `MembershipCheckProps` 인터페이스를 사용하여, 멤버십 상태를 확인하고 해당 서버의 멤버인지 아닌지를 검사하는 React 함수형 컴포넌트를 정의한다.
    
3. `serverId` 추출: `useParams()` 훅을 사용하여 현재 URL의 `serverId` 파라미터 값을 추출한다.
    
4. `useMembershipContext()` 훅을 사용하여 멤버십 컨텍스트로부터 `isMember` 함수를 가져온다.
    
5. `useEffect()` 훅: 컴포넌트가 마운트될 때와 `serverId`가 변경될 때마다 실행되는 효과를 정의한다. 이 효과는 `checkMembership` 함수를 호출하여 현재 서버의 멤버십 상태를 확인하고, 에러 발생 시 에러를 콘솔에 출력한다.
    
6. `<>{children}</>`: 컴포넌트 자체에는 렌더링할 내용이 없으며, `children` 프로퍼티로 전달된 컴포넌트(들)를 반환한다. 이를 통해 `MembershipCheck`를 사용한 컴포넌트 내에서 멤버십 상태 확인 후 렌더링할 내용을 표시할 수 있다.
    

 `MembershipCheck` 컴포넌트는 멤버십 상태를 확인하고, 이를 활용하는 컴포넌트 내에서 조건부 렌더링 등의 작업을 수행하는 데 사용될 수 있다.

### MemberContext.tsx

```typescript
+import React, { createContext, useContext } from "react";
import useMembership from "../services/membershipService";

interface IuseServer {
    joinServer: (serverId: number) => Promise<void>;
    leaveServer: (serverId: number) => Promise<void>;
    isMember: (serverId: number) => Promise<boolean>;
    isUserMember: boolean;
    error: Error | null;
    isLoading: boolean;
}

const MembershipContext = createContext<IuseServer | null>(null);

export function MembershipProvider(props: React.PropsWithChildren<{}>) {
  const membership = useMembership();
  return (
    <MembershipContext.Provider value={membership}>
      {props.children}
    </MembershipContext.Provider>
  );
}

export function useMembershipContext(): IuseServer {
  const context = useContext(MembershipContext);

  if (context === null) {
    throw new Error("Error - You have to use the MembershipProvider");
  }
  return context;
}

export default MembershipProvider;
```

React 컨텍스트(Context)를 사용해서 멤버십 관련 기능을 제공하는 프로바이더와 훅을 사용한다. 이 컨텍스트를 사용하여 컴포넌트 간에 멤버십 관련 데이터와 기능을 공유할 수 있다.

1. `IuseServer` 인터페이스: `useMembership` 서비스에서 제공하는 멤버십 관련 함수와 상태를 정의한 인터페이스다.
    
2. `MembershipContext` 컨텍스트 생성: `createContext` 함수를 사용하여 멤버십 관련 데이터와 함수를 담을 컨텍스트를 생성한다. 초기값은 `null`로 설정되어 있다.
    
3. `MembershipProvider` 컴포넌트: 멤버십 관련 기능을 제공하는 `useMembership` 훅의 반환값을 `MembershipContext.Provider`로 제공한다. 이 컴포넌트는 컨텍스트를 설정하고, 자식 컴포넌트들을 렌더링한다.
    
4. `useMembershipContext` 훅: 현재 컨텍스트를 가져와서 사용하는 훅이다. 만약 컨텍스트가 `null`이라면 에러를 던진다.
    
5. `MembershipProvider` 컴포넌트 내부에서 `useMembership` 훅을 사용하여 멤버십 관련 함수와 상태를 가져온 후, 해당 값을 `MembershipContext.Provider`의 `value`로 전달한다.
    
6. `useMembershipContext` 훅을 사용하여 멤버십 관련 함수와 상태를 다른 컴포넌트에서 사용할 수 있다.
    

멤버십 관련 기능을 효율적으로 컨텍스트로 관리하며, 컴포넌트 간에 이러한 기능을 쉽게 공유할 수 있도록 도와준다. `MembershipProvider`를 사용하여 컨텍스트를 설정하고, `useMembershipContext`를 통해 멤버십 관련 함수와 상태에 접근할 수 있다.

### membershipService.ts

```typescript
+import { useState } from "react";
import useAxiosWithJwtInterceptor from "../helpers/jwtinterceptor";
import { BASE_URL } from "../config";
import axios from "axios";

interface IuseServer {
    joinServer: (serverId: number) => Promise<void>;
    leaveServer: (serverId: number) => Promise<void>;
    isMember: (serverId: number) => Promise<boolean>;
    isUserMember: boolean;
    error: Error | null;
    isLoading: boolean;
  }


  const useMembership = (): IuseServer => {
    const jwtAxios = useAxiosWithJwtInterceptor()
    const [error, setError] = useState<Error | null>(null)
    const [isLoading, setIsLoading] = useState(false)
    const [isUserMember, setIsUserMember] = useState(false)

    const joinServer = async (serverId: number): Promise<void> => {
        setIsLoading(true);
        try{
            await jwtAxios.post(`${BASE_URL}/membership/${serverId}/membership/`, {}, {withCredentials: true})
            setIsLoading(false)
            setIsUserMember(true)
        } catch (error: any) {
            setError(error)
            setIsLoading(false)
            throw error;
        }
    }

    const leaveServer = async (serverId: number): Promise<void> => {
        setIsLoading(true);
        try {
          await jwtAxios.delete(`${BASE_URL}/membership/${serverId}/membership/remove_member/`, { withCredentials: true });
          setIsLoading(false);
          setIsUserMember(false);
        } catch (error: any) {
          setError(error);
          setIsLoading(false);
          throw error;
        }
      };

      const isMember = async (serverId: number): Promise<any> => {
        setIsLoading(true);
        try {
          const response = await jwtAxios.get(`${BASE_URL}/membership/${serverId}/membership/is_member/`, { withCredentials: true });
          setIsLoading(false);
          setIsUserMember(response.data.is_member);
        } catch (error: any) {
          setError(error);
          
          setIsLoading(false);
          throw error;
        }
      };

    return { joinServer, leaveServer, error, isLoading, isMember, isUserMember }
  }
  export default useMembership
```

 `useMembership` 훅은 멤버십 관련 기능을 포함하며, 서버에 가입하거나 나가는 등의 작업을 수행할 수 있도록 한다. 코드 내용에 대한 간략한 설명은 다음과 같다.

1. `useAxiosWithJwtInterceptor`: JWT 인터셉터가 적용된 Axios 인스턴스를 생성하는 헬퍼 함수다. 
	1. JWT 토큰을 사용하여 인증된 요청을 처리하는 이 훅은 Axios 인터셉터를 사용하여 JWT 토큰을 관리하고, 만료된 토큰을 자동으로 갱신하는 기능을 제공한다. 이 커스텀 훅은 JWT 토큰과 인증 관련된 요청을 보낼 때 토큰을 자동으로 처리하고, 만료된 토큰을 갱신하거나 필요한 경우 로그아웃 및 리다이렉션을 처리하는 기능을 제공한다. 이를 통해 인증 관련 로직을 중앙에서 관리하고 컴포넌트에서 간편하게 사용할 수 있다.
    
2. `BASE_URL`: 서버의 기본 URL을 가리키는 상수다.
    
3. `useMembership` 커스텀 훅: 멤버십과 관련된 여러 함수와 상태를 포함하는 훅을 정의한다.
    
    - `joinServer`: 사용자가 서버에 가입하는 기능을 수행한다. POST 요청을 보내고, 에러 처리와 로딩 상태 관리를 수행한다.
    - `leaveServer`: 사용자가 서버에서 나가는 기능을 수행한다. DELETE 요청을 보내고, 에러 처리와 로딩 상태 관리를 수행한다.
    - `isMember`: 사용자의 서버 멤버십 상태를 확인한다. GET 요청을 보내고, 응답을 기반으로 `isUserMember` 상태를 업데이트하며 에러 처리와 로딩 상태 관리를 수행한다.
    - `error`: 현재 발생한 에러를 저장하는 상태 변수다.
    - `isLoading`: 작업이 진행 중인지를 나타내는 상태 변수다.
    - `isUserMember`: 사용자가 해당 서버의 멤버인지 여부를 나타내는 상태 변수다.
4. 각 함수 내부에서는 Axios를 사용하여 서버로 요청을 보내고, 그에 따른 에러 처리와 로딩 상태 관리를 수행한다.
    

이 커스텀 훅은 서버 멤버십과 관련된 다양한 작업을 편리하게 처리하며, 컴포넌트 내에서 필요한 함수와 상태를 가져와 사용할 수 있다.

## Build: Server Membership Chat Restrictions

### consumer.py

```python
from asgiref.sync import async_to_sync

from channels.generic.websocket import JsonWebsocketConsumer

from django.contrib.auth import get_user_model

from server.models import Server

  

from .models import Conversation, Message

  

User = get_user_model()

  
  

class WebChatConsumer(JsonWebsocketConsumer):

    def __init__(self, *args, **kwargs):

        super().__init__(*args, **kwargs)

        self.channel_id = None

        self.user = None

  

    def connect(self):

        self.user = self.scope["user"]

        self.accept()

  

        if not self.user.is_authenticated:

            self.close(code=4001)

  

        self.channel_id = self.scope["url_route"]["kwargs"]["channelId"]

        self.server_id = self.scope["url_route"]["kwargs"]["serverId"]

  

        self.user = User.objects.get(id=self.user.id)

  

        server = Server.objects.get(id=self.channel_id)

        self.is_member = server.member.filter(id=self.user.id).exists()

  

        async_to_sync(self.channel_layer.group_add)(self.channel_id, self.channel_name)

  

    def receive_json(self, content):

        if not self.is_member:

            return

  

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


1. `WebChatConsumer` 클래스: 이 클래스는 `JsonWebsocketConsumer`를 상속한다. 이는 Django Channels에서 WebSocket 연결을 처리하고 JSON 데이터와의 통신을 다루기 위한 기본 클래스다
    
2. `__init__` 메서드: 생성자 메서드로 `channel_id`와 `user` 인스턴스 변수를 초기화한다.
    
3. `connect` 메서드: 이 메서드는 WebSocket 연결이 설정될 때 호출된다. 다음 작업을 수행한다:
    
    - 스코프에서 인증된 사용자를 가져오고 WebSocket 연결을 수락한다.
    - 사용자가 인증되었는지 확인한다. 그렇지 않으면 코드 4001로 연결을 종료한다.
    - URL 경로에서 `channelId`와 `serverId`를 추출한다.
    - 데이터베이스에서 사용자 인스턴스를 가져온다.
    - 사용자가 지정된 서버의 구성원인지 확인한다.
    - `async_to_sync`와 `channel_layer.group_add`를 사용하여 컨슈머의 채널을 그룹에 추가한다.
4. `receive_json` 메서드: 이 메서드는 WebSocket 연결로부터 JSON 메시지를 받았을 때 호출된다. 다음 작업을 수행한다:
    
    - 사용자가 서버의 구성원인지 확인한다. 그렇지 않으면 종료한다.
    - 수신된 JSON 콘텐츠에서 발신자, 메시지 및 `channel_id`를 추출한다.
    - `channel_id`와 관련된 대화를 가져오거나 생성한다.
    - 새로운 메시지를 생성하고 대화와 발신자를 연결한다.
    - `async_to_sync`와 `channel_layer.group_send`를 사용하여 새로운 메시지를 그룹에 전송한다.
5. `chat_message` 메서드: 이 메서드는 채팅 메시지를 WebSocket 컨슈머에게 전송하기 위해 사용된다.
    
6. `disconnect` 메서드: 이 메서드는 WebSocket 연결이 종료될 때 호출된다. 다음 작업을 수행한다:
    
    - `async_to_sync`와 `channel_layer.group_discard`를 사용하여 컨슈머의 채널을 그룹에서 제거한다.
    - 연결 해제를 처리하기 위해 기본 클래스의 `disconnect` 메서드를 호출한다.

전반적으로, 이 코드는 사용자 인증, 구성원 확인 및 Django Channels 애플리케이션 내에서 메시지 전송을 처리한다. 특정 채널 내에서 사용자들이 메시지를 교환하는 채팅 애플리케이션을 위한 WebSocket 컨슈머를 정의한다.


{% endraw %}