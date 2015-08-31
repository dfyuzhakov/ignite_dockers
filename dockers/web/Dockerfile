# Base image
FROM ubuntu:14.04
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

#-----------------------------------------------------------------------------------------------------------------------
# Add repositories. Update and upgrade system.
#-----------------------------------------------------------------------------------------------------------------------
RUN apt-get update
RUN apt-get install -y software-properties-common

## Update repositories
RUN apt-get update && apt-get upgrade -y

#-----------------------------------------------------------------------------------------------------------------------
# Installations
#-----------------------------------------------------------------------------------------------------------------------

## Install helpful utils
RUN apt-get install -y htop vim gzip unzip bzip2 curl wget git

RUN apt-get install -y apache2 libapache2-mod-php5 libapache2-mod-wsgi \
    && a2enmod rewrite && a2enmod ssl

## Install phpbrew requirements. This includes some libs and default php5 from repo
RUN apt-get build-dep -y php5
RUN apt-get install -y php5-cgi php5 php5-dev php-pear autoconf automake curl build-essential libxslt1-dev re2c libxml2 libxml2-dev php5-cli bison libbz2-dev libreadline-dev
RUN apt-get install -y libfreetype6 libfreetype6-dev libpng12-0 libpng12-dev libjpeg-dev libjpeg8-dev libjpeg8 libgd-dev libgd3 libxpm4 libltdl7 libltdl-dev
RUN apt-get install -y libssl-dev openssl
RUN apt-get install -y gettext libgettextpo-dev libgettextpo0
RUN apt-get install -y libicu-dev
RUN apt-get install -y libmhash-dev libmhash2
RUN apt-get install -y libmcrypt-dev libmcrypt4
RUN apt-get install -y libsasl2-dev libmemcached-dev libsphinxclient-dev

## Install database libs
RUN apt-get install -y libmysqlclient-dev libmysqld-dev

## Install mongo libs
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
RUN echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list
RUN apt-get update && apt-get install -y mongodb-org-shell mongodb-org-tools

## Install supervisor
RUN apt-get install -y python-setuptools inotify-tools \
	&& easy_install supervisor

## Install phpbrew
RUN curl -L -O https://github.com/phpbrew/phpbrew/raw/master/phpbrew && \
    chmod +x phpbrew && \
    mv phpbrew /usr/bin/phpbrew

    ### Setup phpbrew to install system wide
    RUN echo "source ~/.phpbrew/bashrc" >> ~/.bashrc
    RUN phpbrew init
    ENV PHPBREW_ROOT /opt/phpbrew

    ### Install php 5.6.10, composer,
    RUN phpbrew install -j $(nproc) 5.6.10 +default +dbs +apxs2 +openssl +cgi
    RUN source ~/.phpbrew/bashrc && \
        phpbrew use php-5.6.10 && \
        phpbrew ext install mongo && \
        phpbrew ext install memcached -- --disable-memcached-sasl
    RUN echo "phpbrew use php-5.6.10" >> /root/.bashrc

    RUN php -r 'readfile("https://getcomposer.org/installer");' | php -- --install-dir=/usr/bin --filename=composer && \
            chmod +x /usr/bin/composer

#-----------------------------------------------------------------------------------------------------------------------
# Configurations
#-----------------------------------------------------------------------------------------------------------------------

## Configure php
ADD settings/php.ini /opt/phpbrew/php/php-5.6.10/etc/php.ini

## Configure apache
ADD settings/apache2.conf /etc/apache2/apache2.conf
ADD settings/sites-enabled /etc/apache2/sites-enabled

## Configure supervisor
ADD settings/supervisord.conf /etc/supervisord.conf

#-----------------------------------------------------------------------------------------------------------------------
# Configure log folders
#-----------------------------------------------------------------------------------------------------------------------

RUN mkdir -p /var/log/supervisor/

CMD ["supervisord", "-c", "/etc/supervisord.conf"]