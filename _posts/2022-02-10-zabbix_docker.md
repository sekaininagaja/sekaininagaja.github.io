---
layout: post
title: zabbix를 docker로 간단하게 설치하기
categories: Docker Zabbix
---

```
docker run --name zabbix-mysql-server -t -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix" -e MYSQL_ROOT_PASSWORD="root" -d mariadb:latest --character-set-server=utf8 --collation-server=utf8_bin --default-authentication-plugin=mysql_native_password
```
```
docker run --name zabbix-server-mysql -t -e DB_SERVER_HOST="zabbix-mysql-server" -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix" -e MYSQL_ROOT_PASSWORD="root"  --link zabbix-mysql-server:mysql  -p 10051:10051 --restart unless-stopped -d zabbix/zabbix-server-mysql:ubuntu-5.0-latest
```

```
docker run --name zabbix-web-nginx-mysql -t -e DB_SERVER_HOST="zabbix-mysql-server" -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix" -e MYSQL_ROOT_PASSWORD="root" --link zabbix-mysql-server:mysql --link zabbix-server-mysql:zabbix-server -p 80:8080 --restart unless-stopped -d zabbix/zabbix-web-nginx-mysql:ubuntu-5.0-latest
```


```
docker run --name zabbix-agent --link zabbix-mysql-server:mysql --link zabbix-server-mysql:zabbix-server -e ZBX_HOSTNAME="Zabbix server" -e ZBX_SERVER_HOST="zabbix-server" -d zabbix/zabbix-agent:ubuntu-5.0-latest
```