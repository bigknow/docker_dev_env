# ----------------------------------------------------------------------------- #
# thebigknow/apache
# version 1.1.0
# ----------------------------------------------------------------------------- #
FROM ubuntu:17.10
MAINTAINER Dan Wanek <dan.wanek@thebigknow.com>

RUN rm /bin/sh && ln -s /bin/bash /bin/sh

RUN apt-get -y update
RUN apt-get -y upgrade
RUN apt-get -y install apache2 iproute2
COPY install/001-tbk.conf /etc/apache2/sites-available/
COPY install/002-tbk-ssl.conf /etc/apache2/sites-available/
COPY install/ssl/ /etc/apache2/ssl/
RUN a2enmod -q headers
RUN a2enmod -q proxy_http
RUN a2enmod -q ssl
RUN a2ensite -q 001-tbk
RUN a2ensite -q 002-tbk-ssl

# Cleanup
RUN apt-get clean autoclean && apt-get autoremove -y && rm -rf /tmp/* /var/tmp/* /var/lib/cache/* /var/lib/log/*

EXPOSE 80

CMD apachectl -D FOREGROUND
