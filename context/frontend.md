# Frontend - Vue.js y Assets

## Arquitectura Frontend

Bagisto tiene dos aplicaciones frontend principales:

1. **Shop**: Frontend de la tienda (público)
2. **Admin**: Panel de administración

Ambas están construidas con Vue.js 3 y utilizan Vite como herramienta de build.

## Aplicación Shop

### Ubicación

```
packages/Webkul/Shop/src/Resources/assets/
├── js/
│   ├── app.js              # Punto de entrada
│   ├── directives/         # Directivas Vue personalizadas
│   └── plugins/            # Plugins Vue (axios, etc.)
├── css/
│   └── app.css            # Estilos principales
└── locales/               # Traducciones i18n
```

### Configuración Vite

El archivo `packages/Webkul/Shop/vite.config.js` configura la compilación:

```javascript
export default defineConfig({
    plugins: [
        vue(),
        laravel({
            input: [
                "src/Resources/assets/css/app.css",
                "src/Resources/assets/js/app.js",
            ],
            buildDirectory: "themes/shop/default/build",
        }),
    ],
});
```

### Punto de Entrada (app.js)

El archivo `app.js` inicializa la aplicación Vue:

```javascript
import { createApp } from 'vue';
import axiosPlugin from './plugins/axios';
// ... otros imports

const app = createApp({});
app.use(axiosPlugin);
// ... configuración
```

### Componentes Vue

Los componentes Vue se definen directamente en las vistas Blade usando:

```blade
<script type="module">
    app.component('v-product-card', {
        template: '#v-product-card-template',
        props: ['product'],
        // ...
    });
</script>
```

### Estructura de Componentes

Los componentes están organizados por funcionalidad en:

```
packages/Webkul/Shop/src/Resources/views/components/
├── ui/                    # Componentes UI genéricos
├── header/                # Componentes del header
├── footer/                # Componentes del footer
├── product/               # Componentes de productos
├── cart/                  # Componentes del carrito
└── checkout/              # Componentes de checkout
```

### Ejemplo de Componente

```blade
<template id="v-product-card-template">
    <div class="product-card">
        <h3>{{ product.name }}</h3>
        <p>{{ product.price }}</p>
        <button @click="addToCart">Añadir al carrito</button>
    </div>
</template>

<script type="module">
    app.component('v-product-card', {
        template: '#v-product-card-template',
        props: ['product'],
        methods: {
            addToCart() {
                this.$axios.post('/shop/api/cart/add', {
                    product_id: this.product.id
                });
            }
        }
    });
</script>
```

## Aplicación Admin

### Ubicación

```
packages/Webkul/Admin/src/Resources/assets/
├── js/
│   ├── app.js              # Punto de entrada
│   └── plugins/             # Plugins Vue
├── css/
│   └── app.css
└── locales/                # Traducciones
```

### Configuración Similar

El Admin sigue la misma estructura que Shop pero con su propia configuración de Vite.

## Integración con Backend

### Axios

Ambas aplicaciones usan Axios para comunicación HTTP con el backend.

Plugin configurado en: `packages/Webkul/Shop/src/Resources/assets/js/plugins/axios.js`

```javascript
import axios from "axios";
window.axios = axios;
window.axios.defaults.headers.common["X-Requested-With"] = "XMLHttpRequest";
```

### Uso en Componentes

```javascript
methods: {
    async fetchProducts() {
        try {
            const response = await this.$axios.get('/shop/api/products');
            this.products = response.data.data;
        } catch (error) {
            console.error(error);
        }
    }
}
```

### Manejo de CSRF

Laravel maneja automáticamente los tokens CSRF mediante cookies `XSRF-TOKEN`. Axios los incluye automáticamente en las peticiones.

## Rutas API

### Shop API

Rutas definidas en: `packages/Webkul/Shop/src/Routes/api.php`

Ejemplos:
- `GET /shop/api/products` - Listar productos
- `POST /shop/api/cart/add` - Añadir al carrito
- `GET /shop/api/cart` - Obtener carrito
- `POST /shop/api/checkout` - Procesar checkout

### Admin API

Rutas definidas en: `packages/Webkul/Admin/src/Routes/rest-routes.php`

Ejemplos:
- `GET /admin/api/products` - Listar productos
- `POST /admin/api/products` - Crear producto
- `PUT /admin/api/products/{id}` - Actualizar producto
- `DELETE /admin/api/products/{id}` - Eliminar producto

## Assets y Build

### Desarrollo

Para desarrollo con hot reload:

```bash
npm run dev
```

O específicamente para Shop:
```bash
cd packages/Webkul/Shop
npm run dev
```

### Producción

Para compilar assets para producción:

```bash
npm run build
```

### Estructura de Build

Los assets compilados se generan en:

```
public/themes/
├── shop/default/build/        # Assets del Shop
└── admin/default/build/       # Assets del Admin
```

### Manifest

Vite genera un `manifest.json` que mapea los nombres de archivos originales a los archivos con hash.

## Temas

Bagisto soporta múltiples temas mediante el sistema de temas.

### Ubicación de Temas

```
packages/Webkul/Theme/src/
public/themes/
```

### Estructura de Tema

Cada tema tiene:
- Assets compilados en `build/`
- Vistas Blade (si están customizadas)
- Configuración en base de datos

### Selección de Tema

El tema se selecciona mediante middleware `Theme` que verifica:
1. Configuración del canal
2. Preferencias del usuario
3. Tema por defecto

## Traducciones Frontend

### i18n

Las traducciones frontend están en formato JSON:

```
packages/Webkul/Shop/src/Resources/assets/locales/
├── hi_IN.json
└── sin.json
```

### Uso en Vue

```javascript
import { createI18n } from 'vue-i18n';
// ... configuración
```

## Componentes UI Comunes

### DataGrid

Sistema de tablas reutilizables para mostrar datos tabulares con:
- Paginación
- Ordenamiento
- Filtros
- Exportación

### Formularios

Componentes de formularios con validación usando VeeValidate.

### Modales

Sistema de modales para diálogos y confirmaciones.

## Responsive Design

Bagisto es responsive y utiliza:
- CSS Grid y Flexbox
- Media queries
- Componentes adaptativos

## Lazy Loading

Implementación de lazy loading para:
- Imágenes de productos
- Componentes Vue
- Rutas

## Performance

### Optimizaciones

- Code splitting por Vite
- Lazy loading de componentes
- Cache de respuestas API
- Compresión de assets

### Caché de Assets

Los assets compilados incluyen hash en el nombre para cache busting:
```
app-C0G0cght.js
```

## Debugging

### Vue DevTools

En desarrollo, se pueden usar Vue DevTools para debugging.

### Console Logs

Los logs de desarrollo están disponibles en la consola del navegador.

## Integración con Blade

Las vistas Blade sirven como contenedores para los componentes Vue:

```blade
<div id="app">
    <v-product-list :products="{{ json_encode($products) }}"></v-product-list>
</div>

<script type="module">
    // Componentes Vue registrados aquí
</script>
```

Esta arquitectura híbrida permite:
- SEO friendly (contenido inicial en HTML)
- Interactividad con Vue
- Server-side rendering de datos iniciales



