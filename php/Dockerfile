FROM php:7.4-apache

RUN apt-get update && apt-get install -y vim git wget curl default-mysql-client ssl-cert
RUN docker-php-ext-install pdo_mysql mysqli

WORKDIR /home/rconfig

RUN rm -rf /etc/apache2/sites-enabled/000-default.conf
RUN rm -rf /etc/apache2/sites-available/default-ssl.conf

ADD apache/rconfig3-site.conf /etc/apache2/sites-available
ADD apache/rconfig3-site-ssl.conf /etc/apache2/sites-available
RUN ln -s /etc/apache2/sites-available/rconfig3-site.conf /etc/apache2/sites-enabled/rconfig3-site.conf
RUN ln -s /etc/apache2/sites-available/rconfig3-site-ssl.conf /etc/apache2/sites-enabled/rconfig3-site-ssl.conf

# Manually set up the apache environment variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

RUN a2enmod ssl
RUN a2enmod rewrite
RUN a2enmod socache_shmcb 
RUN a2ensite rconfig3-site-ssl

RUN service apache2 restart

# RUN git clone https://github.com/rconfig/rconfig.git
RUN chown -R www-data /home/rconfig/
RUN chown -R 33 /home/rconfig

EXPOSE 4443
EXPOSE 8080

CMD /usr/sbin/apache2ctl -D FOREGROUND
