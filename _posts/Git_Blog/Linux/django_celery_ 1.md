---
layout: single
title: " [Linux] gcc 컴파일러, 개발환경"
categories: Linux
tags:
  - Linux
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# 
```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```


```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

zsh 쉘로 바꾸려고 하니까 

```
Zsh is not installed. Please install zsh first.
```

```
sudo apt update
sudo apt install zsh
```

![](https://i.imgur.com/kC7lRgP.png)

```
sudo apt-get install build-essential gdb
```

```
mkdir network_project
cd network_project
mkdir hello
cd hello
code .
```

![](https://i.imgur.com/3Zy52o2.png)

code . 명령어는 vs에디터를 불러와준다

![](https://i.imgur.com/ttdZQeL.png)

include 패스를 못 불러와서 좀 찾아볼라고 폴더 열었더니 난리 부르스

![](https://i.imgur.com/oyEw82F.png)

똥컴이라 느려터지기도하고 가상머신보다는 그냥 듀얼부팅으로 해야겠다고 생각함..

![](https://i.imgur.com/8FUNxCx.png)

파티션 나눠서 한 200기가 할당하려니까 또 난리

[참고영상](https://www.youtube.com/watch?v=DF_TiZrwPAA&ab_channel=PoommelierPrograming)
