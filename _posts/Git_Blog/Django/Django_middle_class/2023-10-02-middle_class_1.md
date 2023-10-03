---
layout: single
title: " [Django DRF] middle_class (1)"
categories: Django_DRF_middle_class
tags:
  - Python
  - DRF
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Setup Logging in Development

Log level describes the severity of the messages that the logger will handle.     
Log levels include one debug, meaning low level system information for debugging purposes.

## LOG LEVEL

- INFO : general system information.
- WARNING : describing a minor problem that has occurred.
- ERROR : describing a major problem that has occurred.
- CRITICAL : describing a critical problem that has occurred.

## HANDLERS

- Handlers are responsible for dispatching log messages to appropriate destinations such as the console output files, email or external services.

## FILTERS

- Filters are used to filter out log messages based on some criteria, such as log levels.
- As not all messages need to be stored or transmitted.
- Filters can be applied to both loggers and handlers.

## FORMATTERS

- Define what our log statements will look like.
- Ultimately, a log record needs to be rendered as text
- Describe the exact format of the text.
- By default, logs are in a log record format and need to be converted before being sent to anyone.



```python
LOGGING = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "verbose": {
            "format": "%(levelname)s %(name)-12s %(asctime)s %(module)s "
            "%(process)d %(thread)d %(message)s"
        }
    },
    "handlers": {
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "formatter": "verbose",
        }
    },
    "root": {"level": "INFO", "handlers": ["console"]},
}
```

1. `version`: 로깅 설정 파일의 버전을 지정한다. 주로 1로 설정된다.
2. `disable_existing_loggers`: 기존 로거를 비활성화할지 여부를 결정하는 옵션. False로 설정하면 기존 로거들은 비활성화되지 않는다.
3. `formatters`: 로그 메시지의 출력 형식을 정의하는 섹션 "verbose"라는 포매터로 정의했으며, 이 포매터는 로그 메시지의 다양한 속성(로그 레벨, 로거 이름, 시간, 모듈 이름, 프로세스 ID, 스레드 ID, 메시지)을 지정된 형식에 따라 출력할 수 있다.
   - `%(levelname)s`: 로그 레벨을 출력한다. 예를 들어, "INFO", "DEBUG", "WARNING" 등이 될 수 있다.
   -  `%(name)-12s`: 로거의 이름을 출력한다. `-12s`는 12자리를 할당하고 왼쪽으로 정렬하라는 의미한다. 이를 통해 로거 이름이 12자 이하인 경우 오른쪽에 공백이 추가된다.
   - `%(asctime)s`: 로그 메시지가 생성된 시간을 출력한다. 이 시간 형식은 기본적으로 "%Y-%m-%d %H:%M:%S,%f" 다. 예를 들어, "2023-10-02 15:30:45,123456"와 같이 표시된다.
   - `%(module)s`: 로그 메시지를 생성한 모듈의 이름을 출력한다.
   - `%(process)d`: 현재 프로세스의 ID를 출력한다.
   - `%(thread)d`: 현재 스레드의 ID를 출력한다.
   - `%(message)s`: 실제 로그 메시지의 내용을 출력한다. 이 부분은 로그 메시지의 텍스트 내용을 출력한다.
4. `handlers`: 로그 메시지를 처리하는 핸들러를 정의하는 섹션 "console"로 핸들러를 설정했는데, 이 핸들러는 로그 메시지를 콘솔에 출력하는 역할을 한다. 이 핸들러는 로그 레벨이 DEBUG 이상인 메시지를 출력한다.
5. `root`: 루트 로거의 설정을 정의하는 섹션 
   "level"은 루트 로거의 로그 레벨을 나타내며, 여기서는 INFO로 설정했다. "handlers"는 해당 로거에 적용할 핸들러를 나타낸다. 여기서는 "console" 핸들러를 사용하여 루트 로거에 로그 메시지를 출력한다.