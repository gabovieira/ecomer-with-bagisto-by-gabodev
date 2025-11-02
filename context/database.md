# Base de Datos - Esquema y Relaciones

## Sistema de Base de Datos

Bagisto utiliza MySQL/MariaDB como base de datos principal. Las migraciones están distribuidas en cada módulo siguiendo el patrón de Laravel.

## Configuración

La configuración de base de datos se encuentra en `config/database.php`:

- **Conexión por defecto**: MySQL
- **Charset**: utf8mb4
- **Collation**: utf8mb4_unicode_ci
- **Strict mode**: Deshabilitado por compatibilidad

### Variables de Entorno

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bagisto
DB_USERNAME=root
DB_PASSWORD=
```

## Estructura de Migraciones

Las migraciones están organizadas por módulo:

```
packages/Webkul/{Module}/src/Database/Migrations/
```

### Convención de Nombres

Las migraciones siguen el formato:
```
YYYY_MM_DD_HHMMSS_create_{table}_table.php
```

## Tablas Principales

### Core (Sistema Base)

#### channels
Almacena los canales (stores) del sistema multi-tienda.

#### locales
Idiomas y locales disponibles en el sistema.

#### currencies
Monedas soportadas con tasas de cambio.

#### exchange_rates
Tasas de cambio entre monedas.

#### address
Direcciones reutilizables (clientes, pedidos, etc.).

### Productos

#### products
Tabla principal de productos.

**Columnas principales:**
- `id`: ID único
- `sku`: SKU único del producto
- `type`: Tipo de producto (simple, configurable, virtual, etc.)
- `parent_id`: ID del producto padre (para variantes)
- `attribute_family_id`: Familia de atributos

**Relaciones:**
- `belongsTo(Product)` → `parent_id` (productos padre/hijo)
- `belongsTo(AttributeFamily)`
- `belongsToMany(Category)` → `product_categories`
- `hasMany(ProductImage)`
- `hasMany(ProductAttributeValue)`

#### product_flat
Tabla optimizada para búsquedas y listados. Contiene datos "aplanados" del producto por canal e idioma.

**Columnas principales:**
- `product_id`: Referencia al producto
- `channel`: Canal
- `locale`: Idioma
- `name`, `price`, `status`, etc. (datos denormalizados)

**Índice único:** `(product_id, channel, locale)`

#### product_categories
Tabla pivot entre productos y categorías (many-to-many).

#### product_inventories
Gestión de inventario por fuente de almacenamiento.

#### product_attribute_values
Valores de atributos del producto (nombre, descripción, precio, etc.).

### Categorías

#### categories
Categorías de productos con estructura jerárquica (nested sets).

**Columnas principales:**
- `id`, `parent_id`, `position`
- `name`, `slug`, `description`
- `status`, `display_mode`

**Relaciones:**
- `belongsToMany(Product)` → `product_categories`
- Auto-referencia para estructura jerárquica

### Clientes

#### customers
Tabla principal de clientes.

**Columnas principales:**
- `id`, `first_name`, `last_name`, `email`, `phone`
- `password`, `api_token`
- `customer_group_id`: Grupo de clientes
- `status`, `is_verified`, `subscribed_to_news_letter`

**Relaciones:**
- `hasMany(Address)`
- `hasMany(Order)`
- `hasMany(Cart)`
- `hasMany(ProductReview)`
- `belongsTo(CustomerGroup)`

#### customer_groups
Grupos de clientes (por defecto, mayoristas, etc.).

#### customer_addresses
Direcciones de clientes.

### Ventas y Pedidos

#### orders
Tabla principal de pedidos.

**Columnas principales:**
- `id`, `increment_id`: ID único y número de pedido
- `status`: Estado del pedido (pending, processing, completed, etc.)
- `customer_id`, `customer_email`: Cliente
- `channel_id`: Canal de venta
- `grand_total`, `base_grand_total`: Total del pedido
- `sub_total`, `shipping_amount`, `tax_amount`: Desglose
- `coupon_code`: Cupón aplicado
- `applied_cart_rule_ids`: Reglas de carrito aplicadas

**Relaciones:**
- `hasMany(OrderItem)`: Items del pedido
- `hasMany(Invoice)`: Facturas
- `hasMany(Shipment)`: Envíos
- `hasMany(Refund)`: Reembolsos
- `hasMany(OrderTransaction)`: Transacciones
- `hasMany(OrderAddress)`: Direcciones (envío/facturación)
- `hasOne(OrderPayment)`: Pago
- `belongsTo(Customer)`
- `belongsTo(Channel)`

#### order_items
Items individuales de un pedido.

**Columnas principales:**
- `id`, `order_id`, `product_id`
- `sku`, `name`, `type`
- `qty_ordered`, `qty_shipped`, `qty_invoiced`, `qty_refunded`
- `price`, `base_price`, `total`, `base_total`

#### order_addresses
Direcciones asociadas a pedidos (envío y facturación).

#### order_invoices
Facturas generadas para pedidos.

#### order_shipments
Envíos realizados para pedidos.

#### order_refunds
Reembolsos procesados.

#### order_transactions
Transacciones de pago asociadas a pedidos.

#### order_payments
Información de pago del pedido.

### Carrito de Compras

#### cart
Carritos de compra (antes de convertirse en pedidos).

**Columnas principales:**
- `id`, `customer_id`, `channel_id`
- `is_guest`: Si es carrito de invitado
- `coupon_code`: Cupón aplicado
- `sub_total`, `grand_total`

**Relaciones:**
- `hasMany(CartItem)`
- `hasMany(CartAddress)`
- `belongsTo(Customer)` (nullable para invitados)

#### cart_items
Items en el carrito.

#### cart_addresses
Direcciones del carrito (envío/facturación).

### Atributos

#### attributes
Atributos configurables de productos (color, tamaño, etc.).

#### attribute_families
Familias de atributos (grupos de atributos).

#### attribute_options
Opciones de atributos (valores posibles).

### Inventario

#### inventory_sources
Fuentes de inventario (almacenes).

#### inventories
Stock de productos por fuente.

### Reglas y Promociones

#### cart_rules
Reglas de carrito (descuentos).

#### catalog_rules
Reglas de catálogo (descuentos por producto).

### CMS

#### cms_pages
Páginas de contenido estático.

### Pagos y Envíos

#### payment_methods
Métodos de pago disponibles.

#### shipping_methods
Métodos de envío disponibles.

#### tax_categories
Categorías de impuestos.

#### tax_rates
Tarifas de impuestos.

## Relaciones Principales

### Flujo de Compra

```
Customer → Cart → CartItems → Order → OrderItems
                        ↓
                   OrderAddress
                        ↓
                  OrderPayment
                        ↓
                   Invoice/Shipment
```

### Productos y Catálogo

```
Product ←→ Categories (many-to-many)
  ↓
ProductAttributeValues
  ↓
ProductFlat (denormalizado por canal/idioma)
  ↓
CartItems / OrderItems
```

### Jerarquías

- **Categories**: Estructura jerárquica con nested sets
- **Products**: Productos padre (configurable) → Productos hijo (variantes)

## Sistema de Product Flat

`product_flat` es una tabla optimizada que contiene datos denormalizados de productos por canal e idioma. Esto permite:

- Búsquedas más rápidas
- Menos joins en consultas de listados
- Fácil filtrado por canal/idioma

Se mantiene sincronizada automáticamente cuando se actualizan productos.

## Índices y Optimización

### Índices Principales

- `products.sku`: Único
- `product_flat(product_id, channel, locale)`: Único
- `orders.customer_id`: Índice para búsquedas
- `order_items.order_id`: Para joins eficientes

### Consideraciones de Performance

- Uso de `product_flat` para listados públicos
- Cache de consultas frecuentes
- Índices en claves foráneas
- Paginación en listados grandes

## Seeders

Los seeders están en cada módulo:

```
packages/Webkul/{Module}/src/Database/Seeders/
```

Y también en la raíz:
```
database/seeders/
```

Los seeders principales crean:
- Datos de ejemplo
- Configuraciones por defecto
- Canales y locales iniciales

## Factories

Para testing, existen factories en:

```
packages/Webkul/{Module}/src/Database/Factories/
```

Permiten crear datos de prueba fácilmente.

## Migraciones Personalizadas

Las migraciones custom del proyecto van en:

```
database/migrations/
```

Mientras que las de módulos están en cada módulo respectivo.


