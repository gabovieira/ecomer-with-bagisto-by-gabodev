# Despliegue - Instalación y Configuración

## Requisitos del Sistema

### Servidor

- **PHP**: 8.2 o superior
- **Composer**: 2.x
- **Node.js**: 16+ (para compilar assets)
- **npm/yarn**: Para gestionar dependencias frontend

### Base de Datos

- **MySQL**: 5.7+ o **MariaDB**: 10.3+
- Privilegios para crear bases de datos y tablas

### Extensiones PHP Requeridas

- BCMath
- Ctype
- cURL
- DOM
- Fileinfo
- JSON
- Mbstring
- OpenSSL
- PDO
- Tokenizer
- XML

### Opcionales (Recomendados)

- **Redis**: Para caché y sesiones
- **Elasticsearch**: 7.x para búsqueda avanzada
- **Supervisor**: Para workers de colas

## Opciones de Despliegue

### 1. Docker / Laravel Sail

Laravel Sail es la forma más sencilla de desarrollar y desplegar localmente.

#### Instalación

```bash
# Si no tienes Sail instalado
composer require laravel/sail --dev

# Publish sail
php artisan sail:install

# Iniciar contenedores
./vendor/bin/sail up -d
```

#### Archivo docker-compose.yml

Bagisto incluye un `docker-compose.yml` configurado con:

- **Laravel**: Aplicación PHP
- **MySQL**: Base de datos
- **Redis**: Cache y sesiones
- **Elasticsearch**: Motor de búsqueda
- **Kibana**: Visualización de Elasticsearch
- **Mailpit**: Cliente de correo para desarrollo

#### Variables de Entorno Docker

```env
APP_PORT=80
VITE_PORT=5173
FORWARD_DB_PORT=3306
FORWARD_REDIS_PORT=6379
FORWARD_MAILPIT_PORT=8025
```

#### Comandos Útiles

```bash
# Acceder al contenedor
./vendor/bin/sail shell

# Ejecutar comandos artisan
./vendor/bin/sail artisan migrate

# Compilar assets
./vendor/bin/sail npm run build

# Ver logs
./vendor/bin/sail logs
```

### 2. Instalación Manual

#### Paso 1: Clonar o Descargar

```bash
git clone https://github.com/bagisto/bagisto.git
cd bagisto
```

#### Paso 2: Instalar Dependencias

```bash
# Dependencias PHP
composer install

# Dependencias JavaScript
npm install
```

#### Paso 3: Configurar Entorno

```bash
# Copiar archivo de configuración
cp .env.example .env

# Generar clave de aplicación
php artisan key:generate
```

#### Paso 4: Configurar Base de Datos

Editar `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bagisto
DB_USERNAME=root
DB_PASSWORD=tu_password
```

#### Paso 5: Ejecutar Migraciones

```bash
php artisan migrate

# Opcional: Seeders con datos de ejemplo
php artisan db:seed
```

#### Paso 6: Compilar Assets

```bash
# Desarrollo
npm run dev

# Producción
npm run build
```

#### Paso 7: Configurar Permisos

```bash
# Storage y cache deben ser escribibles
chmod -R 775 storage bootstrap/cache
chown -R www-data:www-data storage bootstrap/cache
```

#### Paso 8: Crear Usuario Admin

Bagisto tiene un instalador GUI que se ejecuta automáticamente en la primera visita o:

```bash
php artisan bagisto:install
```

### 3. Instalación con GUI

Bagisto incluye un instalador web interactivo:

1. Acceder a `http://tu-dominio.com` o `http://tu-dominio.com/install`
2. Seguir el asistente de instalación:
   - Verificar requisitos
   - Configurar base de datos
   - Crear usuario admin
   - Configurar tienda

### 4. Instalación en Cloud (AWS AMI)

Bagisto ofrece una AMI preconfigurada en AWS Marketplace:

1. Ir a [AWS Marketplace - Bagisto](https://aws.amazon.com/marketplace/pp/prodview-r3xv62axcqkpa)
2. Lanzar instancia
3. Seguir instrucciones de la AMI

## Configuración para Producción

### 1. Optimizaciones

```bash
# Cache de configuración
php artisan config:cache

# Cache de rutas
php artisan route:cache

# Cache de vistas
php artisan view:cache

# Optimizar autoloader
composer install --optimize-autoloader --no-dev
```

### 2. Variables de Entorno Producción

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://tu-dominio.com

LOG_CHANNEL=daily
LOG_LEVEL=error

CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis
```

### 3. Configuración del Servidor Web

#### Nginx

```nginx
server {
    listen 80;
    server_name tu-dominio.com;
    root /ruta/a/bagisto/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

#### Apache

Asegurarse de que `.htaccess` esté en `public/`:

```apache
<IfModule mod_rewrite.c>
    <IfModule mod_negotiation.c>
        Options -MultiViews -Indexes
    </IfModule>

    RewriteEngine On

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]
</IfModule>
```

### 4. Configurar Workers de Colas

Usando Supervisor:

```ini
[program:bagisto-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /ruta/a/bagisto/artisan queue:work redis --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=www-data
numprocs=2
redirect_stderr=true
stdout_logfile=/ruta/a/bagisto/storage/logs/worker.log
stopwaitsecs=3600
```

Iniciar supervisor:

```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start bagisto-worker:*
```

### 5. Configurar Cron Jobs

```bash
* * * * * cd /ruta/a/bagisto && php artisan schedule:run >> /dev/null 2>&1
```

### 6. SSL/HTTPS

Configurar certificado SSL (Let's Encrypt recomendado):

```bash
sudo certbot --nginx -d tu-dominio.com
```

Actualizar `.env`:
```env
APP_URL=https://tu-dominio.com
```

## Actualización

### Proceso de Actualización

1. **Backup**:
```bash
# Base de datos
mysqldump -u usuario -p bagisto > backup.sql

# Archivos
tar -czf backup.tar.gz /ruta/a/bagisto
```

2. **Actualizar Código**:
```bash
git pull origin main
# o
composer update
```

3. **Actualizar Dependencias**:
```bash
composer install
npm install
npm run build
```

4. **Ejecutar Migraciones**:
```bash
php artisan migrate
```

5. **Limpiar Caches**:
```bash
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear
```

6. **Rebuild Caches**:
```bash
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

## Troubleshooting

### Problemas Comunes

#### Error 500
- Verificar logs en `storage/logs/laravel.log`
- Verificar permisos de `storage/` y `bootstrap/cache/`
- Verificar `.env` configurado correctamente

#### Error de Base de Datos
- Verificar conexión en `.env`
- Verificar que MySQL esté corriendo
- Verificar permisos de usuario de BD

#### Assets no cargan
- Ejecutar `npm run build`
- Verificar que Vite esté corriendo en desarrollo
- Limpiar cache del navegador

#### Colas no procesan
- Verificar que Supervisor esté corriendo
- Verificar conexión Redis
- Revisar logs de workers

## Herramientas de Monitoreo

### Logs

Los logs se encuentran en:
- `storage/logs/laravel.log`: Logs de aplicación
- `storage/logs/worker.log`: Logs de workers (si Supervisor)

### Debug Bar

En desarrollo, Bagisto incluye DebugBar para debugging. Se desactiva en producción con `APP_DEBUG=false`.

## Consideraciones de Seguridad

1. **Permisos de Archivos**:
```bash
chmod -R 755 storage bootstrap/cache
```

2. **No Exponer `.env`**:
Asegurarse de que `.env` no sea accesible públicamente.

3. **Firewall**:
Configurar firewall para permitir solo puertos necesarios.

4. **Actualizaciones**:
Mantener Laravel y dependencias actualizadas.

5. **Backups Regulares**:
Implementar sistema de backups automáticos.


