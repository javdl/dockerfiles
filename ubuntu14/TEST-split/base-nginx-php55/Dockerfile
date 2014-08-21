FROM joostlaan/base-nginx
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>

# Basic Requirements
RUN apt-get -y install php5-fpm php5-mysql pwgen python-setuptools curl git unzip supervisor

# PHP / CMS / Framework Requirements
RUN apt-get -y install php5-curl php5-gd php5-intl php-pear php5-imagick php5-imap php5-mcrypt php5-memcache php5-ming php5-ps php5-pspell php5-recode php5-sqlite php5-tidy php5-xmlrpc php5-xsl

# php-fpm config
gRUN sed -i -e "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/upload_max_filesize\s*=\s*2M/upload_max_filesize = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/post_max_size\s*=\s*8M/post_max_size = 100M/g" /etc/php5/fpm/php.ini
RUN sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php5/fpm/php-fpm.conf
RUN find /etc/php5/cli/conf.d/ -name "*.ini" -exec sed -i -re 's/^(\s*)#(.*)/\1;\2/g' {} \;

# Add your nginx config
ADD ./docker/www.conf /etc/php5/fpm/pool.d/www.conf
ADD ./docker/nginx-site.conf /etc/nginx/sites-enabled/default
ADD ./docker/supervisord.conf /etc/supervisord.conf

# Joomla Initialization and Startup Script
ADD ./docker/start.sh /start.sh
RUN chmod 755 /start.sh

# private expose
EXPOSE 80

CMD ["/bin/bash", "/start.sh"]