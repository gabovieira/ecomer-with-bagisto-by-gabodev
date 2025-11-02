# 📦 Guía de Instalación

Guía paso a paso para instalar y configurar el proyecto **Ecomer with Bagisto by Gabodev**.

---

## 📋 Requisitos Previos

Antes de comenzar, asegúrate de tener instalado:

- **PHP** >= 8.2 con extensiones:
  - `ext-calendar`
  - `ext-curl`
  - `ext-intl`
  - `ext-mbstring`
  - `ext-openssl`
  - `ext-pdo`
  - `ext-pdo_mysql`
  - `ext-tokenizer`
- **Composer** >= 2.0
- **Node.js** >= 18.x y **npm** >= 9.x
- **MySQL** >= 8.0
- **Redis** (opcional, para caché)
- **Elasticsearch** >= 8.10 (opcional, para búsqueda avanzada)
- **Git**

---

## 🚀 Instalación en Windows (XAMPP/Laravel Herd)

### Paso 1: Clonar el Repositorio

```bash
git clone https://github.com/gabovieira/ecomer-with-bagisto-by-gabodev.git
cd ecomer-with-bagisto-by-gabodev
```

### Paso 2: Instalar Dependencias PHP

```bash
composer install
```

Este comando descargará todas las dependencias de Laravel y Bagisto.

### Paso 3: Configurar Variables de Entorno

Copia el archivo de ejemplo:

```bash
copy .env.example .env
```

Edita el archivo `.env` con tu editor favorito y configura:

```env
APP_NAME="Ecomer by Gabodev"
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bagisto_db
DB_USERNAME=root
DB_PASSWORD=

CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_CONNECTION=sync
```

Genera la clave de aplicación:

```bash
php artisan key:generate
```

### Paso 4: Crear Base de Datos

Crea una base de datos MySQL desde phpMyAdmin o línea de comandos:

```sql
CREATE DATABASE bagisto_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Paso 5: Ejecutar Migraciones y Seeders

```bash
php artisan migrate
php artisan db:seed
```

Esto creará todas las tablas necesarias y poblará datos iniciales.

### Paso 6: Crear Enlace Simbólico de Storage

```bash
php artisan storage:link
```

### Paso 7: Instalar Dependencias Frontend

```bash
npm install
```

### Paso 8: Compilar Assets

Para desarrollo (con hot-reload):

```bash
npm run dev
```

Para producción:

```bash
npm run build
```

### Paso 9: Crear Usuario Administrador

Ejecuta el instalador de Bagisto:

```bash
php artisan bagisto:install
```

O accede directamente a `/admin` y crea tu cuenta de administrador.

### Paso 10: Iniciar el Servidor

```bash
php artisan serve
```

Accede a la aplicación:
- **Frontend**: http://localhost:8000
- **Admin Panel**: http://localhost:8000/admin

---

## 🐳 Instalación con Docker (Opcional)

Si prefieres usar Docker:

```bash
docker-compose up -d
```

Esto levantará los contenedores necesarios (PHP, MySQL, Redis, etc.).

---

## ✅ Verificación de Instalación

Para verificar que todo está correcto:

1. Accede a http://localhost:8000
2. Deberías ver la página principal de la tienda
3. Accede a `/admin` para el panel de administración
4. Verifica que puedes crear productos y realizar pedidos de prueba

---

## 🔧 Solución de Problemas Comunes

### Error: "Class 'PDO' not found"
- Instala la extensión `php_pdo_mysql` en tu `php.ini`

### Error: "Composer memory limit"
- Ejecuta: `php -d memory_limit=-1 composer install`

### Error: "npm ERR! peer dep missing"
- Ejecuta: `npm install --legacy-peer-deps`

### Error: "SQLSTATE[HY000] [2002] Connection refused"
- Verifica que MySQL esté corriendo
- Confirma las credenciales en `.env`

### Error: "Storage link failed"
- Ejecuta manualmente: `php artisan storage:link --force`

---

## 📚 Próximos Pasos

Una vez instalado:

1. Revisa la [Guía de Uso](usage-guide.md)
2. Consulta las [Notas para Desarrolladores](developer-notes.md)
3. Explora la [Documentación Técnica](../context/)

---

**¿Necesitas ayuda?** Abre un issue en [GitHub](https://github.com/gabovieira/ecomer-with-bagisto-by-gabodev/issues)

