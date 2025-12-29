FROM php:8.2-fpm

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libzip-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

# Configure and install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j"$(nproc)" gd \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath zip

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory inside the container
WORKDIR /var/www

# IMPORTANT: context is ./backend, so composer files are at the root of the context
COPY composer.json composer.lock* ./

# Install PHP dependencies (no dev)
RUN composer install \
    --no-dev \
    --no-interaction \
    --no-scripts \
    --optimize-autoloader

# Now copy the rest of the backend source code
COPY . /var/www

# Ensure proper permissions for storage and cache (ignore if dirs missing)
RUN chown -R www-data:www-data storage bootstrap/cache || true

EXPOSE 9000

# Default command used by the nginx container as PHP-FPM upstream
CMD ["php-fpm"]