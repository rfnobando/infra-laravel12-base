# Build stage
FROM php:8.4-fpm AS build

# Copy project for build
COPY ./app /var/www/app
WORKDIR /var/www/app

# Install necessary build tools and dependencies
RUN apt-get update && apt-get install -y git unzip curl \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer install --no-dev --optimize-autoloader


# Final stage
FROM php:8.4-apache

# Copy built project to final image
COPY --from=build /var/www/app /var/www/app
COPY ./infra/docker/prod/apache2/sites-available/app.conf /etc/apache2/sites-available/app.conf
WORKDIR /var/www/app

# Install Docker PDO extension and config Apache2
RUN docker-php-ext-install pdo pdo_mysql \
    && a2enmod rewrite \
    && a2dissite 000-default.conf \
    && a2ensite app.conf \
    && chown -R www-data:www-data /var/www \
    && find /var/www -type d -exec chmod 755 {} \; \
    && find /var/www -type f -exec chmod 644 {} \; \
    && chmod -R 775 /var/www/app/storage \
    && chmod -R 775 /var/www/app/bootstrap/cache

EXPOSE 80

CMD ["apache2-foreground"]
