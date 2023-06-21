---

layout: single
title: "[Django] 장고를 사용하면서 쓰면 유용한 vs코드 익스텐션"
categories: Django
tag: [Python,"[Django] VScode extensions for python & Django "]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---

# PEP-8 ?


파이썬으로 프로그래밍을 할 때 필수는 아니지만 PEP-8를 따르는게 좋다.   
예를 들어 변수를 설정할 때 자바처럼 카멜케이스를 쓰는 게 아니라 스네이크 표기법을 쓴다.    

> 카멜케이스 : myNameIsYohan 
> 스네이크케이스 : my_name_is_yohan
> 케밥케이스 : my-name-is-yohan
> 파스칼표기법 : MyNameIsYohan

지금 이렇게 내용을 정리하는 이유는 첫 번째 프로젝트를 만들고 이력서를 여기 저기 넣었는다 전부다 탈락했다 ^^     

<iframe src="https://giphy.com/embed/OPU6wzx8JrHna" width="480" height="398" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/spongebob-squarepants-sad-OPU6wzx8JrHna"></a></p>
Pycham을 사용할 경우 PEP-8에 안 맞으면 노란색 물결표시가 나면서 귀찮게 계속 표기사 되는데
VS코드를 사용하면 그런게 따로 나오지 않아서 신경을 안 쓰게 된다. (아무래도 혼자 프로젝트를 진행하다 보니 더 주의하지 않았던거 같다....)     

<iframe src="https://giphy.com/embed/1o1unIxJepjwepb6WS" width="480" height="360" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>
<p><a href="https://giphy.com/gifs/laff-tv-omg-shocked-shook-4VUgpQ9FiYEBCA9wM1"></a></p>  

서류탈락? 오히려 좋다!     
이렇게 부주의했던 부분을 다시 상기할 수 있었고    
개인적으로 이런 경험을 하면 더 오기가 생기고 더 잘하고 싶게 만들어져서 좋은 계기가 되었다고 생각한다.    

물론 PEP-8 저거 하나로 떨어진 이유는 아니겠지만 문제점을 하나씩 고치다보면 더 좋은 개발자에 한 발자국 더 다가갈 수 있다고 생각한다.    

문제는 이 PEP-8 을 다 외울 수 도 없고 어디 좋은 방법 없을까 찾아보니 여으윽시!
이 세상에 답은 이미 나와있고 나는 찾기만 하면된다 

자동으로  PEP-8 을 해결해주는 익스텐션 발견 

<div style="width:480px"><iframe allow="fullscreen" frameBorder="0" height="270" src="https://giphy.com/embed/702ybfQFkrkrWnIByR/video" width="480"></iframe></div>

## Python Black Formatter

![](https://i.imgur.com/Y4JFYrg.png)

해당 익스텐션을 설치한 후
```python
pip install black
```

터미널에서 아래와 같이 실행
```python
black {파일 또는 폴더 이름}
```

하지만 이 방법도 은근히 귀찮아서 다른 사람들은 어떻게 쓰는지 찾아보니
여으으윽시! 

## Black Formatter auto settings

json 파일을 만들어서 컨트롤이 가능하다.

우선 `.git ignore` 처럼 `.vscode` 폴더를 만들어준다.
여기서 settings.json 파일은 만든후 수동으로 컨드롤이 가능하다

![](https://i.imgur.com/XXafaTf.png)

예를 들어 json 파일에 아래와 같이 넣으면 위의 설정을 컨트롤 가능하다.

```python
editor.formatOnSave
```

혹시라도 json 파일이 설정파일을 컨트롤되지 않는다면 vscode 설정에서
Open workspace settings(json)을 선택해주면 된다.

![](https://i.imgur.com/igq0kaZ.png)

이전에 만들었던 파일은 아래와 같이 입력하면 모든 파일을 검사하면서 수정해준다.

```python
black .
```

![](https://i.imgur.com/bWpXIOp.png)

## Flake 8

혹시 다른 파이썬 개발자들은 어떻게 환경 설정하는지 궁금해서 찾아봤는데 Flask8 도 많이 쓰는거 같다.

이전에 Black Formatter 는 저장하는 순간 pep-8의 규칙에 맞게 알아서 변환되면서 저장되는데
Flake 8은 프로그램을 실행하지 않고 문법 오류나 코딩 규약 위반 등을 분석해준다.

한 마디로 너무나 편해진다

<div style="width:480px"><iframe allow="fullscreen" frameBorder="0" height="270" src="https://giphy.com/embed/MtGY4FcgMmgzFXSXca/video" width="480"></iframe></div>


Black Formatter 와 같이 사용하면 충돌이 날 수 있어서 다음과 같은 셋팅을 해주는게 좋다.

```python
settings.json

{
    "editor.formatOnSave": true,
    "python.formatting.provider": "black",
    "python.formatting.blackArgs": [
        "--line-length",
        "88"
    ],
    "python.linting.enabled": true,
    "python.linting.lintOnSave": true,
    "python.linting.flake8Enabled": true,
    "python.linting.flake8Args": [
        "--max-line-length",
        "88"
    ],

    "[python]": {
        "editor.codeActionsOnSave": {
            "source.organizeImports": true
        }
    }
}
```

88은 검사 코드 줄이 88자의 최대라는 의미다.

![](https://i.imgur.com/Tgz1CsP.png)




![](https://i.imgur.com/b426sI8.png)

설치하자마자 엄청난 오류들이 뜨는 데 사실상 오류가 아닌 경우도 많다.

![](https://i.imgur.com/6S8xbJE.png)

아주 엄격하게 검사를 해준다.

![](https://i.imgur.com/vGPBDlP.png)
위와 같이 오류가 아닌 경우에도 오류라고 나오는 경우가 있는데 이건 또 따로 세분화해서 설정 가능하다.
