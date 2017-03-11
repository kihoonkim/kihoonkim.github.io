---
title: Docker Commands
date: 2016-01-20 16:16:39
tags: docker
categories: Docker
thumbnail: /images/docker.png
---
# search image
```bash
docker search ubuntu
docker search mongo
```

# pull image
```bash
docker pull mongo
```

# image 목록 보기
```bash
docker images
```

# image를 container로 실행
```bash
docker run --name mgdb mongo
docker ps -a
docker rm mgdb
```

# 실행중인 container에 접속
```bash
docker run -d --name mgdb mongo
docker ps
docker logs -f mgdb
docker attach mgdb
docker start mgdb
docker exec mgdb echo "test"
```

# dockerfile 만들기
```bash
vi dockerfile
```
```bash
FROM java:openjdk-8u45-jdk
MAINTAINER koreakihoon@gmail.com
ADD user-0.0.1-SNAPSHOT.jar .
CMD java -jar user-0.0.1-SNAPSHOT.jar
EXPOSE 8081
```

# dockerfile로 image 만들기
```bash
docker build -t user_svc .
docker images
```

# container link
```bash
docker run -d -p 8081:8081 --link mgdb:mgdb --name user_svc user_svc
docker ps
```

# container 중지 및 삭제
```bash
docker stop user_svc   mgdb
docker rm    user_svc   mgdb
```

# image 삭제
```bash
docker rmi user_svc
docker rmi mongo
```

# docker compose 설정
```bash
vi docker-compose.yml
```
```yaml
users:
  buaild: .
  ports:
    - "8081:8081"
  links:
    - mgdb
mgdb:
  image: mongo
```

# docker compose로 실행
```bash
docker-compose up
docker images
docker ps
```
