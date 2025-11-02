# 👨‍💻 Notas para Desarrolladores

Guía técnica para desarrolladores que trabajen en el proyecto **Ecomer with Bagisto by Gabodev**.

---

## 🏗️ Arquitectura del Proyecto

### Estructura Modular

Bagisto utiliza una arquitectura modular donde cada funcionalidad está encapsulada en un paquete independiente dentro de `packages/Webkul/`.

### Flujo de Datos

```
Request → Route → Controller → Repository → Model → Database
                      ↓
                  Service (opcional)
```

### Convenciones de Código

- **PSR-4 Autoloading**: Namespaces según estructura de carpetas
- **PSR-12 Coding Style**: Código formateado con Laravel Pint
- **Repository Pattern**: Lógica de negocio en repositorios
- **Service Layer**: Servicios para lógica compleja

---

## 📁 Estructura de Carpetas Clave

```
app/
├── Http/
│   ├── Controllers/     # Controladores personalizados
│   └── Middleware/      # Middleware personalizado
└── Providers/           # Service providers personalizados

packages/Webkul/
├── Core/                # Núcleo del sistema
├── Admin/               # Panel de administración
├── Shop/                # Frontend de la tienda
├── Product/             # Gestión de productos
└── [Otros módulos]/
```

---

## 🔧 Desarrollo Local

### Configuración de Entorno

El archivo `.env` debe tener:

```env
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000

# Base de datos local
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=bagisto_db

# Caché local
CACHE_DRIVER=file
SESSION_DRIVER=file
```

### Comandos Útiles

```bash
# Limpiar caché
php artisan cache:clear
php artisan config:clear
php artisan view:clear
php artisan route:clear

# Recargar autoloader
composer dump-autoload

# Ejecutar migraciones
php artisan migrate
php artisan migrate:fresh --seed

# Servir aplicación
php artisan serve

# Compilar assets en desarrollo
npm run dev

# Ejecutar tests
php artisan test
# o
phpunit
```

---

## 🧩 Crear un Nuevo Módulo

### Estructura de un Módulo

```
packages/Webkul/TuModulo/
├── src/
│   ├── Contracts/
│   ├── Models/
│   ├── Repositories/
│   ├── Http/
│   │   ├── Controllers/
│   │   └── Requests/
│   └── Providers/
│       └── TuModuloServiceProvider.php
├── database/
│   └── migrations/
├── resources/
│   ├── views/
│   └── assets/
└── routes/
    ├── admin-routes.php
    └── shop-routes.php
```

### Service Provider

```php
<?php

namespace Webkul\TuModulo\Providers;

use Illuminate\Support\ServiceProvider;

class TuModuloServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Cargar migraciones
        $this->loadMigrationsFrom(__DIR__ . '/../database/migrations');
        
        // Cargar vistas
        $this->loadViewsFrom(__DIR__ . '/../resources/views', 'tu-modulo');
        
        // Publicar assets
        $this->publishes([
            __DIR__ . '/../resources/assets' => public_path('themes/default/assets'),
        ], 'public');
    }
    
    public function register()
    {
        // Registrar bindings
    }
}
```

---

## 🔌 Eventos y Listeners

Bagisto utiliza eventos de Laravel para desacoplar funcionalidades.

### Registrar Eventos

```php
// En el Service Provider
protected $listen = [
    'checkout.order.save.after' => [
        'App\Listeners\SendOrderConfirmation',
    ],
];
```

---

## 🎨 Personalizar Frontend

### Temas

Los temas se encuentran en `packages/Webkul/Theme/src/Resources/themes/`

### Modificar Vistas

1. Copia la vista que quieres modificar a `resources/themes/default/`
2. Modifica según tus necesidades
3. El sistema cargará automáticamente tu versión personalizada

### Assets (CSS/JS)

1. Modifica archivos en `resources/themes/default/assets/`
2. Ejecuta `npm run dev` o `npm run build`
3. Los cambios se reflejarán en el frontend

---

## 🗄️ Base de Datos

### Migraciones

Las migraciones están en cada módulo:

```
packages/Webkul/[Modulo]/database/migrations/
```

### Modelos

Todos los modelos extienden de clases base de Bagisto y siguen Eloquent ORM.

### Relaciones

Consulta `context/database.md` para ver el esquema completo de relaciones.

---

## 🧪 Testing

### Configuración

```bash
# Instalar dependencias de testing
composer require --dev pestphp/pest

# Ejecutar tests
php artisan test
```

### Escribir Tests

```php
use Tests\TestCase;

class ProductTest extends TestCase
{
    public function test_can_create_product()
    {
        $product = Product::factory()->create();
        
        $this->assertDatabaseHas('products', [
            'id' => $product->id
        ]);
    }
}
```

---

## 🔒 Seguridad

### Buenas Prácticas

1. **Validación**: Siempre valida inputs con Form Requests
2. **Autorización**: Usa Policies para controlar acceso
3. **Sanitización**: Limpia datos de usuario antes de guardar
4. **CSRF Protection**: Laravel lo maneja automáticamente
5. **SQL Injection**: Usa Eloquent, nunca queries raw sin bindings

### Vulnerabilidades

Si encuentras una vulnerabilidad, no la publiques. Contacta a través de:
- Email: [tu-email]
- GitHub Security: Private disclosure

---

## 📦 Publicar Cambios

### Git Workflow

1. Crea una rama:
   ```bash
   git checkout -b feature/mi-funcionalidad
   ```

2. Realiza cambios y commits:
   ```bash
   git add .
   git commit -m "feat: agregar nueva funcionalidad"
   ```

3. Push y Pull Request:
   ```bash
   git push origin feature/mi-funcionalidad
   ```

### Convenciones de Commits

Sigue [Conventional Commits](https://www.conventionalcommits.org/):
- `feat`: Nueva funcionalidad
- `fix`: Corrección de bug
- `docs`: Documentación
- `refactor`: Refactorización
- `test`: Tests
- `chore`: Mantenimiento

---

## 📚 Recursos Adicionales

- **Documentación Bagisto**: [devdocs.bagisto.com](https://devdocs.bagisto.com/)
- **Documentación Laravel**: [laravel.com/docs](https://laravel.com/docs)
- **Documentación Vue.js**: [vuejs.org](https://vuejs.org/)
- **Documentación Técnica del Proyecto**: `/context`

---

## 🐛 Debugging

### Laravel Debugbar

Si está instalado, accede a la información de debug en la parte inferior de la página en desarrollo.

### Logs

Los logs están en `storage/logs/laravel.log`

```bash
tail -f storage/logs/laravel.log
```

### Tinker

Para probar código rápidamente:

```bash
php artisan tinker
```

---

## 🔄 Actualizaciones

### Actualizar Dependencias

```bash
composer update
npm update
```

### Actualizar Bagisto

Consulta `UPGRADE.md` en la raíz del proyecto para guías de actualización.

---

**Última actualización**: 2025-11-02

