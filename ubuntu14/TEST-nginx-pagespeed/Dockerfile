##
# Custom Nginx build with SPDY 2 & mod_pagespeed support

FROM ubuntu:trusty
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>
RUN echo "deb http://archive.ubuntu.com/ubuntu trusty main universe" > /etc/apt/sources.list

# Add Nginx source repository & signing key
#RUN echo "deb http://nginx.org/packages/ubuntu/ precise nginx" >> /etc/apt/sources.list.d/nginx-precise.list
RUN apt-key adv --fetch-keys http://nginx.org/keys/nginx_signing.key
RUN echo "deb-src http://nginx.org/packages/ubuntu/ trusty nginx" >> /etc/apt/sources.list.d/nginx-trusty.list

RUN apt-get -y update
RUN apt-get -y upgrade

# Set the env variable DEBIAN_FRONTEND to noninteractive
# ENV DEBIAN_FRONTEND noninteractive

# Fix locales
# RUN locale-gen en_US.UTF-8 && dpkg-reconfigure locales

# Enable universe & src repo's
# RUN echo "deb http://archive.ubuntu.com/ubuntu precise main restricted universe\ndeb-src http://archive.ubuntu.com/ubuntu precise main restricted universe\ndeb http://archive.ubuntu.com/ubuntu precise-updates main restricted universe\ndeb-src http://archive.ubuntu.com/ubuntu precise-updates main restricted universe\n" > /etc/apt/sources.list 

# Install build tools for nginx
#RUN apt-get build-dep nginx-full -y
#RUN sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && apt-get update && apt-get -y upgrade && apt-get install -y build-essential && apt-get install -y software-properties-common && apt-get install -y byobu curl git htop man unzip vim wget

#RUN apt-get clean
#RUN rm -rf /var/lib/apt/lists/*



RUN apt-get update &&  apt-get install nano -y
RUN apt-get install wget unzip build-essential -y

# Install Nginx from a tarball
# Install build tools for nginx
ENV NGINX_VERSION 1.6.0
ENV PAGESPEED_VERSION 1.8.31.4
ENV NGX_DEVEL_KIT_VERSION 0.2.19
ENV NGX_SET_MISC_VERSION 0.24
ENV MODULESDIR /usr/src/nginx-modules
RUN cd /usr/src/ && wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz && tar xf nginx-${NGINX_VERSION}.tar.gz && rm -f nginx-${NGINX_VERSION}.tar.gz

ENV NPS_VERSION 1.8.31.4

# Force OpenSSL 1.0.1
RUN cd /usr/src && wget http://www.openssl.org/source/openssl-1.0.1h.tar.gz && tar xvzf openssl-1.0.1h.tar.gz

# Install additional modules
# RUN apt-get update && apt-get install -y build-essential zlib1g-dev libpcre3 libpcre3-dev unzip libc6 libc6-dev libc-dev-bin g++
RUN apt-get install -y build-essential zlib1g-dev libpcre3 libpcre3-dev unzip libc6 libc6-dev libc-dev-bin g++
RUN mkdir ${MODULESDIR}
RUN cd ${MODULESDIR} && \
			wget https://github.com/pagespeed/ngx_pagespeed/archive/release-${NPS_VERSION}-beta.zip && \
			unzip release-${NPS_VERSION}-beta.zip && \
			cd ngx_pagespeed-release-${NPS_VERSION}-beta && \
			wget https://dl.google.com/dl/page-speed/psol/${NPS_VERSION}.tar.gz && \
			tar -xzvf ${NPS_VERSION}.tar.gz
#RUN	wget --no-check-certificate https://github.com/pagespeed/ngx_pagespeed/archive/v${PAGESPEED_VERSION}-beta.zip
#RUN	unzip v${PAGESPEED_VERSION}-beta.zip
#RUN	cd ngx_pagespeed-${PAGESPEED_VERSION}-beta
#RUN	wget --no-check-certificate https://dl.google.com/dl/page-speed/psol/${PAGESPEED_VERSION}.tar.gz
#RUN	tar -xzvf ${PAGESPEED_VERSION}.tar.gz



#	wget --no-check-certificate https://github.com/simpl/ngx_devel_kit/archive/v${NGX_DEVEL_KIT_VERSION}.tar.gz && \
#	tar -xzvf v${NGX_DEVEL_KIT_VERSION}.tar.gz \

#	wget --no-check-certificate https://github.com/openresty/set-misc-nginx-module/archive/v${NGX_SET_MISC_VERSION}.tar.gz && \
#	tar -xzvf v${NGX_SET_MISC_VERSION}.tar.gz

# Compile nginx
RUN cd /usr/src/nginx-${NGINX_VERSION} && ./configure \
	--prefix=/etc/nginx \
	--sbin-path=/usr/sbin/nginx \	
	--conf-path=/etc/nginx/nginx.conf \
	--error-log-path=/var/log/nginx/error.log \
	--http-log-path=/var/log/nginx/access.log \
	--pid-path=/var/run/nginx.pid \
	--lock-path=/var/run/nginx.lock \
	--with-http_ssl_module \
	--with-http_realip_module \
	--with-http_addition_module \
	--with-http_sub_module \
	--with-http_dav_module \
	--with-http_flv_module \
	--with-http_mp4_module \
	--with-http_gunzip_module \
	--with-http_gzip_static_module \
	--with-http_random_index_module \
	--with-http_secure_link_module \
	--with-http_stub_status_module \
	--with-mail \
	--with-mail_ssl_module \
	--with-file-aio \
	--with-http_spdy_module \
	--with-cc-opt='-g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Wformat-security -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2' \
	--with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,--as-needed' \
	--with-ipv6 \
	--with-sha1=/usr/include/openssl \
 	--with-md5=/usr/include/openssl \
	--with-openssl='../openssl-1.0.1h' \
	--add-module=${MODULESDIR}/ngx_pagespeed-release-${NPS_VERSION}-beta
#	--add-module=${MODULESDIR}/ngx_pagespeed-${PAGESPEED_VERSION}-beta
#	--add-module=${MODULESDIR}/ngx_devel_kit-${NGX_DEVEL_KIT_VERSION} \
#    --add-module=${MODULESDIR}/set-misc-nginx-module-${NGX_SET_MISC_VERSION}

RUN cd /usr/src/nginx-${NGINX_VERSION} && make && make install

ADD nginx /etc/nginx/

# Turn off nginx starting as a daemon
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

RUN mkdir /var/ngx_pagespeed_cache
RUN chown -R www-data:www-data /var/ngx_pagespeed_cache

# Purge APT cache
RUN apt-get clean all

EXPOSE 80 443 8080
CMD ["nginx"]
