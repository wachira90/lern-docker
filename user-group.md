# docker set user group

## docker compose

````
version: '3'
services:
    app:
        image: alpine
        user: "${UID}:${GID}"
````

````
$ docker-compose run app id
WARNING: The UID variable is not set. Defaulting to a blank string. 
WARNING: The GID variable is not set. Defaulting to a blank string. 
Creating docker-user-demo_app_run ... done 
uid=0(root) gid=0(root) groups=0(root)
````


````
$ env UID=${UID} GID=${GID} docker-compose run app id
 Creating docker-user-demo_app_run … done
 uid=1001 gid=0(root)
````



````
$ export UID=$(id -u)
bash: UID: readonly variable # ignore this, per above
$ export GID=$(id -g)
$ docker-compose run app id
Creating docker-user-demo_app_run … done
 uid=1001 gid=1001
````

````
export UID=$(id -u) 
export GID=$(id -g)
````

````
$ docker-compose run app id
Creating docker-user-demo_app_run ... done 
uid=1001 gid=1001
````


