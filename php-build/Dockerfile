FROM bccq/centos

LABEL version="1.0"
LABEL maintainer="justinchou<zhou78620051@126.com>"

# ADD into image, COPY into container
ADD ./deps /data

WORKDIR /data

# RUN into image when `docker build`, CMD into container when `docker run`
RUN groupadd www && useradd -g www www

# Update cmake
RUN yum remove -y cmake

RUN tar zxvf cmake-3.10.2-Linux-x86_64.tar.gz
ENV PATH "$PATH:/data/cmake-3.10.2-Linux-x86_64/bin:/usr/local/php7/bin:/usr/local/php7/sbin"

# Update libzip
RUN tar -zxvf libzip-1.5.2.tar.gz
RUN cd libzip-1.5.2 && mkdir build && cd build && cmake .. && make -j4 && make install

# Refresh the config
RUN cp local.conf /etc/ld.so.conf.d/local.conf && ldconfig -v

# Install php
RUN tar zxf php-7.3.10.tar.gz
RUN cd php-7.3.10 && ./configure --prefix=/usr/local/php7/ \
  --with-config-file-path=/usr/local/php7/etc \
  --with-config-file-scan-dir=/usr/local/php7/etc/conf.d \
  --enable-fpm --with-fpm-user=www \
  --with-fpm-group=www --enable-soap \
  --with-openssl --with-openssl-dir \
  --with-mcrypt --with-pcre-regex \
  --with-zlib --with-iconv --with-bz2 \
  --enable-calendar --with-curl --with-cdb \
  --enable-dom --enable-exif --with-pcre-dir \
  --enable-ftp --with-gd --with-jpeg-dir \
  --with-png-dir --with-freetype-dir \
  --with-gettext --with-gmp --with-mhash \
  --enable-mbstring --with-libmbfl --with-onig \
  --enable-pdo --with-pdo-mysql --with-zlib-dir \
  --with-readline --enable-session --enable-shmop \
  --enable-simplexml --enable-sockets \
  --enable-sysvmsg --enable-sysvsem \
  --enable-sysvshm --enable-wddx --with-libxml-dir \
  --with-xsl --enable-zip --enable-mysqlnd \
  --with-mysqli --without-pear --enable-pcntl --enable-bcmath \
  && make && make install

COPY ./plugins/redis-4.0.0.tgz /data
RUN tar xf redis-4.0.0.tgz
RUN cd redis-4.0.0 && phpize && ./configure --with-php-config=/usr/local/php7/bin/php-config && make && make install

COPY ./plugins/swoole-4.4.8.tgz /data
RUN tar xf swoole-4.4.8.tgz
RUN cd swoole-4.4.8 && phpize && ./configure --with-php-config=/usr/local/php7/bin/php-config && make && make install

COPY ./plugins/mongodb-1.6.0.tgz /data
RUN tar xf mongodb-1.6.0.tgz
RUN cd mongodb-1.6.0 && phpize && ./configure --with-php-config=/usr/local/php7/bin/php-config && make && make install

RUN rm -rf \
  cmake-3.10.2-Linux-x86_64.tar.gz \
  libzip-1.5.2.tar.gz libzip-1.5.2 \
  php-7.3.10.tar.gz php-7.3.10 \
  redis-4.0.0.tgz redis-4.0.0 \
  swoole-4.4.8.tgz swoole-4.4.8 \
  mongodb-1.6.0.tgz mongodb-1.6.0

COPY ./plugins/conf.d /usr/local/php7/etc/conf.d
COPY ./plugins/php.ini /usr/local/php7/etc/php.ini

RUN cp /usr/local/php7/etc/php-fpm.conf.default /usr/local/php7/etc/php-fpm.conf
RUN cp /usr/local/php7/etc/php-fpm.d/www.conf.default /usr/local/php7/etc/php-fpm.d/www.conf

