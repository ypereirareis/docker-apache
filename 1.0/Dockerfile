FROM ubuntu:14.04

# Install Requirements
RUN apt-get update -qq && apt-get install -qqy \
    sudo \
    wget \
    curl \
    git \
    apt-utils \
    acl \
    && echo "Europe/Paris" > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata \
    && echo 'alias ll="ls -lah --color=auto"' >> /etc/bash.bashrc

# Variables Apache
ENV APACHE_RUN_USER  www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_PID_FILE  /var/run/apache2.pid
ENV APACHE_RUN_DIR   /var/run/apache2
ENV APACHE_LOCK_DIR  /var/lock/apache2
ENV APACHE_LOG_DIR   /var/log/apache2
ENV APACHE_USER_UID 0

# Repository PHP 5.6
RUN apt-get update -qq && apt-get install -qqy \
    software-properties-common \
    python-software-properties
RUN add-apt-repository ppa:ondrej/php5-5.6

# Java, PHP && Apache
RUN apt-get update -qq && apt-get install -qqy --force-yes \
    default-jre \
    php5 \
    php-apc \
    php5-cli \
    php5-xdebug \
    php5-intl \
    php5-mysql \
    php5-curl \
    php5-gd \
    apache2 \
    apache2-mpm-worker \
    apache2-utils \
    libapache2-mod-php5 \
    libapache2-modsecurity \
    libapache2-mod-evasive

# Config Apache
ADD conf/apache/vhost.conf /etc/apache2/sites-available/web.conf
RUN a2enmod rewrite \
    && rm -f /etc/apache2/sites-enabled/000-default.conf \
    && rm -f /etc/apache2/sites-available/000-default.conf \
    && rm -rf /var/www/html \
    && a2ensite web

RUN a2enmod security2
RUN a2enmod evasive

# Config PHP
ADD conf/php5/apc.ini /etc/php5/mods-available/apc.ini
ADD conf/php5/php.ini /tmp/php.ini
RUN cat /tmp/php.ini >> /etc/php5/apache2/php.ini \
    && cat /tmp/php.ini >> /etc/php5/cli/php.ini \
    && rm /tmp/php.ini


RUN sed -i \
    -e 's~^ServerSignature On$~ServerSignature Off~g' \
    -e 's~^ServerTokens OS$~ServerTokens Prod~g' \
    /etc/apache2/apache2.conf

RUN echo "ServerSignature Off" >> /etc/apache2/apache2.conf
RUN echo "ServerTokens Prod" >> /etc/apache2/apache2.conf

# Logs
RUN mkdir -p /var/log/apache2 \
    && chown -R www-data:www-data /var/log/apache2

# Sources
WORKDIR /var/www

EXPOSE 80

VOLUME ["/var/log/apache2", "/var/www"]

ADD script/start.sh /root/start.sh

CMD ["/bin/bash", "/root/start.sh"]
