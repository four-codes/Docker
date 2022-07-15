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
