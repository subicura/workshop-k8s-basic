# Docker Compose

도커 컴포즈에 대해 기본적인 사용법을 실습합니다.

## Task 1. Docker Compose 기본 실습

### YAML

[YAML Syntax](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html)

### 기본 명령어

| 명령어  |  설명  |
|---|---|
| up | 시작 |
| stop | 중지 |
| down | 중지 후 삭제 |
| ps | 목록 |
| logs | 로그보기 |

### 도커 컴포즈 실습

`guide-02`라는 폴더를 만듭니다.

**간단한 웹 애플리케이션 생성**

guide-02/docker-workshop-app/docker-compose.yml

```yml
version: '3'
services:
  web:
    image: subicura/docker-workshop-app:1
    ports:
      - "4567:4567"
```

```
docker-compose up -d
```

http://xxxx:4567 접속

**wordpress 생성**

guide-02/wordpress/docker-compose.yml

```yml
version: '3'
services:
  wordpress:
    image: wordpress
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_NAME: wp
      WORDPRESS_DB_USER: wp
      WORDPRESS_DB_PASSWORD: wp
    ports:
      - "8000:80"
    restart: always
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: wp
      MYSQL_DATABASE: wp
      MYSQL_USER: wp
      MYSQL_PASSWORD: wp
```

```
docker-compose up -d
```

**목록**

```
docker-compose ps
```

**로그**

```
docker-compose logs -f
```

**종료 후 제거**

```
docker-compose down
```

## Exam 1. 방명록 만들기

guide-02/guestbook/docker-compose.yml

**frontend**

이미지
- subicura/guestbook-frontend:latest

환경변수
- PORT # ex) 8000
- GUESTBOOK_API_ADDR # ex) APISERVER:8000

**backend**

이미지
- subicura/guestbook-backend:latest

환경변수
- PORT # ex) 8000
- GUESTBOOK_DB_ADDR # ex) DB:27017

**mongodb**

이미지
- mongo:4

기본포트
- 27017

## 정리

```
docker-compose down
docker system prune -a
```



