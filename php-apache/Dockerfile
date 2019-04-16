FROM ubuntu:18.04
RUN apt-get update \
    && apt-get install sudo apache2 curl wget gnupg unzip vim zip elinks git -y
RUN useradd -ms /bin/bash student
RUN usermod -a -G sudo student
RUN echo 'student ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
RUN apt-get install -y locales \
    && locale-gen en_US.UTF-8
USER root

# Install dependencies
RUN curl -sL https://deb.nodesource.com/setup_11.x | sudo bash - \
    && sudo apt-get install nodejs -y
RUN sudo apt-get install -y --no-install-recommends \
    build-essential git dos2unix pylint virtualenv python3-dev python3-pip openssh-server

RUN apt-get update -y

# ------------------------------------------------------------------------------
# Install Composer and PHP 7.2 and configure Apache
USER root
ENV TZ=Asia/Tokyo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN apt-get install php -y
RUN apt-get install -y apache2-bin libapache2-mod-php libicu-dev libmcrypt-dev libpng-dev libpq-dev libssl-dev libxml2-dev libzip-dev php-bcmath php-bz2 php-cgi php-cli php-common php-curl php-dba php-dev php-enchant php-fpm php-gd php-gmp php-imap php-interbase php-intl php-json php-ldap php-mbstring php-mysql php-odbc php-opcache php-pear php-pgsql php-phpdbg php-pspell php-readline php-recode php-snmp php-soap php-sqlite3 php-sybase php-tidy php-xml php-xmlrpc php-xsl php-zip
USER root

USER root
RUN echo 'AddDefaultCharset utf-8' >> /etc/apache2/apache2.conf
USER root

RUN sudo apt install php-cli php-mbstring -y
WORKDIR /root/
RUN sudo curl -sS https://getcomposer.org/installer -o composer-setup.php
RUN sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer

USER root
ADD ./php.ini /etc/php/7.2/apache2/php.ini.newlines
RUN cat /etc/php/7.2/apache2/php.ini.newlines >> /etc/php/7.2/apache2/php.ini \
    && rm -rf /etc/php/7.2/apache2/php.ini.newlines
ADD ./default.conf /etc/apache2/sites-available/000-default.conf
ADD ./docker-php.sh /docker-php.sh
RUN dos2unix /docker-php.sh
RUN sudo chmod 777 /docker-php.sh
RUN sudo chmod +x /docker-php.sh
ENTRYPOINT ["/docker-php.sh"]

COPY apache-start.sh /usr/local/bin/
RUN dos2unix /usr/local/bin/apache-start.sh
RUN sudo chmod 777 /usr/local/bin/apache-start.sh
RUN chmod +x /usr/local/bin/apache-start.sh
WORKDIR /var/www/html

RUN a2enmod rewrite

EXPOSE 80
CMD ["apache-start.sh"]

# ------------------------------------------------------------------------------
# Clean up.
RUN rm -rf /var/lib/apt/lists/*