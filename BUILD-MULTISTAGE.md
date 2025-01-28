# BUILD MULTISTAGE


## DEFAULT BUILD

```Dockerfile
# FROM public.ecr.aws/docker/library/maven:3.9.0-eclipse-temurin-8-alpine AS build
FROM public.ecr.aws/docker/library/maven:3.8.6-eclipse-temurin-8-alpine AS build
WORKDIR /app
COPY . .
RUN mvn clean install -DskipTests
FROM docker.io/wachiradu/cache:oms-v1
WORKDIR /application
EXPOSE 8080
COPY --from=build /app/target/*.jar /application/jmrtd-api.jar
CMD java -jar /application/jmrtd-api.jar && updatedb && locate -e bench-repo
```

## BUILD ARGUMENT


### ARG BUILD_ENV=test

### RUN npm run build:${BUILD_ENV}

### docker build -t registry.com/web_front:v1 --build-arg BUILD_ENV=dev . --no-cache 


```Dockerfile
FROM public.ecr.aws/docker/library/maven:3.8.6-eclipse-temurin-8-alpine AS build
WORKDIR /app
COPY . .
RUN mvn clean install -P${BUILD_ENV} -DskipTests -Dmaven.repo.local=/app/.m2/repository
FROM docker.io/wachiradu/cache:oms-v1
WORKDIR /application
EXPOSE 10000
COPY --from=build /app/er_market/target/*.jar /application/er_market.jar
CMD java -jar /application/er_market.jar && updatedb && locate -e bench-repo
```

## PRODUCTION

```Dockerfile
FROM public.ecr.aws/docker/library/maven:3.8.6-eclipse-temurin-8-alpine AS build
WORKDIR /app
COPY . .
RUN mvn clean install -Pdev -DskipTests -Dmaven.repo.local=/app/.m2/repository
FROM docker.io/wachiradu/cache:oms-v1
WORKDIR /application
EXPOSE 10000
COPY --from=build /app/er_market/target/*.jar /application/er_market.jar
CMD java -jar /application/er_market.jar && updatedb && locate -e bench-repo
```

## MINI BUILD

```Dockerfile
FROM docker.io/wachiradu/cache:oms-v1
EXPOSE 10000
ADD er_market.jar /application/er_market.jar
CMD java -jar /application/er_market.jar && updatedb && locate -e bench-repo
```


