FROM abprk/ubuntu

MAINTAINER Abhishek Pareek "apareek2@ncsu.edu"

ENV BOOT2DOCKER_ID 1000
ENV BOOT2DOCKER_GID 50
ENV PHP_UPLOAD_MAX_FILESIZE 10M
ENV PHP_POST_MAX_SIZE 10M
ENV APACHE_RUN_USER  	www-data
ENV APACHE_RUN_GROUP 	www-data
ENV APACHE_LOG_DIR   	/var/log/apache2
ENV APACHE_PID_FILE    	/var/run/apache2.pid
ENV APACHE_RUN_DIR     	/var/run/apache2
ENV APACHE_LOCK_DIR    	/var/lock/apache2

RUN usermod -u ${BOOT2DOCKER_ID} www-data && \
	usermod -G staff www-data && \
	groupmod -g $(($BOOT2DOCKER_GID + 10000)) $(getent group $BOOT2DOCKER_GID | cut -d: -f1) && \
	groupmod -g ${BOOT2DOCKER_GID} staff

# # Install Apache mpm worker with php7.2-fpm
# RUN apt-get update && \
#     apt-get install -y apache2-mpm-worker libapache2-mod-fastcgi php7.2-fpm php7.2-mysql mysql-client php7.2-gd php7.2-curl curl php7.2-cli && \
#     echo "ServerName localhost" | tee /etc/apache2/conf-available/servername.conf && \
#     a2enconf servername && \
#     a2enmod actions fastcgi rewrite alias

# Install Apache2 with php-fpm
RUN apt-get update && \
    apt-get install -y apache2 libapache2-mod-fastcgi php-zip php-cli php-common php-dev php-mcrypt php-mysql mysql-client php-gd php-curl curl && \
    echo "ServerName localhost" | tee /etc/apache2/conf-available/servername.conf && \
    a2enconf servername && \
    a2enmod actions fastcgi rewrite alias

ADD php-fpm.conf /etc/apache2/conf-available/php-fpm.conf
RUN mkdir /etc/service/apache2 && \
    mkdir /etc/service/php-fpm

COPY runit/apache2 /etc/service/apache2/run
COPY runit/php-fpm /etc/service/php-fpm/run

RUN a2enconf php-fpm.conf
# RUN a2enconf php-fpm.conf && \
#     sed -i 's/;listen = \/var\/run\/php-fpm.sock/listen = \/var\/run\/php-fpm.sock/' /etc/php/fpm/pool.d/www.conf && sed -i 's/\;listen.mode/listen.mode/' /etc/php/fpm/pool.d/www.conf && sed -i 's/expose_php.*/expose_php = off/g' /etc/php/fpm/php.ini && sed -i 's/allow_url_fopen.*/allow_url_fopen = off/g' /etc/php/fpm/php.ini

EXPOSE 80 443

# Install Composer, Drush & Drupal Console.
RUN apt-get install -y git && \
    curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    composer global require drush/drush:dev-master && \
    composer global update && \
    ln -s /root/.composer/vendor/bin/drush /usr/local/bin/drush && \
    curl https://drupalconsole.com/installer -L -o drupal.phar && \
    mv drupal.phar /usr/local/bin/drupal && \
    chmod +x /usr/local/bin/drupal
