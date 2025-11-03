# Módulo Payment - Métodos de Pago

## Función Principal

El módulo **Payment** gestiona los métodos de pago disponibles en la tienda, su configuración y el procesamiento de transacciones de pago.

## Responsabilidades

1. **Métodos de Pago**: Configuración y gestión de métodos
2. **Procesamiento**: Procesamiento de pagos
3. **Transacciones**: Registro de transacciones
4. **Integraciones**: Integración con pasarelas externas

## Ubicación

```
packages/Webkul/Payment/
```

## Estructura de Archivos

### Modelos

```
src/Models/
├── PaymentMethod.php          # Métodos de pago
└── Payment.php                # Información de pago en pedidos
```

### Repositorios

```
src/Repositories/
└── PaymentMethodRepository.php
```

## Métodos de Pago

### Modelo PaymentMethod

**Tabla**: `payment_methods`

**Columnas principales**:
- `id`: ID único
- `method`: Código del método (ej: 'cashondelivery')
- `method_title`: Nombre del método
- `description`: Descripción
- `sort`: Orden de visualización
- `status`: Activo/Inactivo

### Métodos Disponibles

#### Cash on Delivery (Pago contra reembolso)

Código: `cashondelivery`

Pago al recibir el producto.

#### Money Transfer (Transferencia bancaria)

Código: `moneytransfer`

Transferencia bancaria directa.

#### PayPal

Código: `paypal`

**Módulo separado**: `Webkul\Paypal`

Integración con PayPal para pagos online.

### Métodos Personalizados

Se pueden agregar métodos personalizados mediante:

1. Crear nuevo método en base de datos
2. Crear clase de procesamiento (si es necesario)
3. Configurar en admin

## Integración con Pedidos

### Modelo OrderPayment

**Tabla**: `order_payments`

Almacena información de pago asociada a un pedido.

**Columnas**:
- `order_id`: Pedido
- `method`: Método de pago usado
- `method_title`: Nombre del método
- `status`: Estado del pago
- `amount`: Monto pagado
- `additional`: Información adicional (JSON)

### Relación

```php
$order->payment;  // OrderPayment
```

## Procesamiento de Pago

### Flujo

1. Cliente selecciona método en checkout
2. Método se guarda en `OrderPayment`
3. Se procesa el pago según el método:
   - **Cash on Delivery**: Sin procesamiento inmediato
   - **PayPal**: Redirección a PayPal
   - **Money Transfer**: Instrucciones al cliente

### En Checkout

El módulo Payment se integra con el proceso de checkout:

```php
// Guardar método de pago
$payment = new OrderPayment();
$payment->order_id = $order->id;
$payment->method = $paymentMethod;
$payment->method_title = $methodTitle;
$payment->save();
```

## Transacciones

Las transacciones se registran en `order_transactions` (módulo Sales):

```php
$order->transactions()->create([
    'payment_method' => 'paypal',
    'transaction_id' => 'TXN123',
    'type' => 'sale',
    'amount' => $order->grand_total,
]);
```

## Configuración

### Desde Admin

Los métodos de pago se configuran en:
- Admin → Settings → Payment Methods

Se puede:
- Activar/desactivar métodos
- Configurar orden de visualización
- Configurar parámetros específicos (ej: PayPal API keys)

### Variables de Entorno

Para métodos como PayPal:

```env
PAYPAL_CLIENT_ID=
PAYPAL_CLIENT_SECRET=
PAYPAL_MODE=sandbox|live
```

## Módulo PayPal

Bagisto incluye un módulo separado para PayPal:

**Ubicación**: `packages/Webkul/Paypal/`

### Funcionalidades

- Integración con PayPal API
- Pagos con PayPal
- Reembolsos vía PayPal
- Sandbox para testing

### Configuración

1. Obtener credenciales de PayPal
2. Configurar en `.env`
3. Activar método en Admin

## Crear Método Personalizado

### 1. Crear en Base de Datos

```sql
INSERT INTO payment_methods (method, method_title, description, sort, status)
VALUES ('custom_method', 'Mi Método', 'Descripción', 1, 1);
```

### 2. Agregar Traducción

En archivos de idioma:
```php
'custom_method' => 'Mi Método',
```

### 3. Crear Clase de Procesamiento (Opcional)

Si requiere lógica de procesamiento:

```php
namespace App\Payment;

class CustomMethodProcessor
{
    public function process($order, $paymentData)
    {
        // Lógica de procesamiento
    }
}
```

### 4. Integrar en Checkout

Modificar el proceso de checkout para manejar el nuevo método.

## Estados de Pago

Los pagos pueden tener diferentes estados:

- `pending`: Pendiente
- `paid`: Pagado
- `failed`: Fallido
- `refunded`: Reembolsado

## Reembolsos

Los reembolsos se gestionan desde el módulo Sales, pero pueden interactuar con métodos de pago:

```php
// Reembolsar vía PayPal
if ($order->payment->method === 'paypal') {
    // Lógica de reembolso PayPal
}
```

## Interacción con Otros Módulos

### Dependencias

Payment depende de:
- **Core**: Configuración base

### Usado Por

- **Shop**: Selección de método en checkout
- **Admin**: Configuración de métodos
- **Sales**: Asociación con pedidos y procesamiento

## Ejemplos de Uso

### Obtener Métodos Disponibles

```php
use Webkul\Payment\Models\PaymentMethod;

$methods = PaymentMethod::where('status', 1)
    ->orderBy('sort')
    ->get();
```

### Guardar Método en Pedido

```php
use Webkul\Sales\Models\OrderPayment;

$payment = new OrderPayment();
$payment->order_id = $order->id;
$payment->method = 'cashondelivery';
$payment->method_title = 'Pago contra reembolso';
$payment->status = 'pending';
$payment->save();
```

### Verificar Estado de Pago

```php
$order = Order::find($orderId);

if ($order->payment && $order->payment->status === 'paid') {
    // Pago completado
}
```

### Procesar Pago PayPal

```php
use Webkul\Paypal\Payment\Standard;

$paypal = new Standard();
$response = $paypal->processPayment($order, $paymentData);
```

## Seguridad

### Datos Sensibles

- Las credenciales de API se almacenan en `.env`
- Nunca se exponen en código o vistas
- Se usan conexiones seguras (HTTPS) para pasarelas

### Validación

- Validación de montos
- Verificación de estados de pago
- Prevención de pagos duplicados

## Testing

Para desarrollo, se puede usar:

- **Sandbox de PayPal**: Para probar PayPal
- **Modo test**: Para otros métodos
- **Mocks**: Para testing unitario

## Extensiones

Bagisto Marketplace ofrece extensiones para:
- Stripe
- Razorpay
- Otros métodos de pago

Para integrar nuevos métodos, seguir la estructura del módulo PayPal.



