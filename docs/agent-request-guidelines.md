# 📋 Guía de Peticiones para Control_Agente

Guía de buenas prácticas para realizar peticiones efectivas al agente **Control_Agente**, basada en las convenciones de commits semánticos.

---

## 🎯 Principio General

**Una petición clara y estructurada = respuesta precisa y eficiente**

Al igual que los commits bien escritos hacen que el historial del proyecto sea legible, las peticiones bien estructuradas permiten al agente entender exactamente qué necesitas y ejecutarlo de manera óptima.

---

## 📝 Estructura de una Petición

### Formato Básico

```
<tipo>[alcance]: <descripción>
```

### Ejemplo

```
feat[documentation]: crear guía de instalación en docs/
```

---

## 🏷️ Tipos de Peticiones

### `feat` - Nueva Funcionalidad
Solicita agregar una nueva característica o funcionalidad.

**Ejemplos:**
- `feat[docs]: crear guía de deployment en docs/`
- `feat[git]: agregar script para crear tags de versión`
- `feat[context]: documentar nuevo módulo de pagos`

### `fix` - Corrección
Solicita corregir un bug o error.

**Ejemplos:**
- `fix[readme]: corregir enlace roto en badges`
- `fix[git]: corregir mensaje de commit con caracteres especiales`
- `fix[docs]: actualizar comandos obsoletos en installation-guide.md`

### `docs` - Documentación
Solicita cambios solo en documentación (sin cambios de código).

**Ejemplos:**
- `docs[readme]: actualizar sección de tecnologías`
- `docs[context]: agregar diagrama de flujo en architecture.md`
- `docs[changelog]: actualizar changelog con última versión`

### `refactor` - Refactorización
Solicita mejorar estructura o organización sin cambiar funcionalidad.

**Ejemplos:**
- `refactor[structure]: reorganizar carpetas de documentación`
- `refactor[git]: mejorar mensajes de commits anteriores`
- `refactor[docs]: reorganizar secciones en README`

### `style` - Formato/Estilo
Solicita cambios de formato, espaciado, o estilo visual.

**Ejemplos:**
- `style[readme]: mejorar formato de tablas`
- `style[docs]: unificar formato de código en todas las guías`
- `style[changelog]: mejorar formato visual`

### `perf` - Rendimiento
Solicita optimizaciones de rendimiento.

**Ejemplos:**
- `perf[git]: optimizar comandos para commits rápidos`
- `perf[docs]: reducir tamaño de archivos de documentación`

### `test` - Tests
Solicita agregar o modificar tests.

**Ejemplos:**
- `test[git]: verificar que los commits siguen convenciones`
- `test[docs]: validar que todos los enlaces funcionan`

### `chore` - Mantenimiento
Solicita tareas de mantenimiento, limpieza o configuración.

**Ejemplos:**
- `chore[git]: actualizar .gitignore`
- `chore[docs]: limpiar archivos duplicados`
- `chore[config]: actualizar configuración del proyecto`

### `version` - Versionado
Solicita crear, actualizar o gestionar versiones.

**Ejemplos:**
- `version[release]: crear release v0.2.0`
- `version[changelog]: actualizar changelog para v0.2.0`
- `version[git]: crear tag v0.2.0 y push`

---

## 📍 Alcances (Scope) Comunes

### Documentación
- `docs` - Carpeta /docs
- `context` - Carpeta /context
- `readme` - README.md
- `changelog` - CHANGELOG.md

### Control de Versiones
- `git` - Operaciones Git
- `commit` - Crear commits
- `branch` - Gestión de ramas
- `release` - Releases y tags

### Estructura del Proyecto
- `structure` - Estructura de carpetas/archivos
- `config` - Archivos de configuración
- `license` - LICENSE

### Base de Datos
- `database` - Estructura de BD
- `migrations` - Migraciones

---

## ✅ Reglas para Peticiones Efectivas

### 1. Usa Verbos en Infinitivo o Imperativo

✅ **Bueno:**
```
feat[docs]: crear guía de deployment
fix[readme]: corregir enlace roto
docs[context]: actualizar arquitectura
```

❌ **Evitar:**
```
feat[docs]: creando guía de deployment (gerundio)
fix[readme]: corregí enlace roto (pasado)
docs[context]: se actualiza arquitectura (pasiva)
```

### 2. Sé Conciso en el Título (Máximo 50 caracteres)

✅ **Bueno:**
```
feat[docs]: agregar guía de instalación
fix[git]: corregir mensaje commit
```

❌ **Evitar:**
```
feat[docs]: agregar una nueva guía completa de instalación paso a paso para usuarios (muy largo)
```

### 3. Añade Contexto Adicional cuando sea Necesario

Si necesitas más detalle, usa el formato extendido:

```
feat[docs]: crear guía de deployment

Necesito que incluyas:
- Configuración para producción
- Variables de entorno necesarias
- Comandos de despliegue en servidor
- Verificación post-deployment
```

### 4. Especifica Archivos y Ubicaciones

✅ **Bueno:**
```
docs[context/database.md]: agregar sección de índices
feat[docs/installation-guide.md]: agregar troubleshooting
```

✅ **También Bueno:**
```
fix[readme]: corregir badges de versionado
docs[changelog]: actualizar con cambios de v0.2.0
```

### 5. Usa el Alcance para Contexto

Indica claramente qué parte del proyecto afecta:

```
feat[docs]: crear guía de deployment          # Nueva funcionalidad
fix[context]: corregir diagrama de flujo      # Corrección técnica
docs[readme]: actualizar tecnologías           # Solo documentación
```

---

## 📚 Ejemplos Completos

### Ejemplo 1: Nueva Documentación

```
feat[docs]: crear guía de troubleshooting

Necesito una guía que cubra:
- Problemas comunes de instalación
- Errores de base de datos
- Problemas con permisos de archivos
- Soluciones rápidas
```

### Ejemplo 2: Corrección

```
fix[readme]: corregir badge de versión

El badge muestra v0.1.0 pero la versión actual es v0.2.0
```

### Ejemplo 3: Actualización de Documentación

```
docs[context/architecture.md]: actualizar con nuevo módulo

Se ha agregado un nuevo módulo de Analytics que necesita ser documentado
en la sección de arquitectura.
```

### Ejemplo 4: Git/Versionado

```
version[release]: crear release v0.2.0

Incluye:
- Actualizar CHANGELOG.md
- Crear tag v0.2.0
- Actualizar README con nueva versión
- Push a GitHub
```

### Ejemplo 5: Refactorización

```
refactor[docs]: reorganizar estructura de guías

Reorganizar las guías en docs/ por categorías:
- installation/
- usage/
- development/
```

---

## 🎯 Variaciones Permitidas

### Para Control_Agente Específicamente

Puedes usar el prefijo `Control_Agente:` si prefieres:

```
Control_Agente: feat[docs]: crear guía de deployment
Control_Agente: fix[git]: hacer commit de cambios pendientes
```

### Múltiples Acciones

Puedes combinar acciones:

```
feat[docs]: crear guía y actualizar changelog

- Crear docs/deployment-guide.md
- Actualizar CHANGELOG.md
- Hacer commit y push
```

---

## ⚠️ Errores Comunes a Evitar

### ❌ Demasiado Vago
```
haz algo con los archivos
actualiza cosas
arregla el proyecto
```

### ❌ Demasiado Largo en el Título
```
feat[docs]: crear una nueva guía completa y exhaustiva de deployment que incluya todos los pasos necesarios para desplegar en producción con docker y configuración de variables de entorno (muy largo)
```

### ❌ Sin Tipo
```
crear guía de deployment          # Falta el tipo
docs: algo                        # Falta alcance y descripción
```

### ❌ Sin Contexto
```
fix: error                         # ¿Qué error? ¿Dónde?
feat: agregar                     # ¿Agregar qué? ¿Dónde?
```

---

## 📊 Tabla de Referencia Rápida

| Tipo | Uso | Ejemplo |
|------|-----|---------|
| `feat` | Nueva funcionalidad | `feat[docs]: crear nueva guía` |
| `fix` | Corrección de bug | `fix[readme]: corregir enlace` |
| `docs` | Solo documentación | `docs[context]: actualizar arquitectura` |
| `refactor` | Mejora de estructura | `refactor[docs]: reorganizar carpetas` |
| `style` | Formato/estilo | `style[readme]: mejorar tablas` |
| `version` | Versiones/releases | `version[release]: crear v0.2.0` |
| `chore` | Mantenimiento | `chore[git]: actualizar .gitignore` |

---

## 💡 Consejos Finales

1. **Sé específico**: Indica archivos, carpetas y ubicaciones exactas
2. **Sé conciso**: El título debe ser claro en menos de 50 caracteres
3. **Añade contexto**: Si necesitas detalles, añádelos después del título
4. **Usa el tipo correcto**: Esto ayuda al agente a entender la prioridad
5. **Especifica alcance**: Facilita saber dónde buscar o modificar

---

## 🔄 Flujo de Trabajo Recomendado

1. **Identifica el tipo**: ¿Es nueva funcionalidad, corrección, documentación?
2. **Determina el alcance**: ¿Qué parte del proyecto afecta?
3. **Escribe descripción concisa**: ¿Qué acción específica necesitas?
4. **Añade contexto si es necesario**: Detalles adicionales después del título
5. **Envía la petición**: El agente procesará de manera estructurada

---

**Última actualización**: 2025-11-02  
**Versión**: v0.1.0  
**Referencia**: Basado en [Buenas prácticas para escribir commits en Git - Midudev](https://midu.dev/buenas-practicas-escribir-commits-git/)

