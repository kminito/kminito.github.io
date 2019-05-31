---
layout:     post
title:      "우분투에 Ghidra 설치하기 (Ubuntu 18.04)"
subtitle:   ""
author:     "kminito"
catalog: true
tags:
    - ubuntu
    - ghidra
    - reverse engineering
    - hacking
---

역어셈블을 위한 Ida Pro를 우분투에 설치하기 위해 애를 쓰던 중에, 대신 미국 국가 안보국(NSA)에서 개발하여 오픈소스로 배포한 Ghidra를 추천 받아 설치했다. 내 노트북은 Ubuntu 18.04를 사용하고 있다.

# 1. Intro

Ghidra는 다른 보통의 프로그램들과 달리 실행하면 간단히 설치할 수 있는 설치파일을 따로 제공하지 않는다. 그냥 프로그램이 통째로 압축되어 있는 압축 파일을 다운받아서, 압축을 풀고, 프로그램 실행 파일을 실행하면 Ghidra를 사용할 수 있다.
이 방법은 superuser의 권한이 필요하지 않기 때문에 사용자가 필요에 따라 편하게 실행하고 삭제할 수 있지만, 실행에 필요한 환경이 미리 설치되어 있어야 프로그램을 실행할 수 있다.  

Ghidra의 실행에는 올바른 버전의 Java Runtime Environemnt(JRE)와 Java Development Kit(JDK)가 설치되고, 해당 프로그램이 환경변수에 등록이 되어 있어야 한다.


# 2.. 환경 설치 - JRE, JDK

터미널에서 아래와 같이 입력한다.

```cmd
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt update
sudo apt install openjdk-11-jdk
sudo apt install openjdk-11-jre-headless
```



# 3.. Ghidra 다운로드 및 실행
Ghidra 홈페이지 [https://ghidra-sre.org/](https://ghidra-sre.org/)


Ghidra 홈페이지에서 최신 버전을 다운받을 수 있다. 다운 받아서 적절한 폴더에 압축을 푼다.  
나는 9.0.4 버전을 다운받았다.


```cmd
kminito@min-ideapad:~/apps/ghidra_9.0.4$ ls -al
합계 56
drwxr-xr-x 9 kminito kminito  4096  5월 16 16:08 .
drwxr-xr-x 4 kminito kminito  4096  5월 31 14:24 ..
drwxr-xr-x 5 kminito kminito  4096  5월 16 16:08 Extensions
drwxr-xr-x 7 kminito kminito  4096  5월 16 16:08 GPL
drwxr-xr-x 8 kminito kminito  4096  5월 16 16:08 Ghidra
-rw-r--r-- 1 kminito kminito 11357  5월 16 16:08 LICENSE
drwxr-xr-x 5 kminito kminito  4096  5월 16 16:08 docs
-rwxr-xr-x 1 kminito kminito   875  5월 16 16:08 ghidraRun
-rw-r--r-- 1 kminito kminito   384  5월 16 16:08 ghidraRun.bat
drwxr-xr-x 2 kminito kminito  4096  5월 16 16:08 licenses
drwxr-xr-x 2 kminito kminito  4096  5월 16 16:08 server
drwxr-xr-x 2 kminito kminito  4096  5월 16 16:08 support
```
ghidraRun 파일을 실행하면 ghidra가 실행된다. 실행 권한이 없으면

```cmd
kminito@min-ideapad:~/apps/ghidra_9.0.4$ ./ghidraRun
```

만약 권한이 없다면 아래의 명령어로 실행 권한을 추가해 준다.
```cmd
chmod +x ghidraRun
```
