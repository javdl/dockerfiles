FROM joostlaan/base
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>

# add NGINX ppa repository
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys C300EE8C
RUN echo deb http://ppa.launchpad.net/nginx/stable/ubuntu trusty main | sudo tee /etc/apt/sources.list.d/nginx.list

RUN apt-get -y update

# Basic Requirements
RUN apt-get -y install nginx

# nginx config
RUN sed -i -e"s/keepalive_timeout\s*65/keepalive_timeout 2/" /etc/nginx/nginx.conf
RUN sed -i -e"s/keepalive_timeout 2/keepalive_timeout 2;\n\tclient_max_body_size 100m/" /etc/nginx/nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

RUN mkdir /var/www
RUN chown -R www-data:www-data /var/www

# Add your nginx config
ADD ./docker/nginx-site.conf /etc/nginx/sites-enabled/default
ADD ./docker/supervisord.conf /etc/supervisord.conf

# Joomla Initialization and Startup Script
ADD ./docker/start.sh /start.sh
RUN chmod 755 /start.sh

# private expose
EXPOSE 80

CMD ["/bin/bash", "/start.sh"]