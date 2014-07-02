FROM ubuntu:trusty
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
RUN apt-get -y update
RUN apt-get -y upgrade

# Keep upstart from complaining
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -sf /bin/true /sbin/initctl

# Otherwise you cannot add repositories
RUN apt-get install -y software-properties-common python-software-properties

# add php5.5 repository
#RUN add-apt-repository -y ppa:nginx/stable
#RUN add-apt-repository -y ppa:ondrej/php5 > /dev/null 2>&1 # dirty fix for "invalid UTF-8 in string"
RUN apt-get -y update

# Basic Requirements
RUN apt-get -y install nginx php5-fpm php5-mysql pwgen python-setuptools curl git unzip supervisor

# Use if you need a local MySQL
#RUN apt-get -y install mysql-server mysql-client

# Laravel/PHP Requirements
RUN apt-get -y install php5-curl php5-gd php5-intl php-pear php5-imagick php5-imap php5-mcrypt php5-memcache php5-ming php5-ps php5-pspell php5-recode php5-sqlite php5-tidy php5-xmlrpc php5-xsl
#php5-snmp giving errors on composer install
# mysql config - Use if you need a local MySQL
#RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf

# Laravel requirements
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/bin/composer

# nginx config
RUN sed -i -e"s/keepalive_timeout\s*65/keepalive_timeout 2/" /etc/nginx/nginx.conf
RUN sed -i -e"s/keepalive_timeout 2/keepalive_timeout 2;\n\tclient_max_body_size 100m/" /etc/nginx/nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# php-fpm config
RUN sed -i -e "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/upload_max_filesize\s*=\s*2M/upload_max_filesize = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/post_max_size\s*=\s*8M/post_max_size = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php5/fpm/php-fpm.conf
RUN find /etc/php5/cli/conf.d/ -name "*.ini" -exec sed -i -re 's/^(\s*)#(.*)/\1;\2/g' {} \;
# ADD ./opcache.ini /etc/php5/fpm/conf.d/opcache.ini
RUN sed -i -e "s/;listen.owner\s*=\s*www-data/listen.owner = www-data/g" /etc/php5/fpm/pool.d/www.conf
RUN sed -i -e "s/;listen.group\s*=\s*www-data/listen.group = www-data/g" /etc/php5/fpm/pool.d/www.conf

RUN mkdir /var/www
#RUN git clone https://github.com/joomla/joomla-cms.git /var/www
RUN chown -R www-data:www-data /var/www

# Add your nginx config
ADD ./docker/nginx-site.conf /etc/nginx/sites-enabled/default
ADD ./docker/supervisord.conf /etc/supervisord.conf

# Phpinfo for testing purposes
#RUN mkdir /var/www
#RUN echo "<?php phpinfo(); ?>" > /var/www/phpinfo.php

# Joomla Initialization and Startup Script
ADD ./docker/start.sh /start.sh
RUN chmod 755 /start.sh

# private expose
EXPOSE 80

CMD ["/bin/bash", "/start.sh"]
