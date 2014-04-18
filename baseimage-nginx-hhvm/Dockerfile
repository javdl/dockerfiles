FROM ubuntu
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
RUN apt-get update
RUN apt-get -y upgrade

# Keep upstart from complaining
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -sf /bin/true /sbin/initctl

# Otherwise you cannot add repositories
RUN apt-get install -y software-properties-common python-software-properties
RUN apt-get install -y wget nano

# Add HHVM repository
RUN wget -O - http://dl.hhvm.com/conf/hhvm.gpg.key | sudo apt-key add -
RUN echo deb http://dl.hhvm.com/ubuntu precise main | sudo tee /etc/apt/sources.list.d/hhvm.list
# for ubuntu 12.04
RUN add-apt-repository -y ppa:mapnik/boost

# add nginx repository
RUN add-apt-repository -y ppa:nginx/stable

# Run after setting repositories
RUN apt-get update -y

# Basic Requirements - Installing Nginx before HHVM allowed HHVM to detect Nginx and create the /etc/nginx/hhvm.conf file for you.
RUN apt-get -y install nginx hhvm python-setuptools curl git unzip

# nginx config
RUN sed -i -e"s/keepalive_timeout\s*65/keepalive_timeout 2/" /etc/nginx/nginx.conf
RUN sed -i -e"s/keepalive_timeout 2/keepalive_timeout 2;\n\tclient_max_body_size 100m/" /etc/nginx/nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# Supervisor Install
RUN /usr/bin/easy_install supervisor

RUN mkdir /var/www
RUN chown -R www-data:www-data /var/www

# Phpinfo for testing purposes, with hiphop it will only show text: Hiphop
RUN echo "<?php phpinfo(); ?>" > /var/www/phpinfo.php

# For newer NGINX
ADD ./nginx-site.conf /etc/nginx/sites-enabled/default
ADD ./supervisord.conf /etc/supervisord.conf
ADD ./config.hdf /var/www/config.hdf

# Initialization and Startup Script
ADD ./start.sh /start.sh
RUN chmod 755 /start.sh

# private expose
EXPOSE 80

CMD ["/bin/bash", "/start.sh"]
