# Usa PHP 8.4 con Apache
FROM php:8.4-apache

# Actualizar paquetes e instalar dependencias esenciales y extensiones
RUN apt-get update && apt-get install -y \
    wget \
    mariadb-client \
    git \
    unzip \
    zip \
    curl \
    libzip-dev \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libonig-dev \
    libxml2-dev \
    xfonts-75dpi \
    xfonts-base \
    nano \
    supervisor \
    cron \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql mbstring xml bcmath zip

# Instalar wkhtmltopdf patcheado manualmente
RUN wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz \
    && tar -xvf wkhtmltox-0.12.4_linux-generic-amd64.tar.xz \
    && cp wkhtmltox/bin/wkhtmltopdf /usr/local/bin/wkhtmltopdf \
    && cp wkhtmltox/bin/wkhtmltoimage /usr/local/bin/wkhtmltoimage \
    && chmod +x /usr/local/bin/wkhtmltopdf /usr/local/bin/wkhtmltoimage \
    && rm -rf wkhtmltox*

# Crear directorios de logs para Apache y corregir permisos
RUN mkdir -p /var/log/apache2 && \
    touch /var/log/apache2/error.log /var/log/apache2/access.log && \
    chown -R www-data:www-data /var/log/apache2

# Instalar Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Habilitar mod_rewrite para Laravel en Apache
RUN a2enmod rewrite

# Crear carpeta de proyectos y asegurar permisos correctos
RUN mkdir -p /var/www/projects && chown -R www-data:www-data /var/www/projects

# Copiar todos los VirtualHosts desde `docker/apache/sites/`
COPY docker/apache/sites/*.conf /etc/apache2/sites-available/

# Habilitar todos los VirtualHosts disponibles
RUN find /etc/apache2/sites-available/ -type f -name "*.conf" -exec a2ensite $(basename {} .conf) \;

# Configurar Apache para leer los VirtualHosts
RUN echo "IncludeOptional /etc/apache2/sites-available/*.conf" >> /etc/apache2/apache2.conf

# Configurar un ServerName global para evitar advertencias en Apache
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Configurar el directorio de trabajo
WORKDIR /var/www/projects

# Iniciar Apache en primer plano
CMD ["apachectl", "-D", "FOREGROUND"]
