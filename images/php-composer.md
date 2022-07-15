```Dockerfile

FROM php:7.4.0-alpine-buster
WORKDIR /var/www/html/
RUN docker-php-ext-install mysqli pdo pdo_mysql
RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer
COPY . .
RUN composer install
```


```yml
---
version: "3.5"
services:
  lumen:
    ports:
      - "8000:8000"
    image: ravimanickavasagam/php 
    command: php -S lumen:8000 -t public
    restart: always
```


```Dockerfile
FROM node:12.13.0 as build
WORKDIR /usr/src/app
COPY package.json /usr/src/app/
RUN npm install -g npm@latest
RUN npm install react@latest --lagecy-peer-deps --force
Copy . .
RUN npm run build

FROM ubuntu:latest
RUN apt update && apt install nginx -y
RUN mkdir -p /var/www/html/react.thecloudtech.in
COPY --from=build /usr/src/app/build /var/www/html/react.thecloudtech.in
RUN rm -rf etc/nginx/sites-enabled/default

COPY certs/react.thecloudtech.in/react.thecloudtech.in.conf /etc/nginx/sites-enabled/react.thecloudtech.in.conf
COPY certs/lumen.thecloudtech.in/lumen.thecloudtech.in.conf /etc/nginx/sites-enabled/lumen.thecloudtech.in.conf

RUN mkdir -p /etc/nginx/ssl-certificate/
COPY certs/react.thecloudtech.in/certificate.crt /etc/nginx/ssl-certificate/
COPY certs/react.thecloudtech.in/private.key /etc/nginx/ssl-certificate/

RUN mkdir -p /etc/nginx/ssl/
COPY certs/lumen.thecloudtech.in/certificate.crt /etc/nginx/ssl/
COPY certs/lumen.thecloudtech.in/private.key /etc/nginx/ssl/

RUN ln -sf /dev/stdout /var/log/nginx/react.thecloudtech.in.access.log \
        && ln -sf /dev/stderr /var/log/nginx/react.thecloudtech.in.error.log
CMD ["nginx", "-g", "daemon off;"]
```

```
server {
    listen       80;
    server_name  react.thecloudtech.in;
    return 301 https://react.thecloudtech.in;

    location / {
        root   /var/www/html/react.thecloudtech.in/;
        index  index.html index.htm;
    }
}

server {
    listen               443 ssl;
    ssl                  on;
    ssl_certificate      /etc/nginx/ssl-certificate/certificate.crt;
    ssl_certificate_key  /etc/nginx/ssl-certificate/private.key;
    server_name          react.thecloudtech.in;
    access_log           /var/log/nginx/react.thecloudtech.in.access.log;
    error_log            /var/log/nginx/react.thecloudtech.in.error.log;

    location     / {
        root         /var/www/html/react.thecloudtech.in/;
        index        index.html index.htm;
    }
}   
```

