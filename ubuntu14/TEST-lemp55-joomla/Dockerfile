# runnable base
FROM ubuntu:trusty
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>
##RUN echo "deb http://archive.ubuntu.com/ubuntu trusty main universe" > /etc/apt/sources.list
RUN apt-get update
RUN apt-get -y upgrade

# Keep upstart from complaining
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -sf /bin/true /sbin/initctl

# Let the conatiner know that there is no tty
ENV DEBIAN_FRONTEND noninteractive

# Otherwise you cannot add repositories
RUN apt-get install -y software-properties-common python-software-properties
RUN apt-get install -y wget nano openssl libssl-dev

# REPOS
## RUN apt-get -y update && locale-gen en_GB.UTF-8
## RUN echo "Europe/London" | tee /etc/timezone; dpkg-reconfigure --frontend noninteractive tzdata
##RUN apt-get install -y -q python-software-properties
##RUN add-apt-repository -y "deb http://archive.ubuntu.com/ubuntu $(lsb_release -sc) universe"
##RUN add-apt-repository -y "deb http://archive.ubuntu.com/ubuntu $(lsb_release -sc) multiverse"
##RUN add-apt-repository -y "deb http://archive.ubuntu.com/ubuntu $(lsb_release -sc)-updates multiverse"

#RUN apt-get -y update

# BASICS
RUN apt-get install -y -q vim nano curl git subversion make wget build-essential g++ sendmail unzip logrotate python-setuptools

## Nginx
RUN wget -q http://nginx.org/keys/nginx_signing.key -O- | apt-key add -
#RUN add-apt-repository -y "deb http://nginx.org/packages/ubuntu/ $(lsb_release -sc) nginx"
RUN mkdir -p /usr/src/nginx; cd /usr/src/nginx; wget -q https://github.com/pagespeed/ngx_pagespeed/archive/v1.8.31.3-beta.zip;\
  unzip v1.8.31.3-beta.zip;\
  cd /usr/src/nginx/ngx_pagespeed-1.8.31.3-beta/;\
  /bin/bash scripts/pagespeed_libraries_generator.sh > /usr/src/nginx/pagespeed_libraries.conf;\
  wget -q https://dl.google.com/dl/page-speed/psol/1.8.31.3.tar.gz;\
  tar -xzf 1.8.31.3.tar.gz

RUN DEBIAN_FRONTEND=noninteractive apt-get -y update;\
  apt-get build-dep -y -q nginx;\
  apt-get install -y -q nginx;\
  cd /usr/src/nginx; apt-get source -q -y nginx;\
  cd /usr/src/nginx/nginx-*/;\
  ./configure --add-module=/usr/src/nginx/ngx_pagespeed-1.8.31.3-beta --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-mail --with-mail_ssl_module --with-file-aio --with-http_spdy_module --with-cc-opt='-g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Wformat-security -Wp,-D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,--as-needed' --with-ipv6;\
  make; make install; mkdir -p /var/ngx_pagespeed_cache; chown -R www-data:www-data /var/ngx_pagespeed_cache;\
  mv /usr/src/nginx/pagespeed_libraries.conf /etc/nginx/pagespeed_libraries.conf;\
  rm -rf /usr/src/nginx
  

## PHP
#RUN add-apt-repository -y ppa:ondrej/php5; apt-get -y update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y -q\
  openssh-server supervisor\
  cron\
  php5-fpm php5 php5-cli php5-dev php-pear php5-common php5-apcu\
  php5-mcrypt php5-gd php5-mysql php5-curl php5-json\
  memcached php5-memcached\
  imagemagick graphicsmagick graphicsmagick-libmagick-dev-compat php5-imagick trimage

RUN pecl install memcache; 
ADD ./docker/php5/mods-available/memcache.ini /etc/php5/mods-available/memcache.ini
RUN /usr/sbin/php5enmod memcache

## CONFIG
# USER
RUN useradd -d /var/www/app --no-create-home --shell /bin/bash -g www-data -G adm user

# Nginx+FPM
ADD ./docker/nginx/sites-enabled/default /etc/nginx/sites-enabled/default
ADD ./docker/nginx/nginx.conf /etc/nginx/nginx.conf

# PHP
ADD ./docker/php5/fpm/php.ini /etc/php5/fpm/php.ini
ADD ./docker/php5/fpm/php-fpm.conf /etc/php5/fpm/php-fpm.conf
RUN rm -rf /etc/php5/fpm/pool.d/*
ADD ./docker/php5/fpm/pool.d/www1.conf /etc/php5/fpm/pool.d/www1.conf
ADD ./docker/php5/fpm/pool.d/www2.conf /etc/php5/fpm/pool.d/www2.conf
ADD ./docker/php5/mods-available/opcache.ini /etc/php5/mods-available/opcache.ini
RUN mkdir -p /var/log/app; chmod 664 /var/log/app/; chown user:www-data /var/log/app/





# Pull Laravel project from github
#RUN rm -rf /var/www/vhosts/laravel
RUN git clone https://github.com/joomla/joomla-cms.git /var/www

# ADD . /var/www

#RUN mkdir /var/www
RUN chown -R www-data:www-data /var/www

# Phpinfo for testing purposes, with hiphop it will only show text: Hiphop
RUN echo "<?php phpinfo(); ?>" > /var/www/phpinfo.php




# Supervisor Install
RUN /usr/bin/easy_install supervisor

# Supervisor Install
#RUN /usr/bin/easy_install supervisor
#RUN apt-get -y install supervisor

# Supervisord
#RUN mkdir -p /var/log/supervisor

# SSH
#ADD ./docker/ssh/sshd_config /etc/ssh/sshd_config
ADD ./docker/supervisord.conf /etc/supervisord.conf

# Initialization and Startup Script
ADD ./docker/start.sh /start.sh
RUN chmod 755 /start.sh

# private expose
EXPOSE 80

CMD ["/bin/bash", "/start.sh"]