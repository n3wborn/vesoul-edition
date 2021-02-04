FROM php:7.4-apache


# Set localhost as server name
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf


# Update and Install requirements
RUN apt-get update \
    && apt-get install -y --no-install-recommends     \
      locales apt-utils git libicu-dev g++ libpng-dev \
      libxml2-dev libzip-dev libonig-dev libxslt-dev  \
      vim \
    ;


# Sel locale
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    echo "fr_FR.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen


# Install Composer
RUN curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
   mv composer.phar /usr/local/bin/composer


# Configure
RUN docker-php-ext-configure intl
RUN docker-php-ext-install pdo pdo_mysql gd opcache intl zip calendar dom mbstring zip gd xsl
RUN pecl install apcu xdebug && docker-php-ext-enable apcu xdebug

# Run as user user in www/html dir
RUN addgroup --system stef --gid 1000 && \
    adduser --system stef --uid 1000 --ingroup stef && \
    usermod -aG www-data stef

USER stef
WORKDIR /var/www/