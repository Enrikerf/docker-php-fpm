FROM php:7.4.7-fpm-alpine3.12

# ---------------------------------------------------------- Setup build options
ARG DOCKER_PHP_ENABLE_APCU
ARG DOCKER_PHP_ENABLE_COMPOSER
ARG DOCKER_PHP_ENABLE_LDAP
ARG DOCKER_PHP_ENABLE_MEMCACHED
ARG DOCKER_PHP_ENABLE_MONGODB
ARG DOCKER_PHP_ENABLE_MYSQL
ARG DOCKER_PHP_ENABLE_POSTGRESQL
ARG DOCKER_PHP_ENABLE_REDIS
ARG DOCKER_PHP_ENABLE_SYMFONY
ARG DOCKER_PHP_ENABLE_XDEBUG

RUN echo -n "With apcu support:          " ; if [[ "${DOCKER_PHP_ENABLE_APCU}" = "on" ]] ;        then echo "Yes"; else echo "No" ; fi && \
    echo -n "With composer support:      " ; if [[ "${DOCKER_PHP_ENABLE_COMPOSER}" = "on" ]] ;    then echo "Yes"; else echo "No" ; fi && \
    echo -n "With ldap support:          " ; if [[ "${DOCKER_PHP_ENABLE_LDAP}" = "on" ]] ;      then echo "Yes"; else echo "No" ; fi && \
    echo -n "With memcached support:     " ; if [[ "${DOCKER_PHP_ENABLE_MEMCACHED}" = "on" ]] ;  then echo "Yes"; else echo "No" ; fi && \
    echo -n "With mongodb support:       " ; if [[ "${DOCKER_PHP_ENABLE_MONGODB}" = "on" ]] ;    then echo "Yes"; else echo "No" ; fi && \
    echo -n "With mysql support:         " ; if [[ "${DOCKER_PHP_ENABLE_MYSQL}" = "on" ]] ;    then echo "Yes"; else echo "No" ; fi && \
    echo -n "With postgresql support:    " ; if [[ "${DOCKER_PHP_ENABLE_POSTGRESQL}" = "on" ]] ; then echo "Yes"; else echo "No" ; fi && \
    echo -n "With redis support:         " ; if [[ "${DOCKER_PHP_ENABLE_REDIS}" = "on" ]] ;    then echo "Yes"; else echo "No" ; fi && \
    echo -n "With symfony support:       " ; if [[ "${DOCKER_PHP_ENABLE_SYMFONY}" = "on" ]] ;    then echo "Yes"; else echo "No" ; fi && \
    echo -n "With xdebug support:        " ; if [[ "${DOCKER_PHP_ENABLE_XDEBUG}" = "on" ]] ;      then echo "Yes"; else echo "No" ; fi

# --------------------------------------------------------- Install dependancies
RUN apk add --update --no-cache \
        # Dependancy for intl \
        icu-libs \
        libintl \
        # Dependancy for zip \
        libzip


# --------------------------------------------------- Install build dependancies
RUN apk add --update --no-cache --virtual .docker-php-global-dependancies \
        # Build dependencies for gd \
        freetype-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        # Build dependency for gettext \
        gettext-dev \
        # Build dependency for intl \
        icu-dev \
        # Build dependencies for XML part \
        libxml2-dev \
        ldb-dev \
        # Build dependencies for Zip \
        libzip-dev \
        # Build dependancies for Pecl \
        autoconf \
        g++ \
        make \
        # Build dependancy for APCu \
        pcre-dev \
        # Misc build dependancy \
        wget


# ------------------------------------------------------- Install php extensions
RUN php -m && \
    docker-php-ext-configure opcache --enable-opcache && \
    docker-php-ext-configure zip && \
    docker-php-ext-install zip \
        opcache && \
    php -m

# --------------------------------------------------- Conditionnal installations
# Enable APCu
RUN if [[ "${DOCKER_PHP_ENABLE_APCU}" = "on" ]] ; then \
      pecl install apcu && \
      docker-php-ext-enable apcu && \
      php -m; \
    else \
      echo "Skip apcu support"; \
    fi

# Enable LDAP
RUN if [[ "${DOCKER_PHP_ENABLE_LDAP}" = "on" ]] ; then \
      # Dependancy for ldap \
      apk add --update --no-cache \
          libldap && \
      # Build dependancy for ldap \
      apk add --update --no-cache --virtual .docker-php-ldap-dependancies \
          openldap-dev && \
      docker-php-ext-configure ldap && \
      docker-php-ext-install ldap && \
      apk del .docker-php-ldap-dependancies && \
      php -m; \
    else \
      echo "Skip ldap support"; \
    fi

# Enable Memcached
RUN if [[ "${DOCKER_PHP_ENABLE_MEMCACHED}" = "on" ]] ; then \
      # Dependancy for ldap \
      apk add --update --no-cache \
          libmemcached-libs && \
      # Build dependancies for memcached \
      apk add --update --no-cache --virtual .docker-php-memcached-dependancies \
          cyrus-sasl-dev \
          libmemcached-dev && \
      pecl install memcached && \
      docker-php-ext-enable memcached && \
      apk del .docker-php-memcached-dependancies && \
      php -m; \
    else \
      echo "Skip memcached support"; \
    fi

# Enable MongoDB
RUN if [[ "${DOCKER_PHP_ENABLE_MONGODB}" = "on" ]] ; then \
      apk add --update --no-cache --virtual .docker-php-mongodb-dependancies \
          # Dependancies for MongoDB \
          heimdal-dev && \
      pecl install mongodb && \
      docker-php-ext-enable mongodb && \
      apk del .docker-php-mongodb-dependancies && \
      php -m; \
    else \
      echo "Skip mongodb support"; \
    fi

# Enable MySQL
RUN if [[ "${DOCKER_PHP_ENABLE_MYSQL}" = "on" ]] ; then \
      apk add --update --no-cache --virtual .docker-php-mysql-dependancies \
          # Dependancy for mysql \
          mysql-client && \
      # MySQLnd is already compiled. \
      # See: https://github.com/docker-library/php/issues/167 \
      docker-php-ext-configure mysqli && \
      docker-php-ext-configure pdo_mysql && \
      docker-php-ext-install mysqli \
          pdo_mysql && \
      apk del .docker-php-mysql-dependancies && \
      php -m; \
    else \
      echo "Skip mysql support"; \
    fi

# Enable PostgreSQL
RUN if [[ "${DOCKER_PHP_ENABLE_POSTGRESQL}" = "on" ]] ;  then \
      # Dependancy for PostgreSQL \
      apk add --update --no-cache \
          libpq && \
      # Build dependancies for PostgreSQL \
      apk add --update --no-cache --virtual .docker-php-postgresql-dependancies \
          postgresql-client \
          postgresql-dev && \
      docker-php-ext-configure pdo_pgsql && \
      docker-php-ext-configure pgsql && \
      docker-php-ext-install pdo_pgsql \
          pgsql && \
      apk del .docker-php-postgresql-dependancies && \
      php -m; \
    else \
      echo "Skip postgresql support"; \
    fi

# Enable Redis
RUN if [[ "${DOCKER_PHP_ENABLE_REDIS}" = "on" ]] ;  then \
      pecl install redis && \
      docker-php-ext-enable redis && \
      php -m; \
    else \
      echo "Skip redis support"; \
    fi

# Enable Symfony extensions
RUN if [[ "${DOCKER_PHP_ENABLE_SYMFONY}" = "on" ]] ; then \
      # Dependancy for PostgreSQL \
      apk add --update --no-cache \
          yaml && \
      # Build dependancy for YAML \
      apk add --update --no-cache --virtual .docker-php-symfony-dependancies \
          yaml-dev && \
      pecl install yaml && \
      docker-php-ext-enable yaml; \
      apk del .docker-php-symfony-dependancies && \
      php -m; \
    else \
      echo "Skip symfony support"; \
    fi

RUN if [[ "${DOCKER_PHP_ENABLE_XDEBUG}" = "on" ]] ; then \
      yes | pecl install xdebug-2.9.6 && \
      docker-php-ext-enable xdebug  && \
      php -m; \
    else \
      echo "Skip xdebug support"; \
    fi

# Install composer.
RUN if [[ "${DOCKER_PHP_ENABLE_COMPOSER}" = "on" ]] ; then \
      EXPECTED_SIGNATURE=$(wget -q -O - https://composer.github.io/installer.sig) && \
      php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
      ACTUAL_SIGNATURE=$(php -r "echo hash_file('SHA384', 'composer-setup.php');") && \
      if [ "$EXPECTED_SIGNATURE" != "$ACTUAL_SIGNATURE" ]; then \
        >&2 echo 'ERROR: Invalid installer signature' && \
        rm composer-setup.php && \
        exit 1; \
      else \
        php composer-setup.php --install-dir=/usr/bin --filename=composer && \
        RESULT=$? && \
        rm composer-setup.php && \
        exit $RESULT && \
        composer -V; \
      fi; \
    else \
      echo "Skip composer support"; \
    fi

ADD etc/php/php.ini "$PHP_INI_DIR/php.ini"
ADD etc/php/conf.d "$PHP_INI_DIR/conf.d"
ADD etc/php-fpm.d/www.conf "/usr/local/etc/php-fpm.d/www.conf"

RUN if [[ "${DOCKER_PHP_ENABLE_XDEBUG}" = "off" ]] ; then \
      ls "$PHP_INI_DIR/conf.d" && \
      rm "$PHP_INI_DIR/conf.d/docker-php-ext-xdebug.ini" && \
      echo "remove xdebug file" && \
      php -m; \
    else \
      echo "-"; \
    fi
RUN if [[ "${DOCKER_PHP_ENABLE_APCU}" = "off" ]] ; then \
      ls "$PHP_INI_DIR/conf.d" && \
      rm "$PHP_INI_DIR/conf.d/docker-php-ext-apcu.ini" && \
      echo "remove apcu file" && \
      php -m; \
    else \
      echo "-"; \
    fi

# Clean
RUN apk del .docker-php-global-dependancies && \
    rm -rf /var/cache/apk/* && \
    docker-php-source delete

WORKDIR /var/www/app