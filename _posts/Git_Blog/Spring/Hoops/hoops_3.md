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


## @RequestParam vs @PathVariable 뭐가 다르지?

- 위 2개의 어노테이션은 GET 방식에서 주로 쓴다.
- http의 비연결성을 극복하고 데이터를 전달하기 위한 방법들 중 하나로 uri를 통해 전달된 값을 파라미터로 받아오는 역할을 한다.
```
- http://localhost:8000/board?page=1&listSize=10  
- http://localhost:8000/board/1
```

여러개의 값을 받을 때는 `@RequestParam`  ,하나의 값을 받을 때는 `@PathVariable` 여기서 `@PathVariable` 은 주로 uri에 전달 되는 값을 받아오는데 주로 post 요청에서 많이 쓴다.  
보통 `@RequestBody` 를 통해서 Body 값에 json 형태로 요청하는데 해당 방식을 사용할 수 없을 때 `@PathVariable` 를 사용한다.   

그럼 `@RequestParam` 을 사용하면 GET 요청으로도 충분히 동적으로 쿼리를 만들어 사용할 수 있다고 생각했다.   

근데 또 다시 생각해보니까 의문점이 생겼다.  
## @GetMapping + @RequestParam 이랑 @PostMapping + @RequestBody 랑 별 차이가 없지 않나?

그래서 찾아보니 `@PostMapping`과 `@RequestBody`, `@GetMapping`과 `@RequestParam`이 이렇게 다르게 사용되는 이유는 HTTP 프로토콜의 설계 원칙과 RESTful 아키텍처의 제약 사항이 있다.

1. **HTTP GET 메서드의 제약 사항**
    - GET 요청은 서버의 리소스를 조회하기 위한 용도로 설계되었다.
    - GET 요청에 본문(request body)을 포함하는 것은 HTTP 스펙을 위반하는 행위다.
    - 따라서 GET 요청에서는 Query Parameter를 사용하여 필요한 데이터를 전달한다.
    - 이러한 제약 사항 때문에 `@GetMapping`에서는 `@RequestBody` 대신 `@RequestParam`을 주로 사용한다.
2. **HTTP POST 메서드의 목적**
    - POST 요청은 서버에 새로운 리소스를 생성하거나 기존 리소스를 업데이트하기 위해 사용된다.
    - POST 요청은 본문(request body)에 데이터를 포함할 수 있다.
    - 이러한 목적에 따라 `@PostMapping`에서는 `@RequestBody`를 사용하여 클라이언트로부터 전송된 데이터를 자바 객체로 deserialize 한다.
3. **RESTful 아키텍처 스타일**
    - RESTful 아키텍처에서는 HTTP 메서드를 의미에 맞게 사용하는 것이 중요합니다.
    - GET은 리소스 조회, POST는 리소스 생성, PUT은 리소스 업데이트, DELETE는 리소스 삭제 등의 용도로 사용된다.
    - 이러한 규약을 따르면서 데이터를 효율적으로 전송하기 위해 `@RequestBody`와 `@RequestParam`을 적절히 구분하여 사용한다.

## 그럼 동적으로 여러 조건에 따라 결과 값도 달라지게 만들려면 어떻게 해야될까?


```java
public class GameUserController {  
  
  private final GameUserService gameUserService;  
  
  @GetMapping("/search")  
  public ResponseEntity<List<GameSearchResponse>> findFilteredGames(  
      @RequestParam(required = false) LocalDate localDate,  
      @RequestParam(required = false) CityName cityName,  
      @RequestParam(required = false) FieldStatus fieldStatus,  
      @RequestParam(required = false) Gender gender,  
      @RequestParam(required = false) MatchFormat matchFormat) {  
    return ResponseEntity.ok(  
        gameUserService.findFilteredGames(localDate,  
            cityName, fieldStatus, gender, matchFormat));  
  }
```

이런 식으로 기본 값을 false로 두고 해당 파라미터의 값이 들어오면 객체로서 Specification 를 사용해서 쿼리를 조정해준다.   

```java
public List<GameSearchResponse> findFilteredGames(  
    LocalDate localDate,  
    CityName cityName,  
    FieldStatus fieldStatus,  
    Gender gender,  
    MatchFormat matchFormat) {  
  
  Specification<GameEntity> spec = getGameEntitySpecification(  
      localDate, cityName, fieldStatus, gender, matchFormat);  
  
  List<GameEntity> gameListNow = gameUserRepository.findAll(spec);  
  
  Long userId = null;  
  
  return getGameSearchResponses(gameListNow, userId);  
}
private static Specification<GameEntity> getGameEntitySpecification(  
    LocalDate localDate, CityName cityName, FieldStatus fieldStatus,  
    Gender gender, MatchFormat matchFormat) {  
  Specification<GameEntity> spec = Specification.where(  
      GameSpecifications.startDate(LocalDate.now())  
          .and(GameSpecifications.notDeleted()));  
  
  if (localDate != null) {  
    spec = Specification.where(  
        GameSpecifications.withDate(localDate)  
            .and(GameSpecifications.notDeleted()));  
  }  
  if (cityName != null) {  
    spec = spec.and(GameSpecifications.withCityName(cityName));  
  }  
  if (fieldStatus != null) {  
    spec = spec.and(GameSpecifications.withFieldStatus(fieldStatus));  
  }  
  if (gender != null) {  
    spec = spec.and(GameSpecifications.withGender(gender));  
  }  
  if (matchFormat != null) {  
    spec = spec.and(GameSpecifications.withMatchFormat(matchFormat));  
  }  
  return spec;  
}
```

```java
public class GameSpecifications {  
  
  public static Specification<GameEntity> withCityName(CityName cityName) {  
    return (root, query, criteriaBuilder) ->  
        criteriaBuilder.equal(root.get("cityName"), cityName);  
  }  
  
  public static Specification<GameEntity> withFieldStatus(  
      FieldStatus fieldStatus) {  
    return (root, query, criteriaBuilder) ->  
        criteriaBuilder.equal(root.get("fieldStatus"), fieldStatus);  
  }  
  
  public static Specification<GameEntity> withGender(Gender gender) {  
    return (root, query, criteriaBuilder) ->  
        criteriaBuilder.equal(root.get("gender"), gender);  
  }  
  
  public static Specification<GameEntity> withMatchFormat(  
      MatchFormat matchFormat) {  
    return (root, query, criteriaBuilder) ->  
        criteriaBuilder.equal(root.get("matchFormat"), matchFormat);  
  }  
  
  public static Specification<GameEntity> startDate(  
      LocalDate date) {  
    return (root, query, criteriaBuilder) ->  
        criteriaBuilder.equal(  
            root.get("startDateTime").as(LocalDate.class), date);  
  }  
  
  public static Specification<GameEntity> notDeleted() {  
    return (root, query, criteriaBuilder) ->  
        criteriaBuilder.isNull(root.get("deletedDateTime"));  
  }  
  
  public static Specification<GameEntity> withDate(LocalDate date) {  
    return (root, query, criteriaBuilder) ->  
        criteriaBuilder.equal(  
            root.get("startDateTime").as(LocalDate.class), date);  
  }  
}
```

Specification를 사용하면 직접 쿼리를 작성하는 것 보다 조건을 동적으로 구성하거나 실행 로직 분리로 인해 코드 가독성에 대한 이점이 있다.  
또한 재사용성이 높아 중복 코드를 줄일 수 있고 객체 지향 프로그래밍 스타일에 부합하다고 생각한다.   

하지만 단점도 있는데 지금 조건이 그렇게 많지 않은데도 복잡해 보인다.  
여기서 더 조건이 많아지면 그만큼 복잡해질 수도 있고 불필요한 조인이나 서브쿼리등 성능 이슈가 발생할 수 있다.  

또한 복잡한 쿼리를 작성하기 어려울 수 있다.  

성능 면에서는 직접쿼리가 좋다고 생각하지만 재사용성이나 가독성이 좋지 않다고 생각한다.   
또한 SQL인젝션 공격 등의 보안 위험도 생각해 볼 수 있다.  

솔직히 간단한 쿼리인데도 귀찮아서 이렇게 만들게 되었다.  
아마 내가 SQL을 더 잘 다뤘으면 쿼리로 해결했을 것 같다...