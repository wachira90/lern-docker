#  connect from a container to a service on the myhost

### use `host.docker.internal` when need connect to myhost

## 1. Run the following command to start a simple HTTP server on port 8000.

````
python -m http.server 8000
````
If you have installed Python 2.x, run python -m SimpleHTTPServer 8000.

## 2. Now, run a container, install curl, and try to connect to the host using the following commands:

````
 docker run --rm -it alpine sh
 apk add curl
 curl http://host.docker.internal:8000
 exit
````

#### more info

`https://docs.docker.com/desktop/mac/networking/`
