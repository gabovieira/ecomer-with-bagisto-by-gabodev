# Módulo Admin - Panel de Administración

## Función Principal

El módulo **Admin** proporciona el panel de administración completo para gestionar todas las funcionalidades de Bagisto. Es la interfaz backend donde los administradores gestionan productos, pedidos, clientes, configuración y más.

## Responsabilidades

1. **Dashboard**: Vista general del negocio
2. **Gestión de Catálogo**: Productos, categorías, atributos
3. **Gestión de Clientes**: Clientes, grupos, reviews
4. **Gestión de Ventas**: Pedidos, facturas, envíos
5. **Marketing**: Reglas de carrito, catálogo, comunicaciones
6. **Configuración**: Sistema, canales, locales, monedas

## Ubicación

```
packages/Webkul/Admin/
```

## Estructura de Archivos

### Controladores

Los controladores están organizados por funcionalidad:

```
src/Http/Controllers/
├── Controller.php                    # Base
├── DashboardController.php           # Dashboard
├── ConfigurationController.php       # Configuración
├── Catalog/                          # Catálogo
│   ├── AttributeController.php
│   ├── CategoryController.php
│   └── ProductController.php
├── Customers/                        # Clientes
│   ├── CustomerController.php
│   ├── GroupController.php
│   └── ReviewController.php
├── Sales/                             # Ventas
│   ├── OrderController.php
│   ├── InvoiceController.php
│   └── ShipmentController.php
├── Marketing/                        # Marketing
│   ├── CartRuleController.php
│   └── CommunicationController.php
└── Settings/                         # Configuración
    ├── ChannelController.php
    └── CurrencyController.php
```

### Rutas

Las rutas están organizadas en archivos separados:

```
src/Routes/
├── web.php                  # Rutas principales
├── auth-routes.php          # Autenticación
├── catalog-routes.php       # Rutas de catálogo
├── customers-routes.php     # Rutas de clientes
├── sales-routes.php        # Rutas de ventas
├── marketing-routes.php    # Rutas de marketing
├── settings-routes.php     # Rutas de configuración
└── rest-routes.php         # API REST
```

### Vistas

Las vistas están organizadas por sección:

```
src/Resources/views/
├── layouts/                # Layouts base
├── dashboard/              # Dashboard
├── catalog/                # Catálogo
├── customers/              # Clientes
├── sales/                  # Ventas
├── marketing/              # Marketing
└── settings/               # Configuración
```

### Frontend

```
src/Resources/assets/
├── js/
│   ├── app.js              # Punto de entrada Vue
│   └── plugins/            # Plugins Vue
└── css/
    └── app.css            # Estilos
```

## Funcionalidades Principales

### Dashboard

Vista general con:
- Estadísticas de ventas
- Pedidos recientes
- Gráficos y reportes
- Notificaciones

**Controlador**: `DashboardController`

### Gestión de Productos

CRUD completo de productos:
- Crear/editar/eliminar productos
- Gestión de atributos
- Imágenes y galería
- Variantes (configurable products)
- Inventario

**Controlador**: `Catalog\ProductController`

**Rutas**:
- `GET /admin/catalog/products`: Listar
- `GET /admin/catalog/products/create`: Crear
- `POST /admin/catalog/products`: Guardar
- `GET /admin/catalog/products/{id}/edit`: Editar
- `PUT /admin/catalog/products/{id}`: Actualizar

### Gestión de Categorías

- Crear estructura jerárquica
- Gestión de slug y URL
- Imágenes de categoría
- Productos por categoría

**Controlador**: `Catalog\CategoryController`

### Gestión de Pedidos

- Ver lista de pedidos
- Detalles de pedido
- Crear facturas
- Crear envíos
- Procesar reembolsos
- Comentarios de pedido
- Cambiar estado

**Controlador**: `Sales\OrderController`

### Gestión de Clientes

- Lista de clientes
- Perfil de cliente
- Direcciones del cliente
- Grupos de clientes
- Reviews de clientes
- Notas internas

**Controlador**: `Customers\CustomerController`

### Marketing

#### Reglas de Carrito

Descuentos aplicados al carrito basados en condiciones.

**Controlador**: `Marketing\CartRuleController`

#### Reglas de Catálogo

Descuentos aplicados a productos.

**Controlador**: `Marketing\CatalogRuleController`

### Configuración del Sistema

- Canales (tiendas)
- Monedas y tasas de cambio
- Locales (idiomas)
- Métodos de pago
- Métodos de envío
- Impuestos
- Configuración general

## Sistema de Autenticación

### Guard

El Admin usa el guard `admin`:

```php
// middleware
Route::middleware(['auth:admin'])->group(function () {
    // rutas protegidas
});
```

### Modelo

**Modelo**: `Webkul\User\Models\Admin`

**Tabla**: `admins`

### Login

Rutas de autenticación en `auth-routes.php`:
- `GET /admin/login`: Formulario de login
- `POST /admin/login`: Procesar login
- `POST /admin/logout`: Cerrar sesión

## Sistema ACL (Access Control List)

Control de acceso por roles y permisos:

### Configuración

Ubicación: `src/Config/acl.php`

Define todos los permisos disponibles:
```php
return [
    'catalog' => [
        'products' => [
            'index',
            'create',
            'edit',
            'delete',
        ],
    ],
];
```

### Uso

```php
use Webkul\Core\Facades\Acl;

// Verificar permiso
if (Acl::check('catalog.products.index')) {
    // mostrar contenido
}
```

### Roles

Los roles se gestionan desde:
- Admin → Settings → Roles

Cada rol tiene permisos asignados.

## Sistema de Menús

Los menús del admin se gestionan dinámicamente:

### Configuración

Ubicación: `src/Config/menu.php`

### Uso

```php
use Webkul\Core\Facades\Menu;

Menu::addItem('catalog.products', 'Productos', 'admin.catalog.products.index');
```

## DataGrids

Sistema de tablas reutilizables para listar datos:

```
src/DataGrids/
├── Catalog/
│   ├── ProductDataGrid.php
│   └── CategoryDataGrid.php
├── Sales/
│   └── OrderDataGrid.php
└── Customers/
    └── CustomerDataGrid.php
```

Características:
- Paginación
- Ordenamiento
- Filtros
- Acciones masivas
- Exportación

## API REST

El Admin también expone una API REST:

**Rutas**: `src/Routes/rest-routes.php`

Ejemplos:
- `GET /admin/api/products`
- `POST /admin/api/products`
- `PUT /admin/api/products/{id}`
- `DELETE /admin/api/products/{id}`

## Reporting

Sistema de reportes y estadísticas:

```
src/Helpers/Reporting/
├── AbstractReporting.php
├── Cart.php
├── Customer.php
├── Product.php
├── Sale.php
└── Visitor.php
```

Reportes disponibles:
- Ventas
- Productos más vendidos
- Clientes
- Carritos abandonados
- Visitantes

## Interacción con Otros Módulos

### Dependencias

Admin depende de:
- **Core**: Funcionalidades base
- **Product**: Gestión de productos
- **Customer**: Gestión de clientes
- **Sales**: Gestión de ventas
- **Payment/Shipping**: Configuración

### Ejemplos

#### Crear Producto

```php
// Admin usa ProductRepository del módulo Product
use Webkul\Product\Repositories\ProductRepository;

$productRepository->create($data);
```

#### Crear Pedido Manualmente

```php
// Admin usa SalesRepository del módulo Sales
use Webkul\Sales\Repositories\OrderRepository;

$orderRepository->create($orderData);
```

## Eventos

Admin emite y escucha eventos:

### Emitidos

- Configuración actualizada
- Producto creado/actualizado desde admin
- Pedido procesado

### Escuchados

- `OrderCreated`: Actualizar dashboard
- `ProductCreated`: Indexar en búsqueda
- `CustomerRegistered`: Enviar notificación

## Extensibilidad

Para extender el Admin:

1. **Agregar nuevas secciones**: Crear controladores y rutas
2. **Agregar permisos**: Extender `acl.php`
3. **Agregar menús**: Usar sistema de menús
4. **Crear DataGrids**: Para nuevas listas
5. **Agregar reportes**: Extender `AbstractReporting`

## Ejemplos de Uso

### Acceder al Admin

URL por defecto: `http://tu-dominio.com/admin`

### Crear Usuario Admin

Mediante el instalador o:

```bash
php artisan tinker
$admin = new \Webkul\User\Models\Admin();
$admin->name = 'Admin';
$admin->email = 'admin@example.com';
$admin->password = bcrypt('password');
$admin->save();
```

### Personalizar Dashboard

Extender `DashboardController` y agregar widgets personalizados.


