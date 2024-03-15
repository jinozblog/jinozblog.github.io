---
title: Ubuntu Server 23.04 Nginx 설치
categories: [Ubuntu, Nginx]
tags: [ubuntu, server, docker, nginx, web-server]
---

## Server Environment

- HW : R-pi 4 (arm64)
- OS : Ubuntu Server 23.04 GNU/Linux 6.2.0-1004-raspi aarch64
- Docker version 24.0.2, build cb74dfc

---

## Intro

- dokcer pull Nginx on Docker-Hub
- Nginx Run on Docker
- web-page Test on Docker Nginx
- Use Nginx .conf(options)

---

## docker pull Nginx on Docker-Hub

> $ sudo docker pull nginx

---

## Nginx Run on Docker

### 1. Create Container

> $ sudo docker run --name docker-nginx -p 9880:80 -d nginx

<pre>
    --name  
    Contatiner Name : docker-nginx

    -p  
    Port : local-machine-port:Internal-container-port
    
    -d  
    detached mode : docker image
</pre>

### 2. browser 접속

browser 주소창에서

> YourIP:9880

## web-page Test on Docker Nginx

### 1. index 파일 수정

> $ mkdir -p ~/persona-nginx/html  
> $ cd ~/persona-nginx/html   
> $ nano index.html   

```html   
<html>
  <head>
    <title>Docker nginx Test</title>
  </head>

  <body>
    <div class="container">
      <h1>Hello persona-nginx</h1>
      <p>This Nginx page on Docker persona-ngin</p>
    </div>
  </body>
</html>
```

### 2. Run persona-nginx Container

browser 주소창에서

> docker run --name persona-nginx -p 80:80 -d -v ~/persona-nginx/html:/usr/share/nginx/html nginx

### 3. browser 접속

browser 주소창에서

> YourIP:9880

##### 참고

Nginx Container Connect

> $ sudo docker exec -it docker-nginx /bin/bash

Nginx Container default
/usr/share/nginx/html

## Use Nginx .conf(options)

### 1. copy nginx .conf

> $ cd ~/docker-nginx  
> $ sudo docker cp docker-nginx:/etc/nginx/conf.d/default.conf default.conf

### 2. remove docker-nginx Container

> $ sudo docker stop docker-nginx  
> $ sudo docker rm docker-nginx

### 3. Create(Run) docker-nginx Container

> $ sudo docker run --name docker-nginx -p 9880:80 -v ~/docker-nginx/html:/usr/share/nginx/html -v ~/docker-nginx/default.conf:/etc/nginx/conf.d/default.conf -d nginx  
> $ sudo docker restart docker-nginx

### 4. index.html

> $ cd ~/docker-nginx/html  
> $ nano index.html

```html   
<html>
  <head>
    <title>Docker nginx Test</title>
  </head>

  <body>
    <div class="container">
      <h1>Hello docker-nginx</h1>
      <p>This Nginx page</p>
    </div>
  </body>
</html>
```

### 4. browser 접속

browser 주소창에서

> YourIP:9880

Result

<pre>

<html>
  <head>
    <title>Docker nginx Test</title>
  </head>

  <body>
    <div class="container">
      <h2>Hello docker-nginx</h2>
      <p>This Nginx page</p>
    </div>
  </body>
</html>

</pre>

---

<br>

#### [Docker 명령어 참고]

##### - Docker 실행 Container 확인

> $ sudo docker ps

##### - Docker 중지 Container 확인

> $ sudo docker ps -a

##### - Docker Container 중지

> $ sudo docker stop [container id]

##### - Docker Container 삭제

> $ sudo docker rm [container id]
