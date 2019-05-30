---
layout: post
title: "uWSGI + Django - Secret Key 환경변수로 설정하기"
category: backend
tag:
  - django

---

Nginx + uWSGI + Django를 Vultr 에 올리고 있었는데, 서버에서 새로운 장고 프로젝트를 생성하면 정상 작동하는데, 로컬에서 작업중인 프로젝트를 git clone 해오면 Internal Server Error가 발생했다.  

`sudo journalctl -u uwsgi`로 로그를 살펴보니 환경변수로 저장해놓은 django의 secret key를 찾지 못한 것이었다. 찾아보니 uWSGI는 가상환경의 activate 파일에 export 하는 것으로는 환경변수가 작동하지 않는다고 한다.

---

## uWSGI에서 환경변수 설정하기


1. 해당 프로젝트의 uwsgi를 실행하기 위한 ini 파일을 연다. (경로는 본인의 ini 파일 위치에 맞게 수정)

    ```
    sudo nano /etc/uwsgi/sites/chemreg.ini
    ```

2. 아래와 같이 env='변수명'='변수내용'의 형식으로 내용을 추가한다.  

    ```
    [uwsgi]
    (생략)
    env=SECRET_KEY_CHEMREG='*sfxe=w2akadjlfadalejlzjc+f5dai#n3d)ys2ggg$jafsc8'
    ```
  나는 환경변수 명을 'SECRET_KEY_CHEMREG'로 지정했다.  
  변수 설정이 완료되면 settings.py의 secret key는 값을 삭제하고 해당 환경변수로 대체한다

    ```
    # settings.py

    SECRET_KEY = os.environ["SECRET_KEY_CHEMREG"]
    ```


3. uwsgi를 다시 시작한다.  
    ```
    sudo systemctl restart uwsgi
    ```

4. 끝
