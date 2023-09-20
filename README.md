# lern-docker

lerning docker command and tip

## Test golang

```sh
mkdir code/
cd code/
go mod init code
nano main.go 
go run ./main.go
go build
./code
```

### main.go

```go
package main
import (
    "fmt"
    "log"
    "net/http"
)
func homePage(w http.ResponseWriter, r *http.Request){
    fmt.Fprintf(w, "Welcome to the HomePage!")
    fmt.Println("Endpoint Hit: homePage")
}
func api(w http.ResponseWriter, r *http.Request){
  fmt.Fprintf(w, "Welcome to the Api!")
  fmt.Println("Endpoint Hit: Api")
}
func handleRequests() {
    http.HandleFunc("/", homePage)
    http.HandleFunc("/api", api)
    fmt.Println("REST STARTING .... ")
    log.Fatal(http.ListenAndServe(":8099", nil))
}
func main() {
    handleRequests()
}
```

## nano Dockerfile

```Dockerfile
FROM golang:1.20.4
WORKDIR /go
COPY /code /go
CMD ["./code"]
EXPOSE 8099
```
## folder tree

```
.
├── code
│   ├── code
│   ├── go.mod
│   └── main.go
└── Dockerfile
```

## build 

```sh
docker build -f Dockerfile -t golang:1.20.4-test
```

## attach show console

```sh
docker run --name golang -p 8099:8099 -it golang:1.20.4-test
```

## run in detach mode

```sh
docker run --name golang -p 8099:8099 -d golang:1.20.4-test
```

## stop service name

```sh
docker stop golang
```

# delete service name

```sh
docker rm golang
```

## delete all images (be careful)

```
docker rmi $(docker images -aq)
```

## show all container

```sh
docker ps -a
docker ps -a | grep golang
```
## stop all container

```
docker stop $(docker images -aq)
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
docker rmi $(docker images -q)
```

## stop container on runing by id

```
docker stop $(docker images -q)
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

## run into container cli

```
# with host network for access internet 
docker run --rm -it -v /home/docker/jk-project/:/usr/src/ --network=host --entrypoint bash node:16.13.2

# with user node
docker run --rm -it -v /home/docker/jk-project/:/usr/src/ -u node  --entrypoint bash node:16.13.2

docker run --rm -it -v /home/docker/jk-project/:/usr/src/ --network=host -u node  --entrypoint bash node:16.13.2
```

## delete untag images 

```
docker rmi $(docker images -f "dangling=true" -q) --force
```

