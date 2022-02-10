---
layout: post
title: zabbix를 docker로 간단하게 설치하기
categories: Docker Zabbix
---
# 시작하며
예전에는 로컬 환경에서 zabbix를 테스트 할때는 버철 박스를 이용했었지만 docker를 이용하면 더 간단하게 zabbix를 구축할수 있습니다.

## mariadb 컨테이너
```
docker run --name zabbix-mysql-server -t -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix" -e MYSQL_ROOT_PASSWORD="root" -d mariadb:latest --character-set-server=utf8 --collation-server=utf8_bin --default-authentication-plugin=mysql_native_password
```
mariadb이미지를 이용해서 zabbix-mysql-server 컨테이너를 만듭니다.

## zabbix-server 컨테이너
```
docker run --name zabbix-server-mysql -t -e DB_SERVER_HOST="zabbix-mysql-server" -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix" -e MYSQL_ROOT_PASSWORD="root"  --link zabbix-mysql-server:mysql  -p 10051:10051 --restart unless-stopped -d zabbix/zabbix-server-mysql:ubuntu-5.0-latest
```
zabbix/zabbix-server-mysql:ubuntu-5.0-latest
zabbix server 5.0-latest 우분투 베이스 이미지를 이용해서 작성


 \-\-link zabbix-mysql-server:mysql 

mariadb를 링크 걸어줌 

## zabbix-web 컨테이너
```
docker run --name zabbix-web-nginx-mysql -t -e DB_SERVER_HOST="zabbix-mysql-server" -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix" -e MYSQL_ROOT_PASSWORD="root" --link zabbix-mysql-server:mysql --link zabbix-server-mysql:zabbix-server -p 80:8080 --restart unless-stopped -d zabbix/zabbix-web-nginx-mysql:ubuntu-5.0-latest
```
zabbix의 웹 컨테이너 엔진을 이용하는 이미지를 이용해서 작성

## zabbix-agent 컨테이너
```
docker run --name zabbix-agent --link zabbix-mysql-server:mysql --link zabbix-server-mysql:zabbix-server -e ZBX_HOSTNAME="Zabbix server" -e ZBX_SERVER_HOST="zabbix-server" -d zabbix/zabbix-agent:ubuntu-5.0-latest
```
agent 컨테이너 작성