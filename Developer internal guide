# Guía interna de desarrollo
Documentación operativa para alumnos y nuevas incorporaciones (mantenimiento, entornos locales y workflows).

> Nota: Este documento **no inventa** información del proyecto; únicamente reordena y clarifica lo ya documentado.

---

## Índice
- [1. Mantenimiento de proyectos](#1-mantenimiento-de-proyectos)
  - [1.1 Configuración inicial del proyecto](#11-configuración-inicial-del-proyecto)
  - [1.2 Solución de errores comunes](#12-solución-de-errores-comunes)
  - [1.3 Actualización de WordPress y PHP](#13-actualización-de-wordpress-y-php)
  - [1.4 Carbon Fields](#14-carbon-fields)
  - [1.5 Mantenimientos preventivos](#15-mantenimientos-preventivos)
  - [1.6 Migrar de Gulp a Yarn](#16-migrar-de-gulp-a-yarn)
- [2. Sage](#2-sage)
  - [2.1 Identificación de versión](#21-identificación-de-versión)
  - [2.2 Compatibilidad con Node.js](#22-compatibilidad-con-nodejs)
  - [2.3 Instalación y arranque](#23-instalación-y-arranque)
  - [2.4 Instalación de Yarn](#24-instalación-de-yarn)
  - [2.5 Scripts por versión](#25-scripts-por-versión)
  - [2.6 Gestión de versiones con NVM](#26-gestión-de-versiones-con-nvm)
- [3. Revisión de spam](#3-revisión-de-spam)
- [4. Prestashop](#4-prestashop)
  - [4.1 Configuración de entorno local](#41-configuración-de-entorno-local)
  - [4.2 Solución de errores comunes](#42-solución-de-errores-comunes)
- [5. Creación de entornos y tipos de mantenimiento](#5-creación-de-entornos-y-tipos-de-mantenimiento)
  - [5.1 Tipos de mantenimiento](#51-tipos-de-mantenimiento)
  - [5.2 Checklist de nuevo entorno](#52-checklist-de-nuevo-entorno)
- [6. PHP](#6-php)

---

# 1. Mantenimiento de proyectos

## 1.1 Configuración inicial del proyecto

### Paso 1. Descargar repositorio en `htdocs`
Clonar el repositorio correspondiente (sustituir el nombre por el del proyecto):

```bash
git clone https://github.com/hexer-dev/dob-wordpress-xxxxxxxxxx.git
```

---

### Paso 2. Seleccionar la versión de PHP del proyecto (LAMPP + LVM)

1) **Desmontar la versión previa utilizada** (desactivar la versión actual de PHP usada por LAMPP):

```bash
lvm disable
```

2) **Seleccionar la versión de LAMPP pertinente** para el proyecto (ejemplo: 7.4 para muchos WordPress antiguos):

```bash
lvm use 7.4
```

3) **Reiniciar Apache y MySQL** para aplicar los cambios:

```bash
sudo lampp restart
```

---

### Paso 3. Crear una base de datos y enlazarla a MySQL

**Opción 1 (rápida):**

```bash
echo "create database wp_xxxxxxxxx" | mysql -u root -p
```

**Opción 2 (paso a paso):**

```bash
mysql -u root -p
```

```sql
CREATE DATABASE wp_xxxxxx;
EXIT;
```

---

### Paso 4. Importar la base de datos SQL a MySQL

**Opción 1 (con barra de progreso):**

```bash
pv xxxxxxxxxxxxxxxx.sql | mysql -u root -p wp_xxxxxxxxxx
```

> `pv` permite ver el progreso en terminal.

**Opción 2 (importación directa):**

```bash
mysql -u root -p wp_xxxxxx < /home/ruta/de/la/bbdd/xxxxxxx.sql
```

#### Posibles errores en importación

| Error / síntoma | Causa probable | Solución |
|---|---|---|
| Error en línea 1 | SQL con comentarios/código al inicio | Eliminar el bloque inicial, guardar y reintentar |
| `ERROR: Unknown command '\0'` | Caracteres NULL en el archivo SQL | Limpiar el archivo y reimportar |

**Limpieza de caracteres NULL:**

```bash
tr -d '\000' < archivo_original.sql > archivo_limpio.sql
```

---

### Paso 5. Configurar enlace simbólico para `web.local`

1) **Desvincular el proyecto anterior**:

```bash
unlink web.local
```

2) **Enlazar el nuevo proyecto**:

```bash
ln -s dob-wordpress-ejemplo/ web.local
```

---

### Paso 6. Configurar `wp-config.php`

**Elementos importantes a verificar:**
- Nombre correcto de la base de datos
- Credenciales (usuario/contraseña)
- Prefijo de tablas (si existe)
- Debug en desarrollo (si procede)

**Ejemplo:**

```php
define('DB_NAME', 'wp_xxxxxxxxx');
define('DB_USER', 'root');
define('DB_PASSWORD', '');
define('DB_HOST', 'localhost');
define('DB_CHARSET', 'utf8');
define('DB_COLLATE', '');

// Debug en desarrollo
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
```

---

### Paso 7. Verificar funcionamiento
Abrir `web.local` en el navegador y comprobar que carga correctamente.

---

## 1.2 Solución de errores comunes

### Navegación fallida entre páginas
**Síntoma:** los enlaces internos no funcionan.  
**Solución:**
1. WordPress Admin → **Ajustes** → **Enlaces permanentes**
2. Click en **Guardar cambios**
3. Esto regenera `.htaccess`

---

### Bloques que no aparecen en WordPress
**Causa probable:** `.htaccess` / permalinks.  
**Solución:** regenerar enlaces permanentes (igual que el punto anterior).

---

### Bloques corruptos (Carbon Fields)
**Síntoma:** bloques personalizados dejan de funcionar tras una actualización.  
**Causa probable:** dependencias de Carbon Fields desactualizadas.  
**Solución:**

```bash
cd wp-content/themes/nombre_del_tema
composer update
```

---

## 1.3 Actualización de WordPress y PHP

> **Orden crítico para evitar colapsos del sitio.**

### Proceso de actualización gradual
1. Actualizar **plugins** compatibles con la versión actual
2. Actualizar **PHP** gradualmente (ej.: 5.6 → 7.0 → 7.4)
3. **Verificar funcionamiento** tras cada cambio
4. Actualizar plugins restantes (pueden aparecer nuevas actualizaciones)
5. Actualizar **tema** activo
6. Actualizar **WordPress Core**
7. Verificación final y testing completo

### Recomendaciones
- Backup completo antes de iniciar
- Probar primero en entorno de desarrollo
- Documentar cada paso y errores
- Tener plan de rollback

---

## 1.4 Carbon Fields

### Objetivo
Entender el flujo completo:
1) registro del campo → 2) gestión en backoffice → 3) recuperación en controller → 4) renderizado en Blade.

---

### 1) El registro (definición del campo)
**Fichero:** `app/Options/Options.php` (o donde se definan `theme_options`).

Aquí definimos la existencia del campo y su **ID único**.

```php
->add_tab(__('Contacto'), [
    Field::make('text', 'ID_UNICO_DEL_CAMPO', __('Etiqueta Visible')),
])
```

**Conceptos clave:**
- **ID único**: nombre técnico con el que se recupera el dato (ej.: `theme_shortcode_form_header`)
- **Tipo de campo**: `text`, `image`, `complex`, `select`, etc.

---

### 2) La gestión (Backoffice)
**Lugar:** panel de administración de WordPress.

- Tras registrar el código, el campo aparecerá en el panel
- Navegar a la sección correspondiente (ej.: Ajustes del Sitio > Contacto)
- Rellenar el campo (ej.: shortcode de Contact Form 7)
- **Guardar** (sin guardar, no persiste en base de datos)

---

### 3) El procesamiento (Controller)
**Fichero:** `app/Controllers/App.php` (o controller específico).

#### A) Crear la función de recuperación

```php
public function getMiNuevoDato() : string
{
    return carbon_get_theme_option('ID_UNICO_DEL_CAMPO') ?: '';
}
```

#### B) Inyectar en la vista (método `with()`)

```php
public function with()
{
    return [
        'variableEnBlade' => $this->getMiNuevoDato(),
    ];
}
```

---

### 4) Renderizado (Frontend)
**Fichero:** `resources/views/partials/header.blade.php` (o la vista correspondiente).

```php
<div class="servicios-content__form">
  @if ($getFormHeader)
    <section class="form-header">
      {{-- Usamos {!! !!} para renderizar el shortcode como HTML --}}
      {!! do_shortcode($getFormHeader) !!}
    </section>
  @endif
</div>
```

---

### Guía de resolución de problemas (debug)
Si el elemento no aparece en el navegador, verificar en este orden:

1. **ID**: ¿El ID en `Options.php` coincide EXACTO con `carbon_get_theme_option()`?
2. **Blade**: ¿Se añadió la variable al array `with()` del controller?
3. **Persistencia**: ¿Al recargar admin el dato sigue guardado?
4. **Sintaxis**: `{{ }}` para texto, `{!! !!}` para shortcodes/HTML.

---

## 1.5 Mantenimientos preventivos

**Frecuencia:** cada 3 meses

### Checklist
- [ ] Actualizaciones menores de WordPress, tema y plugins
- [ ] Menús y navegación: verificar enlaces
- [ ] Maquetación: revisar responsive
- [ ] Formularios: probar envío y recepción
- [ ] Rendimiento: analizar velocidad de carga
- [ ] Seguridad: verificar plugins de seguridad
- [ ] Backups: confirmar que se generan correctamente
- [ ] SSL: verificar certificado válido

**Documentación obligatoria:** anotar en comentarios de la tarea **OK/KO** por punto, incluyendo acciones correctivas realizadas.

---

## 1.6 Migrar de Gulp a Yarn

> Nota sobre GitHub: si usas emojis en títulos, los anchors pueden variar. Para enlaces estables, **evita emojis en headings** o usa headings sin emojis.

### 1) Estructura recomendada

Dentro del tema:

```text
mi-tema/
│
├── css/
│   ├── sass/          ← SOLO .sass / .scss (fuentes)
│   │   ├── base.sass
│   │   ├── custom.sass
│   │   └── all.sass   ← entrypoint
│   │
│   ├── css/           ← compilado intermedio (se regenera)
│   │   └── all.css
│   │
│   └── min.css        ← archivo final que carga WordPress
│
├── package.json
├── postcss.config.js
└── node_modules/
```

### Nunca mezclar
- `.css` dentro de `css/sass/`
- `.min.css` dentro de `css/css/`

---

### 2) Instalar dependencias
Desde la raíz del tema:

```bash
yarn init -y
yarn add -D sass postcss postcss-cli postcss-import cssnano autoprefixer concurrently
```

---

### 3) Crear el entrypoint SASS

**Si usas `.sass` (indented):** `css/sass/all.sass`

```sass
@use "base"
@use "custom"
```

**Si usas `.scss`:** `css/sass/all.scss`

```scss
@use "base";
@use "custom";
```

Puedes añadir más imports según el proyecto:

```sass
@use "variables"
@use "mixins"
@use "header"
@use "footer"
```

---

### 4) `postcss.config.js`
Crear en la raíz del tema:

```js
module.exports = {
  plugins: [
    require("postcss-import"),
    require("autoprefixer"),
    require("cssnano")({ preset: "default" }),
  ],
};
```

---

### 5) `package.json` plantilla final reutilizable

#### Para `.sass`

```json
{
  "name": "mi-tema",
  "version": "1.0.0",
  "private": true,
  "license": "MIT",
  "scripts": {
    "scss": "sass css/sass/all.sass css/css/all.css --no-source-map",
    "minify": "postcss css/css/all.css -o css/min.css",
    "build": "yarn scss && yarn minify",
    "watch": "concurrently \"sass --watch css/sass/all.sass:css/css/all.css --no-source-map\" \"postcss css/css/all.css -o css/min.css --watch\""
  },
  "devDependencies": {
    "autoprefixer": "^10.4.24",
    "concurrently": "^9.2.1",
    "cssnano": "^7.1.2",
    "postcss": "^8.5.6",
    "postcss-cli": "^11.0.1",
    "postcss-import": "^16.1.1",
    "sass": "^1.97.1"
  },
  "packageManager": "yarn@1.22.19"
}
```

#### Para `.scss`
Solo cambia `all.sass → all.scss` en los scripts.

---

### 6) Uso diario
Compilar una vez:

```bash
yarn build
```

Desarrollo con `watch`:

```bash
yarn watch
```

---

### 7) `functions.php`
Si el tema antiguo ya carga `min.css`, no hay que tocar nada:

```php
wp_enqueue_style('cssTema', get_template_directory_uri() . '/css/min.css');
```

---

### 8) Limpieza recomendada (migración desde Gulp)

Eliminar:
- `gulpfile.js`
- `node_modules` antiguos
- `package-lock.json` si usaba npm
- cualquier `*.min.css` generado por Gulp

Luego:

```bash
rm -rf node_modules
yarn install
```

---

### Errores típicos

- **“Input Error: You must pass a valid list of files to parse”**  
  No existe `css/css/all.css` → revisa que el entrypoint `all.sass` existe y que la ruta del script es correcta.

- **“Cannot find module 'autoprefixer'”**  
  Falta dependencia → `yarn add -D autoprefixer`

- **Duplicados `.min.min.css`**  
  Estás minificando `*.min.css` → solo minificar `all.css`.

---

# 2. Sage

## 2.1 Identificación de versión
Revisar `package.json`:

```json
"@roots/sage": "^6.12.3"
```

```json
"laravel-mix": "^x.x.x"
```

**Correspondencias:**
- `@roots/sage 6.x.x` → **Sage 10** (usa Bud)
- Presencia de `laravel-mix` → **Sage 9** (usa Mix)

---

## 2.2 Compatibilidad con Node.js
- **Sage 9**: Node.js 10–14 (recomendado: v12)
- **Sage 10**: Node.js 16+ (recomendado: v16 o v18 LTS)

---

## 2.3 Instalación y arranque

### ¿Hay que ejecutar `yarn` antes de nada?
**Sí**, siempre al clonar/iniciar un proyecto por primera vez.

**Ubicación:**

```bash
cd wp_content/themes/nombre_proyecto
```

**Comando:**

```bash
yarn
```

**Esto hace:**
- Instala dependencias del `package.json`
- Prepara comandos `yarn dev`, `yarn build`, etc.
- Configura Bud/Mix y compiladores

---

## 2.4 Instalación de Yarn

### Error común
Si el sistema sugiere instalar `cmdtest`, **NO** hacerlo (no es el Yarn correcto).

### Opciones correctas

**Opción 1 (recomendada si Node ≥ 16.10): Corepack**

```bash
corepack enable
corepack prepare yarn@stable --activate
yarn -v
```

**Opción 2: global con npm**

```bash
npm install -g yarn
yarn -v
```

---

## 2.5 Scripts por versión

### Sage 9 (Laravel Mix)
Ejemplo de scripts:

```json
{
  "scripts": {
    "build": "webpack --progress --config resources/assets/build/webpack.config.js",
    "build:production": "webpack --env.production --progress --config resources/assets/build/webpack.config.js",
    "start": "webpack --hide-modules --watch --config resources/assets/build/webpack.config.js",
    "rmdist": "rimraf dist",
    "lint": "npm run -s lint:scripts && npm run -s lint:styles"
  }
}
```

**Workflow Sage 9**
1. Desarrollo: `yarn start` (watch)
2. Build final: `yarn build`
3. Producción: `yarn build:production`
4. Git: `git add .` → `git commit` → `git push`

---

### Sage 10 (Bud)
Ejemplo de scripts:

```json
{
  "scripts": {
    "dev": "bud dev",
    "build": "bud build",
    "translate": "yarn translate:pot && yarn translate:update"
  }
}
```

> Puedes migrar y utilizar Yarn revisando el apartado [Migrar de Gulp a Yarn](#16-migrar-de-gulp-a-yarn).

**Workflow Sage 10**
1. Desarrollo: `yarn dev` (watch / hot reload)
2. Build final: `yarn build`
3. Git: `git add .` → `git commit` → `git push`

---

## 2.6 Gestión de versiones con NVM

### ¿Qué es NVM?
**Node Version Manager** permite:
- Instalar múltiples versiones de Node.js
- Cambiar de versión según proyecto
- Evitar conflictos entre proyectos

### Comandos esenciales

```bash
node -v
nvm list
nvm install 16
nvm use 16
cat package.json | grep -A 5 '"engines"'
```

### Workflow completo con NVM (proyecto Sage)

```bash
cd wp_content/themes/nombre_proyecto
cat package.json | grep '"node":'
nvm use 16
yarn
yarn dev   # o yarn start según versión
```

---

# 3. Revisión de spam

## Herramientas anti-spam obligatorias

### Checklist
- [ ] Honeypot activado en formularios
- [ ] reCAPTCHA configurado (v2 o v3)
- [ ] Akismet activo y configurado
- [ ] Flamingo instalado (registro de mensajes)

### Plugins (referencia)
- Contact Form 7: Honeypot + reCAPTCHA
- Forminator: reCAPTCHA integrado
- WPForms: protección anti-spam incluida

### Proceso de revisión
1. Configurar herramientas anti-spam
2. Cambiar tarea a **REVISANDO**
3. Monitorizar durante 5–7 días
4. Documentar resultados (reducción spam)
5. Cerrar tarea con reporte

### reCAPTCHA: notas
- Clave asociada a **grupo de dominios**
- Incluir dominio principal y subdominios
- Verificar funcionamiento en formularios

## Mantenimiento adicional

### Caché
- Borrar caché de WP Rocket tras cambios
- Verificar que formularios funcionan con/ sin caché

### Logs
- Revisar `/var/logs` antes de subir cambios
- Eliminar logs con errores críticos
- Documentar errores recurrentes

---

# 4. Prestashop

## 4.1 Configuración de entorno local

### 1) Descargar repositorio

```bash
git clone https://github.com/hexer-dev/dob-prestashop-xxxxxxxxx.git
cd dob-prestashop-xxxxxxxxx
```

---

### 2) Configuración de dominio falso

#### Modificar virtual hosts
Editar: `/opt/lampp/etc/extra/httpd-vhosts.conf`

```xml
######################### URL PROYECTO XXX #########################
<VirtualHost *:80>
    ServerAdmin xxxxxxx@hexer.dev
    DocumentRoot "/opt/lampp/htdocs/dob-prestashop-xxxxxxxx"
    ServerName xxxxxxx.com
    ServerAlias www.xxxxxxx.com
    ErrorLog "logs/prestashop-error_log"
</VirtualHost>

<VirtualHost *:443>
    DocumentRoot "/opt/lampp/htdocs/dob-prestashop-xxxxxxxxx"
    ServerName xxxxxxx.com
    ServerAlias www.xxxxxxx.com
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/xxxxxxx.com-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/xxxxxxx.com-selfsigned.key
</VirtualHost>
```

#### Crear certificados SSL

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/xxxxxxx.com-selfsigned.key \
  -out /etc/ssl/certs/xxxxxxx.com-selfsigned.crt
```

#### Configurar hosts
Editar `/etc/hosts`:

```text
127.0.0.1 xxxxxxx.com www.xxxxxxx.com
```

---

### 3) Configurar base de datos
Editar `app/config/parameters.php`:

```php
<?php return array (
  'parameters' => array (
    'database_host' => 'localhost',
    'database_port' => '',
    'database_name' => 'prestashop_xxxxxx',
    'database_user' => 'root',
    'database_password' => '',
    'database_prefix' => 'ps_',
    'database_engine' => 'InnoDB',
    'mailer_transport' => 'smtp',
    'mailer_host' => 'localhost',
    'mailer_user' => null,
    'mailer_password' => null,
    'secret' => 'xxxxxxxxxxxxx',
    'ps_caching' => 'CacheMemcache',
    'ps_cache_enable' => false,
    'ps_creation_date' => '2024-01-01',
  ),
);
```

---

### 4) Vaciar caché
Eliminar la carpeta `/var/cache` (del proyecto).

---

## 4.2 Solución de errores comunes

### Activar modo Debug
Para ver el error real:

1. Ir a `/config/define.inc.php`
2. Cambiar `_PS_MODE_DEV_` de `false` a `true`

---

### Proyecto no carga en local
**Causa:** URL incorrecta en base de datos  
**Solución:**
1. phpMyAdmin
2. Tabla `ps_shop_url`
3. Cambiar dominio por el local (ej.: `proyecto.local`)

---

### Error 404
Verificar:
- `ServerName` y `ServerAlias` en vhosts (sin `/` al final)
- Existencia de carpetas: `var/cache`, `var/logs`, `var/sessions`

---

### `Directory not writable`

```bash
mkdir -p var/logs var/cache var/sessions
chmod -R 775 var/
chown -R www-data:www-data var/
```

---

### Error 500 / caché corrupto

```bash
rm -rf var/cache/prod/
rm -rf var/cache/*
php bin/console cache:clear --env=prod
```

---

# 5. Creación de entornos y tipos de mantenimiento

## 5.1 Tipos de mantenimiento

| Tipo | Nivel | Frecuencia | Incluye |
|---|---:|---|---|
| Mantenimiento Básico | 1 | Mensual | Plugins menores, verificación básica, limpieza spam, backup básico, formularios |
| Mantenimiento Profesional | 2 | Trimestral | Auditoría seguridad, optimización rendimiento, actualización completa, análisis logs, prueba backup/restore, informe técnico |

---

## 5.2 Checklist de nuevo entorno

### Configuración inicial
- [ ] Repositorio clonado correctamente
- [ ] Versión PHP correcta configurada
- [ ] Base de datos creada e importada
- [ ] Configuración actualizada (wp-config / parameters.php / etc.)
- [ ] Permisos de archivos configurados
- [ ] SSL/certificados generados (si aplica)

### Verificación funcional
- [ ] Sitio carga correctamente
- [ ] Navegación entre páginas funciona
- [ ] Formularios envían emails
- [ ] Imágenes se muestran correctamente
- [ ] Admin accesible y funcional

### Documentación
- [ ] Credenciales documentadas
- [ ] Configuraciones especiales anotadas
- [ ] Errores conocidos listados
- [ ] Procedimientos específicos documentados

---

# 6. PHP

Puede darse el caso de que un proyecto solicite instalación de PHP a nivel de sistema (por ejemplo, al ejecutar `composer update`).

**Criterio del equipo:**
- No instalar PHP a nivel de sistema si el entorno debe gestionarse con **LAMPP + LVM**.
- En su lugar, asegurarse de que los enlaces apunten a la carpeta de LAMPP, ya que LVM administra automáticamente la versión levantada para cada proyecto.

Si por error se instala PHP a nivel de sistema:
- Eliminar la instalación del sistema
- Reconfigurar enlaces para que apunten a LAMPP (gestionado por LVM)

---
