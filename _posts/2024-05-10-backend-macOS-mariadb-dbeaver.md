---
title: macOS mariadb & dbeaver
categories: [backend,mariadb,macOS]
tags: [macos,mariadb,mysql,database,dbeaver]
---

## Environment

- HW : Mac-mini M2
- OS : Sonoma 14.4.1
- mariadb   
    mariadb from 11.3.2-MariaDB, client 15.2 for osx10.19 (arm64) using  EditLine wrapper   
- dbeaver-community   
    버전24.0.2.202404071654   

---

## Intro

1. mariadb    

2. dbeaver-community


---

## 1. mariadb    
most popular Open-Source RDB(Relational DataBase)   
MySQL이 오라클에 인수되면서 MariaDB 재단(mariadb.org)에서 개발      

#### Install & Run    
```zsh
  brew install mariadb
  mysql.server start
```

#### version 확인 및 접속    
```zsh
  mariadb -V
  mariadb
```

#### 사용법    
- DataBase List   
    MariaDB [(none)]> show databases;   
- DataBase Change   
    MariaDB [(none)]> use mysql;    
- mysql host, user, password   
    MariaDB [mysql]> select host, user, password from user;   
- set root@localhost password   
    MariaDB [mysql]> alter user root@localhost identified via mysql_native_password using password('PASSWORD');   
    MariaDB [mysql]> flush privileges;    

- Create User:'userID'@'localhost' or 'userID'@'%', PASSWORD     
    MariaDB [(none)]> create user 'persona'@'localhost' identified by 'PASSWORD';    
- 권한 부여     
    MariaDB [(none)]> grant all privileges on *.* to 'persona'@'localhost' identified by 'PASSWORD';    
- 설정 반영   
    MariaDB [(none)]> flush privileges;   
- exit   
    MariaDB [(none)]> quit;   

## 2. dbeaver-community
free cross-platform database tool
SQL Client & Management tool  

#### Install & Run
```zsh
  brew install --cask dbeaver-community
  dbeaver
```
spotlight: dbeaver


#### local mariadb connect   
새 데이터베이스 연결: control + shift + N   
Select your database: MariaDB > Next    
![dbeaver_conn_mariadb](/assets/img/dbeaver_conn_mariadb.png)

  > Server Host: localhost, Port: 3306    
  > Username: root    
  > Passward:   

left-lower [Test Connection] click: 필요한 파일 다운로드    

Finish(완료)

