FROM joostlaan/base-nginx-php55
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list

RUN git clone https://github.com/joomla/joomla-cms.git /var/www
RUN chown -R www-data:www-data /var/www

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