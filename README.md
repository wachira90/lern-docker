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

## socket permision

### error message 

```
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get
"http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json": dial unix /var/run/docker.sock: connect: permission denied

user@bkk-vpn:~$ ls -la /var/run/docker.sock
srw-rw---- 1 root docker 0 ม.ค.  31 22:27 /var/run/docker.sock

user@bkk-vpn:~$ sudo chmod 666 /var/run/docker.sock

user@bkk-vpn:~$ ls -la /var/run/docker.sock
srw-rw-rw- 1 root docker 0 ม.ค.  31 22:27 /var/run/docker.sock
```

### fix by permission

```
sudo chmod 666 /var/run/docker.sock
```



