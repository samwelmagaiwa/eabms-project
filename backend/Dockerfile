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

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd pdo_mysql mbstring exif pcntl bcmath zip

# Get Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www

# Copy only composer files first → enables Docker layer caching
COPY ./backend/composer.json ./backend/composer.lock* ./

# Install PHP dependencies (cached unless composer files change)
RUN composer install \
    --optimize-autoloader \
    --no-dev \
    --no-scripts \
    --no-interaction \
    --prefer-dist \
    --no-progress

# Now copy the rest of the application code
COPY ./backend /var/www

# Generate optimized autoloader + trigger post-install scripts
RUN composer dump-autoload --optimize && \
    composer run-script post-autoload-dump || true

# Create storage directories and fix permissions
RUN mkdir -p /var/www/storage/logs \
            /var/www/storage/framework/cache \
            /var/www/storage/framework/sessions \
            /var/www/storage/framework/views \
            /var/www/bootstrap/cache \
    && chown -R www-data:www-data \
        /var/www/storage \
        /var/www/bootstrap/cache \
    && chmod -R 775 \
        /var/www/storage \
        /var/www/bootstrap/cache

# Switch to non-root user (required for production)
USER www-data

# Expose PHP-FPM port
EXPOSE 9000

# Start PHP-FPM
CMD ["php-fpm"]