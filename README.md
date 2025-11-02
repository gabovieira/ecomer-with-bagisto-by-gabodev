<p align="center">
  <h1 align="center">🛍️ Ecomer with Bagisto by Gabodev</h1>
  <p align="center">Aplicación web e-commerce desarrollada con Bagisto (Laravel + Vue.js)</p>
  <p align="center">Solución modular, escalable y totalmente documentada</p>
</p>

<p align="center">
  <a href="https://github.com/gabovieira/ecomer-with-bagisto-by-gabodev">
    <img src="https://img.shields.io/badge/build-passing-brightgreen?style=for-the-badge" alt="Build Status">
  </a>
  <a href="https://github.com/gabovieira/ecomer-with-bagisto-by-gabodev/releases">
    <img src="https://img.shields.io/badge/version-v0.1.0-blue?style=for-the-badge" alt="Version">
  </a>
  <a href="https://github.com/gabovieira/ecomer-with-bagisto-by-gabodev/blob/main/LICENSE">
    <img src="https://img.shields.io/badge/license-MIT-green?style=for-the-badge" alt="License">
  </a>
  <a href="https://laravel.com">
    <img src="https://img.shields.io/badge/Laravel-11.0-red?style=for-the-badge&logo=laravel" alt="Laravel">
  </a>
  <a href="https://vuejs.org">
    <img src="https://img.shields.io/badge/Vue.js-3.0-4FC08D?style=for-the-badge&logo=vue.js" alt="Vue.js">
  </a>
  <a href="https://www.bagisto.com">
    <img src="https://img.shields.io/badge/Bagisto-eCommerce-orange?style=for-the-badge" alt="Bagisto">
  </a>
</p>

---

## 🌟 Descripción General

**Ecomer with Bagisto by Gabodev** es una solución completa de e-commerce construida sobre el framework Bagisto, que combina la potencia de Laravel 11 con la interactividad de Vue.js. Este proyecto ofrece una base sólida, modular y escalable para desarrollar tiendas en línea profesionales con características avanzadas de gestión de productos, pedidos, clientes y pagos.

### 🎯 Objetivo del Proyecto

Proporcionar una plataforma e-commerce:
- ✅ **Modular**: Arquitectura basada en módulos independientes
- ✅ **Escalable**: Preparada para crecer con tu negocio
- ✅ **Documentada**: Documentación técnica y funcional completa
- ✅ **Mantenible**: Código limpio siguiendo mejores prácticas
- ✅ **Versionada**: Control de versiones con Git y Semantic Versioning

---

## ⚙️ Tecnologías Principales

| Categoría | Tecnología | Versión | Propósito |
|-----------|-----------|---------|-----------|
| **Backend** | Laravel | 11.0 | Framework PHP principal |
| **Frontend** | Vue.js | 3.0 | Framework JavaScript progresivo |
| **E-commerce** | Bagisto | 2.3+ | Framework de comercio electrónico |
| **Lenguaje** | PHP | 8.2+ | Lenguaje de programación |
| **Base de Datos** | MySQL | 8.0+ | Sistema de gestión de base de datos |
| **Cache** | Redis | - | Sistema de caché y sesiones |
| **Búsqueda** | Elasticsearch | 8.10+ | Motor de búsqueda avanzada |
| **Build Tool** | Vite | - | Herramienta de construcción |
| **Autenticación** | Laravel Sanctum | 4.0 | Autenticación API |
| **Container** | Docker | - | Contenedores para desarrollo |

---

## 🚀 Instalación Paso a Paso

### 📋 Requisitos Previos

- PHP 8.2 o superior
- Composer
- Node.js y npm
- MySQL 8.0+
- Git

### 🔧 Pasos de Instalación

#### 1. Clonar el Repositorio

```bash
git clone https://github.com/gabovieira/ecomer-with-bagisto-by-gabodev.git
cd ecomer-with-bagisto-by-gabodev
```

#### 2. Instalar Dependencias PHP

```bash
composer install
```

#### 3. Configurar Variables de Entorno

```bash
cp .env.example .env
php artisan key:generate
```

Edita el archivo `.env` con tus credenciales de base de datos:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=tu_base_datos
DB_USERNAME=tu_usuario
DB_PASSWORD=tu_contraseña
```

#### 4. Ejecutar Migraciones

```bash
php artisan migrate
php artisan db:seed
```

#### 5. Instalar Dependencias Frontend

```bash
npm install
```

#### 6. Compilar Assets

```bash
npm run build
```

O para desarrollo con hot-reload:

```bash
npm run dev
```

#### 7. Iniciar el Servidor

```bash
php artisan serve
```

Accede a la aplicación en: `http://localhost:8000`

---

## 🧩 Estructura del Proyecto

```
ecomer-with-bagisto-by-gabodev/
├── app/                    # Código de aplicación personalizado
│   ├── Http/
│   │   ├── Controllers/    # Controladores custom
│   │   └── Middleware/     # Middleware custom
│   └── Providers/          # Service providers
│
├── bootstrap/              # Archivos de arranque de Laravel
│
├── config/                 # Archivos de configuración
│
├── context/                # 📚 Documentación técnica
│   ├── overview.md         # Visión general del proyecto
│   ├── architecture.md     # Arquitectura del sistema
│   ├── backend.md          # Detalles del backend
│   ├── frontend.md         # Detalles del frontend
│   ├── database.md         # Estructura de base de datos
│   ├── deployment.md       # Guía de despliegue
│   └── config.md           # Configuración avanzada
│
├── docs/                   # 📘 Guías funcionales
│   ├── installation-guide.md
│   ├── usage-guide.md
│   ├── developer-notes.md
│   └── changelog-summary.md
│
├── database/               # Migraciones y seeders
│   ├── migrations/
│   └── seeders/
│
├── packages/               # Módulos Bagisto
│   └── Webkul/
│       ├── Admin/          # Panel de administración
│       ├── Core/           # Núcleo del sistema
│       ├── Shop/           # Frontend de la tienda
│       ├── Product/        # Gestión de productos
│       ├── Sales/           # Gestión de ventas
│       └── ...
│
├── public/                 # Archivos públicos
│
├── resources/              # Recursos (views, assets)
│
├── routes/                 # Definiciones de rutas
│
├── storage/                # Archivos almacenados
│
├── tests/                  # Tests automatizados
│
├── .gitignore             # Archivos ignorados por Git
├── CHANGELOG.md           # Historial de cambios
├── LICENSE                # Licencia MIT
└── README.md              # Este archivo
```

---

## 💡 Características Principales

| Característica | Descripción | Estado |
|----------------|-------------|--------|
| 🛒 **Gestión de Productos** | CRUD completo de productos con variantes, atributos y categorías | ✅ |
| 👥 **Gestión de Clientes** | Sistema completo de registro, autenticación y perfiles | ✅ |
| 📦 **Gestión de Pedidos** | Procesamiento y seguimiento de pedidos en tiempo real | ✅ |
| 💳 **Múltiples Métodos de Pago** | Integración con PayPal, Stripe y otros gateways | ✅ |
| 🚚 **Sistema de Envíos** | Configuración flexible de métodos y tarifas de envío | ✅ |
| 📊 **Panel de Administración** | Dashboard completo con estadísticas y reportes | ✅ |
| 🔍 **Búsqueda Avanzada** | Búsqueda con Elasticsearch y filtros múltiples | ✅ |
| 🌍 **Multi-idioma** | Soporte para múltiples idiomas y monedas | ✅ |
| 🎨 **Temas Personalizables** | Sistema de temas flexible y fácil de personalizar | ✅ |
| 📱 **Responsive Design** | Interfaz adaptativa para móviles y tablets | ✅ |
| 🔐 **Seguridad Avanzada** | Implementación de mejores prácticas de seguridad | ✅ |
| 📈 **Analytics y Reportes** | Estadísticas detalladas de ventas y productos | ✅ |

---

## 🧱 Documentación Técnica

La carpeta `/context` contiene documentación técnica detallada sobre:

- **`overview.md`**: Visión general del proyecto, stack tecnológico y propósito
- **`architecture.md`**: Arquitectura del sistema, capas y relaciones
- **`backend.md`**: Detalles del backend, APIs y servicios
- **`frontend.md`**: Estructura del frontend, componentes Vue.js
- **`database.md`**: Esquema de base de datos y relaciones
- **`deployment.md`**: Guías de despliegue en diferentes entornos
- **`config.md`**: Configuración avanzada del sistema

> 📖 **Para más detalles técnicos**, consulta los archivos en `/context`

---

## 📘 Guías Funcionales

La carpeta `/docs` contiene guías prácticas para:

- **`installation-guide.md`**: Guía detallada de instalación paso a paso
- **`usage-guide.md`**: Manual de uso para administradores y usuarios
- **`developer-notes.md`**: Notas para desarrolladores que trabajen en el proyecto
- **`changelog-summary.md`**: Resumen de cambios por versión

> 📘 **Para guías prácticas**, consulta los archivos en `/docs`

---

## 👤 Autor

**José Gabriel Vieira (Gabodev)**

- 🌐 GitHub: [@gabovieira](https://github.com/gabovieira)
- 📧 Email: [Añadir tu email si lo deseas]
- 💼 Perfil: Desarrollador Full Stack especializado en Laravel y Vue.js

---

## 🪪 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo [LICENSE](LICENSE) para más detalles.

```
MIT License

Copyright (c) 2025 José Gabriel Vieira (Gabodev)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction...
```

---

## 🧾 Versionado

Este proyecto utiliza [Semantic Versioning](https://semver.org/lang/es/) (SemVer) para el control de versiones.

**Formato**: `MAJOR.MINOR.PATCH` (ej: `v1.0.0`)

- **MAJOR**: Cambios incompatibles con versiones anteriores
- **MINOR**: Nuevas funcionalidades compatibles hacia atrás
- **PATCH**: Correcciones de bugs compatibles hacia atrás

### 🔄 Convención de Commits

Utilizamos [Conventional Commits](https://www.conventionalcommits.org/) para mantener un historial claro:

- `feat`: Nueva funcionalidad
- `fix`: Corrección de bug
- `docs`: Documentación
- `style`: Formato, sin cambios en código
- `refactor`: Refactorización
- `test`: Tests
- `chore`: Mantenimiento

**Ejemplo:**
```bash
git commit -m "feat: agregar sistema de cupones de descuento"
```

---

## 📈 Estado del Proyecto

🟡 **En Desarrollo** - Versión `v0.1.0`

- ✅ Estructura base implementada
- ✅ Documentación técnica completa
- ✅ Control de versiones configurado
- 🚧 Funcionalidades adicionales en desarrollo
- 📋 Roadmap pendiente de publicación

---

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Por favor:

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'feat: Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

---

## 📞 Soporte

Para soporte, abre un issue en el [repositorio de GitHub](https://github.com/gabovieira/ecomer-with-bagisto-by-gabodev/issues).

---

<p align="center">
  <strong>Desarrollado con ❤️ por <a href="https://github.com/gabovieira">Gabodev</a></strong>
</p>

<p align="center">
  ⭐ Si te gusta este proyecto, ¡dale una estrella!
</p>
