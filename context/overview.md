# Overview - Bagisto E-Commerce Framework

## Propósito del Proyecto

Bagisto es un framework de e-commerce open source construido sobre Laravel que permite crear tiendas en línea completas y escalables. Este proyecto proporciona una solución modular y flexible para gestionar productos, pedidos, clientes, pagos y envíos, entre otras funcionalidades de comercio electrónico.

## Stack Tecnológico

### Backend
- **Laravel 11**: Framework PHP principal
- **PHP 8.2**: Lenguaje de programación con todas las extensiones necesarias
- **MySQL**: Base de datos relacional principal
- **Redis**: Sistema de caché y sesiones
- **Elasticsearch**: Motor de búsqueda para productos

### Frontend
- **Vue.js**: Framework JavaScript progresivo para interfaces de usuario
- **Vite**: Herramienta de build y desarrollo para assets
- **Axios**: Cliente HTTP para comunicación con el backend
- **Blade**: Motor de plantillas de Laravel

### Herramientas y Servicios
- **Laravel Sanctum**: Autenticación API
- **Laravel Octane**: Servidor de alto rendimiento
- **Docker/Laravel Sail**: Contenedores para desarrollo y despliegue
- **Guzzle**: Cliente HTTP para servicios externos
- **Intervention Image**: Manipulación de imágenes

## Estructura de Carpetas Principales

```
my-bagisto-store/
├── app/                    # Aplicación principal (código custom)
│   ├── Http/
│   │   ├── Controllers/    # Controladores custom
│   │   └── Middleware/     # Middleware custom
│   └── Providers/          # Service providers custom
├── bootstrap/              # Archivos de arranque de Laravel
├── config/                 # Archivos de configuración
├── database/               # Migraciones y seeders principales
│   ├── migrations/         # Migraciones de Laravel base
│   └── seeders/            # Seeders de datos iniciales
├── lang/                   # Traducciones globales
├── packages/               # Módulos del sistema (arquitectura modular)
│   └── Webkul/             # Namespace de módulos Webkul
│       ├── Admin/           # Panel de administración
│       ├── Core/           # Núcleo del sistema
│       ├── Shop/           # Frontend de la tienda
│       ├── Product/        # Gestión de productos
│       ├── Sales/          # Gestión de ventas y pedidos
│       ├── Customer/       # Gestión de clientes
│       └── [otros módulos] # Más de 30 módulos adicionales
├── public/                 # Punto de entrada web
│   └── themes/             # Assets compilados de temas
├── resources/              # Recursos sin compilar
│   ├── css/
│   └── js/
├── routes/                 # Rutas principales
│   ├── web.php            # Rutas web
│   └── console.php        # Comandos de consola
├── storage/                # Archivos de almacenamiento
├── tests/                  # Tests automatizados
└── vendor/                 # Dependencias de Composer

```

## Arquitectura Modular

Bagisto utiliza una arquitectura modular basada en paquetes Laravel. Cada módulo se encuentra en `packages/Webkul/` y sigue una estructura estándar:

- **src/**: Código fuente del módulo
  - **Http/Controllers/**: Controladores
  - **Models/**: Modelos Eloquent
  - **Database/Migrations/**: Migraciones de base de datos
  - **Providers/**: Service providers del módulo
  - **Resources/**: Vistas, assets, traducciones
  - **Routes/**: Rutas del módulo

## Convenciones del Proyecto

### Nombrado
- Los módulos usan el namespace `Webkul\{ModuleName}`
- Los controladores siguen el patrón `{ModuleName}Controller`
- Las rutas están organizadas por módulo en `src/Routes/`

### Registro de Módulos
Los módulos se registran en:
- `bootstrap/providers.php`: Service providers principales
- `config/concord.php`: Módulos Concord

### Estructura MVC
- **Models**: En `packages/Webkul/{Module}/src/Models/`
- **Views**: En `packages/Webkul/{Module}/src/Resources/views/`
- **Controllers**: En `packages/Webkul/{Module}/src/Http/Controllers/`

### Base de Datos
- Las migraciones están en cada módulo: `packages/Webkul/{Module}/src/Database/Migrations/`
- Se ejecutan automáticamente al cargar el módulo

## Módulos Principales

### Core
- Sistema de canales, monedas, locales
- Gestión de direcciones
- Helpers y utilidades comunes

### Admin
- Panel de administración completo
- Gestión de catálogo, clientes, ventas
- Configuración del sistema

### Shop
- Frontend de la tienda
- Carrito de compras
- Checkout y procesos de compra

### Product
- Gestión de productos
- Atributos y familias de atributos
- Variantes y configuraciones

### Sales
- Gestión de pedidos
- Facturas y envíos
- Reembolsos y transacciones

Para más detalles sobre cada módulo, ver la documentación específica en `context/modules-*.md`.

## Requisitos del Sistema

- PHP 8.2 o superior
- Composer 2.x
- Node.js 16+ y npm/yarn
- MySQL 5.7+ o MariaDB 10.3+
- Redis (opcional pero recomendado)
- Elasticsearch 7.x (opcional para búsqueda avanzada)

## Punto de Entrada

El punto de entrada de la aplicación es `public/index.php`, que carga el autoloader de Composer y arranca la aplicación Laravel.


