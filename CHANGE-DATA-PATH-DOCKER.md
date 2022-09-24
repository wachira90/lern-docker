# CHANGE-DATA-PATH-DOCKER

## on user docker

````
mkdir .data

nano /etc/docker/daemon.json

{
"graph": "/home/docker/.data"
}

systemctl restart docker
````
