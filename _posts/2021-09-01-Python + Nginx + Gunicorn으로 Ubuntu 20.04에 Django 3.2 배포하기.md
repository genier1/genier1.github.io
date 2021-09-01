

### EC2 접속

pem키를 활용해서 배포하고자 하는 EC2에 접속합니다.

```bash
# ex) ssh -i test.pem ubuntu@ec2-1-1-1-1.ap-northeast-2.compute.amazon.com
ssh -i '파일명.pem' 'ec2 주소'
```

ssh 접속 시에 아래와 같은 에러가 난다면 key 파일의 권한을 수정해줍니다.

```bash
Permissions 0644 for '파일명.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "파일명.pem": bad permissions
```

```python
# Key 파일 권한 수정 
chmod 400 '파일명.pem'
```

## Python 설치

### 기본 프로그램 설치

```bash
# 새로 추가된 패키지 등, 변경된 패키지 정보를 업데이트
$ sudo apt update

# Python을 설치하기 위한 필수 패키지 설치 
$ sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev libsqlite3-dev wget libbz2-dev
```

### Python 설치

```bash
$ wget https://www.python.org/ftp/python/3.9.1/Python-3.9.1.tgz
$ tar -xf Python-3.9.1.tgz
$ cd Python-3.9.1
$ ./configure --enable-optimizations
$ make -j 12 
$ sudo make altinstall 

## Python 3.9가 잘 설치되었는지 확인 
$ python3.9 --version
>> Python 3.9.1
```

### Alternative

```bash
# 파이썬 위치 확인 
$ which python3.9
>> /usr/bin/python3.9

# Python 3.9를 Alternatives로 등록 
$ sudo update-alternatives --install /usr/bin/python python /usr/local/bin/python3.9 1 

# 만약 여러 버전의 python이 설치되어 있다면 아래의 명령어로 확인
$ sudo update-alternatives --config python

There are 3 choices for the alternative python (providing /usr/bin/python).

  Selection    Path                      Priority   Status
------------------------------------------------------------
* 0            /usr/bin/python3.9         3         auto mode
  1            /usr/bin/python2.7         1         manual mode
  2            /usr/bin/python3.6         2         manual mode
  3            /usr/bin/python3.9         3         manual mode

Press <enter> to keep the current choice[*], or type selection number: 3

$ python -V
>> Python 3.9.1
```

### Virtualenv 설치

```bash
# python3-venv 설치 
$ sudo apt install python3-venv

# Virtualenv 폴더 생성 
$ python3 -m venv my-project-env

# Virtualenv 실행 
$ source my-project-env/bin/activate
```

### Django 프로젝트 Clone

```bash
# Git을 통해 프로젝트 다운
$ git clone <프로젝트 링크>

# 필요한 패키지 설치
$ pip install -r requirements.txt
```

## Gunicorn + Nginx

파이썬과 장고 셋팅이 끝났다면 이제 본격적으로 gunicorn과 Nginx 설정을 시작합니다.

### Gunicorn

1. `sudo vi /etc/systemd/system/gunicorn.socket` 을 통해 gunicorn 소켓 파일을 생성하고 아래의 코드를 입력합니다.

```bash
# /etc/systemd/system/gunicorn.socket

[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target
```

1. `sudo vi /etc/systemd/system/gunicorn.service` 으로 gunicorn 서비스 파일을 생성하고 아래의 코드를 입력합니다.
- 아래의 코드 중 분홍색으로 된 부분은 각자의 설정에 맞게 변경해서 입력해야 합니다.

```bash
# /etc/systemd/system/gunicorn.service

[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=sammy
Group=www-data
WorkingDirectory=/home/sammy/myprojectdir
ExecStart=/home/sammy/myprojectdir/myprojectenv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/run/gunicorn.sock \
          myproject.wsgi:application

[Install]
WantedBy=multi-user.target
```

1. 아래 명령어를 통해 gunicorn.sock 파일을 생성합니다. sock을 통해 연결되고 나면 systemd가 gunicorn.service 파일을 시작합니다.

```bash
$ sudo systemctl start gunicorn.socket
$ sudo systemctl enable gunicorn.socket
```

1. 아래 명령어를 통해 gunicorn이 잘 동작하는지 확인합니다. 

```bash
$ sudo systemctl status gunicorn.socket
```

5. gunicorn이 잘 동작한다면 4번의 명령어를 수행했을 때 아래와 같은 출력값을 확인 할 수 있습니다. 

```bash
● gunicorn.socket - gunicorn socket
     Loaded: loaded (/etc/systemd/system/gunicorn.socket; enabled; vendor prese>
     Active: active (running) since Tue 2021-07-20 03:04:27 UTC; 14s ago>
   Triggers: ● gunicorn.service
     Listen: /run/gunicorn.sock (Stream)
     CGroup: /system.slice/gunicorn.socket
```

### Nginx

1. gunicorn을 잘 설정했다면, `sudo vi /etc/nginx/sites-available/<프로젝트명>` 을 입력하여 Nginx 설정 파일을 생성하고 아래의 코드를 입력합니다.

```bash
server {
    listen 80;
    server_name <도메인명 또는 IP>;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /home/<프로젝트 폴더>;
    }
}
```

1. 해당 파일을 `/etc/nginx/sites-enabled` 으로 복사해줍니다. `sudo nginx -t` 을 통해 nginx 설정에 오류가 있는지 확인해 보세요.

```bash
$ sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled
```

1. `sudo nginx -t` 를 실행했을 때, 에러가 없다면 아래의 코드를 통해 Nginx를 재시작합니다.

```bash
$ sudo systemctl restart nginx
```

1. 마지막으로 80포트를 오픈할 것이므로 필요하지 않은 8000포트는 제거해줍니다. 

```bash
$ sudo ufw delete allow 8000
$ sudo ufw allow 'Nginx Full'
```

### 오류 발생 시

1. gunicorn에 문제 발생 시 
    1. `sudo journalctl -u gunicorn` 을 통해 gunicorn 어플리케이션 로그를 확인합니다.
    2. `sudo journalctl -u gunicorn.socket` 을 통해 소켓 로그를 살펴봅니다. 
    3. 경험 상, gunicorn에 문제가 발생하는 이유는 장고 설정 및 코드와 관련이 많습니다. allowed_host나 기타 등등의 설정을 다시 한번 확인해 보세요.
2. Nginx에 문제 발생 시 
    1. `sudo journalctl -u nginx` 을 통해 Nginx Process 로그를 확인합니다.
    2. `sudo less /var/log/nginx/access.log` 을 통해 Nginx 엑세스 로그를 확인합니다.
    3. `sudo less /var/log/nginx/error.log` 을 통해 Nginx 에러 로그를 확인합니다. 

### 출처

- [How To Set Up Django with Postgres, Nginx, and Gunicorn on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-20-04)
- [[우분투/Ubuntu 20.04] 파이썬 최신 버전(3.9.X) 설치](https://ieworld.tistory.com/9)
- [Ubuntu - Python 3.9 설치 방법](https://codechacha.com/ko/ubuntu-install-python39/)