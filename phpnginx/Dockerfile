FROM phusion/baseimage:0.9.19

MAINTAINER Liu Yang <yangliu@chuanty.com>
# INSPIRED BY https://github.com/LaraDock/docker-images

# Default baseimage settings
ENV HOME /root
RUN /etc/my_init.d/00_regen_ssh_host_keys.sh
CMD ["/sbin/my_init"]
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update --fix-missing
RUN apt-get install -y --force-yes python-software-properties
RUN add-apt-repository ppa:ondrej/php
RUN apt-get update --fix-missing 
RUN apt-get install -y  --allow-unauthenticated  nginx \
    php5.6-fpm php5.6-cli php5.6-dev php5.6-mysql php5.6-mcrypt \
    php5.6-curl php5.6-gd php5.6-intl php5.6-zip php5.6-imap php5.6-xml php5.6-mbstring php-imagick  php-xdebug  libmemcached11 php-mongodb php-memcached && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* \
           /tmp/* \
           /var/tmp/*
           

 # Set The Timezone

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# Configure nginx
RUN echo "daemon off;" >> /etc/nginx/nginx.conf
RUN sed -i "s/sendfile on/sendfile off/" /etc/nginx/nginx.conf
RUN mkdir -p /var/www
RUN mkdir -p /var/run/php

#add phalcon5.6 extension
ADD build/php/cphalcon-3.0.4 /cphalcon-3.0.4
WORKDIR /cphalcon-3.0.4/build
RUN ./install
ADD build/php/phalcon.ini /etc/php/5.6/mods-available/
RUN ln -s /etc/php/5.6/mods-available/phalcon.ini /etc/php/5.6/cli/conf.d/20-phalcon.ini
RUN ln -s /etc/php/5.6/mods-available/phalcon.ini /etc/php/5.6/fpm/conf.d/20-phalcon.ini


# Configure PHP
RUN sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/5.6/cli/php.ini
RUN sed -i "s/display_errors = .*/display_errors = On/" /etc/php/5.6/cli/php.ini
RUN sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/5.6/cli/php.ini
RUN sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/5.6/cli/php.ini
RUN sed -i "s/;date.timezone =.*/date.timezone = Asia\/Shanghai/" /etc/php/5.6/cli/php.ini

RUN sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/5.6/fpm/php.ini
RUN sed -i "s/display_errors = .*/display_errors = On/" /etc/php/5.6/fpm/php.ini
RUN sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/5.6/fpm/php.ini
RUN sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/5.6/fpm/php.ini
RUN sed -i "s/;date.timezone =.*/date.timezone = Asia\/Shanghai/" /etc/php/5.6/fpm/php.ini
RUN sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php/5.6/fpm/php-fpm.conf
RUN sed -i "s/;pid = \/run\/php\/php5.6-fpm.pid/pid = \/var\/run\/php\/php7.0-fpm.pid/" /etc/php/5.6/fpm/php-fpm.conf

RUN phpenmod mcrypt

# Add nginx service
RUN mkdir /etc/service/nginx
ADD build/nginx/run.sh /etc/service/nginx/run
RUN chmod +x /etc/service/nginx/run

# Add PHP service
RUN mkdir /etc/service/phpfpm
ADD build/php/run.sh /etc/service/phpfpm/run
RUN chmod +x /etc/service/phpfpm/run



VOLUME ["/var/www", "/etc/nginx/sites-available", "/etc/nginx/sites-enabled"]

WORKDIR /var/www

EXPOSE 80
