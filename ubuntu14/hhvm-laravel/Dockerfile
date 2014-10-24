FROM joostlaan/baseimage-nginx-hhvm
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>

# Install composer
RUN wget http://getcomposer.org/composer.phar && \
    chmod +x composer.phar && \
    mv composer.phar /usr/local/bin/composer

# Required for laravel
RUN ln -s /usr/local/bin/composer /usr/bin/composer

# Install PHPUnit
RUN curl -qO https://phar.phpunit.de/phpunit.phar && \
    chmod +x phpunit.phar && \
    mv phpunit.phar /usr/local/bin/phpunit

# Install Laravel
RUN git clone https://github.com/laravel/laravel.git /var/www/vhosts/laravel/ && \
    composer --working-dir=/var/www/vhosts/laravel install --prefer-dist && \
    chown -R www-data:www-data /var/www/vhosts/laravel

# For newer NGINX
ADD ./nginx-site.conf /etc/nginx/sites-enabled/default
ADD ./supervisord.conf /etc/supervisord.conf
ADD ./config.hdf /var/www/vhosts/laravel/config.hdf

# Initialization and Startup Script
ADD ./start.sh /start.sh

