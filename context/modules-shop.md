# Módulo Shop - Frontend de la Tienda

## Función Principal

El módulo **Shop** proporciona el frontend público de la tienda. Es la interfaz que ven los clientes para navegar productos, añadir al carrito, realizar compras y gestionar su cuenta.

## Responsabilidades

1. **Catálogo de Productos**: Listado y detalles de productos
2. **Carrito de Compras**: Gestión del carrito
3. **Checkout**: Proceso de compra
4. **Área de Cliente**: Perfil, pedidos, direcciones
5. **Búsqueda**: Búsqueda de productos
6. **Comparación**: Comparar productos

## Ubicación

```
packages/Webkul/Shop/
```

## Estructura de Archivos

### Controladores

```
src/Http/Controllers/
├── Controller.php              # Base
├── HomeController.php          # Página principal
├── ProductController.php       # Productos
├── CartController.php          # Carrito
├── OnepageController.php       # Checkout
├── SearchController.php        # Búsqueda
├── CompareController.php       # Comparar
├── Customer/                    # Área de cliente
│   ├── Account/
│   ├── CustomerController.php
│   └── SessionController.php
└── API/                        # API REST
    ├── ProductController.php
    ├── CartController.php
    └── CustomerController.php
```

### Rutas

```
src/Routes/
├── web.php                     # Rutas principales
├── store-front-routes.php       # Páginas públicas
├── customer-routes.php          # Área de cliente
├── checkout-routes.php          # Checkout
└── api.php                     # API REST
```

## Funcionalidades Principales

### Catálogo de Productos

#### Listado de Productos

**Ruta**: `GET /` o `GET /category/{slug}`

**Controlador**: `ProductsCategoriesProxyController`

Muestra productos por categoría con:
- Filtros por atributos
- Ordenamiento
- Paginación
- Vista de lista/grilla

#### Detalle de Producto

**Ruta**: `GET /product/{slug}`

**Controlador**: `ProductController@index`

Muestra:
- Información completa del producto
- Galería de imágenes
- Variantes (si es configurable)
- Reviews
- Productos relacionados
- Upsells/Cross-sells

### Carrito de Compras

#### Añadir al Carrito

**Ruta**: `POST /shop/api/cart/add`

**Controlador**: `API\CartController@store`

```javascript
axios.post('/shop/api/cart/add', {
    product_id: 1,
    quantity: 2,
    // opciones para productos configurables
});
```

#### Ver Carrito

**Ruta**: `GET /checkout/cart`

**Controlador**: `CartController@index`

#### Actualizar Carrito

**Ruta**: `PUT /shop/api/cart/update`

**Controlador**: `API\CartController@update`

#### Eliminar Item

**Ruta**: `DELETE /shop/api/cart/remove/{id}`

**Controlador**: `API\CartController@destroy`

### Checkout

Proceso de compra en una sola página (onepage checkout).

#### Rutas

- `GET /checkout`: Vista de checkout
- `POST /checkout`: Procesar pedido
- `POST /shop/api/checkout/save-address`: Guardar dirección
- `POST /shop/api/checkout/save-shipping`: Guardar método de envío
- `POST /shop/api/checkout/save-payment`: Guardar método de pago

**Controlador**: `OnepageController`

#### Pasos del Checkout

1. **Direcciones**: Envío y facturación
2. **Método de Envío**: Seleccionar método
3. **Método de Pago**: Seleccionar forma de pago
4. **Revisar Pedido**: Confirmar antes de enviar
5. **Procesar**: Crear pedido

### Área de Cliente

#### Autenticación

- `GET /customer/login`: Formulario de login
- `POST /customer/login`: Procesar login
- `GET /customer/register`: Formulario de registro
- `POST /customer/register`: Procesar registro
- `POST /customer/logout`: Cerrar sesión

#### Perfil

- `GET /customer/profile`: Ver perfil
- `PUT /customer/profile`: Actualizar perfil
- `PUT /customer/password`: Cambiar contraseña

#### Direcciones

- `GET /customer/addresses`: Listar direcciones
- `POST /customer/addresses`: Crear dirección
- `PUT /customer/addresses/{id}`: Actualizar
- `DELETE /customer/addresses/{id}`: Eliminar

#### Pedidos

- `GET /customer/orders`: Listar pedidos
- `GET /customer/orders/{id}`: Ver pedido
- `POST /customer/orders/{id}/cancel`: Cancelar pedido

### Búsqueda

**Ruta**: `GET /search`

**Controlador**: `SearchController`

Funcionalidades:
- Búsqueda por texto
- Filtros por categoría, precio, etc.
- Búsqueda por imagen (opcional)

### Comparación de Productos

**Ruta**: `GET /compare`

**Controlador**: `CompareController`

Permite comparar múltiples productos lado a lado.

## Frontend Vue.js

### Componentes Principales

Los componentes Vue están en las vistas Blade:

```
src/Resources/views/components/
├── ui/                    # Componentes UI
├── header/                # Header
├── footer/                # Footer
├── product/               # Componentes de producto
│   ├── card.blade.php
│   └── view.blade.php
├── cart/                  # Carrito
│   ├── mini-cart.blade.php
│   └── cart.blade.php
└── checkout/              # Checkout
    ├── address.blade.php
    └── shipping.blade.php
```

### Integración con Backend

Todos los componentes usan Axios para comunicación:

```javascript
// Ejemplo en componente Vue
methods: {
    async addToCart(productId, quantity) {
        try {
            const response = await this.$axios.post('/shop/api/cart/add', {
                product_id: productId,
                quantity: quantity
            });
            // Actualizar carrito en UI
        } catch (error) {
            // Manejar error
        }
    }
}
```

## API REST

El Shop expone una API REST completa:

### Productos

- `GET /shop/api/products`: Listar productos
- `GET /shop/api/products/{id}`: Detalle de producto

### Carrito

- `GET /shop/api/cart`: Obtener carrito
- `POST /shop/api/cart/add`: Añadir producto
- `PUT /shop/api/cart/update`: Actualizar item
- `DELETE /shop/api/cart/remove/{id}`: Eliminar item

### Cliente

- `GET /shop/api/customer`: Perfil del cliente
- `PUT /shop/api/customer`: Actualizar perfil
- `GET /shop/api/customer/orders`: Pedidos del cliente

### Checkout

- `POST /shop/api/checkout/save-address`: Guardar dirección
- `POST /shop/api/checkout/save-shipping`: Guardar envío
- `POST /shop/api/checkout/save-payment`: Guardar pago

## Middleware

### AuthenticateCustomer

Protege rutas que requieren autenticación de cliente.

```php
Route::middleware(['auth:customer'])->group(function () {
    // rutas protegidas
});
```

### Currency

Gestiona la moneda actual basada en:
- Configuración del canal
- Selección del usuario

### Locale

Gestiona el idioma actual.

### Theme

Selecciona el tema activo.

### CacheResponse

Cachea respuestas HTTP completas para mejorar rendimiento.

## Interacción con Otros Módulos

### Dependencias

Shop depende de:
- **Core**: Channels, locales, monedas
- **Product**: Catálogo de productos
- **Customer**: Gestión de clientes
- **Checkout**: Proceso de checkout
- **Cart**: Gestión de carrito
- **Sales**: Creación de pedidos

### Flujo de Compra

```
Shop (Frontend)
    ↓
CartController (añadir al carrito)
    ↓
Cart Repository (Checkout module)
    ↓
OnepageController (checkout)
    ↓
Sales Repository (crear orden)
    ↓
Order Model
```

## Eventos

Shop emite eventos:

- `CartAdded`: Producto añadido al carrito
- `CheckoutStarted`: Inicio de checkout
- `OrderPlaced`: Pedido realizado

## Personalización

### Temas

Shop soporta múltiples temas:

```
public/themes/shop/
├── default/              # Tema por defecto
└── custom/               # Tema personalizado
```

### Override de Vistas

Las vistas se pueden sobrescribir copiando archivos del módulo a:
```
resources/themes/shop/default/views/
```

## Ejemplos de Uso

### Obtener Productos

```php
use Webkul\Product\Repositories\ProductRepository;

$products = app(ProductRepository::class)->getAllProducts($categoryId);
```

### Añadir al Carrito desde Backend

```php
use Webkul\Checkout\Repositories\CartRepository;

$cart = app(CartRepository::class)->addProduct($productId, $data);
```

### Crear Pedido

```php
use Webkul\Sales\Repositories\OrderRepository;

$order = app(OrderRepository::class)->create($orderData);
```


