FROM joostlaan/baseimage-nginx-hhvm
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>

# Laravel requirements
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/bin/composer

# Install composer
RUN bash -c "wget http://getcomposer.org/composer.phar && chmod +x composer.phar && mv composer.phar /usr/local/bin/composer"

# Install PHPUnit
RUN bash -c "wget https://phar.phpunit.de/phpunit.phar && chmod +x phpunit.phar && mv phpunit.phar /usr/local/bin/phpunit"

# Pull Laravel project from github
RUN git clone https://github.com/laravel/laravel.git /var/www/vhosts/laravel/

RUN composer --working-dir=/var/www/vhosts/laravel install --prefer-dist

RUN chown -R www-data:www-data /var/www/vhosts/laravel

# Add config files (always at the end of Dockerfile to improve caching so we have faster builds)

# For newer NGINX
ADD ./nginx-site.conf /etc/nginx/sites-enabled/default
ADD ./supervisord.conf /etc/supervisord.conf
ADD ./config.hdf /var/www/vhosts/laravel/config.hdf

# Initialization and Startup Script
ADD ./start.sh /start.sh
RUN chmod 755 /start.sh

# private expose
EXPOSE 80

CMD ["/bin/bash", "/start.sh"]
