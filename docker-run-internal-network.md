# docker run internal network.

## install docker-compose

````
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
````

## docker-compose.yml

````
version: '3'

services:
  service1:
    image: wachira90/nginx:1.22
    networks:
      - my_network
  service2:
    image: wachira90/nginx:1.22
    networks:
      - my_network

networks:
  my_network:
    driver: bridge
    internal: true
````

## list network 
````
root@node35716-env-6051616:~/example# docker network ls
NETWORK ID     NAME                 DRIVER    SCOPE
882c7168b74f   bridge               bridge    local
264e601f601c   example_my_network   bridge    local
f48c323e01a6   host                 host      local
43252ee60855   none                 null      local
root@node35716-env-6051616:~/example#
````

## inspect network
````
docker network inspect example_my_network
````

## result

````
root@node35716-env-xxxx:~/example# docker network inspect example_my_network
[
    {
        "Name": "example_my_network",
        "Id": "264e601f601ce2d94e1b29123241f042f8279c743b0196b4496aaadff66bd3ec",
        "Created": "2023-01-04T22:14:14.311771013+07:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": true,
        "Attachable": true,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "6f8e9fdbcc52cad95abea4e82bec86a36af14a9a2a78b8e5ad99aa585951d976": {
                "Name": "example_service1_1",
                "EndpointID": "a8b9a6c955d48a06fad759974703ecf58aba7cd5bcc4a3492f59adb787890e22",
                "MacAddress": "02:42:ac:13:00:03",
                "IPv4Address": "172.19.0.3/16",
                "IPv6Address": ""
            },
            "90a81924928bd678f0d11d2b61fbd196948ac1ae543609501c8040629919c602": {
                "Name": "example_service2_1",
                "EndpointID": "bee6ac3679faa17fdf8f58186d61967c45cc8fcec6ec22a8e108e1c0c266b6b5",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {
            "com.docker.compose.network": "my_network",
            "com.docker.compose.project": "example",
            "com.docker.compose.version": "1.29.2"
        }
    }
]
root@node35716-env-xxxx:~/example#
````

## test and result

````
root@node35716-env-xxxx:~/example# curl http://172.19.0.2
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
root@node35716-env-xxxx:~/example#
````
