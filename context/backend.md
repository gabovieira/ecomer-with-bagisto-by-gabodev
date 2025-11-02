# Backend - Arquitectura Laravel

## Arquitectura MVC

Bagisto sigue el patrón Model-View-Controller (MVC) de Laravel, con una estructura modular donde cada módulo implementa su propia capa MVC.

### Estructura General

```
packages/Webkul/{Module}/src/
├── Http/
│   ├── Controllers/        # Controladores (C)
│   ├── Middleware/         # Middleware personalizado
│   ├── Requests/           # Form requests (validación)
│   └── Resources/          # API Resources
├── Models/                 # Modelos Eloquent (M)
├── Database/
│   ├── Migrations/         # Migraciones de base de datos
│   └── Factories/          # Factories para testing
└── Resources/
    └── views/              # Vistas Blade (V)
```

## Service Providers

Los service providers registran servicios, rutas, vistas, migraciones y otros componentes de cada módulo.

### Tipos de Service Providers

#### 1. CoreModuleServiceProvider
Base para todos los módulos. Extiende de `Konekt\Concord\BaseModuleServiceProvider` y proporciona:
- Registro automático de migraciones
- Registro de modelos
- Registro de vistas
- Registro de rutas

Ubicación: `packages/Webkul/Core/src/Providers/CoreModuleServiceProvider.php`

#### 2. ModuleServiceProvider
Cada módulo tiene su propio `ModuleServiceProvider` que extiende de `CoreModuleServiceProvider`:

```php
namespace Webkul\{Module}\Providers;

use Webkul\Core\Providers\CoreModuleServiceProvider;

class ModuleServiceProvider extends CoreModuleServiceProvider
{
    // Configuración específica del módulo
}
```

#### 3. Service Providers Principales
Además del ModuleServiceProvider, algunos módulos tienen service providers adicionales:

- **AdminServiceProvider**: `packages/Webkul/Admin/src/Providers/AdminServiceProvider.php`
- **ShopServiceProvider**: `packages/Webkul/Shop/src/Providers/ShopServiceProvider.php`
- **CoreServiceProvider**: `packages/Webkul/Core/src/Providers/CoreServiceProvider.php`

### Registro de Service Providers

Los service providers se registran en dos lugares:

1. **bootstrap/providers.php**: Service providers principales que se cargan siempre
2. **config/concord.php**: Módulos que se registran mediante Concord

Ejemplo de registro:

```php
// bootstrap/providers.php
Webkul\Core\Providers\CoreServiceProvider::class,
Webkul\Shop\Providers\ShopServiceProvider::class,
```

## Sistema de Rutas

Bagisto organiza las rutas por módulo y tipo de aplicación.

### Rutas Web

#### Admin Routes
- Ubicación: `packages/Webkul/Admin/src/Routes/`
- Archivos:
  - `web.php`: Rutas principales del admin
  - `auth-routes.php`: Autenticación
  - `catalog-routes.php`: Gestión de catálogo
  - `customers-routes.php`: Gestión de clientes
  - `sales-routes.php`: Gestión de ventas
  - `settings-routes.php`: Configuración
  - `rest-routes.php`: API REST del admin

#### Shop Routes
- Ubicación: `packages/Webkul/Shop/src/Routes/`
- Archivos:
  - `web.php`: Rutas principales de la tienda
  - `store-front-routes.php`: Páginas públicas (home, productos, categorías)
  - `customer-routes.php`: Área de cliente
  - `checkout-routes.php`: Proceso de checkout
  - `api.php`: API REST de la tienda

### Rutas API

Las rutas API están separadas y usan Laravel Sanctum para autenticación:

- **Admin API**: `packages/Webkul/Admin/src/Routes/rest-routes.php`
- **Shop API**: `packages/Webkul/Shop/src/Routes/api.php`

### Prefijos de Rutas

- Admin: `/admin` o configurable
- Shop API: `/shop/api`
- Admin API: `/admin/api`

## Middleware

Bagisto utiliza middleware personalizado para diferentes funcionalidades:

### Middleware del Shop

- **AuthenticateCustomer**: Autenticación de clientes
- **Currency**: Gestión de moneda
- **Locale**: Gestión de idioma
- **Theme**: Selección de tema
- **CacheResponse**: Cacheo de respuestas

Ubicación: `packages/Webkul/Shop/src/Http/Middleware/`

### Middleware Global

Los middleware globales se registran en `app/Http/Middleware/` o en los service providers de cada módulo.

## Controladores

### Estructura de Controladores

Los controladores están organizados por funcionalidad:

```
packages/Webkul/{Module}/src/Http/Controllers/
├── {Module}Controller.php          # Controlador principal
├── {SubModule}/                     # Subcarpetas por funcionalidad
│   └── {SubModule}Controller.php
└── API/                             # Controladores de API
    └── APIController.php
```

### Ejemplo: Shop Controllers

```
Shop/src/Http/Controllers/
├── ProductController.php           # Productos
├── CartController.php              # Carrito
├── OnepageController.php           # Checkout
├── Customer/
│   ├── Account/                    # Área de cliente
│   └── CustomerController.php
└── API/
    └── ProductController.php       # API de productos
```

### Convenciones

- Los controladores extienden de `Webkul\{Module}\Http\Controllers\Controller`
- Los controladores de API extienden de `Webkul\{Module}\Http\Controllers\API\APIController`
- Se usa inyección de dependencias para servicios y repositorios

## Modelos Eloquent

### Ubicación

Los modelos están en: `packages/Webkul/{Module}/src/Models/`

### Convenciones

- Extienden de `Illuminate\Database\Eloquent\Model`
- Implementan contratos (interfaces) en `Contracts/`
- Usan traits para funcionalidades compartidas

### Ejemplo de Modelo

```php
namespace Webkul\Product\Models;

use Webkul\Product\Contracts\Product as ProductContract;

class Product extends Model implements ProductContract
{
    // ...
}
```

## Repositorios

Bagisto utiliza el patrón Repository para abstraer el acceso a datos. Los repositorios están en:

`packages/Webkul/{Module}/src/Repositories/`

### Uso de Repositorios

Los controladores inyectan repositorios en lugar de usar modelos directamente:

```php
class ProductController
{
    public function __construct(
        protected ProductRepository $productRepository
    ) {}
}
```

## Form Requests (Validación)

Las validaciones se manejan mediante Form Requests:

Ubicación: `packages/Webkul/{Module}/src/Http/Requests/`

Ejemplo:
```php
namespace Webkul\Customer\Http\Requests;

class RegistrationRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'first_name' => 'required',
            'email' => 'required|email|unique:customers',
            // ...
        ];
    }
}
```

## API Resources

Para formatear respuestas de API, se usan API Resources:

Ubicación: `packages/Webkul/{Module}/src/Http/Resources/`

Ejemplo:
```php
namespace Webkul\Shop\Http\Resources;

class ProductResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            // ...
        ];
    }
}
```

## Eventos y Listeners

Bagisto utiliza el sistema de eventos de Laravel para desacoplar funcionalidades.

### Ubicación

- **Eventos**: `packages/Webkul/{Module}/src/Events/`
- **Listeners**: `packages/Webkul/{Module}/src/Listeners/`

### Service Providers de Eventos

Cada módulo puede tener un `EventServiceProvider` que registra eventos y listeners:

`packages/Webkul/{Module}/src/Providers/EventServiceProvider.php`

### Ejemplo de Eventos Comunes

- `OrderCreated`: Cuando se crea un pedido
- `OrderCanceled`: Cuando se cancela un pedido
- `CustomerRegistered`: Cuando se registra un cliente
- `ProductCreated`: Cuando se crea un producto

## Sistema de Módulos (Concord)

Bagisto usa [Konekt Concord](https://konekt.dev/concord/) para gestionar módulos. Concord proporciona:

- Registro automático de módulos
- Gestión de modelos y contratos
- Sistema de convenciones

### Configuración

`config/concord.php` contiene la lista de módulos registrados y la convención a usar (`CoreConvention`).

### CoreConvention

Ubicación: `packages/Webkul/Core/src/CoreConvention.php`

Define las convenciones del proyecto, como rutas de migraciones, modelos, vistas, etc.

## Autenticación

### Admin

- Usa guard `admin` configurado en `config/auth.php`
- Modelo: `Webkul\User\Models\Admin`
- Middleware: `auth:admin`

### Customer (Shop)

- Usa guard `customer` configurado en `config/auth.php`
- Modelo: `Webkul\Customer\Models\Customer`
- Middleware: `auth:customer`

### API

- Usa Laravel Sanctum
- Tokens almacenados en `personal_access_tokens`
- Headers requeridos: `Authorization: Bearer {token}`

## Cache y Optimización

- **Cache de respuestas**: Middleware `CacheResponse` en el Shop
- **Cache de consultas**: Redis para cache de consultas
- **Response Cache**: Usa `spatie/laravel-responsecache` para cachear respuestas completas
- **Vite**: Compilación y optimización de assets frontend

## Colas (Queues)

Bagisto utiliza el sistema de colas de Laravel para tareas asíncronas:

- Emails
- Generación de reportes
- Procesamiento de imágenes
- Sincronización de datos

Configuración en `config/queue.php`. Soporta drivers: sync, database, redis.

## Helpers y Utilidades

Ubicación: `packages/Webkul/Core/src/Http/helpers.php`

Contiene funciones helper globales utilizadas en toda la aplicación.


