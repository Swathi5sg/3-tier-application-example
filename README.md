# 3-tier-application-example

This is a 3-tier application example which involves applications like mysql as database, phpadmin as app-server and nginx as reverse-proxy.

Here we are using docker-compose to deploy all the 3 applications.

This a docker-compose.yaml file

```
version: '2'
services:
 php:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    restart: on-failure
    networks:
      - net
    deploy:
        resources:
          limits:
             cpus: '1'
             memory: 256M     
    links:
      - mysql:db
    depends_on:
      - mysql
 
 mysql:
    image: k0st/alpine-mariadb
    container_name: mysql
    restart: on-failure
    networks:
      - net
    deploy:
       resources:
          limits:
             cpus: '1'
             memory: 256M     
    volumes:
      - ./data/mysql:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=mydb
      - MYSQL_USER=myuser
      - MYSQL_PASSWORD=mypass
 
 nginx:
    image: nginx:stable-alpine
    container_name: nginx
    restart: on-failure
    networks:
      - net
    deploy:
       resources:
          limits:
             cpus: '1'
             memory: 256M     
    ports:
      - "81:80"
    volumes:
      - ./nginx/log:/var/log/nginx
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - php
networks:
  net:
     driver: bridge
```

Here we are taking nginx.conf file in order to implement nginx as a reverse-proxy for the all the request that hit the php app

This is nginx.conf file

```
worker_processes 1;
events {
 worker_connections 1024;
}
http {
 sendfile off;
 server {
 listen 80;
 location / {
 proxy_pass http://php;
 proxy_set_header Host $host;
 proxy_redirect off;
 }
 }
}
```

Now execute the docker-compose.yaml file to deploy the applocations.
```
sudo docker-compose up -d
```


To check the the running application

![Screen Shot 2022-05-09 at 4 39 07 PM](https://user-images.githubusercontent.com/35251635/167399590-9a75bde6-73ca-4c93-b7c7-ca3f0d4a2575.png)



Now access the phpmyadmin portal on the web using your localhost ip and port 81, which has been mapped in docker-compose file

![Screen Shot 2022-05-09 at 4 39 53 PM](https://user-images.githubusercontent.com/35251635/167400079-63118b4a-e673-4691-9ffc-8cffdb1ee6f7.png)


     
