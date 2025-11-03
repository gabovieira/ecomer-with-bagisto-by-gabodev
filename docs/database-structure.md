# 📊 Estructura de Base de Datos - Bagisto

Documentación completa de la estructura de la base de datos del proyecto **Ecomer with Bagisto by Gabodev**.

---

## 🗄️ Configuración General

- **Motor**: MySQL / MariaDB
- **Charset**: utf8mb4
- **Collation**: utf8mb4_unicode_ci
- **Strict Mode**: Deshabilitado (por compatibilidad)

---

## 📋 Tablas por Módulo

### 🔵 Core (Sistema Base)

#### `channels`
Canales/tiendas del sistema multi-tienda.

```sql
- id (PK)
- code (único)
- name
- description
- theme
- hostname
- logo
- favicon
- default_locale_id
- base_currency_id
- root_category_id
- homepage_seo_meta_title
- homepage_seo_meta_description
- is_maintenance_on
- maintenance_mode_text
- allowed_ips
- sort_order
- is_default
- status
- created_at, updated_at
```

#### `locales`
Idiomas y locales disponibles.

```sql
- id (PK)
- code (único)
- name
- created_at, updated_at
```

#### `currencies`
Monedas soportadas.

```sql
- id (PK)
- code (único)
- name
- symbol
- status
- created_at, updated_at
```

#### `exchange_rates`
Tasas de cambio entre monedas.

```sql
- id (PK)
- target_currency
- rate
- created_at, updated_at
```

#### `addresses`
Direcciones reutilizables.

```sql
- id (PK)
- address_type (shipping/billing)
- customer_id (FK → customers)
- cart_id (FK → cart)
- order_id (FK → orders)
- first_name
- last_name
- email
- phone
- address1
- address2
- city
- state
- country
- postcode
- default_address
- created_at, updated_at
```

#### `countries`
Países disponibles.

#### `states`
Estados/provincias.

---

### 🛒 Productos

#### `products`
Tabla principal de productos.

```sql
- id (PK)
- sku (único)
- type (simple, configurable, virtual, downloadable, grouped, bundle, booking)
- parent_id (FK → products, nullable)
- attribute_family_id (FK → attribute_families)
- additional (JSON)
- created_at, updated_at

Índices:
- UNIQUE(sku)
- INDEX(parent_id)
- INDEX(attribute_family_id)
```

#### `product_flat`
Tabla optimizada para búsquedas (datos denormalizados).

```sql
- id (PK)
- product_id (FK → products)
- channel (canal)
- locale (idioma)
- name
- url_key
- type
- sku
- short_description
- description
- price
- cost
- special_price
- special_price_from
- special_price_to
- status
- weight
- ... (muchas más columnas denormalizadas)

Índices:
- UNIQUE(product_id, channel, locale)
- INDEX(channel)
- INDEX(locale)
- INDEX(status)
```

#### `product_categories`
Tabla pivot productos ↔ categorías (many-to-many).

```sql
- product_id (FK → products)
- category_id (FK → categories)
```

#### `product_attribute_values`
Valores de atributos de productos.

```sql
- id (PK)
- product_id (FK → products)
- attribute_id (FK → attributes)
- channel
- locale
- text_value
- boolean_value
- integer_value
- float_value
- date_value
- json_value
- unique_id (único)
- created_at, updated_at
```

#### `product_images`
Imágenes de productos.

```sql
- id (PK)
- product_id (FK → products)
- path
- type (image/video)
- position
- created_at, updated_at
```

#### `product_inventories`
Inventario por fuente de almacenamiento.

```sql
- id (PK)
- product_id (FK → products)
- vendor_id (nullable)
- inventory_source_id (FK → inventory_sources)
- qty
- created_at, updated_at
```

#### `product_price_indices`
Índice de precios optimizado.

```sql
- id (PK)
- product_id (FK → products)
- channel_id (FK → channels)
- customer_group_id (FK → customer_groups)
- min_price
- regular_min_price
- max_price
- regular_max_price
- created_at, updated_at
```

#### `product_inventory_indices`
Índice de inventario optimizado.

```sql
- id (PK)
- product_id (FK → products)
- channel_id (FK → channels)
- qty
- product_number
- created_at, updated_at
```

#### `product_reviews`
Reseñas de productos.

```sql
- id (PK)
- product_id (FK → products)
- customer_id (FK → customers)
- title
- rating
- comment
- status (approved/pending/disapproved)
- created_at, updated_at
```

#### `product_downloadable_links`
Enlaces descargables (productos digitales).

```sql
- id (PK)
- product_id (FK → products)
- url
- file
- file_name
- type
- price
- sample_url
- sample_file
- download_bought
- sort_order
- created_at, updated_at
```

#### `product_bundle_options`
Opciones de productos bundle.

```sql
- id (PK)
- product_id (FK → products)
- type
- is_required
- sort_order
- created_at, updated_at
```

#### `product_customizable_options`
Opciones personalizables de productos.

```sql
- id (PK)
- product_id (FK → products)
- type (text, textarea, select, radio, checkbox, etc.)
- is_required
- sort_order
- created_at, updated_at
```

---

### 📁 Categorías

#### `categories`
Categorías de productos (estructura jerárquica con nested sets).

```sql
- id (PK)
- position
- status
- parent_id (FK → categories, nullable)
- display_mode
- description
- slug
- meta_title
- meta_description
- meta_keywords
- created_at, updated_at

Índices:
- INDEX(parent_id)
- INDEX(slug)
```

#### `category_translations`
Traducciones de categorías.

```sql
- id (PK)
- category_id (FK → categories)
- locale
- name
- description
- slug
- meta_title
- meta_description
- meta_keywords
- created_at, updated_at
```

#### `category_filterable_attributes`
Atributos filtrables por categoría.

```sql
- category_id (FK → categories)
- attribute_id (FK → attributes)
```

---

### 👥 Clientes

#### `customers`
Tabla principal de clientes.

```sql
- id (PK)
- first_name
- last_name
- email (único)
- password
- gender
- date_of_birth
- phone
- customer_group_id (FK → customer_groups)
- subscribed_to_news_letter
- is_verified
- is_suspended
- token
- api_token
- status
- notes
- created_at, updated_at

Índices:
- UNIQUE(email)
- INDEX(customer_group_id)
- INDEX(status)
```

#### `customer_groups`
Grupos de clientes (General, Mayoristas, etc.).

```sql
- id (PK)
- name
- code (único)
- is_user_defined
- created_at, updated_at
```

#### `customer_addresses`
Direcciones de clientes.

```sql
- id (PK)
- customer_id (FK → customers)
- address_type
- first_name
- last_name
- email
- phone
- address1
- address2
- city
- state
- country
- postcode
- default_address
- created_at, updated_at
```

#### `customer_social_accounts`
Cuentas sociales vinculadas (login social).

```sql
- id (PK)
- customer_id (FK → customers)
- provider
- provider_id
- created_at, updated_at
```

---

### 🛍️ Carrito de Compras

#### `cart`
Carritos de compra.

```sql
- id (PK)
- customer_id (FK → customers, nullable)
- customer_email
- customer_first_name
- customer_last_name
- is_guest
- channel_id (FK → channels)
- coupon_code
- sub_total
- tax_total
- discount_amount
- grand_total
- applied_cart_rule_ids (JSON)
- shipping_method
- shipping_amount
- created_at, updated_at

Índices:
- INDEX(customer_id)
- INDEX(channel_id)
```

#### `cart_items`
Items en el carrito.

```sql
- id (PK)
- cart_id (FK → cart)
- product_id (FK → products)
- sku
- type
- name
- coupon_code
- quantity
- price
- base_price
- total
- base_total
- tax_percent
- tax_amount
- discount_percent
- discount_amount
- created_at, updated_at
```

#### `cart_addresses`
Direcciones del carrito.

```sql
- id (PK)
- cart_id (FK → cart)
- address_type
- first_name
- last_name
- email
- phone
- address1
- address2
- city
- state
- country
- postcode
- created_at, updated_at
```

---

### 📦 Pedidos y Ventas

#### `orders`
Tabla principal de pedidos.

```sql
- id (PK)
- increment_id (único, número de pedido)
- status
- channel_id (FK → channels)
- channel_name
- channel_type
- customer_id (FK → customers, nullable)
- customer_type
- customer_email
- customer_first_name
- customer_last_name
- customer_phone
- customer_group_id (FK → customer_groups)
- shipping_method
- shipping_title
- shipping_amount
- shipping_amount_invoiced
- shipping_amount_refunded
- base_shipping_amount
- base_shipping_amount_invoiced
- base_shipping_amount_refunded
- coupon_code
- applied_cart_rule_ids (JSON)
- discount_amount
- base_discount_amount
- discount_amount_invoiced
- discount_amount_refunded
- tax_amount
- base_tax_amount
- tax_amount_invoiced
- tax_amount_refunded
- sub_total
- base_sub_total
- sub_total_invoiced
- sub_total_refunded
- grand_total
- base_grand_total
- grand_total_invoiced
- grand_total_refunded
- payment_method
- payment_method_title
- created_at, updated_at

Índices:
- UNIQUE(increment_id)
- INDEX(customer_id)
- INDEX(status)
- INDEX(channel_id)
```

#### `order_items`
Items individuales de pedidos.

```sql
- id (PK)
- order_id (FK → orders)
- product_id (FK → products)
- product_type
- sku
- name
- description
- qty_ordered
- qty_shipped
- qty_invoiced
- qty_canceled
- qty_refunded
- price
- base_price
- total
- base_total
- tax_percent
- tax_amount
- base_tax_amount
- discount_percent
- discount_amount
- base_discount_amount
- created_at, updated_at

Índices:
- INDEX(order_id)
- INDEX(product_id)
```

#### `order_addresses`
Direcciones de pedidos (envío/facturación).

```sql
- id (PK)
- order_id (FK → orders)
- address_type
- customer_id (FK → customers, nullable)
- first_name
- last_name
- email
- phone
- address1
- address2
- city
- state
- country
- postcode
- created_at, updated_at
```

#### `order_invoices`
Facturas de pedidos.

```sql
- id (PK)
- increment_id (único)
- order_id (FK → orders)
- state
- email_sent
- total_qty
- base_currency_code
- channel_currency_code
- order_currency_code
- subtotal
- base_subtotal
- tax_amount
- base_tax_amount
- discount_amount
- base_discount_amount
- grand_total
- base_grand_total
- created_at, updated_at
```

#### `order_shipments`
Envíos de pedidos.

```sql
- id (PK)
- order_id (FK → orders)
- total_qty
- carrier_title
- tracking_number
- created_at, updated_at
```

#### `order_refunds`
Reembolsos.

```sql
- id (PK)
- increment_id (único)
- order_id (FK → orders)
- state
- email_sent
- total_qty
- adjustment_refund
- base_adjustment_refund
- adjustment_fee
- base_adjustment_fee
- subtotal
- base_subtotal
- tax_amount
- base_tax_amount
- discount_amount
- base_discount_amount
- grand_total
- base_grand_total
- created_at, updated_at
```

#### `order_payments`
Información de pago.

```sql
- id (PK)
- order_id (FK → orders)
- method
- method_title
- created_at, updated_at
```

#### `order_transactions`
Transacciones de pago.

```sql
- id (PK)
- transaction_id
- payment_method
- order_id (FK → orders)
- invoice_id (FK → order_invoices, nullable)
- status
- type
- amount
- currency
- created_at, updated_at
```

---

### 🔧 Atributos

#### `attributes`
Atributos configurables.

```sql
- id (PK)
- code (único)
- type (text, textarea, price, boolean, select, multiselect, etc.)
- admin_name
- is_required
- is_unique
- validation
- position
- is_user_defined
- value_per_locale
- value_per_channel
- is_filterable
- is_configurable
- is_visible_on_front
- created_at, updated_at
```

#### `attribute_families`
Familias de atributos.

```sql
- id (PK)
- code (único)
- name
- status
- is_user_defined
- created_at, updated_at
```

#### `attribute_options`
Opciones de atributos.

```sql
- id (PK)
- attribute_id (FK → attributes)
- admin_name
- sort_order
- swatch_value (para colores)
- created_at, updated_at
```

#### `attribute_translations`
Traducciones de atributos.

```sql
- id (PK)
- attribute_id (FK → attributes)
- locale
- name
- created_at, updated_at
```

---

### 📊 Inventario

#### `inventory_sources`
Fuentes de inventario (almacenes).

```sql
- id (PK)
- code (único)
- name
- description
- contact_name
- contact_email
- contact_number
- contact_fax
- country
- state
- city
- street
- postcode
- priority
- latitude
- longitude
- status
- created_at, updated_at
```

---

### 🎟️ Reglas y Promociones

#### `cart_rules`
Reglas de carrito (descuentos).

```sql
- id (PK)
- name
- description
- starts_from
- ends_till
- status
- coupon_type (no_coupon, specific_coupon, auto_generated)
- use_auto_generation
- coupon_code
- uses_per_coupon
- times_used
- condition_type
- conditions (JSON)
- end_other_rules
- uses_per_customer
- sort_order
- action_type
- discount_amount
- discount_quantity
- discount_step
- apply_to_shipping
- free_shipping
- sort_order
- created_at, updated_at
```

#### `catalog_rules`
Reglas de catálogo.

```sql
- id (PK)
- name
- description
- starts_from
- ends_till
- status
- condition_type
- conditions (JSON)
- end_other_rules
- action_type
- discount_amount
- sort_order
- created_at, updated_at
```

---

### 💳 Pagos e Impuestos

#### `payment_methods`
Métodos de pago.

```sql
- id (PK)
- code (único)
- method
- method_title
- description
- sort_order
- status
- created_at, updated_at
```

#### `tax_categories`
Categorías de impuestos.

```sql
- id (PK)
- code (único)
- name
- description
- created_at, updated_at
```

#### `tax_rates`
Tarifas de impuestos.

```sql
- id (PK)
- identifier
- country
- state
- tax_rate
- zip_code
- zip_from
- zip_to
- created_at, updated_at
```

#### `tax_mappings`
Mapeo de categorías de impuestos con tarifas.

```sql
- id (PK)
- tax_category_id (FK → tax_categories)
- tax_rate_id (FK → tax_rates)
- created_at, updated_at
```

---

### 🚚 Envíos

#### `shipping_methods`
Métodos de envío.

```sql
- id (PK)
- code (único)
- method
- method_title
- description
- active
- sort_order
- created_at, updated_at
```

---

### 📄 CMS

#### `cms_pages`
Páginas de contenido estático.

```sql
- id (PK)
- layout
- url_key (único)
- html_content
- meta_title
- meta_description
- meta_keywords
- content_heading
- status
- created_at, updated_at
```

#### `cms_page_translations`
Traducciones de páginas CMS.

```sql
- id (PK)
- cms_page_id (FK → cms_pages)
- locale
- page_title
- url_key
- html_content
- meta_title
- meta_description
- meta_keywords
- content_heading
- created_at, updated_at
```

---

### 👨‍💼 Administración

#### `admins`
Administradores del sistema.

```sql
- id (PK)
- name
- email (único)
- password
- api_token
- status
- role_id (FK → roles)
- created_at, updated_at
```

#### `roles`
Roles de administradores.

```sql
- id (PK)
- name
- description
- permission_type (all/custom)
- permissions (JSON)
- created_at, updated_at
```

---

### 📈 Marketing

#### `marketing_templates`
Plantillas de marketing.

```sql
- id (PK)
- name
- status
- created_at, updated_at
```

#### `marketing_events`
Eventos de marketing.

```sql
- id (PK)
- name
- description
- date
- created_at, updated_at
```

#### `marketing_campaigns`
Campañas de marketing.

```sql
- id (PK)
- name
- subject
- status
- channel_id (FK → channels)
- customer_group_id (FK → customer_groups)
- created_at, updated_at
```

#### `url_rewrites`
Reescritura de URLs (SEO).

```sql
- id (PK)
- entity_type
- request_path (único)
- target_path
- redirect_type
- locale
- created_at, updated_at

Índices:
- UNIQUE(request_path)
- INDEX(entity_type)
```

#### `search_terms`
Términos de búsqueda.

```sql
- id (PK)
- term
- results
- uses
- redirect_url
- created_at, updated_at
```

---

### 🔔 Notificaciones

#### `notifications`
Notificaciones del sistema.

```sql
- id (PK)
- type
- title
- description
- read
- order_id (FK → orders, nullable)
- created_at, updated_at
```

---

### 🎨 Temas

#### `theme_customizations`
Personalizaciones de tema.

```sql
- id (PK)
- type
- name
- sort_order
- status
- channel_id (FK → channels)
- theme
- created_at, updated_at
```

---

## 🔗 Relaciones Principales

### Diagrama ER Simplificado

```
customers
  ├─→ customer_addresses
  ├─→ customer_groups
  ├─→ orders
  └─→ cart

orders
  ├─→ order_items → products
  ├─→ order_addresses
  ├─→ order_invoices
  ├─→ order_shipments
  ├─→ order_refunds
  └─→ order_payments

products
  ├─→ product_categories → categories
  ├─→ product_attribute_values → attributes
  ├─→ product_flat (denormalizado)
  ├─→ product_inventories → inventory_sources
  └─→ cart_items

categories
  └─→ category_translations

cart
  ├─→ cart_items → products
  └─→ cart_addresses
```

---

## 📌 Índices Clave

### Performance

- `products.sku` - UNIQUE
- `product_flat(product_id, channel, locale)` - UNIQUE
- `orders.increment_id` - UNIQUE
- `orders.customer_id` - INDEX
- `order_items.order_id` - INDEX
- `cart.customer_id` - INDEX
- `customers.email` - UNIQUE

---

## 💡 Notas Importantes

1. **Product Flat**: Tabla denormalizada para optimizar búsquedas. Se sincroniza automáticamente.

2. **Nested Sets**: Las categorías usan nested sets para estructura jerárquica eficiente.

3. **Multi-tienda**: El sistema soporta múltiples canales (stores) con `channels`.

4. **Multi-idioma**: Sistema completo de traducciones con `*_translations` tables.

5. **Soft Deletes**: Algunas tablas usan soft deletes (no se muestran en este documento).

---

**Última actualización**: 2025-11-02  
**Versión**: v0.1.0

