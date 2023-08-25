진짜 난리 난리 생고생


ssh 접속하는 방법
https://kitty-geno.tistory.com/72
https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

aws 인스턴스 리눅스 접속
https://www.youtube.com/watch?v=jIxkbXB6-38&ab_channel=JasCloudTech


이게 찐
https://stcodelab.com/entry/Elastic-Beanstalk-Django-mysqlclient-mysql-ERROR

----------------

우분투로 ㄱㄱ
https://seill.tistory.com/1138

mysql 연동
https://www.youtube.com/watch?v=6diwIYhnCQg&t=305s&ab_channel=ParwizForogh

우분투 설치
https://www.youtube.com/watch?v=uiPSnrE6uWE&ab_channel=CloudWithDjango


안되면 가상환경 _ static 폴더 지우기_

sudo apt-get update
sudo apt install git

git clone https://github.com/ramyo564/wanted-pre-onboarding-backend.git

sudo apt-get install python3-dev default-libmysqlclient-dev build-essential

sudo apt install python3-pip -y
ls -lrt
pip install -r requirements.txt



https://devicetests.com/install-mysqlclient-ubuntu-20-10

https://pypi.org/project/mysqlclient/

전체 인스턴스 등록 영상
https://www.youtube.com/watch?v=uiPSnrE6uWE&ab_channel=CloudWithDjango

env 설정

https://velog.io/@wooj/AWS%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8-%EB%B0%B0%ED%8F%AC-S3-%EC%83%9D%EC%84%B1%EB%B6%80%ED%84%B0-%EC%84%A4%EC%A0%95%EA%B9%8C%EC%A7%80

env 생성
touch .env

nano .env

```
SECRET_KEY=django-insecure-n@$m*@ct_=g#))c%^7)=x!ku^!+&ym46xt=6b(4vg#s=0*dxc9

DEBUG=False

DB_USER_NAME=admintest

DB_PASSWORD=Qlalfqjsgh

DB_HOST=database-2.ckpx2tpfazov.ap-northeast-2.rds.amazonaws.com

```

sourcs .env

https 
https://www.youtube.com/watch?v=WS2n8mkrFaY&ab_channel=AWS%EA%B0%95%EC%9D%98%EC%8B%A4

----
linux

https://hyunki99.tistory.com/102

https://stcodelab.com/entry/Elastic-Beanstalk-Django-mysqlclient-mysql-ERROR

```plain
sudo dnf install https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm 

sudo dnf install mysql-community-server

#필요한 종속성 설치 
sudo yum install python3-devel mysql-devel gcc
```

.ebextensions

django.config

option_settings:
  aws:elasticbeanstalk:container:python:
    WSGIPath: core.wsgi:application