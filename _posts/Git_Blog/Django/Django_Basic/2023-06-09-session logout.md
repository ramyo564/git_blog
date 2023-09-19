---
layout: single
title: "[Django] 세션 자동 로그아웃?"
categories: Django_Basic
tags:
  - Python
  - 세션
  - 타이머
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 장고 세션 자동 로그아웃

```python
#Django Session Timeout Code

SESSION_COOKIE_AGE = 3600 # 60 min

SESSION_SAVE_EVERY_REQUEST = True
```

settings.py 아무곳에 해당 코드를 넣어주면 해당 시간이 지난 후 자동으로 로그아웃된다.