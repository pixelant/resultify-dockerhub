############################################################
# Web server docker image for PDF generator
#
# resultify/puppeteer-php7.4-apache2.4-ubuntu18.04
#
############################################################

FROM ubuntu:18.04

LABEL os="ubuntu:18.04"
LABEL http-server="apache2.4"
LABEL php="7.4"
LABEL image.name="resultify/puppeteer-php7.4-apache2.4-ubuntu18.04"

ENV DEBIAN_FRONTEND=noninteractive
ENV TERM=xterm-256color
ENV APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=1

RUN apt-get update \
&& apt-get install software-properties-common=0.96.24.* -y --no-install-recommends \
&& add-apt-repository ppa:ondrej/php -y \
&& apt-get install -y --no-install-recommends\
    # Install tools
    gpg-agent \
    apt-utils=1.6.* \
    openssl=1.1.* \
    nano=2.9.* \
    curl=7.58.* \
    iputils-ping=3:* \
    locales=2.* \
    # Install apache
    apache2=2.4.* \
    libapache2-mod-php7.4=7.4.* \
    # Install php
    php7.4-cli=7.4.* \
    php7.4-json=7.4.* \
    php7.4-curl=7.4.* \
    php7.4-fpm=7.4.* \
    php7.4-ldap=7.4.* \
    php7.4-mbstring=7.4.* \
    php7.4-soap=7.4.* \
    php7.4-xml=7.4.* \
    php7.4-zip=7.4.* \
    php7.4-intl=7.4.* \
&& rm -rf /var/lib/apt/lists/*

# Install puppeteer
RUN curl -sL https://deb.nodesource.com/setup_13.x | bash
RUN apt-get update \
&& apt-get install -y --no-install-recommends \
    nodejs \
    gconf-service \
    libasound2 \
    libatk1.0-0 \
    libc6 \
    libcairo2 \
    libcups2 \
    libdbus-1-3 \
    libexpat1 \
    libfontconfig1 \
    libgcc1 \
    libgconf-2-4 \
    libgdk-pixbuf2.0-0 \
    libglib2.0-0 \
    libgtk-3-0 \
    libnspr4 \
    libpango-1.0-0 \
    libpangocairo-1.0-0 \
    libstdc++6 \
    libx11-6 \
    libx11-xcb1 \
    libxcb1 \
    libxcomposite1 \
    libxcursor1 \
    libxdamage1 \
    libxext6 \
    libxfixes3 \
    libxi6 \
    libxrandr2 \
    libxrender1 \
    libxss1 \
    libxtst6 \
    ca-certificates \
    fonts-liberation \
    libappindicator1 \
    libnss3 \
    lsb-release \
    xdg-utils \
    wget \
&& npm install --global --unsafe-perm puppeteer

# Set locales
RUN locale-gen en_US.UTF-8 en_GB.UTF-8 sv_SE.UTF-8 de_DE.UTF-8 da_DK.UTF-8 fr_FR.UTF-8 es_ES.UTF-8 it_IT.UTF-8 fi_FI.UTF-8

COPY generator.ini /etc/php/7.4/mods-available/
COPY pdfgenerator.conf /etc/apache2/sites-available/

SHELL ["/bin/bash", "-o", "pipefail", "-c"]
# Configure PHP for generator
RUN phpenmod generator; \
    a2enmod rewrite expires; \
    echo "ServerName localhost" | tee /etc/apache2/conf-available/servername.conf; \
    a2enconf servername; \
    # Configure vhost
    a2dissite 000-default; \
    a2ensite pdfgenerator.conf; \
    # Apache logs should go to stdout / stderr
    ln -sf /dev/stderr /var/log/apache2/error.log; \
    ln -sf /dev/stdout /var/log/apache2/access.log

WORKDIR /var/www/html

RUN mkdir -vp /var/www/html/web

RUN rm index.html

EXPOSE 80 443

ENV DEBIAN_FRONTEND=dialog

CMD ["apache2ctl", "-D", "FOREGROUND"]
