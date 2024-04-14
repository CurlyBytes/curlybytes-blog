#Docker file from phpdocker.io template generator
ARG  PHP_VERSION=7.4-fpm

FROM phpdockerio/php:${PHP_VERSION}
WORKDIR "/application"

# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive

ENV appname="blog"
ENV versionmajor="1"
ENV versionminor="1"
ENV versionpatch="1"
ENV appversion="v.${versionmajor}.${versionminor}.${versionpatch}"
ENV appauthor="Francisco Abayon"


LABEL name="${appname}" \
      version="${appversion}" \
      author="${appauthor}" \
      description="CurlyBytes" 


RUN apt-get update && apt-get upgrade -y; \
    apt-get -y --no-install-recommends install \
        git \ 
        php7.4-mbstring \ 
        php7.4-xml \ 
        php7.4-curl \ 
        php7.4-sqlite3 \ 
        php7.4-memcached \ 
        php7.4-mysql \ 
        php7.4-odbc \ 
        php7.4-phpdbg \ 
        php7.4-redis \ 
        php7.4-xdebug; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*
