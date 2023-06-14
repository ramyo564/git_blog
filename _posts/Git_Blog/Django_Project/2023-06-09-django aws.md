---

layout: single
title: "[Django] AWS에 서버 올리기"
categories: Django
tag: [Python,"[Django] AWS "]
toc: true
toc_sticky: true
author_profile: false
sidebar:

---
# 서버를 올려보자

[Deploying a Django application to Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-django.html)

우선 AWS 로 아이디를 만들면 Root account를 볼 수 있는데 어플리케이션 배포를 할 때 root account를 사용하는건 별로 추천되지 않는다.
보안 강화 및 권한 제어, 작업 추적과 분리에 대한 이유로 IAM user를 만들어서 사용한다.

## IAM user 생성하기

오른쪽 상단에 보안 자격 증명을 클릭해준다.
![](https://i.imgur.com/2CCJbT4.png)

![](https://i.imgur.com/cyGJpsb.png)
그 후 왼쪽의 사용자를 누르면 나오는 상자에서 다시 오른쪽 상단에 사용자 추가를 눌러준다.

![](https://i.imgur.com/fOC5wen.png)

![](https://i.imgur.com/SSlD0hL.png)

정말 많은게 있지만 AdministratorAccess 만 체크해준다.
그럼 성공적으로 만들어진다.

## 파이썬 환경 설정

![](https://i.imgur.com/vKR3HZz.png)

awsebcli 를 설치할 때는 가상환경이 아닌 로컬에 설치해야된다.
`pip install awsebcli`

그 후 이전에 만든 프로젝트의 requirements.txt 에 버전을 뽑아와준다.
`pip freeze requirements.txt'

기존 루트폴더에 .ebsxtensions 폴더를 하나 만들어준다.
그 후 django.config 라는 환경 설정 폴더를 만들어준다.

```python
option_settings: aws:elasticbeanstalk:container:python: WSGIPath: greatkart.wsgi:application aws:elasticbeanstalk:environment:proxy:staticfiles: /static: static
```

![](https://i.imgur.com/XhPSVnb.png)


`greatkart` 는 모듈네임으로 처음에 장고를 시작할 때 앱 폴더의 이름으로 쓰면 된다.
그리고 여태까지 작업한 static 파일들을 한 곳에 모아준다.
`python manage.py collectstatic` 를 실행하면 된다.

그리고 `eb init -p python-3.x django-greatkart-app` 로 앱을 만들어준다.
공홈에는 3.7로 나와있긴한데 3.11도 나와있다.
그 후 계정 ID를 입력한다 
오르쪽 상단에 계정 ID : xxxx-xxxx-xxxx 의 숫자 형태로 있다.

![](https://i.imgur.com/2okyrrd.png)

해당 계정 숫자 ID를 입력해주고 아까 만들어둔 IAM 유저를 사용한다.

그 후 보안자격 증명을 클릭
![](https://i.imgur.com/IwzMaOw.png)

그 다음 엑세스키 생성
![](https://i.imgur.com/8fnjV2y.png)

![](https://i.imgur.com/u6Y2Ix0.png)

![](https://i.imgur.com/wMV3bEZ.png)

엑세스키 만들기 완성

그후 터미널에서 eb init 실행

![](https://i.imgur.com/Yj4P3ZO.png)

보통 이렇게 넘어가는데 나는 갑자기 지역이 웨스트로 잡혀서
다시 만들었다.
설정을 본인이 원하는데로 맞춘후
`eb create 원하는이름`  으로 가상환경을 만들어준다.
(시간이 좀 오래걸린다.)

그리고 역시 에러

![](https://i.imgur.com/zrrJuF6.png)
`ERROR: ServiceError - Create environment operation is complete, but with errors. For more information, see troubleshooting documentation.`

## ERROR   Instance deployment failed. For details, see 'eb-engine.log'.

2023-06-09 16:56:56    ERROR   Instance deployment failed. For details, see 'eb-engine.log'.
2023-06-09 16:57:00    ERROR   [Instance: i-016d570f7670243d0] Command failed on instance. Return code: 1 Output: Engine execution has encountered an error..
2023-06-09 16:57:00    INFO    Command execution completed on all instances. Summary: [Successful: 0, Failed: 1].
2023-06-09 16:58:02    ERROR   Create environment operation is complete, but with errors. For more information, see troubleshooting documentation.

ERROR: ServiceError - Create environment operation is complete, but with errors. For more information, see trou

첫 번째 발생한 에러다. 해당 부분을 살펴보기위해 로그를 살펴봤다.

```
2023/06/09 16:56:56.277542 [ERROR] update processes [web healthd nginx cfn-hup] pid symlinks failed with error parse pid  to uint32 failed with error:strconv.ParseUint: parsing "": invalid syntax
2023/06/09 16:56:56.277553 [ERROR] An error occurred during execution of command [app-deploy] - [Track pids in healthd]. Stop running the command. Error: update processes [web healthd nginx cfn-hup] pid symlinks failed with error parse pid  to uint32 failed with error:strconv.ParseUint: parsing "": invalid syntax

```

![](https://i.imgur.com/jlIotji.png)

대충 에러를 보니 초기 설정을 잘못해서 인스턴스를 못 만들고 있는거 같았다.

그래서 현재 잘못 생성된 인스턴스를 삭제하고 초기부터 다시 설정해줬다.

이전에 한 번 사용해서 그런건지 우선 생성된 `.elasticbeanstalk`  폴더의 `config.yml` 을 살펴보면 `default_region` 이 west로 되어 있었다.
이 부분을 서울로 바꿔주고 맨 처음에 잘못 설정했던 파이썬 버젼도 맞춰주었다.

```python
branch-defaults:
  main:
    environment: null
global:
  application_name: django-greatkart-app
  branch: null
  default_ec2_keyname: null
  default_platform: Python 3.11
  default_region: us-west-2
  include_git_submodules: true
  instance_profile: null
  platform_name: null
  platform_version: null
  profile: null
  repository: null
  sc: git
  workspace_type: Application
```

초기 설정이 아니라서 그런지 계속 디폴트로 위와 같이 깔려서 처음 설치후 다시 초기화 설정을 해주었다.
초기화 설정은 아래 코드를 입력하면된다.

```code
eb init --interactiv
```

이렇게 실행해주고 이제 내 환경 설정에 맞게 선택해주면 알아서 해당 파일 정보가 바뀐다.

![](https://i.imgur.com/Yu9ICeW.png)

역시 예상이 맞았다.
예전에는 이런 오류가 나면 뭔 소린지도 모르겠고 그냥 스트레스만 받았는데 하도 오류가 난걸 해결하다보니 요즘은 어떤 오류가 나더라도 별 느낌이 없다. 
처음에는 오류 해결 한 번 하면 희열을 느꼈는데 지금은 그냥 원인 찾고 구글링하고 구글링 해서 없으면 그냥 오류를 단계별로 해결하면 대부분 해결되서 이제는 딱히 어려울 것도 없다

```code
$ eb status <- 실행

Environment details for: django-greatkart-env
  Application name: Upgrade_Django4
  Region: ap-northeast-2
  Deployed Version: app-b6a2-230610_033231534038
  Environment ID: e-prmigw3sjw
  Platform: arn:aws:elasticbeanstalk:ap-northeast-2::platform/Python 3.11 running on 64bit Amazon Linux 2023/4.0.1
  Tier: WebServer-Standard-1.0
  CNAME: django-greatkart-env.eba-hgmyb93v.ap-northeast-2.elasticbeanstalk.com
  Updated: 2023-06-09 18:35:11.251000+00:00
  Status: Ready
  Health: Red
```

위와 같이 Health: Red 로 나오면 그냥 서버 다시 시작하면 된다.
여기서 CNAME은 도메인 네임이다.
해당 도메인 네임을 settings.py 에서 변경해야 한다.

```settings.py
ALLOWED_HOSTS = ['django-greatkart-env.eba-hgmyb93v.ap-northeast-2.elasticbeanstalk.com']
```

## Elastic Beanstalk Restart

서버를 다시 시작하는 방법은 Elastic Beanstalk 에 들어가서 다시 시작하면 된다.

![](https://i.imgur.com/HTbvDO3.png)

밑에는 이전에 초기 설정을 잘못해서 생성된건데 시간이 지나면 알아서 없어진다고 한다.

![](https://i.imgur.com/b5FRUhW.png)

![](https://i.imgur.com/o6soT9Q.png)

## 환경설정


![](https://i.imgur.com/erkRrs9.png)

![](https://i.imgur.com/m98DTj3.png)

여기서 . env 에서 설정했던걸 해주면 된다.
API 키 등을 여기에 넣어주면 된다.

## ERROR Environment health has transitioned from Info to Degraded

![](https://i.imgur.com/GD6i05A.png)

역시 한 번에 되는게 없다.
찾아 보니 명령제한 시간 때문에 그런거 같았다.
명령제한 시간은 
해당 부분을 1800 으로 늘리고 해결
아마 회원가입 인증 이메일을 보내는 부분에서 시간이 좀 걸려서 그런거 같다.
[참고](https://serverfault.com/questions/992394/elastic-beanstalk-health-degraded)

![](https://i.imgur.com/KnkjNEm.png)

## hosting 반영하기

```code
eb deploy
```

프로젝트 폴더에서 배포를 해주기전 git 버전 관리를 업데이트 해주고 deploy를 이어서 해주면 된다.

## Database 연결

![](https://i.imgur.com/9DLWsAx.png)

채용공고를 보면 장고 + mysql을 쓰는거 같은데 이 부분이 궁금해서 찾아보면 대부분 장고에서 PostgreSQL 을 쓴다고 한다.
요즘에는 둘의 성능차가 그렇게 크지는 않지만 대부분 커뮤니티에서 장고의 제약 없이 데이터베이스를 쓰려면 PostgreSQL이 가장 좋다고들 한다.
그렇다고 mysql이 지원 안되거나 안좋은건 아니라고 한다.

![](https://i.imgur.com/wy68xQr.png)

![](https://i.imgur.com/Jlva19d.png)

엔진버전을 제외하고는 그냥 있는 그대로 사용하면 된다.

```settings.py

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}


--- 아래와 같이 변경
import os
if 'RDS_DB_NAME' in os.environ:
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': os.environ['RDS_DB_NAME'],
            'USER': os.environ['RDS_USERNAME'],
            'PASSWORD': os.environ['RDS_PASSWORD'],
            'HOST': os.environ['RDS_HOSTNAME'],
            'PORT': os.environ['RDS_PORT'],
        }
    }
else:
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': BASE_DIR / 'db.sqlite3',
        }
    }

```

```code
pip install psycopg2-binary

라이브러리 설치 후 버젼 명시해주기

pip freeze > requirements.txt
```

그후 로컬 서버가 아니라 AWS 서버에서 돌아가게 만들기 위해서
.ebextensions 폴더에 db-migrate.config 파일 만들고 아래의 코드를 입력해준다.

```python
container_commands:
  01_migrate:
    command: "source /var/app/venv/*/bin/activate && python3 manage.py migrate"
    leader_only: true
  02_createsuperuser:
    command: "source /var/app/venv/*/bin/activate && echo from accounts.models import Account; Account.objects.create_superuser('first_name', 'last_name', 'your-email@gmail.com', 'yourusername', 'password') | python manage.py shell"
    leader_only: true
option_settings:
  aws:elasticbeanstalk:application:environment:
    DJANGO_SETTINGS_MODULE: greatkart.settings
```

2번은 관리자 아이디를 만드는거라 만들고 지워야한다.
AWS에서 migrate를 하는 작업이다.

![](https://i.imgur.com/fL2Y167.png)

https://stackoverflow.com/questions/63748708/elasticbeanstalk-failed-to-deploy-worker-environment-var-pids-web-pid-no-such

```code
2023-06-13 02:33:51,961 [ERROR] Command 02_createsuperuser (source /var/app/venv/*/bin/activate && echo from accounts.models import Account; Account.objects.create_superuser('zxzx', 'zxzx', 'zxzx@zxzx.com', 'zxzx', '@@@@@@@') | python manage.py shell) failed
2023-06-13 02:33:51,961 [ERROR] Error encountered during build of postbuild_0_Upgrade_Django4_2: Command 02_createsuperuser failed
Traceback (most recent call last):
  File "/usr/lib/python3.9/site-packages/cfnbootstrap/construction.py", line 579, in run_config
    CloudFormationCarpenter(config, self._auth_config, self.strict_mode).build(worklog)
  File "/usr/lib/python3.9/site-packages/cfnbootstrap/construction.py", line 277, in build
    changes['commands'] = CommandTool().apply(
  File "/usr/lib/python3.9/site-packages/cfnbootstrap/command_tool.py", line 127, in apply
    raise ToolError(u"Command %s failed" % name)
cfnbootstrap.construction_errors.ToolError: Command 02_createsuperuser failed
2023-06-13 02:33:51,962 [ERROR] -----------------------BUILD FAILED!------------------------
2023-06-13 02:33:51,963 [ERROR] Unhandled exception during build: Command 02_createsuperuser failed
```



![](https://i.imgur.com/4dnuNcH.png)


stream_or_string = stream_or_string.decode('cp949')



from django.contrib.contenttypes.models import ContentType

from django.contrib.contenttypes.models import ContentType


![](https://i.imgur.com/0bKVfVJ.png)


```code
botocore.exceptions.ClientError: An error occurred (AccessControlListNotSupported) when calling the PutObject operation: The bucket does not allow ACLs
(venv) 
```

https://stackoverflow.com/questions/54788998/djangoaws-s3-botocore-exceptions-clienterror-an-error-occurred-accessdenied

![](https://i.imgur.com/NOfyH5E.png)
