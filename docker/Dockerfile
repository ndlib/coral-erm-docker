FROM php:5.6-apache
RUN apt-get update && apt-get install -y --no-install-recommends git && rm -rf /var/lib/apt/lists/*
RUN docker-php-ext-install gettext mysqli mbstring

COPY config/php.ini /usr/local/etc/php/conf.d/

RUN cd /var/www/html/ && git clone -b "v3.0.0" https://github.com/coral-erm/coral.git
RUN chmod 777 -R /var/www/html/coral/ && echo -e "DocumentRoot \"/web\" \nServerName localhost \nLoadModule mpm_event_module /usr/lib/apache2/mod_mpm_event.so \nListen 80" | tee /etc/apache2/httpd.conf

CMD apachectl -DFOREGROUND
