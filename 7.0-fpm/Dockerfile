FROM php:7.0-fpm
MAINTAINER Alterway <iac@alterway.fr>

RUN apt-get update && \
    apt-get install -y \
        libpq-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libgmp-dev \
        libxml2-dev \
        zlib1g-dev \
        libncurses5-dev \
        libldb-dev \
        libldap2-dev \
        libicu-dev \
        libmemcached-dev \
        libcurl4-openssl-dev \
        libssl-dev \
        libsqlite3-dev \
        curl \
        ssmtp \
        mysql-client \
        git \
        subversion \
        wget && \
    rm -rf /var/lib/apt/lists/* && \
    wget https://getcomposer.org/download/1.2.4/composer.phar -O /usr/local/bin/composer && \
    chmod a+rx /usr/local/bin/composer

RUN ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h && \
    ln -s /usr/lib/x86_64-linux-gnu/libldap.so /usr/lib/libldap.so && \
    ln -s /usr/lib/x86_64-linux-gnu/liblber.so /usr/lib/liblber.so && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd && \
    docker-php-ext-configure mysqli --with-mysqli=mysqlnd && \
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/lib && \
    docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install pdo_sqlite && \
    docker-php-ext-install ldap && \
    docker-php-ext-install soap && \
    docker-php-ext-install intl && \
    docker-php-ext-install mcrypt && \
    docker-php-ext-install gd && \
    docker-php-ext-install gmp && \
    docker-php-ext-install bcmath && \
    docker-php-ext-install mbstring && \
    docker-php-ext-install zip && \
    docker-php-ext-install pcntl && \
    docker-php-ext-install ftp && \
    docker-php-ext-install sockets && \
    docker-php-ext-install pdo_pgsql pgsql


#install xdebug
COPY xdebug/xdebug-2.6.0.tgz /tmp/
COPY xdebug/xdebug.ini /usr/local/etc/php/conf.d/
RUN cd /tmp/ && tar -xvzf /tmp/xdebug-2.6.0.tgz && cd xdebug-2.6.0 \
       && phpize \
       && ./configure \
       && make \
       && echo "zend_extension = /tmp/xdebug-2.6.0/modules/xdebug.so" >> /usr/local/etc/php/conf.d/xdebug.ini



ADD http://www.zlib.net/zlib-1.2.11.tar.gz /tmp/zlib.tar.gz
RUN tar zxpf /tmp/zlib.tar.gz -C /tmp && \
    cd /tmp/zlib-1.2.11 && \
    ./configure --prefix=/usr/local/zlib && \
    make && make install && \
    rm -Rf /tmp/zlib-1.2.11 && \
    rm /tmp/zlib.tar.gz


ENV LOCALTIME Europe/Paris
ENV PHPFPM__access.format '"%R - %u [%t] \"%m %r\" %s %l %Q %f"'

RUN rm $PHP_INI_DIR/conf.d/docker-php-ext* && \
    echo 'sendmail_path = /usr/sbin/ssmtp -t' >> $PHP_INI_DIR/conf.d/00-default.ini && \
    echo "\ninclude=/usr/local/etc/php-fpm.d/*.conf" >> /usr/local/etc/php-fpm.conf && \
    mkdir -p /usr/local/etc/php-fpm.d && \
    chmod a+w -R $PHP_INI_DIR/conf.d/ /etc/ssmtp /usr/local/etc/php-fpm.d/

COPY docker-entrypoint.sh /entrypoint.sh

WORKDIR /var/www

ENTRYPOINT ["/entrypoint.sh"]
CMD ["php-fpm"]

