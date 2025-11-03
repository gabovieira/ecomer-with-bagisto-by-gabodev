# Módulo Core - Núcleo del Sistema

## Función Principal

El módulo **Core** es el núcleo fundamental de Bagisto. Proporciona funcionalidades base que son utilizadas por todos los demás módulos del sistema. Sin Core, ningún otro módulo puede funcionar.

## Responsabilidades

1. **Gestión de Canales (Channels)**: Sistema multi-tienda
2. **Gestión de Locales**: Multiidioma
3. **Gestión de Monedas**: Multimoneda con tasas de cambio
4. **Sistema de Direcciones**: Direcciones reutilizables
5. **Helpers Globales**: Funciones utilitarias
6. **Convenciones del Sistema**: Define estándares para módulos

## Ubicación

```
packages/Webkul/Core/
```

## Archivos Principales

### Service Providers

- **CoreServiceProvider**: Service provider principal
  - Ubicación: `src/Providers/CoreServiceProvider.php`
  - Registra comandos, helpers, eventos, listeners

- **CoreModuleServiceProvider**: Base para otros módulos
  - Ubicación: `src/Providers/CoreModuleServiceProvider.php`
  - Extendido por todos los ModuleServiceProviders

- **ModuleServiceProvider**: Registro del módulo
  - Ubicación: `src/Providers/ModuleServiceProvider.php`

### Modelos Principales

#### Channel
Gestión de canales (tiendas múltiples).

**Ubicación**: `src/Models/Channel.php`

**Funcionalidad**:
- Cada canal puede tener su propia configuración
- Moneda, idioma, URL específicos
- Productos pueden estar asociados a canales

#### Locale
Gestión de idiomas del sistema.

**Ubicación**: `src/Models/Locale.php`

**Funcionalidad**:
- Soporte multiidioma
- Traducciones de productos, categorías, CMS
- Configuración por canal

#### Currency
Gestión de monedas.

**Ubicación**: `src/Models/Currency.php`

**Funcionalidad**:
- Múltiples monedas
- Tasas de cambio automáticas o manuales
- Conversión de precios

#### Address
Direcciones reutilizables.

**Ubicación**: `src/Models/Address.php`

**Funcionalidad**:
- Usado por customers, orders
- Direcciones de envío y facturación
- Polimórfico (morphs)

#### Country / CountryState
Países y estados/provincias.

**Ubicación**: `src/Models/Country.php`, `src/Models/CountryState.php`

**Funcionalidad**:
- Datos de países y estados
- Validación de direcciones
- Cálculo de impuestos y envíos

### Repositorios

- **ChannelRepository**: `src/Repositories/ChannelRepository.php`
- **LocaleRepository**: `src/Repositories/LocaleRepository.php`
- **CurrencyRepository**: `src/Repositories/CurrencyRepository.php`
- **CountryRepository**: `src/Repositories/CountryRepository.php`
- **CoreConfigRepository**: `src/Repositories/CoreConfigRepository.php`

### Helpers

**Ubicación**: `src/Http/helpers.php`

Funciones helper globales disponibles en toda la aplicación:

```php
core()          // Facade para Core
acl()           // Sistema ACL
menu()          // Sistema de menús
```

### Facades

- **Core**: Acceso a funcionalidades core
- **Acl**: Control de acceso
- **Menu**: Gestión de menús
- **SystemConfig**: Configuración del sistema
- **ElasticSearch**: Cliente de Elasticsearch

## Funcionalidades Específicas

### Sistema de Canales (Multi-tienda)

Permite tener múltiples tiendas en una sola instalación:

```php
use Webkul\Core\Facades\Core;

$channel = Core::getCurrentChannel();
$channel->name;        // Nombre de la tienda
$channel->currency;   // Moneda del canal
$channel->locale;     // Idioma del canal
```

### Gestión de Configuración

El sistema de configuración permite almacenar configuraciones en BD:

```php
// Obtener configuración
core()->getConfigData('general.general.locale_settings.locale');

// Guardar configuración
core()->storeConfig('general.general.locale_settings.locale', 'es');
```

### Sistema de Direcciones

Las direcciones son polimórficas y pueden pertenecer a:
- Customers
- Orders
- Cualquier otro modelo que use Address

```php
$address = new Address();
$address->addressable_type = Customer::class;
$address->addressable_id = $customerId;
// ... campos de dirección
```

### Helpers de Moneda

Formateo y conversión de monedas:

```php
core()->formatPrice($price, $currency);
core()->convertPrice($price, $fromCurrency, $toCurrency);
```

### Sistema ACL

Control de acceso para el admin:

```php
use Webkul\Core\Facades\Acl;

Acl::check('catalog.products.index'); // Verificar permiso
```

### Menús

Gestión de menús del admin:

```php
use Webkul\Core\Facades\Menu;

Menu::addItem('catalog.products', 'Productos', 'admin.catalog.products.index');
```

## Interacción con Otros Módulos

### Todos los Módulos Dependen de Core

Cada módulo necesita Core para:
- **Channels**: Determinar el canal actual
- **Locales**: Traducciones
- **Currencies**: Precios y conversiones
- **Addresses**: Direcciones de clientes y pedidos

### Ejemplos de Uso

#### En Product Module

```php
// Obtener productos del canal actual
$channel = core()->getCurrentChannel();
$products = Product::where('channel_id', $channel->id)->get();
```

#### En Sales Module

```php
// Crear dirección para pedido
$address = new Address();
$address->addressable_type = Order::class;
$address->addressable_id = $orderId;
$address->address_type = 'shipping';
// ...
```

## Base de Datos

### Tablas Principales

- `channels`: Canales (tiendas)
- `channel_translations`: Traducciones de canales
- `locales`: Idiomas disponibles
- `currencies`: Monedas
- `currency_exchange_rates`: Tasas de cambio
- `countries`: Países
- `country_states`: Estados/Provincias
- `addresses`: Direcciones
- `core_config`: Configuración del sistema

## Comandos Artisan

El módulo Core proporciona varios comandos:

- `bagisto:version`: Muestra la versión de Bagisto
- `exchange-rate:update`: Actualiza tasas de cambio
- `invoice:cron`: Procesa facturas vencidas (programado)

## Traits y Utilidades

### TranslatableModel

Modelo base para modelos con traducciones:

```php
use Webkul\Core\Eloquent\TranslatableModel;

class Product extends TranslatableModel
{
    // Soporte automático para traducciones
}
```

### PDFHandler

Trait para generar PDFs:

```php
use Webkul\Core\Traits\PDFHandler;

class Invoice
{
    use PDFHandler;
    
    public function generatePDF()
    {
        return $this->downloadPDF($view, $data);
    }
}
```

## Eventos

Core emite eventos que otros módulos pueden escuchar:

- Configuración actualizada
- Channel cambiado
- Currency actualizado

## Listeners

- **CleanCacheRepository**: Limpia caché cuando es necesario
- **ResponseCacheHit**: Maneja hits de caché de respuestas

## Extensibilidad

Para extender Core, se puede:

1. **Crear nuevos helpers**: Agregar funciones en `src/Http/helpers.php`
2. **Extender modelos**: Usar herencia o traits
3. **Agregar comandos**: Registrar en `CoreServiceProvider`
4. **Escuchar eventos**: Crear listeners personalizados

## Convenciones

Core define las convenciones del sistema en `CoreConvention`:

- Ruta de migraciones: `Database/Migrations`
- Archivo manifest: `Resources/manifest.php`

Estas convenciones son seguidas por todos los módulos.



