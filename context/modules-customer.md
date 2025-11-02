# Módulo Customer - Gestión de Clientes

## Función Principal

El módulo **Customer** gestiona toda la información y funcionalidades relacionadas con los clientes: registro, autenticación, perfiles, direcciones, grupos y reviews.

## Responsabilidades

1. **Registro y Autenticación**: Registro, login, logout de clientes
2. **Perfiles**: Información personal del cliente
3. **Direcciones**: Direcciones de envío y facturación
4. **Grupos**: Clasificación de clientes
5. **Reviews**: Opiniones de clientes sobre productos
6. **Wishlist**: Lista de deseos

## Ubicación

```
packages/Webkul/Customer/
```

## Estructura de Archivos

### Modelos

```
src/Models/
├── Customer.php              # Modelo principal
├── CustomerGroup.php          # Grupos de clientes
├── CustomerAddress.php        # Direcciones
├── Wishlist.php               # Lista de deseos
└── ProductReview.php          # Reviews de productos
```

### Repositorios

```
src/Repositories/
├── CustomerRepository.php
├── CustomerGroupRepository.php
├── WishlistRepository.php
└── ProductReviewRepository.php
```

### Controladores

```
src/Http/Controllers/
├── RegistrationController.php  # Registro
├── SessionController.php       # Login/Logout
├── CustomerController.php       # Perfil
├── AddressController.php       # Direcciones
├── WishlistController.php      # Wishlist
└── ReviewController.php         # Reviews
```

## Modelo Customer

### Tabla

**Tabla**: `customers`

### Columnas Principales

- `id`: ID único
- `first_name`, `last_name`: Nombre completo
- `email`: Email (único, nullable)
- `phone`: Teléfono (único, nullable)
- `password`: Contraseña hasheada
- `api_token`: Token para API
- `customer_group_id`: Grupo de clientes
- `status`: Estado (activo/inactivo)
- `is_verified`: Verificación de email
- `subscribed_to_news_letter`: Suscripción
- `date_of_birth`: Fecha de nacimiento
- `gender`: Género

### Relaciones

```php
$customer->addresses;        // Direcciones
$customer->orders;           // Pedidos
$customer->cart;             // Carrito actual
$customer->wishlist;         // Lista de deseos
$customer->reviews;          // Reviews
$customer->group;            // Grupo de clientes
```

## Autenticación

### Guard

El módulo Customer usa el guard `customer`:

```php
// En config/auth.php
'guards' => [
    'customer' => [
        'driver' => 'session',
        'provider' => 'customers',
    ],
],
```

### Login

**Ruta**: `POST /customer/login`

**Controlador**: `SessionController@create`

```php
// Middleware para proteger rutas
Route::middleware(['auth:customer'])->group(function () {
    // rutas protegidas
});
```

### Registro

**Ruta**: `POST /customer/register`

**Controlador**: `RegistrationController@store`

Validaciones:
- Email único
- Contraseña con requisitos
- Términos y condiciones

### Verificación de Email

Opcional: Los clientes pueden verificar su email mediante token.

## Perfil de Cliente

### Ver Perfil

**Ruta**: `GET /customer/profile`

**Controlador**: `CustomerController@index`

### Actualizar Perfil

**Ruta**: `PUT /customer/profile`

**Controlador**: `CustomerController@update`

Datos actualizables:
- Nombre
- Email
- Teléfono
- Fecha de nacimiento
- Género

### Cambiar Contraseña

**Ruta**: `PUT /customer/password`

**Controlador**: `CustomerController@updatePassword`

## Direcciones

### Modelo CustomerAddress

**Tabla**: `customer_addresses`

**Relación**: Pertenece a `Customer`

### Columnas

- `customer_id`: Cliente dueño
- `address_type`: Tipo (shipping, billing)
- `first_name`, `last_name`
- `address1`, `address2`
- `city`, `state`, `country`
- `postcode`, `phone`

### CRUD

- `GET /customer/addresses`: Listar
- `POST /customer/addresses`: Crear
- `PUT /customer/addresses/{id}`: Actualizar
- `DELETE /customer/addresses/{id}`: Eliminar

**Controlador**: `AddressController`

### Dirección por Defecto

Los clientes pueden marcar direcciones como predeterminadas para envío o facturación.

## Grupos de Clientes

### Modelo CustomerGroup

**Tabla**: `customer_groups`

Permite clasificar clientes para:
- Precios especiales
- Descuentos
- Reglas de carrito/catálogo

### Ejemplos

- General (por defecto)
- Mayoristas
- VIP
- Corporativo

### Uso en Precios

Los precios pueden variar por grupo de clientes mediante reglas de catálogo.

## Lista de Deseos (Wishlist)

### Modelo Wishlist

**Tabla**: `wishlist`

**Columnas**:
- `customer_id`: Cliente
- `product_id`: Producto
- `additional`: Opciones adicionales (JSON)

### Funcionalidades

- Añadir producto: `POST /customer/wishlist`
- Eliminar producto: `DELETE /customer/wishlist/{id}`
- Ver lista: `GET /customer/wishlist`

**Controlador**: `WishlistController`

## Reviews de Productos

### Modelo ProductReview

**Tabla**: `product_reviews`

**Columnas**:
- `product_id`: Producto
- `customer_id`: Cliente
- `title`: Título
- `rating`: Calificación (1-5)
- `comment`: Comentario
- `status`: Estado (pending, approved, disapproved)

### Funcionalidades

- Crear review: `POST /customer/reviews`
- Ver reviews: `GET /customer/reviews`

**Controlador**: `ReviewController`

### Aprobación

Los reviews requieren aprobación del admin antes de mostrarse públicamente.

## API del Cliente

### Endpoints

- `GET /shop/api/customer`: Perfil
- `PUT /shop/api/customer`: Actualizar perfil
- `GET /shop/api/customer/orders`: Pedidos
- `GET /shop/api/customer/addresses`: Direcciones
- `POST /shop/api/customer/addresses`: Crear dirección
- `GET /shop/api/customer/wishlist`: Wishlist

## Eventos

El módulo Customer emite eventos:

- `CustomerRegistered`: Al registrarse
- `CustomerUpdated`: Al actualizar perfil
- `CustomerLoggedIn`: Al hacer login
- `ProductReviewed`: Al crear review

### Listeners Comunes

- **EmailNotificationListener**: Envía emails de bienvenida, verificación, etc.
- **StatisticsListener**: Actualiza estadísticas

## Interacción con Otros Módulos

### Dependencias

Customer depende de:
- **Core**: Addresses base, channels

### Usado Por

- **Shop**: Área de cliente, registro, login
- **Admin**: Gestión de clientes
- **Sales**: Asociar pedidos a clientes
- **Cart**: Carrito del cliente
- **Checkout**: Datos del cliente en checkout

## Ejemplos de Uso

### Registrar Cliente

```php
use Webkul\Customer\Repositories\CustomerRepository;

$data = [
    'first_name' => 'Juan',
    'last_name' => 'Pérez',
    'email' => 'juan@example.com',
    'password' => 'password123',
];

$customer = app(CustomerRepository::class)->create($data);
```

### Obtener Cliente por Email

```php
use Webkul\Customer\Models\Customer;

$customer = Customer::where('email', 'juan@example.com')->first();
```

### Añadir Dirección

```php
$addressData = [
    'first_name' => 'Juan',
    'last_name' => 'Pérez',
    'address1' => 'Calle Principal 123',
    'city' => 'Madrid',
    'postcode' => '28001',
    'country' => 'ES',
    'address_type' => 'shipping',
];

$customer->addresses()->create($addressData);
```

### Obtener Pedidos del Cliente

```php
$orders = $customer->orders()
    ->orderBy('created_at', 'desc')
    ->get();
```

### Añadir a Wishlist

```php
use Webkul\Customer\Repositories\WishlistRepository;

app(WishlistRepository::class)->add([
    'customer_id' => $customerId,
    'product_id' => $productId,
]);
```

### Crear Review

```php
use Webkul\Customer\Repositories\ProductReviewRepository;

app(ProductReviewRepository::class)->create([
    'product_id' => $productId,
    'customer_id' => $customerId,
    'title' => 'Excelente producto',
    'rating' => 5,
    'comment' => 'Muy satisfecho',
]);
```

## Seguridad

### Contraseñas

- Hasheadas con bcrypt
- Requisitos de seguridad configurables
- Recuperación mediante token

### Tokens API

- Tokens únicos para cada cliente
- Renovables
- Usados para autenticación API

## Estadísticas

El módulo Customer proporciona datos para:
- Clientes registrados
- Clientes activos vs inactivos
- Conversión de invitados a registrados
- Reviews por cliente


