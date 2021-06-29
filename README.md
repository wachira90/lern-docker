# lern-docker

lerning docker command and tip

## delete all images (be careful)

```
docker rmi $('docker images -aq')
```

## show all container

```
docker ps -a
```
## stop all container

```
docker stop $('docker images -aq')
```

## all id in container (with start and stop container)

```
docker ps -aq
```
## show id all on running container

```
docker ps -q
```

## list images id in machine 
```
docker images -q

```
## delete images all by id 

```
docker rmi $('docker images -q')
```

## stop container on runing by id

```
docker stop $('docker images -q')
```
