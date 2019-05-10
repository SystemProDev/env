# See https://github.com/docker-library/php/blob/master/7.1/fpm/Dockerfile
FROM php:7.1-fpm-jessie
ARG TIMEZONE

# Install Nodejs
RUN curl -sL https://deb.nodesource.com/setup_10.x -o nodesource_setup.sh
RUN bash nodesource_setup.sh

RUN apt-get update && apt-get install -my \
    wget \
    gnupg \
    openssl \
    git \
    unzip \
    zip \
    nodejs \
    build-essential \
    zlib1g-dev \
    libmagickwand-dev \
    libssl-dev \
    libreadline-dev \
    libgdbm-dev \
    libicu-dev \
    libcurl3

# Install libcurl
RUN cp /usr/lib/x86_64-linux-gnu/libcurl.so.3 /usr/lib/
RUN apt-get install libcurl4-openssl-dev -y
RUN env LD_PRELOAD=/usr/lib/libcurl.so.3

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone
RUN printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini
RUN "date"

RUN pecl install APCu-5.1.8
RUN pecl install imagick

# Type docker-php-ext-install to see available extensions
RUN docker-php-ext-configure intl
RUN docker-php-ext-install pdo pdo_mysql zip pcntl shmop intl
RUN docker-php-ext-enable apcu imagick

# Install xdebug
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug
RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_connect_back=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.idekey=\"PHPSTORM\"" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_port=9001" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.max_nesting_level=10000" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini


RUN echo 'alias sf="php app/console"' >> ~/.bashrc
RUN echo 'alias sf3="php bin/console"' >> ~/.bashrc

COPY "memory-limit-php.ini" "/usr/local/etc/php/conf.d/memory-limit-php.ini"
COPY "max_input_vars-php.ini" "/usr/local/etc/php/conf.d/max_input_vars-php.ini"
COPY "max_execution_time-php.ini" "/usr/local/etc/php/conf.d/max_execution_time-php.ini"
COPY "request_terminate_timeout-php.ini" "/usr/local/etc/php/conf.d/request_terminate_timeout-php.ini"

# Install ruby
RUN mkdir RUBY
RUN cd RUBY && wget https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.0.tar.gz \
    && tar xvfz ruby-2.3.0.tar.gz \
    && cd ruby-2.3.0 && ./configure \
    && make \
    && make install

# Install and update npm packages
RUN npm install -g npm
RUN rm -rf node_modules && npm i
RUN npm install uglify-js -g
RUN npm install uglifycss -g
RUN npm install sass -g

RUN ln -s /usr/local/bin/ruby /usr/bin/ruby
RUN rm -r /var/lib/apt/lists/*

RUN echo 172.100.0.1 "symfony.local" >> /etc/hosts

WORKDIR /var/www/symfony
