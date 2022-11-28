# set ipaddress to container

docker-compose.yml

````
version: '2.4'

services:

    web1:
        image: nginx:latest
        volumes:
            - ./conf/nginx.conf:/etc/nginx/nginx.conf
            - ./conf/default.conf:/etc/nginx/conf.d/default.conf
            - ./html/:/usr/share/nginx/html/
        ports:
            - "80:80"
        networks:
            web-nw:
                ipv4_address: 172.16.16.10
                
networks:
  web-nw:
    driver: bridge
    ipam:
      driver: default
      config:
      - subnet: 172.16.16.0/16
        gateway: 172.16.16.1
````        
