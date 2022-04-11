# docker working directory


````
version: "3"
services:
  node1:
    container_name: node1
    image: node:14-alpine3.14
    restart: unless-stopped
    working_dir: /var/www/app/
    volumes:
      - ./app:/var/www/app/:rw
    command: node app.js
    expose:
      - 5000
````
