# Docker Set User Group

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

In **Docker Compose**, you can map or set a **user and group (UID and GID)** for your container in several ways, depending on your goal — whether you want the container process to run under a specific UID/GID, or you want to ensure file ownership matches your host user.

Here’s a breakdown of the best methods:

---

## 🧩 **Option 1: Use `user:` in `docker-compose.yml`**

You can directly set the **UID** and **GID** using the `user` directive.

```yaml
version: "3.9"

services:
  app:
    image: ubuntu:latest
    container_name: myapp
    user: "${UID}:${GID}"     # Map to your host user and group
    volumes:
      - ./data:/app/data
    command: sleep infinity
```

Then run with:

```bash
export UID=$(id -u)
export GID=$(id -g)
docker-compose up -d
```

✅ **Effect:**

* The container runs processes as your current host user.
* Any files written to mounted volumes (`./data`) will have your user ownership, not `root`.

---

## 🧩 **Option 2: Use Dockerfile + Compose (Custom user inside container)**

If the image doesn’t have your UID/GID inside, you can create them.

### Dockerfile

```Dockerfile
FROM ubuntu:latest

ARG UID=1000
ARG GID=1000
RUN groupadd -g ${GID} appgroup && \
    useradd -m -u ${UID} -g ${GID} appuser

USER appuser
WORKDIR /home/appuser
```

### docker-compose.yml

```yaml
version: "3.9"

services:
  app:
    build:
      context: .
      args:
        UID: ${UID}
        GID: ${GID}
    volumes:
      - ./data:/home/appuser/data
    command: sleep infinity
```

Run:

```bash
export UID=$(id -u)
export GID=$(id -g)
docker-compose up -d
```

✅ **Effect:**

* Builds a user inside the container matching your host UID/GID.
* Prevents permission issues for mounted directories.

---

## 🧩 **Option 3: Use entrypoint script to dynamically fix ownership**

If you can’t rebuild the image, you can fix permissions at runtime.

```yaml
version: "3.9"

services:
  app:
    image: ubuntu:latest
    volumes:
      - ./data:/app/data
    entrypoint: >
      bash -c "
      chown -R ${UID}:${GID} /app/data &&
      su -s /bin/bash -c 'your_app_command' \#${UID}"
    environment:
      - UID=${UID}
      - GID=${GID}
```

---

## ✅ **Best Practice for Dev Environments**

* Use **Option 1** if you just need to run as your host user (quick & easy).
* Use **Option 2** if you need a stable container user that matches your host (more portable).

---




