---
layout: single
title: "[Hoops PJ] 트러블 슈팅 & TIL (3)"
categories: Spring_Project_Hoops
toc: true
toc_sticky: true
author_profile: false
sidebar: 
tags:
  - TIL
  - 개발스터디
  - 99클럽
  - 99일지
  - 항해
---

# JWT 토큰 난 좀 더 편하게 쓰고 싶은데

jwt 토큰을 통해 사용자를 인증하는 방법은 여러가지가 있다.

**1. SecurityContextHolder에서 직접 가져오는 방법**

```
Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal(); 
UserDetails userDetails = (UserDetails)principal; 
String username = principal.getUsername(); 
String password = principal.getPassword();
```

첫 번째는 가장 직접적인 방법으로 **'SecurityContextHolder'**에 직접 접근하여 로그인 객체를 가져오는 방법이 있습니다.

해당 코드를 static 메서드로 만들어서 사용할 수도 있지만, 로그인 객체가 필요한 요청마다 해당 메서드를 호출하는 코드가 반복적으로 사용된다는 비효율적인 부분이 발생합니다.

https://wildeveloperetrain.tistory.com/324#google_vignette


네, @AuthenticationPrincipal 사용 시 타입 안전성이 낮아지는 이유를 자세히 설명해 드리겠습니다.

**예시 상황**

- 사용자 정보를 담고 있는 CustomUserDetails 클래스가 있다고 가정합시다.
- 이 CustomUserDetails 클래스는 UserDetails 인터페이스를 구현하고 있습니다.

**@AuthenticationPrincipal 사용 시 문제점**

1. **직접 캐스팅 필요**:
    
    - @AuthenticationPrincipal 어노테이션을 사용하면 반환되는 객체는 Object 타입입니다.
    - 따라서 컨트롤러 메서드에서 해당 객체를 사용하려면 직접 CustomUserDetails 타입으로 캐스팅해야 합니다.
    - 예시 코드:
        
        java
        
        ```java
        @GetMapping("/profile")
        public String getProfile(@AuthenticationPrincipal Object principal) {
            CustomUserDetails userDetails = (CustomUserDetails) principal;
            // userDetails 객체를 사용하여 프로필 정보 처리
        }
        ```
        
    - 이렇게 직접 캐스팅을 해야 하므로 타입 안전성이 낮아집니다.
2. **런타임 오류 발생 가능**:
    
    - 만약 인증 과정에서 principal 객체가 CustomUserDetails 타입이 아닌 다른 타입으로 반환된다면, 캐스팅 과정에서 ClassCastException이 발생할 수 있습니다.
    - 이는 컨트롤러 메서드 실행 중 예기치 않은 오류를 발생시킬 수 있습니다.

**별도의 빈으로 사용자 정보 추출 시 장점**

1. **타입 안전성 향상**:
    
    - 별도의 빈으로 사용자 정보 추출 로직을 캡슐화하면, 컨트롤러 메서드에서 CustomUserDetails 타입으로 직접 주입받을 수 있습니다.
    - 이렇게 하면 타입 안전성이 높아지며, 런타임 오류 발생 가능성이 낮아집니다.
    - 예시 코드:
        
        java
        
        ```java
        @GetMapping("/profile")
        public String getProfile(CustomUserDetails userDetails) {
            // userDetails 객체를 사용하여 프로필 정보 처리
        }
        ```
        
2. **코드 가독성 및 유지보수성 향상**:
    
    - 사용자 정보 추출 로직을 별도의 빈으로 관리하면, 컨트롤러 메서드에서 중복 코드 없이 사용자 정보를 가져올 수 있습니다.
    - 이를 통해 코드의 가독성과 유지보수성이 향상됩니다.

따라서 @AuthenticationPrincipal을 사용하면 직접 캐스팅해야 하므로 타입 안전성이 낮아지지만, 별도의 빈으로 사용자 정보를 추출하면 타입 안전성과 코드 품질이 향상됩니다. 프로젝트의 규모와 복잡도에 따라 적절한 방법을 선택하는 것이 중요합니다.


`@AuthenticationPrincipal` 애노테이션 사용과 별도의 컴포넌트(`JwtTokenExtract`)를 사용하여 현재 인증된 사용자 정보를 추출하는 방식은 각각의 장단점을 가집니다. 아래에서 각 방식의 특징과 장단점을 상세히 설명하겠습니다.

### `@AuthenticationPrincipal` 사용

Spring Security의 `@AuthenticationPrincipal` 애노테이션을 사용하면 컨트롤러 메서드의 인자로 현재 인증된 사용자의 주요 정보를 직접 주입받을 수 있습니다. 이 방식은 다음과 같은 장단점을 가집니다.

#### 장점

- **간결성**: 컨트롤러 메서드에서 직접 인증된 사용자의 정보를 인자로 받을 수 있으므로, 별도의 컴포넌트를 호출하거나 인증 객체를 수동으로 추출하는 등의 추가적인 작업이 필요 없어 코드가 간결해집니다.
- **유연성**: `@AuthenticationPrincipal`은 커스텀 `UserDetailsService`에서 반환하는 사용자 정의 타입으로도 사용할 수 있어, 다양한 타입의 사용자 정보에 쉽게 접근할 수 있습니다.
- **Spring Security 통합**: Spring Security와의 긴밀한 통합을 통해 Security 컨텍스트에 접근하는 표준화된 방법을 제공합니다.

#### 단점

- **유연성의 제한**: 특정 메서드에서만 사용자 정보가 필요한 경우에 유용하지만, 애플리케이션의 여러 부분에서 사용자 정보에 접근해야 하는 경우, `@AuthenticationPrincipal`을 사용하는 것이 반복적이고 비효율적일 수 있습니다.

### 별도 컴포넌트(`JwtTokenExtract`) 사용

별도의 컴포넌트를 정의하여 현재 인증된 사용자 정보를 추출하는 방식은 다음과 같은 장단점을 가집니다.

#### 장점

- **재사용성**: 한 번 정의해두면 애플리케이션의 어디서나 빈을 주입받아 사용할 수 있으므로, 중복 코드의 양을 줄일 수 있습니다. 특히, 여러 컴포넌트에서 사용자 정보가 필요한 경우 매우 유용합니다.
- **커스터마이징**: 별도의 컴포넌트를 통해 사용자 정보를 추출하는 로직을 직접 컨트롤할 수 있으므로, 보다 세밀한 예외 처리나 추가적인 비즈니스 로직을 구현하기 쉽습니다.

#### 단점

- **복잡성**: 간단한 경우에는 `@AuthenticationPrincipal`만으로 충분할 수 있는데, 별도의 컴포넌트를 만드는 것이 과할 수 있습니다. 이 경우, 코드의 복잡성만 증가시킬 수 있습니다.
- **스프링 의존성**: 별도의 컴포넌트를 사용하더라도 결국 Spring Security의 컨텍스트에 의존하게 되므로, 스프링 프레임워크 없이는 재사용성이 떨어집니다.

### 결론

각 방식은 상황에 따라 장단점이 있으므로, 애플리케이션의 특정 요구 사항과 개발 환경을 고려하여 가장 적합한 방식을 선택하는 것이 중요합니다. 단순히 특정 컨트롤러에서만 사용자 정보를 필요로 하는 경우는 `@AuthenticationPrincipal`이 더 간결하고 효육적일 수 있습니다. 반면, 애플리케이션 전반에 걸쳐 사용자 정보에 접근해야 하는 경우나 복잡한 비즈니스 로직을 처리해야 하는 경우, `JwtTokenExtract`와 같은 별도의 컴포넌트를 사용하는 것이 더 나을 수 있습니다.




https://backendhance.com/en/blog/2023/authentication-principal/

https://stackoverflow.com/questions/70626334/convert-jwt-authentication-principal-to-something-more-usable-in-spring

https://velog.io/@hann1233/AuthenticationPrincipal-%EC%A0%81%EC%9A%A9

https://wildeveloperetrain.tistory.com/324

https://velog.io/@jyleedev/AuthenticationPrincipal-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%A0%95%EB%B3%B4-%EB%B0%9B%EC%95%84%EC%98%A4%EA%B8%B0