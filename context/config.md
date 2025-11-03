# Configuración - Variables y Archivos de Configuración

## Variables de Entorno

Bagisto utiliza el archivo `.env` para configurar el entorno. Las variables principales están organizadas por categoría.

### Variables de Aplicación

```env
APP_NAME=Bagisto
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost
APP_TIMEZONE=UTC
APP_LOCALE=en
APP_FALLBACK_LOCALE=en
APP_FAKER_LOCALE=en_US
APP_CURRENCY=USD
```

### Base de Datos

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bagisto
DB_USERNAME=root
DB_PASSWORD=
DB_PREFIX=
```

### Cache y Sesiones

```env
CACHE_DRIVER=redis
SESSION_DRIVER=redis
SESSION_LIFETIME=120

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
REDIS_CLIENT=phpredis
REDIS_DB=0
REDIS_CACHE_DB=1
REDIS_SESSION_DATABASE=2
```

### Colas

```env
QUEUE_CONNECTION=database
```

Opciones: `sync`, `database`, `redis`, `beanstalkd`, `sqs`

### Correo

```env
MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="noreply@example.com"
MAIL_FROM_NAME="${APP_NAME}"
```

### Elasticsearch (Búsqueda)

```env
ELASTICSEARCH_ENABLED=false
ELASTICSEARCH_HOST=localhost
ELASTICSEARCH_PORT=9200
ELASTICSEARCH_SCHEME=http
ELASTICSEARCH_USER=
ELASTICSEARCH_PASS=
```

### Logging

```env
LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug
```

## Archivos de Configuración PHP

Los archivos de configuración están en `config/`. Principales:

### app.php
Configuración general de la aplicación:
- Zona horaria
- Locale
- Proveedores de servicios
- Aliases de clases

### database.php
Configuración de conexiones de base de datos:
- MySQL/MariaDB
- Redis
- Otras conexiones

### cache.php
Configuración de caché:
- Drivers soportados
- TTL (tiempo de vida)
- Prefijos

### queue.php
Configuración de colas:
- Conexiones disponibles
- Workers
- Tiempo de espera

### mail.php
Configuración de correo:
- Drivers
- SMTP
- Templates

### auth.php
Guards y providers de autenticación:

```php
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],
    'admin' => [
        'driver' => 'session',
        'provider' => 'admins',
    ],
    'customer' => [
        'driver' => 'session',
        'provider' => 'customers',
    ],
],
```

### session.php
Configuración de sesiones:
- Driver (redis, database, file)
- Lifetime
- Encryption

### filesystems.php
Configuración de almacenamiento de archivos:
- Disco local
- Disco público
- Configuración de S3 (opcional)

## Configuración de Módulos

Cada módulo puede tener su propio archivo de configuración.

### concord.php
Registro de módulos del sistema:

```php
'modules' => [
    \Webkul\Admin\Providers\ModuleServiceProvider::class,
    \Webkul\Shop\Providers\ModuleServiceProvider::class,
    // ... más módulos
],
```

### breadcrumbs.php
Configuración de breadcrumbs (migas de pan).

### themes.php
Configuración de temas disponibles.

### products.php
Configuración de productos:
- Tipos de productos
- Validaciones

### elasticsearch.php
Configuración de Elasticsearch si está habilitado.

### imagecache.php
Configuración de caché de imágenes.

### responsecache.php
Configuración de caché de respuestas HTTP.

### sitemap.php
Configuración de sitemaps XML.

### openai.php
Configuración de integración con OpenAI (si MagicAI está habilitado).

## Configuración en Base de Datos

Muchas configuraciones se almacenan en base de datos y se gestionan desde el Admin:

- Canales (channels)
- Monedas (currencies)
- Locales (locales)
- Métodos de pago
- Métodos de envío
- Configuración general del sistema

### Acceso a Configuración

```php
use Webkul\Core\Models\CoreConfig;

// Obtener configuración
core()->getConfigData('general.general.locale_settings.locale');

// Guardar configuración
core()->storeConfig('general.general.locale_settings.locale', 'es');
```

## Variables de Entorno de Producción

Para producción, es importante configurar:

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://tu-dominio.com

DB_HOST=tu-servidor-db
DB_DATABASE=bagisto_prod
DB_USERNAME=usuario_seguro
DB_PASSWORD=password_fuerte

CACHE_DRIVER=redis
SESSION_DRIVER=redis

QUEUE_CONNECTION=redis

MAIL_MAILER=smtp
MAIL_HOST=smtp.tu-proveedor.com
MAIL_PORT=587
MAIL_USERNAME=usuario
MAIL_PASSWORD=password

ELASTICSEARCH_ENABLED=true
```

## Configuración de Servicios Externos

### PayPal

```env
PAYPAL_CLIENT_ID=
PAYPAL_CLIENT_SECRET=
PAYPAL_MODE=sandbox|live
```

### Google reCAPTCHA

```env
RECAPTCHA_SITE_KEY=
RECAPTCHA_SECRET_KEY=
```

### Social Login

```env
FACEBOOK_CLIENT_ID=
FACEBOOK_CLIENT_SECRET=

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
```

## Archivo .env.example

El proyecto incluye un `.env.example` con todas las variables documentadas. Se debe copiar a `.env` en la instalación:

```bash
cp .env.example .env
php artisan key:generate
```

## Validación de Variables

Bagisto incluye un validador de variables de entorno en `Webkul\Core\Providers\EnvValidatorServiceProvider` que verifica las variables requeridas al iniciar la aplicación.

## Configuración por Entorno

Se pueden usar archivos `.env.{environment}` para diferentes entornos:

- `.env.local`
- `.env.staging`
- `.env.production`

Laravel carga automáticamente el archivo apropiado según `APP_ENV`.

## Cache de Configuración

Para producción, se recomienda cachear la configuración:

```bash
php artisan config:cache
```

Esto mejora el rendimiento al no leer los archivos de configuración en cada request.

Para limpiar el cache:
```bash
php artisan config:clear
```

## Variables de Desarrollo

Para desarrollo local con Laravel Sail:

```env
APP_PORT=80
VITE_PORT=5173
FORWARD_DB_PORT=3306
FORWARD_REDIS_PORT=6379
FORWARD_MAILPIT_PORT=8025
```



