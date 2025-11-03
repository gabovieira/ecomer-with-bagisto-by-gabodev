# Packages - Sistema Modular

## Introducción

Bagisto utiliza una arquitectura modular donde cada funcionalidad está encapsulada en un "package" o módulo independiente. Todos los módulos están ubicados en `packages/Webkul/`.

## Estructura de un Módulo

Cada módulo sigue una estructura estándar:

```
packages/Webkul/{Module}/
├── composer.json              # Configuración del paquete
├── package.json               # Si tiene assets frontend
├── vite.config.js             # Si usa Vite
├── src/
│   ├── Providers/
│   │   ├── ModuleServiceProvider.php    # Service provider principal
│   │   └── {Module}ServiceProvider.php # Service provider específico
│   ├── Http/
│   │   ├── Controllers/       # Controladores
│   │   ├── Middleware/        # Middleware personalizado
│   │   ├── Requests/          # Form requests
│   │   └── Resources/         # API Resources
│   ├── Models/                # Modelos Eloquent
│   ├── Contracts/             # Interfaces/Contratos
│   ├── Repositories/          # Repositorios
│   ├── Database/
│   │   ├── Migrations/        # Migraciones
│   │   ├── Seeders/           # Seeders
│   │   └── Factories/          # Factories para testing
│   ├── Routes/                # Rutas del módulo
│   ├── Resources/
│   │   ├── views/             # Vistas Blade
│   │   ├── assets/            # Assets sin compilar
│   │   └── lang/              # Traducciones
│   ├── Events/                # Eventos
│   ├── Listeners/             # Listeners
│   ├── Mail/                  # Mailables
│   └── Config/                # Archivos de configuración
├── tests/                     # Tests del módulo
└── README.md                  # Documentación del módulo
```

## Registro de Módulos

### Service Providers

Los módulos se registran mediante service providers:

1. **bootstrap/providers.php**: Service providers principales
2. **config/concord.php**: Módulos registrados vía Concord

### ModuleServiceProvider

Cada módulo debe tener un `ModuleServiceProvider` que extiende de `CoreModuleServiceProvider`:

```php
namespace Webkul\{Module}\Providers;

use Webkul\Core\Providers\CoreModuleServiceProvider;

class ModuleServiceProvider extends CoreModuleServiceProvider
{
    /**
     * Models.
     */
    protected $models = [
        // Modelos del módulo
    ];
}
```

### CoreModuleServiceProvider

Ubicación: `packages/Webkul/Core/src/Providers/CoreModuleServiceProvider.php`

Este service provider base:
- Registra migraciones automáticamente
- Registra modelos
- Registra vistas
- Registra rutas
- Registra eventos

## Convenciones

### Naming

- **Namespace**: `Webkul\{ModuleName}`
- **Service Provider**: `{ModuleName}ServiceProvider`
- **Controller**: `{ModuleName}Controller`
- **Model**: `{ModuleName}` (singular)

### Rutas

Las rutas se organizan en archivos dentro de `src/Routes/`:

- `web.php`: Rutas web principales
- `api.php`: Rutas API
- `{feature}-routes.php`: Rutas por funcionalidad

### Migraciones

Las migraciones deben seguir el formato:
```
YYYY_MM_DD_HHMMSS_create_{table}_table.php
```

Se ejecutan automáticamente al cargar el módulo si las migraciones están habilitadas.

### Modelos y Contratos

Cada modelo implementa un contrato (interface):

```php
namespace Webkul\Product\Models;

use Webkul\Product\Contracts\Product as ProductContract;

class Product extends Model implements ProductContract
{
    // ...
}
```

Esto permite:
- Polimorfismo
- Testing con mocks
- Intercambiabilidad

## Módulos Principales

### Core

**Función**: Núcleo del sistema

**Ubicación**: `packages/Webkul/Core/`

**Responsabilidades**:
- Gestión de channels (tiendas)
- Gestión de locales (idiomas)
- Gestión de currencies (monedas)
- Sistema de direcciones
- Helpers globales
- Base para otros módulos

**Dependencias**: Ninguna (es la base)

### Admin

**Función**: Panel de administración

**Ubicación**: `packages/Webkul/Admin/`

**Responsabilidades**:
- Dashboard
- Gestión de catálogo
- Gestión de clientes
- Gestión de ventas
- Configuración del sistema

**Dependencias**: Core, Product, Customer, Sales

### Shop

**Función**: Frontend de la tienda

**Ubicación**: `packages/Webkul/Shop/`

**Responsabilidades**:
- Interfaz pública
- Carrito de compras
- Checkout
- Área de cliente

**Dependencias**: Core, Product, Customer, Cart, Checkout

### Product

**Función**: Gestión de productos

**Ubicación**: `packages/Webkul/Product/`

**Responsabilidades**:
- CRUD de productos
- Atributos de productos
- Familias de atributos
- Product Flat (optimización)

**Dependencias**: Core, Attribute, Category

### Sales

**Función**: Gestión de ventas

**Ubicación**: `packages/Webkul/Sales/`

**Responsabilidades**:
- Órdenes
- Facturas
- Envíos
- Reembolsos
- Transacciones

**Dependencias**: Core, Customer, Payment, Shipping

### Customer

**Función**: Gestión de clientes

**Ubicación**: `packages/Webkul/Customer/`

**Responsabilidades**:
- Registro de clientes
- Autenticación
- Perfiles
- Direcciones
- Grupos de clientes

**Dependencias**: Core

### Payment

**Función**: Métodos de pago

**Ubicación**: `packages/Webkul/Payment/`

**Responsabilidades**:
- Configuración de métodos de pago
- Procesamiento de pagos
- Gestión de transacciones

**Dependencias**: Core, Sales

### Shipping

**Función**: Métodos de envío

**Ubicación**: `packages/Webkul/Shipping/`

**Responsabilidades**:
- Configuración de métodos de envío
- Cálculo de costos
- Gestión de envíos

**Dependencias**: Core, Sales

## Módulos Adicionales

### Attribute

Gestión de atributos de productos (color, tamaño, etc.)

### Category

Gestión de categorías de productos

### CartRule

Reglas de carrito (descuentos)

### CatalogRule

Reglas de catálogo (descuentos por producto)

### Checkout

Proceso de checkout

### CMS

Páginas de contenido estático

### Inventory

Gestión de inventario

### Marketing

Herramientas de marketing

### Notification

Sistema de notificaciones

### Tax

Gestión de impuestos

### Theme

Sistema de temas

### User

Gestión de usuarios admin

## Dependencias entre Módulos

```
Core (sin dependencias)
    ↓
├── Product, Customer, Payment, Shipping, etc.
    ↓
├── Shop (depende de: Product, Customer, Cart, Checkout)
    ↓
└── Sales (depende de: Customer, Payment, Shipping)
```

## Crear un Módulo Personalizado

### 1. Estructura de Carpetas

```bash
packages/Vendor/ModuleName/
├── composer.json
└── src/
    ├── Providers/
    │   └── ModuleServiceProvider.php
    └── ...
```

### 2. composer.json

```json
{
    "name": "vendor/module-name",
    "description": "Module description",
    "type": "library",
    "autoload": {
        "psr-4": {
            "Vendor\\ModuleName\\": "src/"
        }
    }
}
```

### 3. ModuleServiceProvider

```php
namespace Vendor\ModuleName\Providers;

use Webkul\Core\Providers\CoreModuleServiceProvider;

class ModuleServiceProvider extends CoreModuleServiceProvider
{
    protected $models = [
        // Modelos
    ];
}
```

### 4. Registrar en config/concord.php

```php
'modules' => [
    // ... módulos existentes
    \Vendor\ModuleName\Providers\ModuleServiceProvider::class,
],
```

### 5. Registrar Service Provider

En `bootstrap/providers.php`:

```php
\Vendor\ModuleName\Providers\ModuleServiceProvider::class,
```

## Composer Path Repository

Bagisto configura `composer.json` para reconocer packages locales:

```json
"repositories": [{
    "type": "path",
    "url": "packages/*/*",
    "options": {
        "symlink": true
    }
}]
```

Esto permite usar packages locales como si fueran de Composer.

## Testing de Módulos

Cada módulo puede tener sus propios tests:

```
packages/Webkul/{Module}/tests/
├── Unit/
└── Feature/
```

Ejecutar tests de un módulo:

```bash
php artisan test packages/Webkul/{Module}/tests
```

## Versionado

Los módulos siguen el versionado de Bagisto:
- Compatibilidad con versiones específicas de Laravel
- Versionado semántico (si se publican como packages independientes)

## Documentación de Módulos

Cada módulo puede incluir:
- `README.md`: Documentación del módulo
- Comentarios en código
- DocBlocks en clases y métodos

Para más detalles sobre módulos específicos, ver:
- `context/modules-core.md`
- `context/modules-admin.md`
- `context/modules-shop.md`
- etc.



