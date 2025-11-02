# 📖 Guía de Uso

Manual de uso para administradores y usuarios del sistema **Ecomer with Bagisto by Gabodev**.

---

## 🎯 Visión General

Esta guía te ayudará a utilizar todas las funcionalidades principales de la plataforma e-commerce.

---

## 👤 Panel de Administración

### Acceso al Panel

1. Navega a `http://tu-dominio.com/admin`
2. Ingresa tus credenciales de administrador
3. Serás redirigido al dashboard principal

### Dashboard

El dashboard muestra:
- **Estadísticas de ventas**: Total de ventas, pedidos, clientes
- **Gráficos de rendimiento**: Ventas por período, productos más vendidos
- **Pedidos recientes**: Lista de los últimos pedidos
- **Accesos rápidos**: Enlaces a funciones comunes

---

## 🛒 Gestión de Productos

### Crear un Producto

1. Ve a **Catálogo > Productos**
2. Haz clic en **"Agregar Producto"**
3. Completa la información:
   - **Nombre del producto**
   - **SKU** (código único)
   - **Descripción** (con editor WYSIWYG)
   - **Precio** y **Precio especial** (opcional)
   - **Stock** disponible
   - **Atributos**: Talla, Color, etc.
4. **Sube imágenes** del producto
5. **Asigna categorías**
6. Guarda el producto

### Variantes de Producto

Para productos con variantes (ej: camisetas en diferentes tallas):

1. Al crear el producto, define los atributos configurables
2. Configura cada variante con su precio y stock individual
3. Cada variante puede tener imágenes propias

### Gestión de Inventario

- **Actualizar stock**: Ve al producto y modifica la cantidad
- **Historial de inventario**: Revisa movimientos de stock
- **Alertas de bajo stock**: Configura alertas cuando el stock sea bajo

---

## 📦 Gestión de Pedidos

### Ver Pedidos

1. Ve a **Ventas > Pedidos**
2. Verás una lista con todos los pedidos
3. Filtra por estado, fecha, cliente, etc.

### Procesar un Pedido

1. Haz clic en un pedido para ver detalles
2. Estados disponibles:
   - **Pendiente**: Pedido recibido, pendiente de procesar
   - **Procesando**: En preparación
   - **Completado**: Listo para envío
   - **Cancelado**: Pedido cancelado
3. Actualiza el estado según corresponda
4. Genera factura o nota de crédito si es necesario

### Gestión de Envíos

1. Crea métodos de envío en **Configuración > Envíos**
2. Configura tarifas según peso, precio o destino
3. Asigna métodos de envío a cada pedido

---

## 👥 Gestión de Clientes

### Ver Clientes

1. Ve a **Clientes > Todos los Clientes**
2. Busca clientes por nombre, email o ID
3. Ve el historial de pedidos de cada cliente

### Crear Cliente Manualmente

1. Ve a **Clientes > Agregar Cliente**
2. Completa la información:
   - Nombre, email, teléfono
   - Direcciones de envío y facturación
3. Puedes crear pedidos en nombre del cliente

---

## 💳 Métodos de Pago

### Configurar Métodos de Pago

1. Ve a **Configuración > Métodos de Pago**
2. Activa los métodos que deseas usar:
   - **PayPal**
   - **Stripe**
   - **Transferencia bancaria**
   - **Contra reembolso**
3. Configura credenciales y parámetros según el método

---

## 🚚 Gestión de Envíos

### Crear Métodos de Envío

1. Ve a **Configuración > Envíos**
2. Crea un nuevo método de envío
3. Configura:
   - **Nombre del método** (ej: "Envío Express")
   - **Tarifa**: Fija, por peso, por precio
   - **Zonas de envío**: Países/regiones donde aplica
   - **Tiempo estimado**: Días de entrega

---

## 📊 Reportes

### Reportes Disponibles

1. **Ventas**: Ingresos por período
2. **Productos**: Más vendidos, menos vendidos
3. **Clientes**: Clientes más activos, nuevos clientes
4. **Inventario**: Productos con bajo stock

### Generar un Reporte

1. Ve a **Reportes**
2. Selecciona el tipo de reporte
3. Define el rango de fechas
4. Haz clic en **"Generar Reporte"**
5. Exporta en CSV o Excel si es necesario

---

## ⚙️ Configuración General

### Configuración de la Tienda

1. Ve a **Configuración > Configuración General**
2. Configura:
   - **Nombre de la tienda**
   - **Logo y favicon**
   - **Moneda y idioma predeterminados**
   - **Zona horaria**
   - **Datos de contacto**

### Temas y Personalización

1. Ve a **Configuración > Temas**
2. Selecciona un tema activo
3. Personaliza colores, fuentes y layouts
4. Previsualiza cambios antes de aplicar

---

## 🔍 Búsqueda y Filtros

### Búsqueda de Productos

- Los clientes pueden buscar productos desde el frontend
- La búsqueda utiliza Elasticsearch (si está configurado)
- Los resultados se filtran automáticamente

### Filtros en Admin

En cualquier lista (productos, pedidos, clientes):
- Usa la barra de búsqueda para filtrar
- Aplica filtros avanzados con los controles disponibles
- Exporta resultados si es necesario

---

## 📱 Frontend - Tienda Online

### Navegación del Cliente

Los clientes pueden:
- **Buscar productos**: Barra de búsqueda en la parte superior
- **Navegar por categorías**: Menú principal
- **Ver carrito**: Icono en la esquina superior derecha
- **Acceder a su cuenta**: Login/Registro

### Proceso de Compra

1. Cliente navega y agrega productos al carrito
2. Procede al checkout
3. Ingresa información de envío y facturación
4. Selecciona método de envío y pago
5. Confirma el pedido
6. Recibe confirmación por email

---

## 🛠️ Mantenimiento

### Limpiar Caché

```bash
php artisan cache:clear
php artisan config:clear
php artisan view:clear
```

### Actualizar Assets

```bash
npm run build
```

### Respaldar Base de Datos

Siempre realiza respaldos regulares de la base de datos MySQL.

---

## ❓ Soporte y Ayuda

- **Documentación técnica**: Carpeta `/context`
- **Issues en GitHub**: [Repositorio del proyecto](https://github.com/gabovieira/ecomer-with-bagisto-by-gabodev/issues)
- **Foros de Bagisto**: [forums.bagisto.com](https://forums.bagisto.com)

---

**Última actualización**: 2025-11-02

