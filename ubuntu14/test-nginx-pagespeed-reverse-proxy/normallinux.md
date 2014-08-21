apt-get update &&
apt-get install -y wget &&
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ABF5BD827BD9BF62 &&
echo deb http://nginx.org/packages/mainline/ubuntu trusty nginx > /etc/apt/sources.list.d/nginx-stable-trusty.list &&
echo deb-src http://nginx.org/packages/mainline/ubuntu trusty nginx > /etc/apt/sources.list.d/nginx-stable-trusty.list &&

apt-get update &&  apt-get install nano -y &&
apt-get upgrade -y &&

NGINX_VERSION=1.6.1 &&
OPENSSL_VERSION=openssl-1.0.1h &&
MODULESDIR=/usr/src/nginx-modules &&
NPS_VERSION=1.8.31.4 &&

cd /usr/src/ && wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz && tar xf nginx-${NGINX_VERSION}.tar.gz && rm -f nginx-${NGINX_VERSION}.tar.gz &&
cd /usr/src/ && wget http://www.openssl.org/source/${OPENSSL_VERSION}.tar.gz && tar xvzf ${OPENSSL_VERSION}.tar.gz &&

apt-get update && apt-get install -y build-essential zlib1g-dev libpcre3 libpcre3-dev unzip &&
mkdir ${MODULESDIR} &&
cd ${MODULESDIR} && \
    wget --no-check-certificate https://github.com/pagespeed/ngx_pagespeed/archive/release-${NPS_VERSION}-beta.zip && \
    unzip release-${NPS_VERSION}-beta.zip && \
    cd ngx_pagespeed-release-${NPS_VERSION}-beta/ && \
    wget --no-check-certificate https://dl.google.com/dl/page-speed/psol/${NPS_VERSION}.tar.gz && \
    tar -xzvf ${NPS_VERSION}.tar.gz &&


# Compile nginx
cd /usr/src/nginx-${NGINX_VERSION} && ./configure \
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
	--with-sha1='../${OPENSSL_VERSION}' \
 	--with-md5='../${OPENSSL_VERSION}' \
	--with-openssl='../${OPENSSL_VERSION}' \
	--add-module=${MODULESDIR}/ngx_pagespeed-release-${NPS_VERSION}-beta &&

cd /usr/src/nginx-${NGINX_VERSION} && make && make install &&

mkdir -p /etc/nginx/ssl









######################################



#Add custom nginx.conf file
ADD docker/nginx.conf /etc/nginx/nginx.conf
ADD docker/pagespeed.conf /etc/nginx/pagespeed.conf
ADD docker/proxy_params /etc/nginx/proxy_params

WORKDIR /etc/nginx/ssl

RUN openssl genrsa  -out server.key 4096
RUN openssl req -new -batch -key server.key -out server.csr
RUN openssl x509 -req -days 10000 -in server.csr -signkey server.key -out server.crt
RUN openssl dhparam -out dhparam.pem 4096

RUN mkdir -p /etc/nginx/sites-enabled

RUN mkdir /app
WORKDIR /app
ADD ./docker/app /app

RUN wget -P /usr/local/bin https://godist.herokuapp.com/projects/ddollar/forego/releases/current/linux-amd64/forego
RUN chmod u+x /usr/local/bin/forego

ADD docker/app/docker-gen docker-gen

EXPOSE 80 443
ENV DOCKER_HOST unix:///tmp/docker.sock

CMD ["forego", "start", "-r"]
