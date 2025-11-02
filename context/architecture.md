# Arquitectura - Relaciones y Flujos del Sistema

## Visión General

Bagisto sigue una arquitectura modular basada en Laravel, donde cada funcionalidad está encapsulada en módulos independientes que interactúan entre sí mediante contratos (interfaces) y eventos.

## Capas de la Arquitectura

### 1. Capa de Presentación

```
Frontend (Vue.js)
    ↓
Blade Templates
    ↓
Controllers
```

- **Shop Frontend**: Interfaz pública de la tienda
- **Admin Frontend**: Panel de administración
- Ambos usan Vue.js para interactividad y Axios para comunicación con el backend

### 2. Capa de Aplicación

```
Controllers
    ↓
Repositories
    ↓
Services (si aplica)
    ↓
Models
```

Los controladores reciben requests, validan datos, y delegan la lógica de negocio a repositorios o servicios.

### 3. Capa de Dominio

```
Models (Eloquent)
    ↓
Database
```

Los modelos encapsulan la lógica de negocio y las relaciones entre entidades.

### 4. Capa de Infraestructura

```
Database
Cache (Redis)
Queue System
File Storage
```

## Diagrama de Módulos Principales

```
┌─────────────────────────────────────────────────────────┐
│                        Core                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐            │
│  │ Channels │  │ Locales  │  │ Currencies│            │
│  └──────────┘  └──────────┘  └──────────┘            │
└─────────────────────────────────────────────────────────┘
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
┌───────▼──────┐  ┌───────▼──────┐  ┌───────▼──────┐
│    Admin     │  │     Shop     │  │   Product    │
│              │  │              │  │              │
│ - Dashboard  │  │ - Frontend    │  │ - Products   │
│ - Catalog    │  │ - Cart       │  │ - Categories │
│ - Customers  │  │ - Checkout   │  │ - Attributes │
│ - Sales      │  │ - Customer   │  │              │
└──────┬───────┘  └──────┬───────┘  └──────┬───────┘
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
              ┌───────────▼───────────┐
              │       Sales           │
              │                       │
              │ - Orders              │
              │ - Invoices            │
              │ - Shipments           │
              │ - Refunds             │
              └───────────────────────┘
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
┌──────▼──────┐  ┌───────▼──────┐  ┌───────▼──────┐
│  Customer   │  │   Payment    │  │  Shipping    │
│             │  │              │  │              │
│ - Accounts  │  │ - Methods    │  │ - Methods    │
│ - Addresses │  │ - Processing │  │ - Rates      │
│ - Reviews   │  │              │  │              │
└─────────────┘  └──────────────┘  └──────────────┘
```

## Flujos Principales

### 1. Flujo de Compra (Shop)

```
Cliente visita tienda
    ↓
ProductController::index()
    ↓
ProductRepository::getAllProducts()
    ↓
Product Model → product_flat (tabla optimizada)
    ↓
Vista Blade + Componentes Vue
    ↓
Cliente añade al carrito
    ↓
CartController::store()
    ↓
CartRepository::addProduct()
    ↓
Cart Model
    ↓
Checkout
    ↓
OnepageController::store()
    ↓
SalesRepository::createOrder()
    ↓
Order Model
    ↓
OrderPayment
    ↓
Invoice / Shipment
```

### 2. Flujo de Administración de Productos

```
Admin accede a panel
    ↓
ProductController::index() (Admin)
    ↓
ProductDataGrid (sistema de tablas)
    ↓
ProductRepository::getAll()
    ↓
Vista con formulario Vue
    ↓
ProductController::store() (Admin)
    ↓
ProductRepository::create()
    ↓
Product Model
    ↓
Sincronización product_flat
    ↓
Evento: ProductCreated
    ↓
Listeners (cache, search index, etc.)
```

### 3. Flujo de Gestión de Pedidos

```
Order creado desde Cart
    ↓
Order Model con status "pending"
    ↓
Payment procesado
    ↓
OrderPayment Model
    ↓
Order status → "processing"
    ↓
Invoice generada
    ↓
Invoice Model
    ↓
Shipment creado
    ↓
Shipment Model
    ↓
Order status → "completed"
    ↓
Eventos: OrderCreated, OrderInvoiced, OrderShipped
    ↓
Listeners (emails, notificaciones, etc.)
```

## Interacciones entre Módulos

### Core ↔ Otros Módulos

El módulo **Core** es el núcleo que proporciona:

- **Channels**: Múltiples tiendas
- **Locales**: Multiidioma
- **Currencies**: Multimoneda
- **Addresses**: Sistema de direcciones reutilizable
- **Helpers**: Funciones globales

Todos los módulos dependen de Core para estas funcionalidades básicas.

### Shop ↔ Product

```
Shop necesita Product para:
- Listar productos
- Mostrar detalles
- Búsquedas
- Filtros por categoría

Product proporciona:
- ProductRepository
- Product Model
- ProductFlat (optimizado)
```

### Shop ↔ Sales

```
Shop crea Orders desde Cart:
- Cart se convierte en Order
- Order contiene OrderItems
- Order genera Invoice/Shipment
```

### Admin ↔ Todos los Módulos

```
Admin gestiona:
- Products (Product module)
- Orders (Sales module)
- Customers (Customer module)
- Settings (Core module)
```

## Sistema de Eventos

Bagisto utiliza eventos de Laravel para desacoplar módulos:

### Eventos Principales

#### Productos
- `ProductCreated`
- `ProductUpdated`
- `ProductDeleted`

#### Pedidos
- `OrderCreated`
- `OrderCanceled`
- `OrderInvoiced`
- `OrderShipped`
- `OrderRefunded`

#### Clientes
- `CustomerRegistered`
- `CustomerUpdated`

### Listeners

Cada módulo puede escuchar eventos de otros módulos:

```
OrderCreated Event
    ↓
├─ EmailNotificationListener
├─ InventoryUpdateListener
├─ StatisticsListener
└─ CustomListeners
```

## Contratos (Interfaces)

Bagisto utiliza contratos para definir interfaces entre módulos:

```php
namespace Webkul\Product\Contracts;

interface Product {
    // Métodos que deben implementar los modelos Product
}
```

Esto permite:
- Polimorfismo
- Fácil testing con mocks
- Intercambiabilidad de implementaciones

## Repositorio Pattern

Cada módulo expone repositorios que encapsulan acceso a datos:

```
Controller
    ↓
Repository
    ↓
Model
    ↓
Database
```

Ventajas:
- Separación de lógica de negocio
- Fácil testing
- Cambios en estructura de BD no afectan controladores

## Sistema de Caché

```
Request
    ↓
CacheResponse Middleware (Shop)
    ↓
¿Cache disponible?
    ├─ Sí → Retorna cache
    └─ No → Procesa request → Guarda cache
```

### Capas de Caché

1. **Response Cache**: Caché de respuestas HTTP completas
2. **Query Cache**: Caché de consultas a BD
3. **View Cache**: Caché de vistas compiladas
4. **Config Cache**: Caché de configuración

## Colas y Tareas Asíncronas

```
Action (ej: crear orden)
    ↓
Dispatch Job
    ↓
Queue
    ↓
Worker procesa
    ↓
SendEmail, GenerateReport, etc.
```

Tareas comunes en cola:
- Emails
- Generación de reportes
- Procesamiento de imágenes
- Sincronización con servicios externos

## Multi-tenancy

Bagisto soporta múltiples tiendas mediante **Channels**:

```
Channel 1 (Store A)
    ├─ Products específicos
    ├─ Currency: USD
    └─ Locale: en

Channel 2 (Store B)
    ├─ Products específicos
    ├─ Currency: EUR
    └─ Locale: es
```

Todos comparten:
- Base de datos
- Código
- Admin panel

## Extensibilidad

### Hooks y Eventos

Los desarrolladores pueden:

1. **Escuchar eventos**: Crear listeners para eventos existentes
2. **Emitir eventos personalizados**: Desde módulos custom
3. **Extender repositorios**: Usar decorators o inheritance
4. **Override vistas**: Sistema de temas

### Packages Custom

Se pueden crear packages siguiendo la estructura:

```
packages/Vendor/Module/
├── src/
│   ├── Providers/
│   │   └── ModuleServiceProvider.php
│   └── ...
└── composer.json
```

Y registrar en `config/concord.php`.

## Seguridad

### Autenticación

- **Admin**: Guard `admin`
- **Customer**: Guard `customer`
- **API**: Laravel Sanctum tokens

### Autorización

- **ACL (Access Control List)**: En Admin module
- **Policies**: Laravel policies para recursos
- **Middleware**: Protección de rutas

## Performance

### Optimizaciones

1. **Product Flat**: Tabla denormalizada para listados
2. **Response Cache**: Cache de páginas completas
3. **Lazy Loading**: Carga diferida de componentes
4. **Database Indexing**: Índices en claves foráneas
5. **Asset Optimization**: Minificación y compresión

### Escalabilidad

- Horizontal: Múltiples workers
- Vertical: Redis para cache distribuido
- CDN: Para assets estáticos

