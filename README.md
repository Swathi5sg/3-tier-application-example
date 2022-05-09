# 3-tier-application-example

This is a 3-tier application example which involves applications like mysql as database, phpadmin as app-server and nginx as reverse-proxy.

Here we are using docker-compose to deploy all the 3 applications.

This a docker-compose.yaml file

'''
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
'''     
     
