## 1. DB 설치
- 공식홈페이지 dmg 파일로 설치 

## 2. DB 세팅 
```
-- 설치 경로 이동
cd /usr/local/mysql/bin

-- mysql 실행
./mysql -uroot -p


-- Database 생성 
mysql> CREATE DATABASE spring; 

-- Database 교체
mysql> show databases;
mysql> use spring;

-- Table 생성
mysql> create table users (
    -> id varchar(10) primary key,
    -> name varchar(20) not null,
    -> password varchar(10) not null
    -> );

```

## 3. springboot maven dependency 추가
```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.30</version>
</dependency>
``` 



### 99. DB 삭제 
```
<MySQL 공식홈페이지의 DMG파일>로 설치했을때,

1. sudo rm -rf /usr/local/mysql

2. sudo rm -rf /Library/PreferencePanes/My*

3. sudo rm -rf /var/db/receipts/com.mysql.*
 
```