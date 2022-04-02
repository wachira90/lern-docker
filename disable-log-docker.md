# disable log docker

## syntax

````
logging:
    driver: none
````

## usage
````
services:
  website:
    image: nginx
    logging:
      driver: none
````

## use another
````
logging:
  driver: "syslog"
````    
