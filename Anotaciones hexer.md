# ÍNDICE
- [1. Mantenimiento de proyectos](#mantenimiento-de-proyectos)
  - [1.1 Actualización de Wordpress y PHP](#actualizacion-de-wordpress-y-php)
    - [1.1.1 Carbon Fields - Flujo](#carbon-fields)
  - [1.2 Mantenimientos preventivos](#mantenimientos-preventivos)
  - [1.3 Migrar de Gulp a Yarn](#migrar-de-gulp-a-yarn)
  - [1.4 Errores]
    - [1.4.1 Error_log](#error_log)
    - [1.4.2 Error SSL](#error-ssl)
- [2. Sage](#sage)
  - [Yarn](#he-de-ejecutar-yarn-antes-de-nada)
  - [Node](#node)
- [3. Revisión de Spam](#revisión-de-spam)
- [4. Prestashop](#prestashop)
- [5. Creación de entornos - nuevos proyectos](#creación-de-entornos)
- [6. PHP](#php)

---

# MANTENIMIENTO DE PROYECTOS 

## Tipos de mantenimiento

### Mantenimiento Básico (Nivel 1)  
**Frecuencia**: Trimestral
**Incluye**:
- Actualizaciones menores de la web y sus componentes (plugins)
- Menús y navegación
- Revisión de maquetación en web y responsive
- Verificación de formularios
- Verificación de funcionamiento básico

### Mantenimiento Profesional (Nivel 2)
**Frecuencia**: Trimestral
**Incluye**:
- Auditoría completa de seguridad
- Optimización de rendimiento
- Actualización completa de componentes
- Análisis de logs detallado
- Backup y restauración test
- Informe técnico completo

## Checklist de nuevo entorno

### Configuración inicial:
- [ ] **Repositorio** clonado correctamente
- [ ] **PHP** versión correcta configurada
- [ ] **Base de datos** creada e importada
- [ ] **Archivos de configuración** actualizados
- [ ] **Permisos** de archivos configurados
- [ ] **SSL** certificados generados (si aplica)

### Verificación funcional:
- [ ] **Sitio carga** correctamente
- [ ] **Navegación** funciona entre páginas  
- [ ] **Formularios** envían emails
- [ ] **Imágenes** se muestran correctamente
- [ ] **Admin** accesible y funcional

### Documentación:
- [ ] **Credenciales** documentadas
- [ ] **Configuraciones** especiales anotadas
- [ ] **Errores** conocidos listados
- [ ] **Procedimientos** específicos documentados

<br>

# MONTAJE DE ENTORNO LOCAL
      

## Configuración inicial del proyecto

### PASO 1. Descargar repositorio en htdocs
Utilizamos la directiva `git clone https://github.com/hexer-dev/dob-wordpress-xxxxxxxxxx.git` (sustituimos el nombre del repositorio por el del proyecto a editar).

### PASO 2. Seleccionar la versión de PHP del proyecto
1. **Desmontar la versión previa utilizada** (Desactivamos la versión actual de PHP usada por LAMPP)
   ```bash
   lvm disable
   ```

2. **Seleccionar la versión de LAMPP pertinente** para el proyecto (ejemplo 7.4 necesaria para muchos proyectos WordPress).
   ```bash
   lvm use 7.4
   ```

3. **Reiniciar Apache y MySQL** para aplicar los cambios.
   ```bash
   sudo lampp restart
   ```

### PASO 3. Crear una base de datos y enlazarla a MySQL
1. **Creación de base de datos con terminal**
   - **Opción 1** (rápida):
     ```bash
     echo "create database wp_xxxxxxxxx" | mysql -u root -p
     ```
   
   - **Opción 2** (paso a paso):
     ```bash
     mysql -u root -p
     ```
     ```sql
     CREATE DATABASE wp_xxxxxx;
     EXIT;
     ```

### PASO 4. Importar la base de datos SQL a MySQL
- **Opción 1** (con barra de progreso):
  ```bash
  pv xxxxxxxxxxxxxxxx.sql | mysql -u root -p wp_xxxxxxxxxx
  ```
  *El comando `pv` permite ver un progreso en la terminal.*

- **Opción 2** (importación directa):
  ```bash
  mysql -u root -p wp_xxxxxx < /home/ruta/de/la/bbdd/xxxxxxx.sql
  ```

#### Posibles errores en importación:
- **Error en línea 1**: El SQL puede contener código comentado al inicio. Elimínalo, guarda y vuelve a importar.
- **ERROR: Unknown command '\0'**: El archivo contiene caracteres NULL. Limpia el archivo antes de importar:
  ```bash
  # Limpiar caracteres NULL
  tr -d '\000' < archivo_original.sql > archivo_limpio.sql
  ```

### PASO 5. Configurar enlace simbólico para web.local
1. **Desvincular proyecto anterior**:
   ```bash
   unlink web.local
   ```

2. **Enlazar nuevo proyecto**:
   ```bash
   ln -s dob-wordpress-ejemplo/ web.local
   ```

### PASO 6. Configurar wp-config.php
**Elementos importantes a verificar**:
- Nombre correcto de la base de datos
- Credenciales de acceso (usuario/contraseña)
- Prefijo de tablas (si existe)
- Configuración de debug (activar en desarrollo)

**Ejemplo de configuración**:
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

### PASO 7. Verificar funcionamiento
Abre el navegador y accede a `web.local` para comprobar que todo funciona correctamente.

## Solución de errores comunes

### 🔧 Navegación fallida entre páginas
**Síntoma**: Los enlaces internos del sitio no funcionan.
**Solución**: 
1. Ir a WordPress Admin → **Ajustes** → **Enlaces permanentes**
2. Hacer clic en **Guardar cambios**
3. Esto regenerará el archivo `.htaccess`

### 🔧 Bloques que no aparecen en WordPress
**Causa**: Problemas con el archivo `.htaccess`
**Solución**: Regenerar enlaces permanentes (mismo proceso que el error anterior)

### 🔧 Bloques corruptos (Carbon Fields)
**Síntoma**: Bloques personalizados dejan de funcionar tras actualización
**Causa**: Dependencias de Carbon Fields desactualizadas
**Solución**:
```bash
# Navegar a la carpeta del tema
cd wp-content/themes/nombre_del_tema
# Actualizar dependencias de Composer
composer update
```

---
<br>

## ACTUALIZACIÓN DE WORDPRESS Y PHP

**⚠️ Orden crítico para evitar colapsos del sitio:**

### Proceso de actualización gradual:
1. **Actualizar plugins** que sean compatibles con la versión actual
2. **Actualizar PHP** gradualmente (ej: de 5.6 → 7.0 → 7.4)
3. **Verificar funcionamiento** tras cada cambio
4. **Actualizar plugins restantes** (aparecerán nuevas actualizaciones disponibles)
5. **Actualizar tema** activo
6. **Actualizar WordPress Core**
7. **Verificación final** y testing completo

### Recomendaciones adicionales:
- **Hacer backup completo** antes de iniciar
- **Probar en entorno de desarrollo** primero
- **Documentar cada paso** y posibles errores
- **Tener plan de rollback** preparado

---

## CARBON FIELDS



### 1. El Registro (Definición del Campo)

**Fichero:** `app/Options/Options.php` (o donde se definan las `theme_options`)

Es el primer paso. Aquí definimos la existencia del campo y su ID único.

```php
// Ejemplo: Añadir un campo de texto en una pestaña existente
->add_tab(__('Contacto'), [
    Field::make('text', 'ID_UNICO_DEL_CAMPO', __('Etiqueta Visible')),
])
```
ID Único: Es el nombre técnico que usaremos para recuperar el dato (ej: theme_shortcode_form_header).
Tipo de campo: Puede ser text, image, complex, select, etc.


### 2. La Gestión (Backoffice)

Lugar: Panel de Administración de WordPress

- Una vez registrado el código, aparecerá visualmente en el panel. 
- Navega hasta la sección (ej: Ajustes del Sitio > Contacto).
- Rellena el campo con la información (ej: El shortcode de Contact Form 7).

Importante: Haz clic en Guardar. Sin este paso, la base de datos no tendrá información que enviar al frontend.


### 3. El Procesamiento (Controller)

Fichero: app/Controllers/App.php (o el controlador específico de la vista)

Sage separa la lógica del diseño. El controlador actúa como puente entre la base de datos y la vista.

A. Crear la función de recuperación
```php
public function getMiNuevoDato() : string
{
    // Recupera el valor guardado usando el ID único del paso 1
    return carbon_get_theme_option('ID_UNICO_DEL_CAMPO') ?: '';
}
```
B. Inyectar en la vista (Método with)
Para que la variable esté disponible en el archivo .blade.php, debe incluirse en el array de retorno del método with().

```php
public function with()
{
    return [
        'variableEnBlade' => $this->getMiNuevoDato(),
    ];
}
```


### 4. Renderizado (Frontend)
Fichero: resources/views/partials/header.blade.php (o la vista correspondiente)

Es donde transformamos el dato en HTML visible.

```php

<div class="servicios-content__form">
  @if ($getFormHeader)
    <section class="form-header">
      {{-- Usamos {!! !!} para renderizar el shortcode como HTML y no como texto plano --}}
      {!! do_shortcode($getFormHeader) !!}
    </section>
  @endif
</div>
```
### Guía de Resolución de Problemas (Debug)
Si el elemento no aparece en el navegador, sigue este orden de verificación:

1. Fuga de datos: ¿Está el ID en Options.php escrito exactamente igual que en carbon_get_theme_option?
2. Visibilidad en Blade: ¿Se ha añadido la variable al array with() del controlador?
3. Persistencia: ¿Si recargas el panel de administración, el dato sigue escrito en la caja de texto? (Si desaparece, hay un error en la definición del campo).
4. Sintaxis Blade: ¿Estás usando {{ }} para texto o {!! !!} para shortcodes/HTML?


---

<br>

## MANTENIMIENTOS PREVENTIVOS

**Frecuencia**: Cada 3 meses

### Lista de verificación:
- [ ] **Actualizaciones menores** de WordPress, tema y plugins
- [ ] **Menús y navegación** - verificar todos los enlaces
- [ ] **Maquetación** - revisar responsive en diferentes dispositivos
- [ ] **Formularios** - probar envío y recepción de emails
- [ ] **Rendimiento** - analizar velocidad de carga
- [ ] **Seguridad** - verificar plugins de seguridad
- [ ] **Backups** - confirmar que se generan correctamente
- [ ] **SSL** - verificar certificado válido

**📋 Documentación**: Anotar en comentarios de la tarea **OK/KO** para cada punto, incluyendo acciones correctivas realizadas.


--- 
<br>

## MIGRAR DE GULP A YARN

### 📁 1. Estructura recomendada

Dentro del tema:
```bash
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

### ⚠️ Nunca mezclar:

- .css dentro de css/sass/
- .min.css dentro de css/css/

### ⚙️ 2. Instalar dependencias

Desde la raíz del tema:

```bash
yarn init -y
yarn add -D sass postcss postcss-cli postcss-import cssnano autoprefixer concurrently
```


### 🧠 3. Crear el entrypoint SASS

Si usas .sass (indented):

css/sass/all.sass
```bash
@use "base"
@use "custom"
```

Si usas .scss:


css/sass/all.scss
```bash
@use "base";
@use "custom";
```

Puedes añadir más imports según tu proyecto:
```bash
@use "variables"
@use "mixins"
@use "header"
@use "footer"
```


### 🛠 4. postcss.config.js

Crear en la raíz del tema:
```javascript
module.exports = {
  plugins: [
    require("postcss-import"),
    require("autoprefixer"),
    require("cssnano")({ preset: "default" }),
  ],
};
```


### 📦 5. package.json plantilla final reutilizable
🔹 Para .sass
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

🔹 Para .scss

Solo cambia:
```bash
all.sass → all.scss
```
en los scripts.


### 🚀 6. Uso diario

Compilar una vez:
```bash
yarn build
```
Desarrollo con watch:
```bash
yarn watch
```


### 🧱 7. functions.php (no tocar si ya usa min.css)

Si el tema antiguo tiene:
```bash
wp_enqueue_style('cssTema', get_template_directory_uri() . '/css/min.css');
```

No necesitas cambiar nada.


### 🧹 8. Limpieza recomendada para migraciones desde Gulp

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


### ⚠️ Errores típicos en migraciones
#### ❌ “Input Error: You must pass a valid list of files to parse”
No existe `css/css/all.css` → revisa que `all.sass` existe.

#### ❌ “Cannot find module 'autoprefixer'”
No está instalado → `yarn add -D autoprefixer`

#### ❌ Duplicados .min.min.css

Estás minificando `*.min.css`.
Solo minifica `all.css`.


---
<br>

## ERRORES
### Error_log

### Error SSL



# SAGE

## Identificación de versión y configuración

### ¿Cómo saber qué versión de Sage tiene un proyecto?
Revisar el archivo `package.json`:

```json
"@roots/sage": "^6.12.3"  // = Sage 10
"laravel-mix": "^x.x.x"   // = Sage 9 (usa Mix en lugar de Bud)
```

**Correspondencias**:
- `@roots/sage 6.x.x` = **Sage 10** (usa Bud)
- Presencia de `laravel-mix` = **Sage 9** (usa Mix)

### Compatibilidad con Node.js:
- **Sage 9**: Node.js 10-14 (recomendado: v12)
- **Sage 10**: Node.js 16+ (recomendado: v16 o v18 LTS)

## Instalación y configuración inicial

### ¿He de ejecutar yarn antes de nada?
**Sí**, siempre ejecutar `yarn` al clonar o iniciar un proyecto por primera vez.

**Ubicación**: `wp_content/themes/nombre_proyecto`
**Comando**: 
```bash
cd wp_content/themes/nombre_proyecto
yarn
```

**Esto hace**:
- Instala dependencias del `package.json`
- Prepara comandos `yarn dev`, `yarn build`, etc.
- Configura Bud/Mix y otros compiladores

### Instalación de Yarn (si no está disponible)

#### ⚠️ Error común: 
Si el sistema sugiere instalar `cmdtest`, **NO lo hagas**. No es el Yarn correcto.

#### ✅ Soluciones correctas:

**Opción 1: Usar Corepack (recomendado para Node ≥16.10)**
```bash
corepack enable
corepack prepare yarn@stable --activate
yarn -v  # verificar instalación
```

**Opción 2: Instalar globalmente con npm**
```bash
npm install -g yarn
yarn -v  # verificar instalación
```

## Scripts de desarrollo por versión
---

<br>

### **SAGE 9** (usa Laravel Mix)
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

**Workflow Sage 9**:
1. **Desarrollo**: `yarn start` (modo watch)
2. **Build final**: `yarn build`
3. **Producción**: `yarn build:production`
4. **Git**: `git add .` → `git commit` → `git push`

<br>

### **SAGE 10**

En caso de usar bud

```json
{
  "scripts": {
    "dev": "bud dev",
    "build": "bud build",
    "translate": "yarn translate:pot && yarn translate:update"
  }
}
```

Puedes migrar y utilizar yarn volviendo a revisar el apartado de [migración de gulp a yarn](#migrar-de-gulp-a-yarn)

**Workflow Sage 10**:
1. **Desarrollo**: `yarn dev` (modo watch con hot reload)
2. **Build final**: `yarn build`
3. **Git**: `git add .` → `git commit` → `git push`

## Gestión de versiones Node.js

### ¿Qué es NVM y para qué sirve?
**Node Version Manager** permite:
- Instalar múltiples versiones de Node.js
- Cambiar entre versiones según proyecto
- Evitar conflictos entre diferentes proyectos

### Comandos NVM esenciales:
```bash
# Ver versión actual
node -v

# Ver versiones instaladas
nvm list

# Instalar versión específica
nvm install 16

# Cambiar a versión específica
nvm use 16

# Ver qué versión requiere el proyecto
cat package.json | grep -A 5 '"engines"'
```

### Workflow completo con NVM:
```bash
# 1. Ir al directorio del tema
cd wp_content/themes/nombre_proyecto

# 2. Verificar versión requerida
cat package.json | grep '"node":'

# 3. Cambiar a versión correcta
nvm use 16  # o la versión requerida

# 4. Instalar dependencias
yarn

# 5. Iniciar desarrollo
yarn dev  # o yarn start según versión
```




# REVISIÓN DE SPAM

## Herramientas anti-spam obligatorias

### Lista de verificación:
- [ ] **Honeypot** activado en formularios
- [ ] **reCAPTCHA** configurado (v2 o v3)
- [ ] **Akismet** activo y configurado
- [ ] **Flamingo** instalado para registro de mensajes

### Plugins específicos:
- **Contact Form 7**: Honeypot + reCAPTCHA
- **Forminator**: reCAPTCHA integrado
- **WPForms**: Protección anti-spam incluida

### Proceso de revisión:
1. **Configurar herramientas** anti-spam
2. **Cambiar tarea a "REVISANDO"** 
3. **Monitorear durante 5-7 días**
4. **Documentar resultados** (reducción de spam)
5. **Finalizar tarea** con reporte

### Configuración reCAPTCHA:
- Clave asociada a **grupo de dominios**
- Configurar tanto dominio principal como subdominios
- Verificar que funciona en formularios de contacto

## Mantenimiento adicional

### Caché:
- **Borrar caché de WP Rocket** después de cambios
- Verificar que formularios funcionan sin caché

### Logs:
- **Revisar `/var/logs`** antes de subir cambios
- Eliminar logs con errores críticos
- Documentar errores recurrentes

---

# PRESTASHOP

## Configuración de entorno local

### 1. Descarga del repositorio
```bash
git clone https://github.com/hexer-dev/dob-prestashop-xxxxxxxxx.git
cd dob-prestashop-xxxxxxxxx
```

### 2. Configuración de dominio falso

#### Modificar virtual hosts
Editar `/opt/lampp/etc/extra/httpd-vhosts.conf`:
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
```
127.0.0.1 xxxxxxx.com www.xxxxxxx.com
```

### 3. Configurar base de datos
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

### 4. Vaciar caché
Eliminar la carpeta `/var/cache`

## Solución de errores comunes

### 🔧 Activar modo Debug
**Si necesitamos ver el error que se está generando**
1. Nos dirigimos, en el proyeco, a la carpeta /config/define.inc.php:
2. cambiar el condicional del debug _PS_MODE_DEV_ de false a -> true

### 🔧 Proyecto no carga en local
**Causa**: URL incorrecta en base de datos
**Solución**:
1. Ir a phpMyAdmin
2. Buscar tabla `ps_shop_url`
3. Cambiar dominio por el local (ej: `proyecto.local`)

### 🔧 Error 404
**Verificar**:
- `ServerName` y `ServerAlias` en vhosts (sin `/` al final)
- Existencia de carpetas: `var/cache`, `var/logs`, `var/sessions`

### 🔧 Directory not writable
```bash
# Crear carpetas faltantes
mkdir -p var/logs var/cache var/sessions

# Aplicar permisos
chmod -R 775 var/
chown -R www-data:www-data var/
```

### 🔧 Error 500 / Caché corrupto
```bash
# Eliminar caché de producción
rm -rf var/cache/prod/

# Limpiar caché completo
rm -rf var/cache/*

# Regenerar caché
php bin/console cache:clear --env=prod
```


---

# PHP

Se puede dar el caso que un proyecto, en un momento concreto, nos pida la instalación de PHP, bien porque lo requiere al hacer un composer update o por lo que sea. Tenemos que decirle que no queremos hacer esta instalación y, en su lugar, hacer que apunte a la carpeta lampp ya que tiene creados enlaces que apuntan a la versión de lampp levantada para el proyecto concreto con LVM. 

Si por casualidad se nos instalase PHP en una versión a nivel de sistema, tendremos que eliminarlo y hacer que el enlace apunte a la carpeta de lampp para que administre de manera automática la versión que levante lvm.
