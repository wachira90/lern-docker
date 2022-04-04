# ipv6 docker compose

## docker-compose.yml

````
version: "2.4"

services:
  alp2:
    container_name: alp2
    image: wachira90/nginx:1.21.4
#    command: ping6 -c 4 2001:db8:a::1
    ports:
      - "7001:80"
    networks:
      - netv61

networks:
  netv61:
    name: netv61
    enable_ipv6: true
    ipam:
      config:
        - subnet: 2001:db8:a::/64
          gateway: 2001:db8:a::1
````

## check

````
docker@test-imac:~/testipv6$ docker network inspect netv61
[
    {
        "Name": "netv61",
        "Id": "16ab8c9a75ae4dfe9d6ce26fd16679460de6c856000d3e7885b49b16bacb224c",
        "Created": "2022-04-04T18:13:13.770576049+07:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": true,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.27.0.0/16",
                    "Gateway": "172.27.0.1"
                },
                {
                    "Subnet": "2001:db8:a::/64",
                    "Gateway": "2001:db8:a::1"
                }
            ]
        },
        "Internal": false,
        "Attachable": true,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "87e4b6408da7d66e2fa54cfba50b687a24f2d2b867eea0387d4e06ad39000a04": {
                "Name": "alp2",
                "EndpointID": "706fe789a0509f0d0e21fc800a724e1d246ffb6dfd599c7371e7600522f52fb5",
                "MacAddress": "02:42:ac:1b:00:02",
                "IPv4Address": "172.27.0.2/16",
                "IPv6Address": "2001:db8:a::2/64"
            }
        },
        "Options": {},
        "Labels": {
            "com.docker.compose.network": "netv61",
            "com.docker.compose.project": "testipv6",
            "com.docker.compose.version": "1.21.2"
        }
    }
]
docker@test-imac:~/testipv6$
````

## create interface ipv6 command
````
docker network create --ipv6 \
--subnet="2001:db8:1::/64" \
--gateway="2001:db8:1::1" mynetv61
````


