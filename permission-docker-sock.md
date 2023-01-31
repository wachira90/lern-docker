# permission docker.sock

## error

Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json": dial unix /var/run/docker.sock: connect: permission denied

````
sysadmin@bkk-vpn:~$ docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json": dial unix /var/run/docker.sock: connect: permission denied
````

## check permission

````
sysadmin@bkk-vpn:~$ ls -la /var/run/docker.sock
srw-rw---- 1 root docker 0 ม.ค.  31 22:27 /var/run/docker.sock
````

## permission

````
sysadmin@bkk-vpn:~$ sudo chmod 666 /var/run/docker.sock
sysadmin@bkk-vpn:~$ ls -la /var/run/docker.sock
srw-rw-rw- 1 root docker 0 ม.ค.  31 22:27 /var/run/docker.sock
````

## test

````
sysadmin@bkk-vpn:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
sysadmin@bkk-vpn:~$
````

