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


## Build : Custom Field Ordered List