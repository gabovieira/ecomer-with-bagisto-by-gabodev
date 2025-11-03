# Módulo Sales - Gestión de Ventas

## Función Principal

El módulo **Sales** gestiona todo el ciclo de vida de las ventas: desde la creación de pedidos hasta facturas, envíos, reembolsos y transacciones de pago.

## Responsabilidades

1. **Órdenes**: Creación y gestión de pedidos
2. **Facturas**: Generación de facturas
3. **Envíos**: Gestión de envíos
4. **Reembolsos**: Procesamiento de reembolsos
5. **Transacciones**: Registro de transacciones de pago
6. **Comentarios**: Comentarios internos sobre pedidos

## Ubicación

```
packages/Webkul/Sales/
```

## Estructura de Archivos

### Modelos Principales

```
src/Models/
├── Order.php                   # Pedido principal
├── OrderItem.php               # Items del pedido
├── OrderAddress.php            # Direcciones (envío/facturación)
├── Invoice.php                 # Facturas
├── InvoiceItem.php             # Items de factura
├── Shipment.php                # Envíos
├── ShipmentItem.php            # Items de envío
├── Refund.php                  # Reembolsos
├── RefundItem.php              # Items de reembolso
├── OrderPayment.php            # Información de pago
├── OrderTransaction.php        # Transacciones
└── OrderComment.php            # Comentarios
```

### Repositorios

```
src/Repositories/
├── OrderRepository.php
├── InvoiceRepository.php
├── ShipmentRepository.php
└── RefundRepository.php
```

## Pedidos (Orders)

### Modelo Order

**Tabla**: `orders`

**Columnas principales**:
- `increment_id`: Número único de pedido (ej: #00001)
- `status`: Estado del pedido
- `customer_id`: Cliente (nullable para invitados)
- `channel_id`: Canal de venta
- `grand_total`: Total del pedido
- `sub_total`: Subtotal
- `shipping_amount`: Costo de envío
- `tax_amount`: Impuestos
- `discount_amount`: Descuentos

### Estados de Pedido

```php
Order::STATUS_PENDING = 'pending';
Order::STATUS_PENDING_PAYMENT = 'pending_payment';
Order::STATUS_PROCESSING = 'processing';
Order::STATUS_COMPLETED = 'completed';
Order::STATUS_CANCELED = 'canceled';
Order::STATUS_CLOSED = 'closed';
Order::STATUS_FRAUD = 'fraud';
```

### Relaciones

```php
$order->items;              // OrderItems
$order->customer;           // Customer (polimórfico)
$order->addresses;          // OrderAddresses
$order->invoices;           // Invoices
$order->shipments;          // Shipments
$order->refunds;            // Refunds
$order->transactions;       // OrderTransactions
$order->payment;            // OrderPayment
$order->comments;           // OrderComments
```

### Crear Pedido

```php
use Webkul\Sales\Repositories\OrderRepository;

$orderData = [
    'cart_id' => $cartId,
    'customer_id' => $customerId,
    'channel_id' => $channelId,
    'shipping_address' => [...],
    'billing_address' => [...],
    'payment' => [...],
    'shipping_method' => 'flatrate',
    'payment_method' => 'cashondelivery',
];

$order = app(OrderRepository::class)->create($orderData);
```

## Items de Pedido (OrderItems)

### Modelo OrderItem

**Tabla**: `order_items`

Cada item representa un producto en el pedido:

- `order_id`: Pedido al que pertenece
- `product_id`: Producto
- `sku`: SKU del producto
- `name`: Nombre del producto
- `qty_ordered`: Cantidad pedida
- `qty_shipped`: Cantidad enviada
- `qty_invoiced`: Cantidad facturada
- `qty_refunded`: Cantidad reembolsada
- `price`: Precio unitario
- `total`: Total del item

### Items Hijo

Para productos configurables, hay items padre e items hijo:

```php
$item->children;  // Items hijo (variantes)
$item->parent;    // Item padre
```

## Facturas (Invoices)

### Modelo Invoice

**Tabla**: `order_invoices`

Una orden puede tener múltiples facturas (parciales).

**Columnas principales**:
- `order_id`: Pedido asociado
- `increment_id`: Número de factura
- `state`: Estado (pending, paid, refunded)
- `grand_total`: Total facturado

### Estados

```php
Invoice::STATE_PAID = 'paid';
Invoice::STATE_PENDING = 'pending';
Invoice::STATE_REFUNDED = 'refunded';
```

### Crear Factura

```php
use Webkul\Sales\Repositories\InvoiceRepository;

$invoiceData = [
    'order_id' => $orderId,
    'invoice_items' => [
        ['order_item_id' => 1, 'qty' => 2],
    ],
];

$invoice = app(InvoiceRepository::class)->create($invoiceData);
```

### Generar PDF

```php
$invoice->pdf();  // Genera PDF de la factura
```

## Envíos (Shipments)

### Modelo Shipment

**Tabla**: `order_shipments`

Un pedido puede tener múltiples envíos (parciales).

**Columnas principales**:
- `order_id`: Pedido asociado
- `increment_id`: Número de envío
- `track_number`: Número de seguimiento
- `carrier`: Transportista
- `total_qty`: Cantidad total enviada

### Crear Envío

```php
use Webkul\Sales\Repositories\ShipmentRepository;

$shipmentData = [
    'order_id' => $orderId,
    'shipment_items' => [
        ['order_item_id' => 1, 'qty' => 2],
    ],
    'track_number' => 'TRACK123',
    'carrier' => 'FedEx',
];

$shipment = app(ShipmentRepository::class)->create($shipmentData);
```

## Reembolsos (Refunds)

### Modelo Refund

**Tabla**: `order_refunds`

**Columnas principales**:
- `order_id`: Pedido asociado
- `increment_id`: Número de reembolso
- `refunded`: Monto reembolsado
- `state`: Estado (pending, refunded)

### Crear Reembolso

```php
use Webkul\Sales\Repositories\RefundRepository;

$refundData = [
    'order_id' => $orderId,
    'refund_items' => [
        ['order_item_id' => 1, 'qty' => 1],
    ],
];

$refund = app(RefundRepository::class)->create($refundData);
```

## Transacciones

### Modelo OrderTransaction

**Tabla**: `order_transactions`

Registra todas las transacciones de pago asociadas a pedidos.

**Columnas principales**:
- `order_id`: Pedido
- `payment_method`: Método de pago
- `transaction_id`: ID de transacción externa
- `type`: Tipo (sale, refund, etc.)
- `amount`: Monto

## Direcciones de Pedido

### Modelo OrderAddress

**Tabla**: `order_addresses`

Almacena direcciones de envío y facturación del pedido.

**Tipos**:
- `shipping`: Dirección de envío
- `billing`: Dirección de facturación

## Comentarios

### Modelo OrderComment

**Tabla**: `order_comments`

Comentarios internos sobre pedidos (notas del admin).

## Flujo de un Pedido

```
1. Carrito convertido en Order
   Status: pending
   
2. Pago procesado
   Status: pending_payment → processing
   
3. Factura generada
   Invoice creado
   
4. Envío preparado
   Shipment creado
   
5. Pedido completado
   Status: completed
   
Opcional:
- Reembolso si es necesario
  Refund creado
  Status: puede cambiar a canceled
```

## Eventos

El módulo Sales emite eventos importantes:

- `OrderCreated`: Al crear un pedido
- `OrderCanceled`: Al cancelar
- `OrderInvoiced`: Al crear factura
- `OrderShipped`: Al crear envío
- `OrderRefunded`: Al crear reembolso

### Listeners Comunes

- **EmailNotificationListener**: Envía emails al cliente
- **InventoryUpdateListener**: Actualiza inventario
- **StatisticsListener**: Actualiza estadísticas
- **SearchIndexListener**: Indexa en búsqueda (si aplica)

## Interacción con Otros Módulos

### Dependencias

Sales depende de:
- **Core**: Channels, addresses
- **Customer**: Clientes
- **Product**: Productos (para order items)
- **Payment**: Procesamiento de pagos
- **Shipping**: Métodos de envío

### Usado Por

- **Shop**: Crea pedidos desde checkout
- **Admin**: Gestiona pedidos, facturas, envíos

## Ejemplos de Uso

### Obtener Pedidos de un Cliente

```php
use Webkul\Sales\Models\Order;

$orders = Order::where('customer_id', $customerId)
    ->orderBy('created_at', 'desc')
    ->get();
```

### Cancelar Pedido

```php
$order = Order::find($orderId);
app(OrderRepository::class)->cancel($order);
```

### Obtener Total Facturado

```php
$totalInvoiced = $order->invoices()->sum('grand_total');
```

### Obtener Items Pendientes de Envío

```php
$pendingItems = $order->items()
    ->whereColumn('qty_ordered', '>', 'qty_shipped')
    ->get();
```

### Generar Factura Completa

```php
$invoiceData = [
    'order_id' => $orderId,
    // Todos los items
    'invoice_items' => $order->items->map(function ($item) {
        return [
            'order_item_id' => $item->id,
            'qty' => $item->qty_ordered,
        ];
    })->toArray(),
];

$invoice = app(InvoiceRepository::class)->create($invoiceData);
```

## Reportes

Sales proporciona datos para reportes:
- Ventas por período
- Productos más vendidos
- Estado de pedidos
- Facturación
- Reembolsos

Los reportes se gestionan en el módulo Admin.



