---
layout: single
title: extends보다 implements를, 그리고 DI를 선택한 이유
categories: Spring_Project_AI_Todo
toc: true
toc_sticky: true
author_profile: false
sidebar: 
tags:
---
# *extends* vs *implements*, 언제 써야 할까? 유연한 설계 고민

*[ AI_Todo 프로젝트 개발기 - Backend #2] extends보다 implements를, 그리고 DI를 선택한 이유*

## 1. 서론: 그럼 왜 유연한 설계가 필요한가? (Why)

백엔드 애플리케이션을 개발하며 **테스트 및 유지보수의 어려움**, 특정 기술에 대한 강한 의존성으로 인한 **유연성 저하** 등의 문제에 직면할 때가 많습니다.   

본 프로젝트에서는 이러한 문제를 해결하고 코드 재사용성을 높이기 위해, `extends` 와 `implements` 의 올바른 사용과 **의존성 주입(DI)** 을 통한 느슨한 결합 설계에 대해 깊이 고민했습니다.

이 글은 그 고민의 결과물로, 저희 프로젝트를 관통하는 **핵심 아키텍처 원칙** 을 다룹니다. 특히 Repository 계층을 분리하여 **도메인 주도 설계(DDD)** 와 **헥사고날 아키텍처** 원칙을 적용한 경험을 공유하고, 이 원칙들이 **어떻게 실제 JWT 인증 시스템 구현으로 이어졌는지** 는 다음 편에서 상세히 다룰 예정입니다.

## 2. `extends` vs `implements`: 적절한 선택을 위한 기본 이해 (What)

### `extends` (확장)

`extends` 는 **클래스 간 상속** 또는 **인터페이스 간 확장**에 사용됩니다. 즉, 클래스가 다른 클래스의 기능을 물려받거나, 인터페이스가 다른 인터페이스의 정의를 확장할 때 사용합니다.

### `implements` (구현)

`implements` 는 클래스가 **인터페이스를 구현**할 때 사용합니다. 인터페이스에 선언된 모든 추상 메서드를 해당 클래스에서 반드시 구현해야 합니다.  

**예시:**

```java
public class TodoRepositoryImpl implements TodoRepository { // ... 인터페이스 메서드 구현 }
```

`implements` 는 주로 "**can-do**" 관계(A는 B의 기능을 수행할 수 있다)에서 사용되며, **유연성**과 **다형성**을 확보하는 데 필수적입니다.   

이를 통해 실제 구현체는 얼마든지 교체 가능하며, 클라이언트 코드는 인터페이스에만 의존하게 되어 **느슨한 결합**을 유도합니다.  

위 예시에서 *TodoRepositoryImpl* 은 *TodoRepository* 인터페이스를 `implements` 함으로써, *TodoRepository* 에 정의된 메서드들을 실제로 구현해야 합니다.  

## 3. 의존성 주입(DI)과 인터페이스 기반 설계의 힘 (How)

테스트 및 유지보수를 편하게 하고, 의존성이나 결합도를 낮추며, 코드 재사용성을 높이려면 어떻게 하면 좋을까요 ?   
그 핵심은 바로 **의존성 주입(DI)**과 **인터페이스 기반 설계**에 있다고 생각합니다.

### DI의 동작 원리 (Spring을 중심으로)

Spring 프레임워크는 의존성 주입을 통해 객체 간의 관계를 관리하고 결합도를 낮춥니다.    
일반적으로 Spring은 **인터페이스**를 보고 그 **구현체**를 자동으로 연결해줍니다.  

1. `@Repository`어노테이션이 붙은 *TodoRepositoryImpl* 클래스는 Spring 컨테이너에 **빈(Bean)** 으로 등록됩니다.
2. *TodoRepositoryImpl* 은 *TodoRepository* 인터페이스를 `implements` 하므로, Spring은 이 구현체를 *TodoRepository* 타입으로 인식합니다.
3. 만약 *TodoService* 와 같이 다른 컴포넌트가 *TodoRepository* 타입의 의존성 주입을 요청하면, Spring은 *TodoRepositoryImpl* 인스턴스를 주입해 줍니다.

*TodoRepositoryImpl* 생성자를 보면 *JpaTodoRepository* 를 주입받는 것을 알 수 있습니다.


```java
public interface TodoRepository {
    Todo save(Todo todo);
    Optional<Todo> findById(Long id);
    List<Todo> findAll();
    void deleteById(Long id);
    List<Todo> findByCompleted(boolean completed);
}

public interface JpaTodoRepository extends JpaRepository<Todo, Long> {
    // Spring Data JPA가 자동으로 구현체를 생성
}

@Repository
public class TodoRepositoryImpl implements TodoRepository {

    private final JpaTodoRepository jpaTodoRepository;

    public TodoRepositoryImpl(JpaTodoRepository jpaTodoRepository) {
        this.jpaTodoRepository = jpaTodoRepository;
    }

    @Override
    public Todo save(Todo todo) {
        return jpaTodoRepository.save(todo);
    }
    // ... 나머지 메서드 구현
}
```

여기서  *JpaTodoRepository* 는  *JpaRepository<Todo, Long>* 를 `extends`한 Spring Data JPA의 인터페이스입니다.   

Spring은 이 인터페이스에 대해서도 자동으로 구현체를 만들어서 *TodoRepositoryImpl* 에 주입해 줍니다.

**간단한 의존성 주입 흐름도:**


```
[Spring Container] 
      ↓ 
JpaTodoRepository (extends JpaRepository) 
      ↓ 주입 
TodoRepositoryImpl (implements TodoRepository) 
      ↓ 주입 
TodoService (uses TodoRepository)
```


### DI와 인터페이스 기반 설계의 장점

이러한 DI와 인터페이스 기반 설계는 다음과 같은 이점을 제공합니다.

- **낮은 결합도:** 
	- 클라이언트(예:  TodoService )는 구체적인 구현체( TodoRepositoryImpl )가 아닌 추상적인 인터페이스( TodoRepository )에만 의존합니다. 
	- 이는 특정 구현 기술에 얽매이지 않고 유연하게 변경하거나 확장할 수 있게 합니다.
- **높은 유연성:** 
	- 필요에 따라 *TodoRepository* 의 다른 구현체(예:  *InMemoryTodoRepository* 또는 *MongoTodoRepository* )로 쉽게 교체할 수 있습니다.
- **쉬운 테스트:** 
	- *TodoService* 를 테스트할 때 실제 *TodoRepositoryImpl* 대신 **Mock 객체**를 주입하여, 데이터베이스와의 상호작용 없이 비즈니스 로직만 독립적으로 테스트할 수 있습니다.
- **코드 재사용성:** 
	- 인터페이스를 통해 표준화된 기능을 제공함으로써, 다양한 상황에서 해당 기능을 재사용할 수 있는 기반을 마련합니다.


## 4. Repository 계층 분리를 통한 아키텍처 개선 

초기 개발 단계에서는 편리함을 위해 JPA 인터페이스가 도메인 리포지토리 인터페이스를 직접 구현하는 방식(`extends` 관계만 활용)을 사용할 수 있습니다.  

그러나 이는 특정 데이터베이스 기술에 대한 강한 의존성을 만들고, 테스트를 어렵게 만드는 한계가 있습니다.  

#### 변경 전 구조의 한계

```
user/ 
├── domain/ 
│   └── repository/ 
│       └── UserRepository.java (인터페이스만 존재) 
└── infrastructure/ 
    └── persistence/ 
        └── JpaUserRepository.java (JPA 인터페이스)
```

위 구조에서는 *UserRepository* 가 *JpaUserRepository* 를 직접 `extends`하거나, *UserService* 에서 *JpaUserRepository* 를 직접 사용하는 등 도메인 계층과 인프라 계층이 강하게 결합될 수 있습니다.

#### 해결책: Repository 계층의 명확한 분리 (`implements` 활용)

이 문제를 해결하기 위해 **도메인 인터페이스**, **인프라 구현체**, **기술 인터페이스**로 계층을 명확하게 분리하는 방식을 채택했습니다.   

이는 Spring의 DI와 `implements`의 장점을 극대화합니다.  

**변경 후 구조:**

```
user/ 
├── domain/ 
│   ├── model/ 
│   │   └── User.java 
│   └── repository/ 
│       └── UserRepository.java (도메인 인터페이스) 
└── infrastructure/  
    └── persistence/ 
        ├── JpaUserRepository.java (JPA 인터페이스) 
        └── UserRepositoryImpl.java (구현체)
```

요약하자면, 의존성 흐름은 다음과 같습니다:

```text
[ 인터페이스 ] UserRepository       ←   도메인에 가까운 추상화 계층

                      ↑

[ 구현 클래스 ] UserRepositoryImpl   ←   JPA 기술과의 연결, 구현체

                      ↑

[ Spring Data JPA ] JpaUserRepository ←   실제 DB 동작 담당 (Spring이 구현)
```

#### 변경된 구조의 주요 이점

이처럼 Repository 계층을 분리함으로써 얻는 이점은 다음과 같습니다:

1. **JPA 기술과 도메인 레이어의 분리:** 
	- 가장 중요한 이점라고 생각합니다. 애플리케이션의 핵심 비즈니스 로직을 담당하는 도메인 레이어가 특정 데이터베이스 기술(JPA)에 종속되지 않습니다. 
	- 만약 향후 MongoDB, Redis 등 다른 저장소로 변경하더라도 *UserRepository* 는 그대로 유지할 수 있으며, *UserRepositoryImpl* 만 해당 기술에 맞게 새로 구현하면 됩니다.
2. **도메인 주도 설계(DDD) 철학에 부합:** 
	- 인프라와 도메인 계층을 느슨하게 결합하여 유지보수성을 높입니다. 
	- 도메인 모델과 비즈니스 로직에 더욱 집중할 수 있게 됩니다.
3. **테스트 용이성 향상:** 
	- *UserService* 와 같은 상위 계층을 테스트할 때, 실제 DB 연결 없이 *UserRepository* 인터페이스의 Mock 객체를 주입하여 테스트할 수 있습니다. 
	- *UserRepositoryImpl* 자체를 테스트할 때도 *JpaUserRepository* 만 Mock 처리하면 되므로, 단위 테스트의 범위와 속도를 최적화할 수 있습니다.
4. **유연한 커스터마이징:**
	- *UserRepositoryImpl* 에서 JPA 기본 기능 외에 추가적인 캐싱, 로깅, 복잡한 쿼리 로직 등을 손쉽게 삽입하거나 변경할 수 있습니다.

```java
@Override
public Optional<User> findByEmail(String email) {
    log.info("이메일로 사용자 조회 요청: {}", email);
    // 필요에 따라 캐싱 로직 추가 가능
    return jpaUserRepository.findByEmail(email);
}
```

**한눈에 보는 구조 비교:**

| 항목        | 변경 전 (강한 결합)               | 변경 후 (느슨한 결합)                    |
| :-------- | :------------------------- | :------------------------------- |
| **구조**    | JPA 인터페이스가 도메인 인터페이스 직접 구현 | 도메인 인터페이스, JPA 인터페이스, 구현 클래스로 분리 |
| **의존성**   | 도메인 ↔ JPA 직접 연결            | 도메인 ↔ 구현체 ↔ JPA (간접 연결)          |
| **유연성**   | 낮음                         | 높음                               |
| **테스트**   | 어려움 (JPA Mock 필요)          | 쉬움 (Impl만 Mock 처리 가능)            |
| **책임 분리** | 모호                         | 명확                               |

**의존성 흐름 시각화:**

```
[Application Layer]
		↓
    UserService (도메인 서비스 사용)

[Domain Layer]
		↓
    UserRepository (Port/도메인 추상화)

---------------------------------------------------------------

[Infrastructure Layer]
		↓
	UserRepositoryImpl (Adapter - 인터페이스 구현)
		↓
	JpaUserRepository (JPA 어댑터 - Spring Data JPA 활용)
```

그렇다면, 이렇게 수립한 아키텍처 원칙들은 다른 부분에서 어떻게 적용되었을까요?  

이어지는 *[ AI_Todo 프로젝트 개발기 #3 ] 인증(Auth) 도메인 심층 구현* 편 글에서는, 이 설계 원칙을 바탕으로 `Auth` 도메인의 인증 시스템을 구현한 구체적인 과정을 확인하실 수 있습니다.

---

## 5. 아키텍처 관점에서의 이해: 헥사고날 아키텍처 vs DDD 아키텍처

Repository 계층 분리 방식은 **도메인 주도 설계(DDD)** 와 **헥사고날 아키텍처(Ports & Adapters)** 라는 두 가지 중요한 아키텍처 개념과 밀접하게 연결됩니다.   

이 둘은 상호 배타적이기보다는 상호 보완적인 관계를 가진다고 생각합니다.  

| 항목        | 헥사고날 아키텍처 (Hexagonal / Ports & Adapters)                                                               | 도메인 주도 설계 (DDD 아키텍처)                                                                                    |
| :-------- | :----------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------ |
| **핵심 개념** | 도메인 로직(핵심)과 외부 세계(입출력)를 **포트와 어댑터로 분리**                                                                | 도메인을 중심으로 소프트웨어를 **모델링**하고 **계층화**                                                                      |
| **관심사**   | **아키텍처적인 구조 분리** (입출력과 도메인 분리)                                                                         | **도메인 모델링과 책임 분리**                                                                                      |
| **대표 구조** | - Domain (Core) <br>- Port (Interface)<br>- Adapter (Impl)                                             | - Entity, Value Object,Aggregate<br>- Repository, Service, Factory 등                                    |
| **목적**    | 외부 의존성과의 느슨한 연결, 유지보수성과 테스트성 향상                                                                        | 복잡한 비즈니스 로직을 명확하게 구조화                                                                                   |
| **예시**    | - `UserRepository` = Port<br>- `UserRepositoryImpl` = Adapter<br>- `JpaUserRepository` = Infra Adapter | - `User` = Aggregate Root<br>- `UserRepository` = Domain Layer<br>- `UserService` = Application Service |


구현한 Repository 계층 분리는 이 두 아키텍처 원칙에 모두 부합합니다.

- **DDD 아키텍처 관점:** 
	- `UserRepository`는 도메인 계층의 추상화된 Repository(Port)로, 비즈니스 로직이 데이터 저장 방식에 의존하지 않도록 합니다. 
	- `User`는 핵심 도메인 모델(Entity 또는 Aggregate Root)입니다.
- **헥사고날 아키텍처 관점:** 
	- `UserRepository`는 시스템의 핵심 도메인이 외부 인프라와 통신하기 위한 **Port(인터페이스)** 역할을 합니다.
	- `UserRepositoryImpl`과 `JpaUserRepository`는 이 Port를 특정 기술(JPA)로 구현하는 **Adapter** 역할을 수행합니다.

---

## 6. 결론 및 회고: 견고한 설계를 위한 고민

이 프로젝트를 통해 `extends`와 `implements`의 기본적인 용법을 넘어, **의존성 주입과 인터페이스 기반 설계**가 실제 백엔드 개발에서 얼마나 강력한 도구인지 체감할 수 있었습니다.   

특히 Repository 계층을 명확하게 분리하고 DDD 및 헥사고날 아키텍처의 원칙을 적용함으로써, 다음과 같은 중요한 교훈을 얻었습니다.

| 항목           | 상속(extends)                        | 의존성 주입(DI, 인터페이스 기반)                     |
| :----------- | :--------------------------------- | :--------------------------------------- |
| **코드 재사용**   | 쉽다 (공통 로직 물려주기)                    | 간접적 (공통 기능은 별도로 구현 또는 라이브러리 활용)          |
| **유연성**      | 낮음 (상속 구조가 고정되어 변경이 어려움)           | 높음 (구현체를 언제든 교체·확장 가능)                   |
| **결합도**      | 높음 (부모-자식 간 강한 연결)                 | 낮음 (인터페이스를 통해 느슨한 연결)                    |
| **테스트 편의성**  | 낮음 (자식-부모 묶여 Mocking이 복잡할 수 있음)    | 높음 (Mock 객체 등 손쉽게 주입하여 독립적 테스트 가능)       |
| **설계 유도 관계** | "is-a" 관계 (진짜 상속받는 것이 자연스러울 때만 추천) | "has-a", "uses-a" 관계 (주입받는 쪽에서 인터페이스 활용) |

- **상속(`extends`)** 은 코드 재사용과 확장을 목적으로 사용되지만, 너무 많이 또는 복잡하게 사용하면 오히려 유지보수를 어렵게 할 수 있다는 걸 이해했습니다.

- **의존성 주입(DI)과 인터페이스 기반 설계**는 결합도를 낮추고, 유연성과 테스트 용이성을 높이기 위한 강력한 패턴입니다. 
	- 기능 교체, 확장, 테스트 등 실무 유지보수에서 매우 강력한 장점을 가집니다.
		- (외부 라이브러리에 의존성이 강할 때)
	- 다만, 모든 곳에 무분별하게 적용하기보다는 **추상화가 정말 필요한 지점**을 파악하고 적용하는 것이 중요하다고 생각합니다. 
	- 불필요하게 많은 추상화는 오히려 로직 변경 비용을 증가시킬 수 있다는 점을 항상 염두에 두어야 합니다.
		- ( 요구사항이 계속 바뀔경우 비용 증가... )
		- ( 변경될 확률이 적을 경우에도 불필요한 비용 증가 )


이번 프로젝트를 통해 이론적인 설계 원칙들이 실제 코드에서 어떻게 구체화되고, 어떤 이점을 가져다주는지 깊이 있게 경험할 수 있었습니다.

단순히 코드를 작성하는 것을 넘어, **더 나은 아키텍처와 유지보수 가능한 시스템을 만들기 위한 고민**의 시작점이 되었습니다. 다음 글에서는 이러한 설계 원칙 위에서 구현된 **실제 인증(Auth) 도메인**에 대해 깊이 있게 다뤄보겠습니다.
