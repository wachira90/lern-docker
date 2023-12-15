# CACHE IMAGES OPERATION

## BUILD ORIGINAL IMAGES CACHE

docker build -t wachiradu/cache:oms-v1 .

Dockerfile

```Dockerfile
FROM openjdk:8-jdk-alpine
RUN apk --no-cache add --update curl
RUN apk --no-cache add --update nmap
RUN apk --no-cache add --update bind-tools
RUN apk --no-cache add --update mysql-client
RUN apk --no-cache add --update mlocate
RUN apk --no-cache add --update openssh
RUN apk --no-cache add --update nano
RUN apk --no-cache add --update busybox-extras
RUN apk --no-cache add --update redis
RUN sh -c updatedb
RUN apk -Uuv add groff less python py-pip
RUN pip install awscli==1.18.223
RUN apk --purge -v del py-pip
RUN rm /var/cache/apk/*
RUN echo http://dl-cdn.alpinelinux.org/alpine/v3.9/main >> /etc/apk/repositories
RUN echo http://dl-cdn.alpinelinux.org/alpine/v3.9/community >> /etc/apk/repositories
RUN apk --no-cache add --update mongodb mongodb-tools
```

## PRODUCTION IMAGES CACHE

Dockerfile 

docker build -t wachiradu/prod-oms:v1 .

```Dockerfile
FROM docker.io/wachiradu/cache:oms-v1
EXPOSE 6005
ADD exchange.jar /application/exchange.jar
CMD java -jar /application/exchange.jar && updatedb && locate -e bench-repo
```
