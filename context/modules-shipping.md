# Módulo Shipping - Métodos de Envío

## Función Principal

El módulo **Shipping** gestiona los métodos de envío disponibles, el cálculo de costos de envío y la configuración de transportistas y tarifas.

## Responsabilidades

1. **Métodos de Envío**: Configuración de métodos disponibles
2. **Cálculo de Costos**: Cálculo automático de costos de envío
3. **Transportistas**: Gestión de transportistas
4. **Tarifas**: Configuración de tarifas de envío

## Ubicación

```
packages/Webkul/Shipping/
```

## Estructura de Archivos

### Modelos

```
src/Models/
└── ShippingMethod.php           # Métodos de envío
```

### Repositorios

```
src/Repositories/
└── ShippingMethodRepository.php
```

## Métodos de Envío

### Modelo ShippingMethod

Los métodos de envío se almacenan en base de datos (configuración).

**Ejemplos de métodos**:
- **Flat Rate**: Tarifa fija
- **Free Shipping**: Envío gratuito
- **Table Rate**: Tarifa por tabla (según peso, precio, etc.)

### Configuración

Los métodos se configuran desde:
- Admin → Settings → Shipping Methods

### Características

Cada método puede tener:
- **Título**: Nombre del método
- **Descripción**: Descripción para el cliente
- **Costo**: Costo base o fórmula de cálculo
- **Disponibilidad**: Por país, región, etc.
- **Tiempo estimado**: Tiempo de entrega estimado

## Cálculo de Costos

### Durante Checkout

El costo de envío se calcula cuando:
1. Cliente añade productos al carrito
2. Cliente selecciona dirección de envío
3. Sistema calcula costo basado en:
   - Método seleccionado
   - Dirección de destino
   - Peso del pedido
   - Valor del pedido

### Fórmulas Comunes

#### Flat Rate (Tarifa Fija)

```
Costo = Tarifa fija configurada
```

#### Por Peso

```
Costo = (Peso × Tarifa por kg) + Costo base
```

#### Por Valor

```
Costo = Porcentaje del valor del pedido
```

#### Table Rate

Basado en tabla de tarifas según:
- Peso
- Precio del pedido
- Destino
- Combinaciones

## Integración con Pedidos

### En Order

**Columnas relacionadas**:
- `shipping_method`: Método de envío usado
- `shipping_title`: Título del método
- `shipping_description`: Descripción
- `shipping_amount`: Costo de envío
- `base_shipping_amount`: Costo en moneda base

### En Shipment

Cuando se crea un envío:

```php
$shipment->carrier = 'FedEx';
$shipment->track_number = 'TRACK123';
```

## Métodos Disponibles

### Flat Rate

El método más simple: costo fijo independiente de peso o distancia.

**Uso**: Ideal para envíos locales o cuando el costo es uniforme.

### Free Shipping

Envío gratuito, generalmente con condiciones:
- Pedido mínimo
- Productos específicos
- Código promocional

### Table Rate

Sistema flexible que permite:
- Múltiples tablas de tarifas
- Condiciones combinadas
- Tarifas por zonas geográficas

## Configuración Avanzada

### Por Canal

Los métodos pueden estar disponibles solo en ciertos canales (tiendas).

### Por Grupo de Clientes

Algunos métodos pueden estar restringidos a ciertos grupos de clientes.

### Por Destino

Disponibilidad basada en:
- País
- Estado/Provincia
- Código postal

## Integración con Transportistas

### Tracking

Cuando se crea un envío, se puede asociar:
- Transportista (FedEx, UPS, DHL, etc.)
- Número de seguimiento
- URL de seguimiento

### APIs Externas

Algunos transportistas ofrecen APIs para:
- Cálculo de tarifas en tiempo real
- Creación de envíos
- Tracking automático

Bagisto permite integrar estas APIs mediante extensiones.

## Relación con Inventario

### Fuentes de Inventario

Los métodos de envío pueden estar asociados a:
- Fuentes de inventario (almacenes)
- Determinación del origen del envío

### Múltiples Almacenes

Si hay múltiples almacenes:
- El sistema determina el almacén más cercano
- Calcula envío desde ese almacén

## Interacción con Otros Módulos

### Dependencias

Shipping depende de:
- **Core**: Channels, addresses (para cálculo)

### Usado Por

- **Shop**: Selección de método en checkout
- **Admin**: Configuración de métodos
- **Sales**: Asociación con pedidos y creación de shipments

### Integración con Checkout

Durante checkout:
1. Cliente selecciona dirección de envío
2. Sistema muestra métodos disponibles
3. Cliente selecciona método
4. Sistema calcula y muestra costo
5. Cliente continúa con pago

## Ejemplos de Uso

### Obtener Métodos Disponibles

```php
use Webkul\Shipping\Repositories\ShippingMethodRepository;

$repository = app(ShippingMethodRepository::class);
$methods = $repository->getAvailableMethods($address, $cart);
```

### Calcular Costo de Envío

```php
$shippingCost = $repository->calculateShipping(
    $method,
    $address,
    $cart
);
```

### Guardar Método en Pedido

```php
$order->shipping_method = 'flatrate';
$order->shipping_title = 'Flat Rate';
$order->shipping_amount = 10.00;
$order->save();
```

### Crear Envío con Tracking

```php
use Webkul\Sales\Repositories\ShipmentRepository;

$shipmentData = [
    'order_id' => $orderId,
    'carrier' => 'FedEx',
    'track_number' => 'TRACK123',
    'shipment_items' => [...],
];

$shipment = app(ShipmentRepository::class)->create($shipmentData);
```

## Extensiones Disponibles

En Bagisto Marketplace se pueden encontrar extensiones para:

- **Integraciones con transportistas**: FedEx, UPS, DHL APIs
- **Métodos de envío personalizados**: Métodos específicos por región
- **Cálculo automático**: Integración con APIs de cálculo de envío

## Configuración de Zonas

### Países

Los métodos pueden estar disponibles solo para ciertos países.

### Estados/Provincias

Restricción por estado o provincia.

### Códigos Postales

Restricción por rango de códigos postales.

## Personalización

### Método Personalizado

Para crear un método de envío personalizado:

1. Crear entrada en base de datos
2. Implementar lógica de cálculo
3. Configurar en admin
4. Agregar traducciones

### Cálculo Personalizado

```php
class CustomShippingCalculator
{
    public function calculate($address, $cart)
    {
        // Lógica personalizada de cálculo
        $cost = // ... cálculo
        
        return $cost;
    }
}
```

## Reportes

El módulo Shipping proporciona datos para:
- Métodos más usados
- Costos de envío por período
- Tiempos de entrega
- Transportistas más utilizados

Los reportes se gestionan en el módulo Admin.

## Consideraciones

### Internacional

Para envíos internacionales:
- Considerar aduanas
- Documentación requerida
- Restricciones de productos
- Tiempos de tránsito extendidos

### Multi-canal

Si hay múltiples canales, cada uno puede tener:
- Métodos de envío diferentes
- Tarifas diferentes
- Restricciones diferentes

### Multi-moneda

Los costos se convierten a la moneda del canal:
- Usando tasas de cambio configuradas
- Mostrando en la moneda del cliente


