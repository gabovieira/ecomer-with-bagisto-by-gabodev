# Módulo Product - Gestión de Productos

## Función Principal

El módulo **Product** gestiona todo lo relacionado con productos: creación, edición, atributos, variantes, categorías y optimización para búsquedas.

## Responsabilidades

1. **CRUD de Productos**: Crear, leer, actualizar, eliminar productos
2. **Gestión de Atributos**: Atributos y familias de atributos
3. **Product Flat**: Tabla optimizada para búsquedas
4. **Tipos de Productos**: Simple, configurable, virtual, descargable, etc.
5. **Relaciones**: Productos relacionados, upsells, cross-sells

## Ubicación

```
packages/Webkul/Product/
```

## Estructura de Archivos

### Modelos

```
src/Models/
├── Product.php                 # Modelo principal
├── ProductFlat.php             # Tabla optimizada
├── ProductImage.php            # Imágenes
├── ProductAttributeValue.php   # Valores de atributos
└── ProductCategory.php         # Relación con categorías
```

### Repositorios

```
src/Repositories/
├── ProductRepository.php
└── ProductFlatRepository.php
```

### Controladores

```
src/Http/Controllers/
├── ProductController.php       # Admin
└── API/
    └── ProductController.php    # API
```

## Tipos de Productos

Bagisto soporta múltiples tipos de productos:

### Simple

Producto básico con un solo SKU.

### Configurable

Producto con variantes (ej: camiseta con diferentes tallas y colores).

**Estructura**:
- Producto padre (configurable)
- Productos hijo (variantes)

### Virtual

Producto sin envío físico (ej: servicio).

### Downloadable

Producto descargable con archivos asociados.

### Bundle

Producto que incluye otros productos.

### Grouped

Grupo de productos relacionados.

## Sistema de Atributos

### Atributos

Los atributos definen propiedades de productos:
- Nombre, descripción, precio
- Color, tamaño, material
- Peso, dimensiones

**Ubicación**: Módulo `Attribute`

**Relación**: `products` → `attribute_family_id` → `attribute_families`

### Familias de Atributos

Grupos de atributos que se pueden aplicar a productos.

Ejemplo: "Camiseta" familia incluye: color, talla, material.

### Valores de Atributos

Se almacenan en `product_attribute_values`:

- Un producto tiene múltiples valores de atributos
- Cada valor corresponde a un atributo específico
- Los valores pueden ser por canal e idioma

## Product Flat

### Propósito

`product_flat` es una tabla denormalizada que optimiza las consultas de listado y búsqueda.

### Ventajas

- Menos joins en consultas
- Búsquedas más rápidas
- Filtrado eficiente por canal/idioma
- Cache más efectivo

### Estructura

```
product_flat
├── product_id
├── channel          # Canal específico
├── locale           # Idioma específico
├── name             # Nombre denormalizado
├── price            # Precio denormalizado
├── status           # Estado
└── ... (más campos)
```

### Sincronización

Se actualiza automáticamente cuando:
- Se crea/actualiza un producto
- Se cambia un valor de atributo
- Se cambia el canal o idioma

**Comando**: Se puede sincronizar manualmente:

```bash
php artisan product:index
```

## Relaciones

### Con Categorías

Many-to-many mediante `product_categories`:

```php
$product->categories;  // Categorías del producto
$category->products;   // Productos de la categoría
```

### Productos Relacionados

Tabla `product_relations`:
- `parent_id`: Producto principal
- `child_id`: Producto relacionado

Usado para:
- Productos relacionados
- Upsells
- Cross-sells

### Imágenes

`product_images`:
- Una imagen tiene `product_id`
- Soporta múltiples imágenes por producto
- Campo `position` para orden

### Videos

Similar a imágenes, tabla `product_videos`.

### Reviews

Relación one-to-many con `product_reviews`:
```php
$product->reviews;          // Todos los reviews
$product->approvedReviews; // Solo aprobados
```

## Inventario

### Relación con Inventory Module

El módulo Product se integra con Inventory para:
- Control de stock
- Fuentes de inventario
- Reservas

**Tabla**: `product_inventories`

## Precios

### Estructura

- `price`: Precio base
- `special_price`: Precio especial
- `special_price_from`: Fecha inicio descuento
- `special_price_to`: Fecha fin descuento

### Índices de Precio

Tabla `product_price_indices`:
- Almacena precios calculados por canal
- Incluye descuentos aplicados
- Optimiza cálculos de precio

### Reglas de Catálogo

Los precios pueden ser afectados por:
- Catalog Rules (descuentos automáticos)
- Customer Groups (precios por grupo)

## Búsqueda

### Elasticsearch

Si está habilitado, los productos se indexan en Elasticsearch para búsquedas avanzadas.

**Configuración**: `config/elasticsearch.php`

**Comando**: Indexar productos:

```bash
php artisan scout:import "Webkul\Product\Models\Product"
```

### Búsqueda Simple

Sin Elasticsearch, usa búsqueda SQL en `product_flat`.

## Repositorio

### ProductRepository

Proporciona métodos para:

```php
// Obtener todos los productos
$repository->getAllProducts($categoryId = null);

// Obtener productos del canal actual
$repository->getChannelProducts($channelId);

// Buscar productos
$repository->searchProducts($term);

// Obtener producto por slug
$repository->findBySlug($slug);

// Crear producto
$repository->create($data);

// Actualizar producto
$repository->update($product, $data);
```

## Validaciones

### SKU Único

Cada producto debe tener un SKU único.

### Atributos Requeridos

Los atributos marcados como requeridos deben tener valores.

### Familias de Atributos

Un producto debe pertenecer a una familia de atributos.

## Eventos

El módulo Product emite eventos:

- `ProductCreated`: Cuando se crea un producto
- `ProductUpdated`: Cuando se actualiza
- `ProductDeleted`: Cuando se elimina

### Listeners Comunes

- Sincronizar `product_flat`
- Indexar en Elasticsearch
- Invalidar caché
- Actualizar índices de precio

## Interacción con Otros Módulos

### Dependencias

Product depende de:
- **Core**: Channels, locales, monedas
- **Attribute**: Atributos y familias
- **Category**: Categorías

### Usado Por

- **Shop**: Muestra productos
- **Admin**: Gestiona productos
- **Cart**: Añade productos al carrito
- **Sales**: Crea items de pedido

## Ejemplos de Uso

### Crear Producto Simple

```php
use Webkul\Product\Repositories\ProductRepository;

$repository = app(ProductRepository::class);

$data = [
    'type' => 'simple',
    'sku' => 'SKU-001',
    'attribute_family_id' => 1,
    // valores de atributos
    'name' => 'Mi Producto',
    'description' => 'Descripción',
    'price' => 99.99,
];

$product = $repository->create($data);
```

### Crear Producto Configurable

```php
// Crear producto padre
$parent = $repository->create([
    'type' => 'configurable',
    'sku' => 'PARENT-001',
    // ...
]);

// Crear variantes
foreach ($variants as $variantData) {
    $variantData['parent_id'] = $parent->id;
    $variant = $repository->create($variantData);
}
```

### Obtener Productos por Categoría

```php
$category = Category::find($categoryId);
$products = $category->products()
    ->where('status', 1)
    ->get();
```

### Búsqueda

```php
$products = $repository->searchProducts('camiseta', [
    'category_id' => 1,
    'min_price' => 10,
    'max_price' => 100,
]);
```

## Optimización

### Product Flat

Mantener `product_flat` sincronizado:
- Se actualiza automáticamente
- Se puede forzar: `php artisan product:index`

### Cache

Los productos se cachean en:
- Response cache (páginas completas)
- Query cache (consultas frecuentes)

### Indexación

Para búsquedas rápidas:
- Usar `product_flat` en lugar de `products`
- Índices en columnas frecuentes
- Elasticsearch para búsquedas complejas


