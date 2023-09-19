---
layout: single
title: "[Django] ORM"
categories: Django_Basic
tags:
  - Python
  - ORM
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
## 장고에서 사용하는 ORM은 뭘까?

 장고를 사용하다 보면 모델을 사용하게 되고 모델을 사용하다보면 당연히 모르는게 생기고 그래서 모델에 대해 잘 몰라서 찾다보면 ORM에 대해 계속 접하게 된다.
 
 대충 그 상황만 해결하고 넘어가거나 대충 알기만하고 정확하게 알지 못 하는게 지속되면 어느 순간 이런 상황이 지겨워져서 그냥 제대로 짚고 넘어가고 싶어진다.
 그게 차라리 편해지기 때문이다. 

## ORM

전통적으로 데이터베이스를 사용하는 프로그램들은 데이터베이스의 데이터를 조회하거나 저장하기 위해 쿼리문을 사용해야 했다. 이 방식은 여전히 많이 사용되고 있는 방식이지만 몇 가지 단점이 있다. 
- 개발자마다 다양한 뭐리문이 만들어지고, 잘못된 쿼리는 시스템의 성능을 저하 시킬 수 있다.
- 테이터 베이스를 변경할 경우 (MySQL -> 오라클) 쿼리문을 모두 해당 데이터베이스의 규칙에 맞게 수정해야된다.


ORM(Object Relational Mapping)을 사용하면 데이터베이스의 테이블을 모델화하여 사용하기 때문에 위에서 열거한 SQL방식의 단점이 모두 없어진다. ORM을 사용하면 개발자별로 독특한 쿼리문이 만들어질 수가 없고 또 쿼리를 잘못 작성할 가능성도 낮아진다. 그리고 데이터베이스 종류가 변경되더라도 쿼리문이 아닌 모델을 사용하기 때문에 프로그램을 수정할 필요가 없다.
- SQL 인젝션과 같은 보안 취약점을 방지하는데도 도움이 된다. ORM은 입력 값을 자동으로 이스케이프 하고, 쿼리를 매개 변수화해서 악의적인 데이터 베이스 조작을 어렵게 만든다.
- 객체 지향 프로그램의 이점을 활용할 수 있다 -> 데이터를 클래스와 메서드로 다루고 코드를 더 쉽게 이해하고 유지보수 할 수 있다.
[출처](https://wikidocs.net/70650)

### ORM에 단점은 없나?

1. 성능 이슈: ORM은 SQL을 추상화하기 때문에 일부 상황에서는 직접 SQL 쿼리를 작성하는 것보다 성능이 떨어질 수 있다. 복잡한 쿼리나 대량의 데이터 처리와 같은 경우에는 직접 SQL을 작성하는 것이 더 효율적일 수 있다. 장고 ORM은 성능 향상을 위한 몇 가지 기능을 제공하지만, 모든 상황에 최적화된 해결책이 아닐 수 있다.
    
2. 학습 곡선: ORM은 별도로 배워야 하는 개념과 기술이므로, 처음 사용하는 개발자에게는 학습 곡선이 존재할 수 있다. ORM의 사용 방법을 익히는 데 시간과 노력이 필요할 수 있다.
    
3. 제한된 데이터베이스 기능: ORM은 대부분의 데이터베이스 기능을 추상화하지만, 모든 기능을 지원하지는 않을 수 있다. 특정 데이터베이스에 특화된 기능을 활용하려면 직접 SQL 쿼리를 작성해야 할 수 있다.

## 쿼리셋

장고 ORM은 데이터베이스와 상호작용하기 위한 쿼리를 생성하고 실행할 수 있는 인터페이스인 쿼리셋(QuerySet)을 제공한다. 
쿼리셋은 데이터베이스로부터 조회한 데이터의 컬렉션으로, 다양한 데이터 조작 작업을 수행할 수 있도록 도와준다. 
여기에는 필터링, 정렬, 그룹화, 연관 관계 처리 등이 포함된다. 다음은 쿼리셋에 대한 몇 가지 중요한 개념과 예시다:

1. 쿼리셋 생성:
    
    `books = Book.objects.all()  # 모든 Book 객체를 가져오는 쿼리셋 생성`
    
2. 필터링:
    
    `published_books = Book.objects.filter(status='published')  # status가 'published'인 Book 객체를 필터링`
    
3. 정렬:
    
    `sorted_books = Book.objects.order_by('-publish_date')  # publish_date를 내림차순으로 정렬한 Book 객체를 가져오는 쿼리셋 생성`
    
4. 연관된 객체 조회:
    
    `author_books = Book.objects.filter(author__name='John')  # author의 name이 'John'인 Book 객체를 필터링`
    
5. 그룹화 및 집계:
    
    `from django.db.models import Count  book_count_by_author = Book.objects.values('author').annotate(total_books=Count('id'))  # 작가별로 책의 개수를 집계하는 쿼리셋 생성`
    
6. 쿼리셋 체이닝:
    
    `recent_books = Book.objects.filter(status='published').order_by('-publish_date')[:5]  # 조건을 연속적으로 체이닝하여 최근 5권의 출판된 책을 가져오는 쿼리셋 생성`
    
7. 쿼리셋 실행:
    
    `books = Book.objects.all()  # 쿼리셋 생성 for book in books:     print(book.title)  # 쿼리셋을 순회하며 결과 데이터에 접근`
    

[공식문서](https://docs.djangoproject.com/en/4.2/topics/db/queries/)
