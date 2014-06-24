FROM joostlaan/trusty-nginx-php55-composer
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>

RUN git clone https://github.com/joomla/joomla-cms.git /var/www

# Add your nginx config
ADD ./docker/nginx-site.conf /etc/nginx/sites-enabled/default
ADD ./docker/supervisord.conf /etc/supervisord.conf

# Phpinfo for testing purposes
#RUN mkdir /var/www
RUN echo "<?php phpinfo(); ?>" > /var/www/phpinfo.php

RUN chown -R www-data:www-data /var/www

# Joomla Initialization and Startup Script
ADD ./docker/start.sh /start.sh
RUN chmod 755 /start.sh

# private expose
EXPOSE 80

CMD ["/bin/bash", "/start.sh"]
