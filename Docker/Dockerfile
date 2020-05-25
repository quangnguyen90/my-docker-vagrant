# Ubuntu

FROM ubuntu:16.04

MAINTAINER Nguyen Phu Quang, nguyenphuquang90@gmail.com

# Download and install php, nginx, and supervisor
RUN apt-get update
RUN apt-get install -y software-properties-common
RUN add-apt-repository ppa:nginx/stable
#RUN apt-get update
#RUN apt-get -y dist-upgrade
RUN apt-get install -y supervisor nginx php7.0 php7.0-fpm php7.0-cli php7.0-common php7.0-mbstring php7.0-gd php7.0-intl php7.0-xml php7.0-mysql php7.0-mcrypt php7.0-zip

# Mysql
RUN echo "mysql-server mysql-server/root_password password docker" | debconf-set-selections
RUN echo "mysql-server mysql-server/root_password_again password docker" | debconf-set-selections
RUN apt-get -y --force-yes install mysql-server
# mysql config
RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf

# Setup config files
RUN echo "daemon off;" >> /etc/nginx/nginx.conf
ADD ./nginx/default /etc/nginx/sites-enabled/default
ADD ./supervisor/supervisord.conf /etc/supervisor/supervisord.conf
ADD ./php-fpm/php-fpm.conf /etc/php/7.0/fpm/pool.d/php-fpm.conf
ADD ./mysql/mysqld.cnf /etc/mysql/mysql.conf.d/mysqld.cnf
ADD ./mysql/supervised_mysql /usr/sbin/supervised_mysql

# Install dependencies
RUN apt-get update && apt-get install -y \
    autoconf \
	dpkg-dev \
    build-essential \
    libpng-dev \
    libfreetype6-dev \
    libicu-dev \
    libxslt1-dev \ 
    sendmail-bin \ 
    sendmail \ 
    sudo \
    libonig-dev \
    libzip-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl \
    libmcrypt-dev \
    mysql-client libmagickwand-dev --no-install-recommends

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Shared volume
RUN mkdir -p /var/www
VOLUME ["/var/www"]

# Default command for container, start supervisor
CMD ["supervisord", "--nodaemon"]
USER root

# Expose port 8001 of the container
EXPOSE 8001

# Extra setup
RUN echo "export TERM=xterm" >>  /root/.bashrc
