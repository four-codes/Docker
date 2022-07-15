```Dockerfile

FROM php:7.4.0-alpine-buster
WORKDIR /var/www/html/
RUN docker-php-ext-install mysqli pdo pdo_mysql
RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer
COPY . .
RUN composer install
```
