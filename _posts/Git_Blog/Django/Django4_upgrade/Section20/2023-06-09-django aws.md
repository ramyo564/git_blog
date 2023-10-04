---
layout: single
title: " [Django] Shopping Mall (15)"
categories: Django_ShoppingMall
tags:
  - Python
  - AWS
  - Django
  - Project_ShoppingMall
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 서버를 올려보자

개념이 없어서 삽질 많이 했었다 ㅜ  
처음부터 개념 공부를 하기에는 흥미가 잘 생기지 않아서 구현부터 시도해 보고 오류 생기거나 개념이 궁금할 때마다 고치면서 진행했다   

(이전에 글을 쓰다가 말았는데 이번에 다시 서버를 올리면서 복습하는 겸 글을 마무리!! )
## Elastic Beanstalk vs EC2

프리티어라도 잘못 건드리면 돈이 와장창 나갈 수도 있고 처음 배울 때는 좀 쉽게 배우는 게 좋다고 생각한다.     
더군다나 별다른 지식도 없으니 빈스톡으로 시작하는 게 더 낫다고 생각했다.

## Django 빈스톡 환경설정

[Deploying a Django application to Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-django.html)

우선 AWS 로 아이디를 만들면 Root account를 볼 수 있는데 어플리케이션 배포를 할 때 root account를 사용하는 건 별로 추천되지 않는다고 한다.
여러 가지 이유가 있는데 간단하게 정리하면

- 보안 강화 및 권한 제어
- 작업 추적과 분리

위와 같은 이유로 IAM user를 만들어서 사용한다.
S3 혹은 DB 등 세분화해서 IAM user를 만들고 권한을 분리해서 관리하면 보안을 강력하게 유지 할 수 있다.

## 파이썬 환경 설정

![](https://i.imgur.com/vKR3HZz.png)

awsebcli 를 설치할 때는 가상환경이 아닌 로컬에 설치해야 한다.    
`pip install awsebcli`     
그 후 이전에 만든 프로젝트의 requirements.txt 에 버전을 뽑아와 준다.    
`pip freeze requirements.txt'
기존 루트 폴더에 .ebsxtensions 폴더를 하나 만들어 준다.    
그 후 django.config 라는 환경 설정 폴더를 만들어 준다.

```python
option_settings: aws:elasticbeanstalk:container:python: WSGIPath: greatkart.wsgi:application aws:elasticbeanstalk:environment:proxy:staticfiles: /static: static
```

![](https://i.imgur.com/XhPSVnb.png)


`greatkart` 는 모듈네임으로 처음에 장고를 시작할 때 앱 폴더의 이름으로 쓰면 된다.    
`python manage.py collectstatic` 를 실행해서 여태까지 작업한 static 파일들을 한곳에 모아준다.    

## 초기 환경 설정 Error 

![](https://i.imgur.com/zrrJuF6.png)
`ERROR: ServiceError - Create environment operation is complete, but with errors. For more information, see troubleshooting documentation.`

### ERROR   Instance deployment failed. For details, see 'eb-engine.log'.

2023-06-09 16:56:56    ERROR   Instance deployment failed. For details, see 'eb-engine.log'.
2023-06-09 16:57:00    ERROR   [Instance: i-016d570f7670243d0] Command failed on instance. Return code: 1 Output: Engine execution has encountered an error..
2023-06-09 16:57:00    INFO    Command execution completed on all instances. Summary: [Successful: 0, Failed: 1].
2023-06-09 16:58:02    ERROR   Create environment operation is complete, but with errors. For more information, see troubleshooting documentation.

ERROR: ServiceError - Create environment operation is complete, but with errors. For more information, see trou

첫 번째 발생한 에러다. 해당 부분을 살펴보기 위해 로그를 살펴봤다.    

```
2023/06/09 16:56:56.277542 [ERROR] update processes [web healthd nginx cfn-hup] pid symlinks failed with error parse pid  to uint32 failed with error:strconv.ParseUint: parsing "": invalid syntax
2023/06/09 16:56:56.277553 [ERROR] An error occurred during execution of command [app-deploy] - [Track pids in healthd]. Stop running the command. Error: update processes [web healthd nginx cfn-hup] pid symlinks failed with error parse pid  to uint32 failed with error:strconv.ParseUint: parsing "": invalid syntax

```

![](https://i.imgur.com/jlIotji.png)

에러를 보니 초기 설정을 잘못해서 인스턴스를 못 만들고 있는 거 같았다.    
잘못 생성된 인스턴스를 삭제하고 초기부터 다시 설정해 줬다.    

이전에 한 번 사용해서 그런 건지 이전에 설정했던 환경설정이 그대로 적용되어 있었다.      
생성된 `.elasticbeanstalk`  폴더의 `config.yml` 을 살펴보면`default_region` 이 west로 되어있고 파이썬 버전도 다르게 설정되어 있었다.
이 부분을 서울로 바꿔주고 맨 처음에 잘못 설정했던 파이썬 버전도 맞춰주었다.    

```python
branch-defaults:
  main:
    environment: null
global:
  application_name: django-greatkart-app
  branch: null
  default_ec2_keyname: null
  default_platform: Python 3.11
  default_region: us-west-2 <- 이부분
  include_git_submodules: true
  instance_profile: null
  platform_name: null
  platform_version: null
  profile: null
  repository: null
  sc: git
  workspace_type: Application
```

```code
eb init --interactiv
```

터미널에서 `eb init --interactiv` 를 실행하면 알아서 처음부터 다시 잡아준다.    
아니면 IAM user에 등록된 부분을 다 삭제해 주면 토큰이 맞지 않아서 처음부터 다시 잡을 수 있다.     

이렇게 실행해 주고 이제 내 환경 설정에 맞게 선택해 주면 해당 파일 정보가 바뀐다.        

![](https://i.imgur.com/Yu9ICeW.png)

다행히 예상이 맞았다.    

처음에는 개념이 너무 없어서 이런 오류들이 힘들었는데 그냥 오류를 접하면 원인 찾고 구글링하고,     
구글링해서 없으면 문제를 단계별로 해결하면서 개념을 숙지하면 대부분 해결된다.     
이제는 딱히 어려울 것도 없다 :)

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

위와 같이 Health: Red 로 나오면 그냥 서버를 다시 시작하면 된다.   

이때 실수했었던 게 AWS에서 빌드가 끝날 때까지 기다리고 했야 했는데 그냥 무시하고 진행해서 오류가 났었다.     

git을 로컬 버전에 맞춰서 설정했다면 AWS와 버전이 맞아야 인스턴스가 빌드 되기 때문에 이 부분을 꼭 확인하자    

위에 CNAME은 도메인 네임이다.    
해당 도메인 네임을 settings.py 에서 변경해야 한다.    
정상적으로 빌드 해서 올렸는데도 계속 페이지를 찾을 수 없다고 해서 찾아보니까 장고의 보안정책 때문에 HOST 주소가 안 맞으면 실행이 안 된다.    

```settings.py
ALLOWED_HOSTS = ['django-greatkart-env.eba-hgmyb93v.ap-northeast-2.elasticbeanstalk.com']
```

`"*"` 를 추가하면 모든 도메인 주소를 허용한다.    
실제로는 cname 과 구매한 도메인 이름만 넣어야 한다.   


## Elastic Beanstalk Restart

서버를 다시 시작하는 방법은 Elastic Beanstalk 에 들어가서 다시 시작하면 된다.     
처음에 모르고 맨날 작업에서 리빌드를 했는데 그냥 서버 다시 시작하기를 누르면 된다.    
![](https://i.imgur.com/o6soT9Q.png)

## 환경설정

![](https://i.imgur.com/erkRrs9.png)

![](https://i.imgur.com/m98DTj3.png)

여기서 . env 에서 설정했던 값들을 넣어주면 알아서 잡아준다.         


## 배포하기 전에 깃 커밋하기

```code
eb deploy
```

계속 반영이 안 돼서 또 찾아보니 변경사항을 배포할 때 꼭 커밋 후에 해야 한다.     


![](https://media0.giphy.com/media/wID3zXURLH1jrjCcZy/giphy.gif?cid=ecf05e4790x2kpgrlt0bqb241ydgkp1vyrx7bzrwt6c7k2dg&ep=v1_gifs_search&rid=giphy.gif&ct=g)

## Database 연결

장고의 데이터베이스는 주로 PostgresSQL을  추천하는 글이 많았다.
인턴십 때 mysql로 DB를 연결했었는데 라이브러리가 계속 설치가 안 되었다.     
이럴 때는 아마존 리눅스에 해당 라이브러리가 없는 경우라서 따로 아마존 환경에 들어가서 라이브러리를 수동으로 설치해야 한다.    

장고에서 PostgresSQL은 아무래도 많이 써서 그런지 라이브러리를 설치할 때 큰 어려움은 없었다.     

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

혹시라도 실패할 경우 sqlite에서 돌아가게 설정했다.     
로컬 서버가 아니라 AWS 서버에서 돌아가게 만들기 위해서  .ebextensions 폴더에 db-migrate.config 파일 만들고 아래의 코드를 입력해 준다.

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

2번은 관리자 아이디를 만드는 거라 만들고 지워야 한다.
AWS에서 migrate를 하는 작업이다.   
근데 어차피 로컬에서 만든 DB를 연결시키면 로컬에서 쓰던 게 그대로 옮겨지니 딱히 크게 상관은 없없다.    

![](https://i.imgur.com/fL2Y167.png)

## DB Error

[참고 자료](https://stackoverflow.com/questions/63748708/elasticbeanstalk-failed-to-deploy-worker-environment-var-pids-web-pid-no-such)

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

위에 관리자 계정이 안 만들어져서 오류가 계속 발생했다.    
Nginx 에서 계속 가상환경을 못 불러오는 게 문제였다.     
그래서 생각한 게 기존에 데이터를 옮길 수만 있다면 관리자 계정도 어차피 옮겨질 거라고 생각해서 관리자 아이디를 만드는 부분을 빼고 마이그레이션 해줬다.      


## Error DB 마이그레이션

기존 sqlite의 정보를 postgresSQL로 옮기려는 방법을 찾아봤는데 json 파일로 전환해서 옮길 수 있었다.    
생각해 보면 대부분 REST API에서 json 형식으로 하니까 안 될 이유도 없을 거라고 생각했다.     

```
python manage.py dumpdata > data.json
```

AWS에 연결된 PostgresSQL 의 엔드포인트와 아이디 비번 등을 설정해 주면 로컬에서도 연결이 된다 해당 상태에서 json 데이터 파일을 이식해 주면 되는데 또 오류가 생겼다.    

해결 방법은 기존에 AWS에서 마이그레이션을 했을 때 생기는 권한 같은 잡다한 설정들을 초기화해 주고 진행해 주면 된다.     

```
python manage.py shell
from django.contrib.contenttypes.models import ContentType
ContentType.objects.all().delete()
exit()
```

![](https://i.imgur.com/0bKVfVJ.png)

완전 초기화해주고 데이터 이전 시작

```
python manage.py loaddata data.json
```

데이터를 이전하는데 있어서 또 오류가 발생했다.     

![](https://i.imgur.com/4dnuNcH.png)

이번에는 유니코드 인코딩이 문제였다.   
해당 부분 인터넷으로 찾아보니까 기존에 물건 등을 등록했을 때 한글로 만들어서 문제가 되었는데 그냥 한글도 인식 가능하게 아래와 같이 변경해 주면 된다.

`stream_or_string = stream_or_string.decode('cp949')`
혹은 utf8로 진행하면 된다.

문제없이 데이터 이전이 성공적으로 되었다.   
DB와 연결시키고 나서 역시나 오류가 발생했다.

```code
botocore.exceptions.ClientError: An error occurred (AccessControlListNotSupported) when calling the PutObject operation: The bucket does not allow ACLs
(venv) 
```

[참고 자료 1](https://stackoverflow.com/questions/54788998/djangoaws-s3-botocore-exceptions-clienterror-an-error-occurred-accessdenied)

[참고 자료 2](https://stackoverflow.com/questions/71080354/getting-the-bucket-does-not-allow-acls-error/71144414#71144414)

어떤 애가 `AWS_DEFAULT_ACL = None` 이렇게 하라고 했는데 해결이 안 돼서 밑에 글을 더 읽어 보니 권한 문제였다.      
static 폴더에서 이미지 등을 못 갖고 왔는데 그냥 권한 문제만 해결해 주면 된다.    

![](https://i.imgur.com/NOfyH5E.png)


이번에는 아래와 같이 또 오류가 발생했다.     

  File "C:\Users\user\Documents\GitHub\Upgrade_Django4\venv\Lib\site-packages\django\core\serializers\json.py", line 67, in Deserializer
    stream_or_string = stream_or_string.decode('cp949')
                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
UnicodeDecodeError: 'cp949' codec can't decode byte 0xad in position 1701: illegal multibyte sequence


```python
def Deserializer(stream_or_string, **options):

    """Deserialize a stream or string of JSON data."""

    if not isinstance(stream_or_string, (bytes, str)):

        stream_or_string = stream_or_string.read()

    if isinstance(stream_or_string, bytes):

        stream_or_string = stream_or_string.decode('UTF8')

```

어차피 한글 문제고 utf8 로 시도하니 문제 해결


## Certificate Error

<iframe width="1254" height="480" src="https://www.youtube.com/embed/ovDJQrIsCuc" title="Why is my AWS Certificate Manager certificate DNS validation status still pending validation?" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
예전에 certifacate를 발급받을 때 DNS 검증으로 Route 53에서 레코드를 생성하면 거의 실시간으로 인증되었는데 한 시간이 넘게 계속 pending 이였다.    
영상에서는 윈도우 환경이 아닌거 같아 찾아보니 윈도우에서 명령어는 좀 달랐다.    

[참고 자료](https://repost.aws/ko/knowledge-center/acm-certificate-pending-validation)

참고 자료에 있는 것처럼 dig이 아니라 nslookup 를 이용하고 영상처럼 cname을 인식 시켜주면 된다.

```
nslookup -type=cname _example-cname.example.com
```

```
nslookup -type=ns example.com
```

문제는 확인 결과 cname도 정상적으로 등록되어 있고 구매했던 도매인의 벨류 값도 다 제대로 인식하고 이상이 없었는데 주말이라서 그런 건지 아니면 아마도 aws 내에서 문제가 있는 것 같았다.    
aws에 따로 문의하고 그러기에는 오래 걸릴 거 같고 지금 당장 내가 해결할 수 있는 방법은 버지니아 북부 리전으로 인증서를 받은 다음 cdn으로 배포하는 식도 있는 데 그냥 이메일 인증으로 빠르게 해결했다.      

![](https://i.imgur.com/zRLtCxq.png)

DNS 검증이 안될 때는 꼭 필요한 게 아니라면 그냥 이메일로 소유권을 확인하는 게 속 편하다.     

![](https://media2.giphy.com/media/LbOm2qGo91bZ6/giphy.gif?cid=ecf05e47791f33p602d6n7p227uepav04zqiopgqc6xsekzl&ep=v1_gifs_related&rid=giphy.gif&ct=g)

## 느낀점 

- 처음부터 개념이나 이론을 공부하면 흥미를 잃어버릴 수 있다. 하지만 개념도 없으니 삽질을 많이 하게 된다.
- 하지만 그 덕분에 기억은 많이 남는다.
	- 서버 지웠다 깔았다만 한 30번 한 거 같다 


