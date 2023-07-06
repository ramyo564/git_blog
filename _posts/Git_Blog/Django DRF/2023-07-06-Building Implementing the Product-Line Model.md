---

layout: single
title: " [Django DRF] Build : Implementing the Product-Line Model "
categories: Django
tag: [Python,"[Django DRF] Project eCommerce RESTful API","[Django DRF] Build : Implementing the Product-Line Model"]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---

# 모델 변경

![](https://i.imgur.com/5ySNHoN.png)

이전 모델에서 달라진 점은 제품에서는 여러 제품 라인을 가질 수 있다.     
예를 들어 티셔츠는 여러가지 색상이나 사이즈를 가질 수 있다.     
이런 부분은 특정 제품의 제품 라인이 된다.      
또한 제품을 활성화 또는 비활성화 하여 is_active가 true로 설정된 제품만 필터링 하여 고객에게 보여줄 수 있도록 업데이트 했다.

![](https://i.imgur.com/a4iPFC5.png)

```python
pytest
```

![](https://i.imgur.com/lQNvpkj.png)

터미널에서 또 오류가 났는데 찾아보니 `__init__.py` 가 문제였다.

![](https://i.imgur.com/2yvlZAz.png)

그 동안  `__init__.py`  파일을 마주쳤지만 정확히는 몰랐는데 한 세 번쯤 마주친거 같으니 좀 더 알아보기 로 했다.

## `__init__.py` 는 뭘까?

Python 에서 `__init__.py` 파일은 해당 디렉토리를 패키지로 인식하기 위해 사용되는 특수한 파일이다.     
디렉토리에 `__init__.py` 파일이 없으면 Python은 해당 디렉토리를 패키지로 간주하지 않고 모듈로 처리한다.     

### 그럼 모듈과 패키지는 또 뭐지?

- 모듈
	- Python 코드가 담긴 파일
	- 변수, 함수, 클래스, 상수등을 포함
	- 다른 Python 프로그램에서 모듈을 임포트해 사용할 수 있음
		- 예시 -> main.py, random.py, detetime.py 등
- 패키지
	- 패키지는 관련된 모듈들을 디렉토리 형태로 구성한 것
	- 패키지는 하나 이상의 모듈을 포함하고 있으며, 하위 패키지를 포함할 수 있다.
	- 패키지는 모듈들을 논리적으로 구조화하고 관리하기 위해 사용된다.
	- 패키지는 `__init__.py` 파일을 포함해서 패키지임을 나타내야한다.
		- 예시 -> numpy, requests, django 등

예를 들어 'math.py' 는 모듈이다.      
이 모듈을 다른 Python 프로그램에서 임포트해서 사용할 수 있다.

패키지는 일반적으로 디렉토리 구조로 표현된다.    
예를 들어 'numpy' 패키지는 'numpy' 라는 디렉토리에 관련된 모듈들이 포함되어 있다.    
이렇게 패키지를 사용하면 관련된 모듈들을 그룹화하고 구조적으로 관리할 수 있다.     


따라서 모듈은 개별적인 코드 파일이고, 패키지는 관련된 모듈들의 집합이라고 할 수 있다.

## Build : Product - Line Serializer

![](https://i.imgur.com/aNmQ4dB.png)

Product 모델에서 테이블이 필요해서 사용했던 FK 키를 구현해준다.     
변수명은 product 다.

## Analysis : User Stories

# Appendix B: User Story Analysis

|   |   |
|---|---|
|**Overview**|   |
|**Title**|Customer Product Browsing Behaviour|
|**Description**|Identifying a basic customer behavioural interaction when browsing products|
|**Actors and Interfaces**|Customer / Web User|
|**Initial Status and Preconditions**|Assumption that customer enters from the root/homepage|
|**Basic Flow**|   |
|Step1: Land on the homepage<br><br>Step2: Select a product category<br><br>Step3: Browse, select, and view individual products related to the selected category<br><br>Step4: Select and view individual product-line details|   |
|**Alternative Flow(s)**|   |
|§  Customers may prefer searching for the product using keyword search features<br><br>§  Customers may navigate to a product from an internal promotional panel|   |

|               |                 |                                                                             |
| ------------- | --------------- | --------------------------------------------------------------------------- |
| **User Type** | **Activity**    | **User Story**                                                              |
| Web User      | Browse Products | Step1: Land on the homepage                                                 |
|        ''       |          ''       | Step2: Select a product category                                            |
|         ''      |         ''        | Step3: Select and view individual products related to the selected category |
|     ''          |     ''            | Step4: Inspect individual product-line details                              |


|                                                   |            |
| ------------------------------------------------- | ---------- |
| **Functional Specifications**                     | **Status** |
| Return all categories                             |   Check     |
| Return all products filtered by category          |         |
| Return individual product and product-line by (x) |          |


## Build : Category Filter with Extra Actions

[DRF ViewSet](https://www.django-rest-framework.org/api-guide/viewsets/)

### ViewSet vs ModelViewSet

ViewSet은 클래스를 상속해서 CRUD 기능을 직접 구현해야 한다.     
반면 ModelViewSet은 ViewSet을 확장해서 연동된 CRUD 기능을 자동으로 제공해주는 클래스다.    
따라서 기본 CRUD 메서드가 자동으로 구현 되어 있고 필터링, 검색, 정렬등의 기능도 자동으로 제공된다.


```python
  
class ProductViewSet(viewsets.ModelViewSet):
    '''
    A simple Viewset for viewing all products

    '''
    queryset = Product.objects.all()
    @extend_schema(responses=ProductSerializer)
    def list(self, request):
        serializer = ProductSerializer(self.queryset, many=True)
        return Response(serializer.data)
```

![](https://i.imgur.com/zzuBc7Y.png)

이전에 GET 기능 밖에 없던 게 put, delete 등 기본적인 기능들이 생겼다.     

### GenericViewSets or ModelViewSets

[차이점](https://stackoverflow.com/questions/25125959/django-rest-framework-generics-or-modelviewsets)

GenericViewSets 은 GenericAPIView 를 상속하지만 기본 동작의 구현은 제공하지 않는다.    
get_object, get_queryset 만 제공한다.

ModelViewSets 은 GenericAPIView 에 상속되며 다양한 구현을 포함한다.     
목록, 검색, 생성, 업데이트 등 기본동작이 다 구현되어있으며 커스텀도 가능하다.     

### 정규식 regex 패턴

[파이썬 정규식](https://docs.python.org/ko/3/howto/regex.html)

파이썬에 내장된 프로그래밍 언어다.    

![](https://i.imgur.com/dMgtUIq.png)


파이썬에서 정규 표현식(Regular Expression)은 텍스트 패턴을 검색, 추출 또는 대체하는 데 사용되는 강력한 도구다.       
정규 표현식은 문자열에서 특정 패턴을 식별하고 일치하는 텍스트를 처리하는 데 유용하다.        

파이썬에서는 `re` 모듈을 사용하여 정규 표현식을 지원한다.       
`re` 모듈은 다양한 함수와 메서드를 제공하여 문자열에 대한 패턴 매칭, 검색, 추출, 대체 등의 작업을 수행할 수 있다.      

파이썬 정규 표현식은 특수한 문자와 메타 문자를 사용하여 패턴을 정의한다. 예를 들어, 다음과 같은 정규 표현식 패턴을 사용하여 이메일 주소를 검색할 수 있다

```python
import re

text = "이메일 주소는 example@example.com입니다."
pattern = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b'

match = re.search(pattern, text)
if match:
    email = match.group()
    print("이메일 주소:", email)
else:
    print("이메일 주소를 찾을 수 없습니다.")

```

위의 예제에서는 `\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b` 패턴을 사용하여 이메일 주소를 검색한다.      
이 정규 표현식은 이메일 주소의 일반적인 형식을 나타내며, 이메일 주소를 찾으면 해당 주소를 출력한다.     

정규 표현식은 다양한 패턴을 정의할 수 있으며, 텍스트 처리에 유용한 많은 기능을 제공한다.     
따라서 파이썬에서 정규 표현식을 익히고 활용하면 텍스트 처리 작업을 더욱 효과적으로 수행할 수 있습니다.


```python
from rest_framework.decorators import action

class ProductViewSet(viewsets.ViewSet):
    '''
    A simple Viewset for viewing all products
    '''
    queryset = Product.objects.all()

    @extend_schema(responses=ProductSerializer)
    def list(self, request):
        serializer = ProductSerializer(self.queryset, many=True)
        return Response(serializer.data)

    @action(
        methods=["get"],
        detail=False,
        url_path=r"category/(?P<category>\w+)/all",
        url_name="all",
            )

    def list_product_by_category(self, request, category=None):
        '''
        An endpoint to return products by category
        '''
        serializer = ProductSerializer(
            self.queryset.filter(category__name=category), many=True
            )
        return Response(serializer.data)
```

`@action` 은 DRF의 데코레이터로 ViewSet에 사용자 정의 액션을 추가하는 데 사용된다.     
해당 액션은 'list_product_by_category' 메소드를 나타낸다.      

methods 는 'list_product_by_category' 액션의 HTTP 메소드를 지정한다. 위의 예시에는 GET 메소드만 허용되도록 설정되어있다.     

detail은 액션이 개별 개체에 개한 작업인지 컬렉션에 대한 작업인지를 나타낸다.     
detail=False로 설정되어 있기 때문에 컬렉션에 대한 작업이다.     

`url_path` 및 `url_name`: 이들은 엔드포인트의 URL 경로를 정의하는 데 사용된다. 위의 예시에서는 `category/(?P<category>\w+)/all`로 설정되어 있으며, `(?P<category>\w+)`는 카테고리를 나타내는 동적 요소를 나타낸다. `url_name`은 이 엔드포인트를 식별하기 위한 이름을 정의한다.

`list_product_by_category` 메소드: 이 메소드는 요청과 카테고리 매개변수를 받아들이고, 해당 카테고리에 속하는 제품들을 쿼리하여 시리얼라이저를 통해 직렬화한 후, 응답으로 반환한다. `ProductSerializer`는 해당 제품들을 직렬화하기 위해 사용된다.

즉, 위의 코드는 `/category/{category}/all` URL을 통해 GET 요청이 들어오면, 해당 카테고리에 속하는 모든 제품들을 반환하는 엔드포인트를 정의하고 있다.

|                                                   |            |
| ------------------------------------------------- | ---------- |
| **Functional Specifications**                     | **Status** |
| Return all categories                             |   Checked     |
| Return all products filtered by category          |   Checked      |
| Return individual product and product-line by (x) |          |


#### `@action -> detail -> 컬렉션?`

`detail` 매개변수는 Django REST Framework에서 사용되는 데코레이터 `@action`의 옵션 중 하나다.     이 옵션은 액션이 컬렉션에 대한 작업인지 개별 개체에 대한 작업인지를 지정한다.     

- 컬렉션(Collection): 컬렉션은 여러 개체로 구성된 그룹이나 목록을 의미한다. 예를 들어, 모든 사용자를 가져오는 엔드포인트는 컬렉션에 대한 작업이다. 컬렉션에 대한 작업은 일반적으로 모든 개체를 반환하거나 필터링된 결과를 반환하는 등의 작업을 수행한다.
    
- 개별 개체(Object): 개별 개체는 컬렉션 내에서 특정한 단일 개체를 의미한다. 예를 들어, 특정 사용자를 가져오는 엔드포인트는 개별 개체에 대한 작업이다. 개별 개체에 대한 작업은 일반적으로 개체의 세부 정보를 반환하거나 수정, 삭제하는 등의 작업을 수행한다.
    

`detail` 매개변수의 값에 따라 액션의 동작이 달라진다:

- `detail=False`: `detail=False`로 설정된 경우, 액션은 컬렉션에 대한 작업이다. 예를 들어, `GET` 요청을 사용하여 모든 사용자를 가져오는 엔드포인트를 생성할 수 있다.
    
- `detail=True`: `detail=True`로 설정된 경우, 액션은 개별 개체에 대한 작업이다. 예를 들어, 특정 사용자를 조회하거나 수정하는 엔드포인트를 생성할 수 있다. 이때 URL 경로에 개별 개체의 식별자가 포함되어야 한다.
    

예를 들어, 아래의 코드는 사용자 컬렉션에 대한 액션과 개별 사용자에 대한 액션을 보여준다:

```python
from rest_framework.decorators import action
from rest_framework.response import Response

class UserViewSet(viewsets.ViewSet):
    queryset = User.objects.all()

    @action(detail=False, methods=['get'])
    def list_users(self, request):
        users = self.queryset
        serializer = UserSerializer(users, many=True)
        return Response(serializer.data)

    @action(detail=True, methods=['get'])
    def retrieve_user(self, request, pk=None):
        user = get_object_or_404(self.queryset, pk=pk)
        serializer = UserSerializer(user)
        return Response(serializer.data)

```

위의 예시에서 `list_users` 액션은 모든 사용자를 반환하는 컬렉션에 대한 작업이다. `retrieve_user` 액션은 특정 사용자를 조회하는 개별 개체에 대한 작업이다. `list_users` 액션은 `detail=False`로 설정되어 있으므로, URL에 개별 사용자의 식별자가 필요하지 않다. `retrieve_user` 액션은 `detail=True`로 설정되어 있으므로, URL에 개별 사용자의 식별자가 포함되어야 한다.

따라서, `detail` 매개변수를 사용하여 컬렉션과 개별 개체에 대한 엔드포인트를 구분하고 해당 작업을 수행할 수 있다.


### DRF에서 정규 표현식 Regex를 사용하는 이유

1. 동적인 URL 처리: 정규 표현식을 사용하여 동적인 URL을 처리할 수 있습니다. URL에서 특정 부분을 동적으로 추출하고 해당 값을 사용하여 데이터 조회 및 조작을 수행할 수 있습니다. 예를 들어, 사용자의 ID나 이름과 같은 동적인 값으로 개별 사용자에 접근하는 경우, 정규 표현식을 사용하여 URL을 구성합니다.
    
2. 유효성 검사: 정규 표현식을 사용하여 URL 형식을 제한하고 유효성을 검사할 수 있습니다. 예를 들어, 이메일 주소 형식이 유효한지 확인하거나, 숫자로 된 ID 형식이 요구되는 경우, 정규 표현식을 사용하여 URL 형식을 제한하고 유효성을 검증할 수 있습니다.
    
3. 유연한 URL 패턴: 정규 표현식을 사용하면 다양한 URL 패턴을 유연하게 정의할 수 있습니다. URL에 대한 다양한 매칭 조건과 형식을 지정할 수 있어서, 다양한 요구사항을 처리하고 다양한 URL 경로를 지원할 수 있습니다.
    
4. 코드 가독성 및 유지보수성: 정규 표현식을 사용하여 URL을 정의하면 코드의 가독성과 유지보수성이 향상됩니다. 정규 표현식을 사용하면 복잡한 URL 패턴을 간결하게 표현할 수 있고, 다른 개발자들이 이해하기 쉬운 코드를 작성할 수 있습니다.
    

따라서, DRF에서 정규 표현식을 사용하여 URL 패턴을 정의하는 것은 동적인 URL 처리, 유효성 검사, 유연한 URL 패턴 등을 위해 필요한 기능을 제공하기 위한 목적으로 사용됩니다.


## Build : Filter Return Single Product

![](https://i.imgur.com/XtQ0fma.png)

Slug 를 추가해준다.     
슬러그는 검색 엔진 (SEO) 최적화를 개선하는데 도움된다.     
또한 동일한 내용을 가진 여러 개의 URL을 구분해 콘텐츠 중복 문제를 방지하거나 검색 엔진에서의 문제를 최소화 할 수 있다.

models.py
![](https://i.imgur.com/08ZsXnR.png)

views.py
![](https://i.imgur.com/tJahe9u.png)

![](https://i.imgur.com/yiw22u7.png)

## Build : Editing Multiple Models in the Django Admin Site

![](https://i.imgur.com/6bhQNPz.png)

TabularInline 을 이용해 해당 어드민을 한 꺼번에 보게 만들려고 한다.

![](https://i.imgur.com/EYHoZ0H.png)

[공홈 InlineModelAdmin objects ](https://docs.djangoproject.com/en/4.2/ref/contrib/admin/)

admin.py

![](https://i.imgur.com/1THI5EF.png)


![](https://i.imgur.com/fxMLjvr.png)


![](https://i.imgur.com/wVkOZyp.png)

p3 으로 출력했을 때 브랜드와 카테고리, 제품정보등을 반환하지만 제품 인라인 정보들은 실제로 반환하지 않는 것을 알 수 있다.    
사실 Serializer에 Productline에 대한 어떠한 참조도 없다.     
이 때문에 현재 시점에서는 제품 데이터를 가져오더라도 실제로는 해당 데이터를 직렬화 할 수 있는 방법이 없다.     

![](https://i.imgur.com/SnNIhAV.png)

현재 retrieve를 보면 실행했던 쿼리를 볼 수 있다.    
이 쿼리는 제품에 대한 모든 것을 반환한다.    
실제로는 제품 테이블에서 모든 것을 반환하고 있다.    
여기에서 개별 제품을 필터링하기 위한 필드를 생성하고 있다.    
Product 테이블에서 brand 와 category 관계를 생성했기 때문에 해당 정보가 반환된다.
이 부분을 다시 해결해 보자


## Build : Handling Reverse Relationships in Serializers

이전에 product line 데이터에 접근 할 수 없었는데 왜냐하면 Product line table에 관계설정이 되어 있었기 때문이다.     
이를 해결하기 위해 역참조를 사용하면 된다.     

models.py      
![](https://i.imgur.com/ZI9AOZE.png)

역참조에서 사용할 변수명을 product_line으로 지정해준다.     

serializers.py
![](https://i.imgur.com/sF6dKEl.png)

다수의 데이터를 serialize 화 하고자 할 때 many=Ture를 사용하면된다.    ![](https://i.imgur.com/Bo8Aesr.png)

product line 데이터가 잘 들어온다.

## Build : Serializer Field Name Mapping and Flattening 

models.py
![](https://i.imgur.com/SOJynSk.png)

serializers.py
![](https://i.imgur.com/tQPRLTg.png)


![](https://i.imgur.com/sTMS1tM.png)

위와 같이 데이터를 컨트롤 할 수 있다.

![](https://i.imgur.com/2m8U3yH.png)

이렇게 지저분하게 나오는 것도 아래와 같이 컨트롤 가능하다.
![](https://i.imgur.com/e7Rx2p0.png)

![](https://i.imgur.com/MvweVgZ.png)

[더 자세한 정보](https://www.django-rest-framework.org/api-guide/relations/)

## Performance : Multiple Queries, Towards Eliminating the N+Query Problem

쿼리를 보기 위해서는

```python
from django.db import connection

class ProductViewSet(viewsets.ViewSet):

    queryset = Product.objects.all()
    lookup_field = "slug"
    
    def retrieve(self, request, slug=None):
        serializer = ProductSerializer(self.queryset.filter(slug=slug), many=True)

		x = Response(serializer.data)
        print(connection.queries)

        return x

```

![](https://i.imgur.com/znEmeTp.png)


이런 식으로 확인할 수 있지만 매우 지저분하다.     

```python
pip install sqlparse
pip install Pygments
```

https://pypi.org/project/sqlparse/
https://pypi.org/project/Pygments/

위 라이브러리를 간단하게 소개하자면    
### sqlparse & Pygments

#### **sqlparse:** 
`sqlparse`는 SQL 문을 파싱하고 분석하기 위한 편리한 방법을 제공하는 Python 라이브러리다.      

이 라이브러리는 SQL 쿼리와 문장을 구조화된 형식으로 파싱하여 SQL 코드를 프로그래밍적으로 다루고 조작하는 데 사용한다.     

`sqlparse`는 다양한 SQL 방언을 처리할 수 있으며, 테이블, 열, 키워드 등의 정보를 SQL 문에서 추출하는 기능을 제공한다.     

또한 적절한 들여쓰기와 개행을 추가하여 SQL 코드를 서식화할 수 있다.      `sqlparse`는 SQL 편집기, 코드 생성기, 데이터베이스 관리 도구 등에서 동적으로 SQL 코드를 분석하거나 수정해야 할 때 주로 사용한다.

다음은 `sqlparse`를 사용하여 SQL 문을 파싱하고 서식화하는 예제다:

```python
import sqlparse

sql = "SELECT * FROM customers WHERE age > 18;"
parsed = sqlparse.parse(sql)
formatted = sqlparse.format(parsed[0], reindent=True, keyword_case='upper')
print(formatted)
****
```

Result
```python
SELECT *
FROM customers
WHERE age > 18;
```

`sqlparse`를 사용하여 SQL 문이 파싱되고 적절한 들여쓰기와 대문자화가 적용된 서식화된 SQL 코드가 출력된다.

#### **Pygments:** 
`Pygments`는 강력한 구문 강조(문법 강조) 라이브러리로, 다양한 프로그래밍 언어, 마크업 언어 및 설정 파일 형식에 대한 구문 강조 기능을 제공한다. 
`Pygments`는 소스 코드를 입력으로 받아 구문 강조가 적용된 HTML, LaTeX 또는 터미널 출력을 생성할 수 있다. 

다양한 스타일과 테마를 지원하여 강조 표시된 코드의 모양을 사용자 정의할 수 있다. 

`Pygments`는 텍스트 편집기, 문서 시스템 및 코드 프리젠터 등에서 코드 스니펫의 가독성과 시각적인 매력을 높이기 위해 널리 사용된다.

다음은 `Pygments`를 사용하여 Python 코드를 강조 표시하는 예제다:


```python
from pygments import highlight
from pygments.lexers import PythonLexer
from pygments.formatters import TerminalFormatter

code = '''
def greet(name):
    print("Hello, " + name + "!")

greet("World")
'''

highlighted_code = highlight(code, PythonLexer(), TerminalFormatter())
print(highlighted_code)

```

Result 
```python
[34mdef[39;49;00m [
```


https://pygments.org/
https://pygments.org/docs/lexers/#pygments.lexers.sql.SqliteConsoleLexer

![](https://i.imgur.com/m8sQRR9.png)


```python
from drf_spectacular.utils import extend_schema
from rest_framework.decorators import action
from rest_framework import viewsets
from rest_framework.response import Response
from django.db import connection
from .models import Brand, Category, Product
from .serializers import BrandSerializer, CategorySerializer, ProductSerializer
from pygments import highlight
from pygments.formatters import TerminalFormatter
from pygments.lexers import SqlLexer
from sqlparse import format

# Create your views here.

class ProductViewSet(viewsets.ViewSet):
    '''
    A simple Viewset for viewing all products

    '''
    queryset = Product.objects.all()
    lookup_field = "slug"

    def retrieve(self, request, slug=None):
        serializer = ProductSerializer(self.queryset.filter(slug=slug), many=True)
        x = self.queryset.filter(slug=slug)
        sqlformatted = format(str(x.query), reindent=True)
        print(highlight(sqlformatted, SqlLexer(), TerminalFormatter()))
        return Response(serializer.data)


...
```
![](https://i.imgur.com/0c9Hz2X.png)


![](https://i.imgur.com/Zb5cWyL.png)

현재 여기서 쿼리문은 6개가 실행된다.

```python
def retrieve(self, request, slug=None):
	serializer = ProductSerializer(self.queryset.filter(slug=slug), many=True)
	data = Response(serializer.data)
	queries = list(connection.queries)
	print(len(queries))
	
	return data
```

![](https://i.imgur.com/QuTmSFR.png)

```python
def retrieve(self, request, slug=None):
	serializer = ProductSerializer(self.queryset.filter(slug=slug), many=True)
	data = Response(serializer.data)
	queries = list(connection.queries)

	for query in queries:
		sqlformatted = format(str(query["sql"]), reindent=True)
		print(highlight(sqlformatted, SqlLexer(), TerminalFormatter()))
	return data
```

![](https://i.imgur.com/f3oKtU1.png)

이런 식으로 쿼리문을 확인할 수 있다.    

현재 쿼리가 6개가 실행되는데 쿼리문은 적으면 적을 수록 성능에 좋다.    
### select_related()

![](https://i.imgur.com/jEXpHST.png)

[select_related() 참고](https://docs.djangoproject.com/en/4.2/ref/models/querysets/)

![](https://i.imgur.com/5AAnK28.png)

아래와 같이 쿼리는 3개로 줄었고 LEFT OUTER JOIN 으로 쿼리문이 들어간다.

![](https://i.imgur.com/N0kw0Aw.png)


![](https://i.imgur.com/xs1JKMr.png)

![](https://i.imgur.com/Zgps4Rh.png)

쿼리는 2개가 실행되지만 selected_related는 역참조는 적용이 안된다고 한다.    
하지만 Django 에서는 다양한 쿼리셋 메서드가 제공되며 그 중 하나가 prefetch_related 다.     

prefetch_related 는 다대다 관계나 역참조와 같은 다대다 관계에서도 작동한다.
[prefetch_related 참고](https://docs.djangoproject.com/en/4.2/ref/models/querysets/)
![](https://i.imgur.com/1Im8L3M.png)

## Build : Creating Custom Managers and QuerySet Methods

models.py
```python 
class ActiveManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset.filter(is_active=True)

class Product(models.Model):
    name = models.CharField(max_length=100)
    slug = models.SlugField(max_length=255)
    description = models.TextField(blank=True)
    is_digital = models.BooleanField(default=False)
    brand = models.ForeignKey(Brand, on_delete=models.CASCADE)
    category = TreeForeignKey("Category", on_delete=models.SET_NULL, null=True, blank=True)
    is_active = models.BooleanField(default=False)

    # default manager
    object = models.Manager()

    # custom manager
    isactive = ActiveManager()

    def __str__(self):
        return self.name
```

views.py
![](https://i.imgur.com/9mIcNhp.png)

모델에서 커스텀한 메니저 변수 isactive를 view 에서 object를 isactive로 갈아 껴준다.

이 때 모델에 설정해 놓은 active를 비활성화 하게 되면 어드민 페이지에서 사라지게 된다.    
따라서 default 매니저도 함께 선언 해줘야 비활성화된 데이터도 함께 보는게 가능하다.

![](https://i.imgur.com/PjSnMZk.png)

### 다른 접근 방법

models.py
```python
class ActiveManager(models.Manager):
    # def get_queryset(self):
    #     return super().get_queryset().filter(is_active=True)
    def isactive(self):
        return self.get_queryset().filter(is_active=True)

class Product(models.Model):
    name = models.CharField(max_length=100)
    slug = models.SlugField(max_length=255)
    description = models.TextField(blank=True)
    is_digital = models.BooleanField(default=False)
    brand = models.ForeignKey(Brand, on_delete=models.CASCADE)
    category = TreeForeignKey("Category", on_delete=models.SET_NULL, null=True, blank=True)
    is_active = models.BooleanField(default=False)

    # # default manager
    # object = models.Manager()
    # # custom manager
    # isactive = ActiveManager()

    objects = ActiveManager()

    def __str__(self):

        return self.name
```


![](https://i.imgur.com/lcd3nkT.png)

이런 식으로 오버라이딩 없이도 사용 가능하다.

### 그럼 models.Manager 와 models.QuerySet 은 뭐가 다르지?
`models.Manager`와 `models.QuerySet`은 Django ORM의 일부로, 데이터베이스와 상호작용하기 위해 사용되는 클래스입니다.

**`models.Manager`:**

- `models.Manager`는 Django 모델 클래스의 기본 매니저입니다. 모델 클래스에는 기본 매니저가 항상 있으며, `objects`라는 이름으로 접근할 수 있습니다.
- `models.Manager`는 데이터베이스 쿼리를 생성하고 실행하는 기능을 제공합니다.
- 모델 클래스의 매니저를 통해 데이터베이스에서 데이터를 조회, 생성, 수정 및 삭제할 수 있습니다.
- 기본적으로 모든 객체를 검색하는 `all()` 메서드와 유사한 기능을 제공합니다.
- 사용자 정의 매니저를 추가하여 추가적인 데이터베이스 쿼리나 특정 조건의 객체를 조회하는 메서드를 구현할 수 있습니다.
- 장점: 모델 클래스와 밀접하게 연결되어 객체의 생성과 관련된 메서드를 통합적으로 관리할 수 있습니다.
- 단점: 특정 조건에 맞는 데이터 조회에 제약이 있을 수 있습니다.

**`models.QuerySet`:**

- `models.QuerySet`은 데이터베이스로부터 조회된 객체의 집합을 나타냅니다.
- 데이터베이스 쿼리를 생성하고 실행하는 데 사용되며, 조회된 객체들을 다루는 다양한 메서드를 제공합니다.
- `models.Manager`를 통해 접근 가능하며, 많은 유용한 메서드들을 제공하여 데이터를 필터링, 정렬, 제한하고 조작할 수 있습니다.
- 메서드 체이닝을 통해 복잡한 쿼리를 구성할 수 있습니다.
- `models.QuerySet`은 지연 실행되는 특성을 가지며, 실제로 데이터베이스 쿼리를 실행하기 전까지는 데이터를 가져오지 않습니다.
- 장점: 데이터를 효과적으로 조작하고 필터링할 수 있는 다양한 메서드를 제공합니다. 지연 실행을 통해 성능을 최적화할 수 있습니다.
- 단점: 모델 클래스와 분리되어 있으므로, 객체의 생성과 관련된 메서드는 `models.Manager`를 통해 제공되지 않습니다.

요약하자면, `models.Manager`는 모델 클래스와 밀접한 관련이 있으며 객체의 생성과 관련된 메서드를 제공하는 반면, `models.QuerySet`은 데이터 조회와 조작에 특화된 메서드를 제공하여 유연한 데이터 처리를 가능하게 합니다. 두 클래스는 함께 사용하여 데이터베이스와 상호작용하는 Django ORM의 핵심 요소입니다.

## Build : Custom Field Ordered List

### order number 만들기

![](https://i.imgur.com/eoprcmH.png)


![](https://i.imgur.com/cFeOnVm.png)

[모델 필드 django](https://docs.djangoproject.com/en/4.2/ref/models/fields/)

![](https://i.imgur.com/sD3K0Za.png)
오더 넘버가 마이너스도 표시 되는 것 보다는 양수로 표시되니 PositiveIntegerField() 로 사용하는게 낫다.     

![](https://i.imgur.com/4S4nAh5.png)

### 커스텀 필드 만들기

위와 같이 만들어 보면 각 제품마다 넘버가 다르게 들어가서 편하긴 한데
유저 입장에서 드래그를 사용하기도 그렇고 수동으로 선택하는 등 불편한 점이 많다.      

![](https://i.imgur.com/47zmW8X.png)

![](https://i.imgur.com/2bEiEFD.png)

```python
from django.db import models
from django.core import checks

class OrderField(models.PositiveIntegerField):

    description = "Ordering field on a unique field"

    def __init__(self, unique_for_field=None, *args, **kwargs):
        self.unique_for_field = unique_for_field
        super().__init__(*args, **kwargs)

    def check(self, **kwargs):
        return [
            *super().check(**kwargs),
            *self._check_for_field_attribute(**kwargs)
        ]

    def _check_for_field_attribute(self, **kwargs):
        if self.unique_for_field is None:
            return [checks.Error("OrderField must define")]
```

우선 OrderField는 models.PositiveIntegerField 를 상속해서 구현했고 양수 값으로 저장하는 필드로서 모델 내에서 순서를 표현하기 위한 정수 값을 저장하는 역할을 한다.     

`OrderField` 클래스는 추가적으로 `unique_for_field` 매개변수를 받을 수 있다. `unique_for_field`는 해당 필드가 고유해야 하는 다른 필드의 이름을 나타낸다. 예를 들어, `unique_for_field='category'`라고 설정하면, 동일한 `category` 필드 값을 갖는 객체들 간에만 `OrderField` 값이 고유해야 한다.      

`check` 메서드는 Django의 필드 유효성 검사를 수행하기 위해 오버라이드되었다. 이 메서드는 필드가 모델에서 올바르게 구성되었는지 확인하는데 사용된다. `super().check(**kwargs)`를 통해 상위 클래스의 `check` 메서드를 호출한 후, `_check_for_field_attribute` 메서드를 호출하여 `unique_for_field` 매개변수의 유효성을 확인한다.     

`_check_for_field_attribute` 메서드는 `unique_for_field`의 값을 확인하여 유효성 검사를 수행한다. 위의 코드에서는 `self.unique_for_field in None`이라는 비교를 수행하고 있다. 하지만 올바른 비교는 `self.unique_for_field is None` 가 되어야 한다. `unique_for_field` 값이 `None`인 경우에는 `checks.Error()`를 반환하여 유효성 검사에서 오류를 표시한다.       

### 모델 필드 유효성 검사

fields.py
![](https://i.imgur.com/spI59yQ.png)
order의 객체는 현재 product를 가리키고 있다.

models.py
![](https://i.imgur.com/Ib49b3S.png)

elif 문에서 모델에 있는 모든 필드를 리슽로 불러온 후 "product" 란에 실제 필드가 만약에 없다면 위와 같은 에러가 나오게 만든다.    
다시 말하자면 마이그레이션을 할 때 필드에 없는 내용을 order 필드에 "product" 혹은 ProductLine 에 있는 내용이 아닌, 예를 들어 "asasdasd"  를 사용한다면 에러가 나온다.      

## 자동으로 오더넘버 만들기

![](https://i.imgur.com/64ckTOD.png)

![](https://i.imgur.com/MNXDlk8.png)
Django 모델의 pre_save 메서드를 오버라이드해서 사용자 정의 동작을 추가한다.      
pre_save 메서드는 모델 객체가 데이터베이스에 저장되기 전에 실행되는 단계에서 호출된다.     

model_instance 는 현재 저장되는 모델 객체의 인스턴스다.    
add 는 레코드가 추가 되었는지 여부를 나타내는 불리언 값이다.    

위 코드에서 pre_save 메서드가 호출되면 model_instance 객체의 self.attname 속성 값을 확인한다. 이 속성은 OrderField 필드의 이름을 나타낸다.     
만약 self.attname 속석의 값이 None 이면 해당 필드에 값이 저장되지 않은 경우이므로 이럴 때는 self.model.objects.all() 을 사용해서 해당 모델의 모든 객체를 조회한다.     

그 후 query 딕셔너리를 생성하고 self.unique_for_field 값을 필드의 실제 값으로 설정해서 쿼리를 생성한다.    

예를 들어, `self.unique_for_field`가 `'product'`이고 모델 객체의 `product` 필드 값이 `<Product: p1>`인 경우 `query`는 `{'product': <Product: p1>}`과 같이 생성된다.

그 후, `query_set.filter(**query)`를 사용하여 위에서 생성한 쿼리를 이용해 필터링된 쿼리셋을 얻는다. 이 쿼리셋은 `unique_for_field` 필드의 값이 `model_instance`의 해당 필드 값과 일치하는 모든 객체를 포함하게 된다.      

만약 `ObjectDoesNotExist` 예외가 발생한다면, 즉, 위의 필터링된 쿼리셋이 비어있는 경우에는 `value`를 1로 설정한다.     

위의 코드에서 `value`는 최종적으로 `pre_save` 메서드에서 반환되는 값이다. 이 값은 모델 객체의 `self.attname` 필드 값이 `None`인 경우에 사용되며, 필터링된 쿼리셋이 비어있을 경우에는 1로 설정된다.     

마지막으로, 만약 `self.attname` 필드 값이 `None`이 아닌 경우, 즉, 필드에 값이 이미 지정되어 있는 경우에는 `super().pre_save(model_instance, add)`을 호출하여 원래의 `pre_save` 메서드를 실행하고 그 결과를 반환한다.      

### 오더넘버 중복 없애기 

[모델 인스턴스 레퍼런스](https://docs.djangoproject.com/en/4.2/ref/models/instances/)

![](https://i.imgur.com/FbDEeF4.png)


![](https://i.imgur.com/D5wbgaz.png)
- `latest(field_name=None)` 메서드: `latest()` 메서드는 쿼리셋을 통해 특정 필드를 기준으로 최신 객체를 가져오는 메서드다. `field_name` 매개변수를 통해 최신 객체를 판별할 필드를 지정할 수 있다. 기본적으로는 모델의 `Meta` 클래스에서 `get_latest_by` 속성이 설정된 필드를 기준으로 최신 객체를 가져온다. `latest()` 메서드는 쿼리셋을 정렬하여 가장 최근에 생성된 객체를 반환한다. 만약 해당 필드를 가진 객체가 없을 경우 `ObjectDoesNotExist` 예외가 발생한다.      
    
- `attname`: `attname`은 Django의 필드 속성 중 하나입니다. 각 필드는 데이터베이스 테이블에 저장되는 값의 컬럼 이름을 가지고 있다. `attname`은 필드의 컬럼 이름을 나타내는 속성이다. 일반적으로 필드의 이름과 동일하지만, 필드 타입에 따라 변환되는 경우가 있을 수 있다.     
    

위의 코드에서 `latest()` 메서드는 `qs` 쿼리셋에서 `attname`을 기준으로 가장 최근 객체를 가져온다. `attname`은 `OrderField` 필드의 컬럼 이름을 가리키며, `last_item`에는 가장 최근 `OrderField` 값을 가진 객체가 저장된다. 이후 `value` 변수는 `last_item.order + 1`을 할당하여 새로운 `OrderField` 값을 생성한다. 이렇게 함으로써 `OrderField` 값이 자동으로 증가하도록 구현된다.     

마지막으로, `else` 블록은 `model_instance` 객체에 `attname` 필드가 이미 존재하는 경우 `super().pre_save(model_instance, add)`를 호출하여 기본 `pre_save()` 메서드를 실행한다.     

즉, 위의 코드는 `pre_save()` 메서드를 오버라이드하여 `OrderField`의 값이 없는 경우 이전 `OrderField` 값 중 가장 최근 값을 가져와 1을 증가시켜 새로운 `OrderField` 값을 생성하고, 값이 이미 있는 경우 기본 `pre_save()` 메서드를 호출하여 원래 동작을 수행하는 로직이다.     
  
하지만 프로덕트 라인을 2개 만든 후 둘 다 오더 넘버를 각 각 1, 1 로 해도 적용된다.      
오더 넘버는 pk 처럼 유니크 값이 되야 되므로 중복을 방지해줘야 한다.     

![](https://i.imgur.com/VtXb8QF.png)
`def clean_fields(self, exclude=None):`는 Django 모델의 메서드 중 하나인 `clean_fields()`를 오버라이드했다. 이 메서드는 모델의 필드 유효성 검사를 수행하는 데 사용된다.     

`clean_fields()` 메서드는 `ModelForm`의 유효성 검사에서 호출되며, 각 필드의 유효성을 검증하기 전에 실행된다. 기본적으로 `clean_fields()` 메서드는 `super().clean_fields(exclude=exclude)`를 호출하여 부모 클래스의 `clean_fields()` 메서드를 실행하고 필드 유효성을 검사한다.     

위의 코드에서 `clean_fields()` 메서드는 부모 클래스인 `super().clean_fields(exclude=exclude)`를 호출하여 필드의 기본 유효성 검사를 수행한 후 추가적인 검증을 진행한다.     

`query_set`은 `ProductLine` 모델에서 현재 객체의 `product` 필드와 동일한 값을 가지는 모든 `ProductLine` 객체의 쿼리셋을 가져온다. 이후 반복문을 통해 쿼리셋 내의 객체들과 현재 객체를 비교하고, 다른 객체의 `id`가 현재 객체의 `id`와 다르고 `order` 필드 값이 중복된 경우 `ValidationError`을 발생시킨다. 이는 중복된 `order` 값을 가지는 객체가 존재할 경우 유효성 검사 실패로 처리된다.       

따라서, `clean_fields()` 메서드를 사용하여 `ProductLine` 모델의 필드 유효성 검사를 커스터마이즈하고 중복된 `order` 값을 가지는 경우 예외를 발생시킬 수 있다.       

self 는 현재 ProductLine 객체를 나타내고 해당 메서드가 속한 클래스의 인스턴스를 참조하는데 사용된다.     
이를 활용해 self 는 ProductLine 객체의 속성 및 메서드에 접근할 수 있다.      

query_set 변수는 ProductLine 모델의 product 필드가 현재 self.product와 동일한 값을 가지는 모든 ProductLine 객체를 조회한 쿼리 셋을 생성하고 query_set 변수는 해당 쿼리셋을 할당 받아 사용한다.      

for object in query_set 은 query_set 내의 각 ProductLine 객체를 순회하면서 object는 query_set에 있는 각 ProductLine 객체를 차례대로 가리킨다.     

즉, `self`는 현재 `ProductLine` 객체를 가리키고, `query_set`과 `object`는 `ProductLine` 모델에서 해당 필드 값과 관련된 다른 객체들을 조회하고, 순회하기 위해 사용되는 변수들이다.     


## Testing : Pytest Adopts and Common Commands

```python
pytest --cov
```

![](https://i.imgur.com/TIHGONa.png)

현재 field.py에서 miss가 17개 models.py 에서 6개, views.py 에서 10개가 나온다.     

--cov를 터미널에서 자동으로 동작하게 하려면 아래와 같이 설정을 추가해주면 된다.     

pytest.ini
```python
[pytest]

DJANGO_SETTINGS_MODULE = drfecommerce.settings.local

python_files = test_*.py

addopts = --cov
```

또한 pytest.ini 가 아닌 setup.cfg 로 설정하고
`[tool:pytest]` 로 변경하면 전체적으로 관리가 가능하다.     

1. `pytest.ini`:
    - `pytest` 프레임워크의 구성을 지정하는 파일이다.
    - `pytest.ini` 파일은 프로젝트 루트 디렉토리에 생성되며, 모든 하위 디렉토리에서 공통으로 사용된다.
    - 주로 `pytest`의 설정 옵션, 플러그인 구성, 테스트 세션 관리 등과 관련된 설정을 지정한다.
    - `pytest.ini` 파일은 `[pytest]` 섹션을 사용하여 설정 옵션을 지정한다.
2. `setup.cfg`:
    - 프로젝트의 빌드, 패키징, 테스트 등을 관리하는 `setuptools`의 설정 파일이다.
    - `setup.cfg` 파일은 주로 프로젝트의 메타데이터, 패키지 구성, 의존성 관리, 빌드 스크립트 등을 지정한다.
    - `setup.cfg` 파일은 `[tool:pytest]` 섹션을 사용하여 `pytest` 관련 설정을 지정한다. 이 섹션을 사용하여 `pytest` 테스트 실행에 대한 옵션, 환경 변수, 플러그인 구성 등을 설정할 수 있다.
    - `setup.cfg`는 또한 `setuptools`의 다른 기능들을 위한 다양한 섹션들을 포함할 수 있다.

따라서, `pytest.ini`는 주로 `pytest` 프레임워크 자체의 구성을 지정하는 데 사용되고, `setup.cfg`는 프로젝트의 빌드 및 패키징 설정에서 `pytest` 관련 설정을 지정하는 데 사용된다.

변경 후 전체적으로 검사가 시행된다.     
![](https://i.imgur.com/4UOv6Xq.png)

만약 개별 테스트를 원할 경우는 이렇게 사용하면 된다.     
![](https://i.imgur.com/trjI6Xc.png)

test_category_get 만 테스트를 진행하고 싶다면 터미널에 `pytest -k test_category_get`  이런 식으로 사용 가능하다.

![](https://i.imgur.com/kenmoM5.png)

추가로 addpts = --cov -x 로 설정하면 모든 테스트를 진행하지 않고 테스트가 실패한 시점부터 멈춘다.          
테스트할게 많을 경우 유용하게 쓸 수 있다.      
또한 pytest -s 를 사용하면 테스트때 디버깅겸 print 문도 출력되니 상황에 맞게 사용하면 좋다.     


## Testing : Models

새롭게 만든 모델을 테스트하기 전에 coverage html을 업데이트해주기 위해 터미널에서 아래의 명령어를 입력해준다. 

```python
coverage html
```

![](https://i.imgur.com/fS6KL0s.png)

![](https://i.imgur.com/TPIAglH.png)


여기에서 모델을 확인 했을 경우 클릭하면 아래와 같이 상세히 잘 나온다.

![](https://i.imgur.com/7H9FwlY.png)

test_models.py

![](https://i.imgur.com/BKfOMaj.png)

factories.py
![](https://i.imgur.com/zI0msY1.png)
conftest.py
![](https://i.imgur.com/J3j3RjB.png)


테스트 결과

![](https://i.imgur.com/C3jl0y4.png)

### 10%의 문제는 무엇일까?

우선 적으로 models.py 의 clean_fields 부분을 clean 으로 변경해줘야 한다.
![](https://i.imgur.com/z6tNkgG.png)
1. clean_fields():
    - `clean_fields`는 Django 모델에서 기본 제공되는 메서드다.
    - 이 메서드는 각 필드의 유효성을 독립적으로 검사다.
    - 필드 유효성 검사는 필드의 `clean_<field_name>()` 메서드를 호출하여 수행된다.
    - `clean_fields`는 단일 필드에 대한 유효성 검사에 사용되며, 필드 간의 상호작용은 처리하지 않는다.
    - 필드 유효성 검사 중에 발생하는 오류는 해당 필드에 대한 `ValidationError`으로 반환된다.
2. clean():
    - `clean`은 Django 모델에서 제공하는 일반적인 유효성 검사 메서드다.
    - `clean`은 여러 필드 간의 상호작용을 처리할 수 있다.
    - 이 메서드는 모든 필드의 유효성을 함께 검사할 수 있으며, 필요한 경우 여러 필드를 동시에 사용하여 유효성 검사를 수행할 수 있다.
    - `clean` 메서드는 필드 유효성 검사 이외에도 다른 데이터 유효성 검사, 외부 리소스와의 상호작용, 데이터 변환 등을 수행할 수 있다.
    - 필드 유효성 검사 중에 발생하는 오류는 해당 필드에 대한 `ValidationError`으로 반환되며, 일반적인 오류는 `ValidationError`을 통해 전달된다.

따라서, `clean`은 보다 유연한 유효성 검사를 수행할 수 있는 메서드이며, 필드 간의 상호작용이 필요한 경우에 유용하게 사용됩니다. `clean_fields`는 각 필드의 독립적인 유효성 검사에 사용되며, 간단한 필드 유효성 검사에 적합하다.      

그리고 test 폴더에 test_models.py 에 ProductLine 모델 테스트 코드를 만들어준다.     

![](https://i.imgur.com/oRZrtcL.png)
1. `test_str_method(self, product_line_factory)`
    - 이 테스트 메서드는 `product_line_factory`를 사용하여 `ProductLine` 객체를 생성하고, 해당 객체의 `__str__()` 메서드가 예상한 문자열을 반환하는지 확인한다.
    - 예상한 문자열은 "12345" 다.
    - `assert` 문을 사용하여 실제 반환된 문자열과 예상한 문자열을 비교한다.
2. `test_duplicate_order_values(self, product_line_factory, product_factory)`
    - 이 테스트 메서드는 `product_factory`와 `product_line_factory`를 사용하여 `Product`와 `ProductLine` 객체를 생성한다.
    - 첫 번째 `product_line_factory` 호출로 `order` 값이 1인 `ProductLine` 객체를 생성한다.
    - 두 번째 `product_line_factory` 호출은 `order` 값이 1이고 동일한 `Product` 객체를 가리키는 `ProductLine` 객체를 생성하고, `clean()` 메서드를 호출한다.
    - clean 메서드는 ProductLine 모델의 중복된 order 값이 발생하는 지를 검새하준다.
    - product_line_factory를 사용해서 생성한 ProductLine 객체들 중 현재 객체 order 값이 있ㅇ즐 경우 ValidationError 예외를 발생하고 해당 예외가 발생하는지 테스트한다. -> ProductLine 모델의 필드 유효성을 검사하고 중복된 값이 있는지 확인할 수 있다.
    - `pytest.raises(ValidationError)` 문을 사용하여 `ValidationError` 예외가 발생하는지 확인한다.
    - with은 pytest 에서 에외를 검증하는 방법 중 하나이다. with 을 사용해서 예외가 발생하더라더 예외를 정리하고 처리할 수 있다.     

### with
`with` 구문은 파이썬에서 컨텍스트 관리자를 사용할 때 일반적으로 사용되는 구문이다. 컨텍스트 관리자는 리소스 할당, 정리, 에러 처리 등과 같이 어떤 작업을 수행하는 동안 관련된 설정을 제공하고 정리하는 역할을 한다. `with` 구문을 사용하면 컨텍스트 관리자를 쉽게 사용할 수 있다.     

일반적인 `with` 구문의 사용법은 다음과 같다.     
```python
with 컨텍스트_관리자 as 변수:
    # 작업 수행
```

`컨텍스트_관리자`는 `__enter__()`와 `__exit__()` 메서드를 구현한 객체다. `as 변수` 부분은 선택적으로, 컨텍스트 관리자에서 반환되는 값을 변수에 할당할 수 있다.     

`with` 구문을 사용하면 `__enter__()` 메서드가 호출되어 설정 작업을 수행하고, `__exit__()` 메서드가 호출되어 정리 작업을 수행한다. 또한, 예외가 발생하면 `__exit__()` 메서드에서 예외 처리를 수행할 수 있다.     

예시를 통해 `with` 구문의 사용법을 이해해보자. 가장 일반적인 예시 중 하나는 파일을 열고 작업을 수행한 후 파일을 닫는 것이다.         

```python
with open('file.txt', 'r') as file:
    data = file.read()
    # 파일 작업 수행

# with 블록을 벗어나면 파일은 자동으로 닫힘

```
위의 예시에서 `open()` 함수는 파일 객체를 반환하고, 이 파일 객체는 `__enter__()` 메서드와 `__exit__()` 메서드를 구현한 컨텍스트 관리자다. `with` 구문을 사용하면 파일을 열고 작업을 수행한 후, `with` 블록을 벗어나면 자동으로 파일이 닫힌다.      

다른 예시로는 데이터베이스 연결을 다루는 경우를 들 수 있다:
```python
import sqlite3

with sqlite3.connect('database.db') as conn:
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM table')
    # 데이터베이스 작업 수행

# with 블록을 벗어나면 연결이 자동으로 닫힘

```

위의 예시에서 `sqlite3.connect()` 함수는 데이터베이스 연결을 반환하고, 이 연결 객체는 `__enter__()` 메서드와 `__exit__()` 메서드를 구현한 컨텍스트 관리자다. `with` 구문을 사용하면 데이터베이스에 연결한 후 작업을 수행하고, `with` 블록을 벗어나면 자동으로 연결이 닫힌다.     

이와 같이, `with` 구문을 사용하면 컨텍스트 관리자를 효율적이고 안전하게 사용할 수 있으며, 리소스의 할당 및 정리를 자동화할 수 있다.     


factories.py
![](https://i.imgur.com/NGSy5cD.png)



![](https://i.imgur.com/wHSCa1L.png)

### product_factory 를 사용했을 때와 사용하지 않았을 때의 예

#### 사용하지 않았을 때의 경우

```python
def test_duplicate_order_values():
    # 1. 제품 생성
    product = Product.objects.create(name="Product 1")

    # 2. 중복된 주문 순서를 가지는 상품 라인 생성
    product_line1 = ProductLine.objects.create(order=1, product=product)
    product_line2 = ProductLine.objects.create(order=1, product=product)

    # 테스트 결과 확인
    assert product_line1.order == 1
    assert product_line2.order == 1  # 중복된 주문 순서가 생성됨

```

#### 사용했을 때의 경우
```python
def test_duplicate_order_values(product_factory):
    # 1. 제품 생성
    product = product_factory()

    # 2. 중복된 주문 순서를 가지는 상품 라인 생성
    product_line1 = ProductLine.objects.create(order=1, product=product)
    product_line2 = ProductLine.objects.create(order=1, product=product)

    # 테스트 결과 확인
    assert product_line1.order == 1
    assert product_line2.order == 1  # 중복된 주문 순서가 생성됨

```


## Testing : API Client End to End testing

test_endpoints.py
```python
class TestProductEndpoints:
    endpoint = "/api/product/"

    def test_return_all_products(self, product_factory, api_client):
        # Arrange
        product_factory.create_batch(4)
        # Act
        response = api_client().get(self.endpoint)
        # Assert

        assert response.status_code == 200
        assert len(json.loads(response.content)) == 4

    def test_return_single_product_by_slug(self, product_factory, api_client):
        obj = product_factory(slug="test-slug")
        response = api_client().get(f"{self.endpoint}{obj.slug}/")
        assert response.status_code == 200
        assert len(json.loads(response.content)) == 1

    def test_return_products_by_category_slug(
        self, category_factory, product_factory, api_client
    ):
        obj = category_factory(slug="test-slug")
        product_factory(category=obj)
        response = api_client().get(f"{self.endpoint}category/{obj.slug}/")

        assert response.status_code == 200
        assert len(json.loads(response.content)) == 1
```

- `test_return_single_product_by_slug`: 슬러그를 기준으로 단일 제품을 반환하는지 확인하는 테스트다. `product_factory`를 사용하여 슬러그가 "test-slug"인 제품을 생성하고, API 클라이언트를 통해 해당 슬러그를 포함한 엔드포인트에 GET 요청을 보낸다. 응답 상태 코드가 200인지 확인하고, 응답 내용의 길이가 1인지 확인한다.
    
- `test_return_products_by_category_slug`: 카테고리 슬러그를 기준으로 제품을 반환하는지 확인하는 테스트다. `category_factory`를 사용하여 슬러그가 "test-slug"인 카테고리를 생성하고, 해당 카테고리에 속하는 제품을 `product_factory`를 사용하여 생성한다. API 클라이언트를 통해 해당 카테고리 슬러그를 포함한 엔드포인트에 GET 요청을 보낸다. 응답 상태 코드가 200인지 확인하고, 응답 내용의 길이가 1인지 확인한다.

views.py

```python
class ProductViewSet(viewsets.ViewSet):
    '''
    A simple Viewset for viewing all products

    '''
    # queryset = Product.isactive.all()
    queryset = Product.objects.all().isactive()
    lookup_field = "slug"

    def retrieve(self, request, slug=None):
        serializer = ProductSerializer(
         self.queryset.filter(slug=slug).select_related("category", "brand"),
            many=True
            )
        data = Response(serializer.data)
        queries = list(connection.queries)
        print(len(queries))

        for query in queries:
            sqlformatted = format(str(query["sql"]), reindent=True)
            print(highlight(sqlformatted, SqlLexer(), TerminalFormatter()))
        return data

    @extend_schema(responses=ProductSerializer)
    def list(self, request):
        serializer = ProductSerializer(self.queryset, many=True)
        return Response(serializer.data)

    @action(
        methods=["get"],
        detail=False,
        url_path=r"category/(?P<slug>[\w-]+)",
        )

    def list_product_by_category_slug(self, request, slug=None):
        '''
        An endpoint to return products by category
        '''

        serializer = ProductSerializer(
            self.queryset.filter(category__slug=slug), many=True
            )
        return Response(serializer.data)
```

슬러그 같은 경우 - 혹은 _ 가 사용되므로 정규식을 `r"category/(?P<slug>[\w-]+)"` 이런 식으로 바꿔야 한다 \w 만 사용할 경우 알파벳과 숫자만 허용되서 오류가 난다.

[파이썬 정규식](https://docs.python.org/ko/3/howto/regex.html)
