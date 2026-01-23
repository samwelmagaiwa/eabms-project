FROM php:8.2-fpm

# System dependencies + PHP extensions
RUN apt-get update && apt-get install -y \
    git curl unzip zip \
    libpng-dev libjpeg-dev libfreetype6-dev \
    libonig-dev libxml2-dev libzip-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install \
        pdo_mysql mbstring exif pcntl bcmath gd zip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www

# Optimize layer caching
COPY composer.json composer.lock ./
RUN composer install --no-dev --optimize-autoloader --no-interaction --no-scripts

# Copy app
COPY . .

# Permissions
RUN chown -R www-data:www-data storage bootstrap/cache

# Security: non-root
USER www-data

EXPOSE 9000

CMD ["php-fpm"]
