# ÍNDICE
- [1. Mantenimiento de proyectos](#mantenimiento-de-proyectos)
  - [1.1 Actualización de Wordpress y PHP](#actualizacion-de-wordpress-y-php)
    - [1.1.1 Carbon Fields - Flujo](#carbon-fields)
  - [1.2 Mantenimientos preventivos](#mantenimientos-preventivos)
  - [1.3 Migrar de Gulp a Yarn](#migrar-de-gulp-a-yarn)
  - [1.4 Errores](#errores)
    - [1.4.1 Revisar error_log](#revisar-error_log)
    - [1.4.2 Error SSL](#error-ssl)
- [2. Sage](#sage)
  - [Yarn](#he-de-ejecutar-yarn-antes-de-nada)
  - [Node](#node)
- [3. Revisión de Spam](#revisión-de-spam)
- [4. Prestashop](#prestashop)
- [5. Creación de entornos - nuevos proyectos](#creación-de-entornos)
- [6. PHP](#php)
- [7. Git](#git)
- [8. Administración de servidor (Linux)](#administrador-de-servidor-(linux))

---

# MANTENIMIENTO DE PROYECTOS 

## Tipos de mantenimiento preventivo

### Mantenimiento Preventivo Básico (Nivel 1)  
**Frecuencia**: Trimestral
**Incluye**:
- Actualizaciones menores de la web y sus componentes (plugins)
- Menús y navegación
- Revisión de maquetación en web y responsive
- Verificación de formularios
- Verificación de funcionamiento básico

### Mantenimiento Preventivo Profesional (Nivel 2)
**Frecuencia**: Trimestral
**Incluye**:
- Auditoría completa de seguridad
- Optimización de rendimiento
- Actualización completa de componentes
- Análisis de logs detallado
- Backup y restauración test
- Informe técnico completo

#### TIENDA WOOCOMMERCE

#### TIENDA PRESTASHOP


**📋 Documentación**: Anotar en comentarios de la tarea **OK/KO** para cada punto, incluyendo acciones correctivas realizadas.

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
Cuando un proyecto presenta algún tipo de error de backend en local, normalmente aparecerá reflejado en el fichero `error_log`. Este fichero es la primera fuente de información para diagnosticar problemas en PHP. 

### Revisar error_log

¿Qué hacer?
1. Abrir el fichero error_log del servidor local.
2. Buscar la fecha y hora en que ocurrió el error.
3. Identificar el tipo de error PHP y su gravedad.

Ejemplo:

```bash
[Wed Feb 25 13:57:52.023268 2026] [php:error] [pid 48737] [client 127.0.0.1:33540] PHP Fatal error:
Uncaught TypeError: sizeof():Argument #1 ($value) must be of type Countable|array, stdClass given in
/opt/htdocs/dob-wordpress-gruposierramorena/wp-content/themes/plantilla-dobuss/codigo/lib/DocumentosDAO.php:246\nStack
trace:\n#0 /opt/htdocs/dob-wordpress-gruposierramorena/wp-content/themes/plantilla-dobuss/codigo/lib/DocumentosDAO.php(86):...
```
Yéndonos al fichero `error_log` podremos observalo y deducir de él que el error PHP es el siguiente: `Uncaught TypeError: sizeof(): Argument #1 ($value) must be of type Countable|array`. 

4. Localizar el fichero y línea de código donde se produjo el error:

Posteriormente nos indica en qué fichero se encuentra el problema y la línea del código: `stdClass given in /opt/htdocs/dob-wordpress-gruposierramorena/wp-content/themes/plantilla-dobuss/codigo/lib/DocumentosDAO.php:246` 

5. Analizar el código en esa ubicación para entender qué función o variable ha causado el problema.

<strong> Consejo: </strong>

- No todos los errores requieren la misma solución; la forma de resolverlos dependerá de la versión de PHP, del tipo de proyecto y de la función implicada.
- Siempre se recomienda no ignorar los errores fatales o warnings que aparecen en `error_log`, ya que son la fuente más fiable para depurar.
- 💡 Mantener siempre abierto `error_log` mientras se trabaja en local ayuda a identificar problemas antes de que afecten al flujo de la aplicación. Es la primera herramienta de depuración que todo desarrollador de PHP debería consultar.


### Error SSL
Hay momentos en los que `error_log` nos arroja un tipo de error en el que nos marca acudir al file_get_contents(....) con el siguiente síntoma de ejemplo:

```bash
Warning:  file_get_contents(): SSL operation failed with code 1. OpenSSL Error messages:\nerror:1416F086:SSL
routines:tls_process_server_certificate:certificate verify failed in
/opt/htdocs/dob-wordpress-gruposierramorena/wp-content/themes/plantilla-dobuss/codigo/lib/PlantillaHTML.php
on line 20, referer: https://web.local/wp-admin/admin.php?page=clientes
```

¿Qué indica?

- PHP intenta acceder a un recurso HTTPS pero no puede verificar el certificado SSL.
- Provoca que la página “pete” al ejecutar `file_get_contents()`.

Para ello hay que recurrir a todas las partes del código que tengan la fila con la llamada al método `file_get_contents()` y donde tiene el valor true ponerle false y todo lo que viene detrás

Solución temporal en desarrollo local:

Justo antes de la llamada hay que igualar la variable de array $arrContextOptions incluyendo este código y detrás la llamada del método `file_get_contents()`:

```php
$arrContextOptions = array(
    "ssl" => array(
        "verify_peer" => false,
        "verify_peer_name" => false,
    ),
);

file_get_contents(of_get_option('logo2', ''), false, stream_context_create($arrContextOptions));
```

Cuando comprobemos que realmente funciona, nos encargamos de borrar de nuevo el código que hemos puesto de prueba y commiteamos, ya que esto responde a un error exclusivo del backend

### Error al cargar backoffice (descarga fichero)
Puede haber un problema cuando se intenta cargar el backoffice en el cual se descarga un fichero e impide la carga de wordpress.

Solución: Hay que borrar el fichero .htaccess y hacer que se regenere de nuevo. Con ello se soluciona y vuelve a cargar el backoffice.


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
1. Nos dirigimos, en el proyeco, a la carpeta /config/defines.inc.php:
2. cambiar el condicional del debug _PS_MODE_DEV_ de false a -> true

### 🔧 Proyecto no carga en local
**Causa**: URL incorrecta en base de datos
**Solución**:
1. Ir a phpMyAdmin
2. Buscar tabla `ps_shop_url`
3. Verificar los campos:
   - domain
   - domain_ssl
4. Cambiar dominio por el local (ej: `proyecto.local`)
5. Verificar que el campo physical_uri sea /.

⚠️ Si sigue sin cargar, revisar también la tabla ps_configuration (PS_SHOP_DOMAIN y PS_SHOP_DOMAIN_SSL).

### 🔧 Error 404
**Verificar**:
- `ServerName` y `ServerAlias` en vhosts (sin `/` al final)
- Que el módulo mod_rewrite esté activo.
- Existencia y permisos de las carpetas:
  - `var/cache`
  - `var/logs`
  - `var/sessions`
- Que el archivo `.htaccess` exista.
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
### 🔧 Imágenes no cargan y enlaces no funcionan o lanzan un error
**Causa**: .htaccess mal/incorrecto
**Solución**:

1. Ir a:
    <strong>Configurar → Parámetros de la tienda → Tráfico & SEO</strong>
2. Ir a la sección Formato de los enlaces.
3. Pulsar Guardar (aunque no cambies nada).

Esto regenerará automáticamente el archivo .htaccess.

⚠️ Asegúrate de que Apache tenga permisos para escribir el archivo.

---

# PHP

Se puede dar el caso que un proyecto, en un momento concreto, nos pida la instalación de PHP, bien porque lo requiere al hacer un composer update o por lo que sea. Tenemos que decirle que no queremos hacer esta instalación y, en su lugar, hacer que apunte a la carpeta lampp ya que tiene creados enlaces que apuntan a la versión de lampp levantada para el proyecto concreto con LVM. 

Si por casualidad se nos instalase PHP en una versión a nivel de sistema, tendremos que eliminarlo y hacer que el enlace apunte a la carpeta de lampp para que administre de manera automática la versión que levante lvm.



---

<br>

# 7.GIT

1. Eliminar solo el último commit
`git reset --hard HEAD~1`
`git push origin dev --force`

Esto elimina solo el último commit.

Tu carpeta de trabajo se ajusta al commit anterior.

El remoto se actualiza con --force.

<br>

2. Añadir nuevos ficheros al último commit o cambiar nombre del commit
`git add nombrechero` Primero hay que añadir el fichero al stage
`git commit --amend -m "nombre del commit que ya se ha hecho anteriormente"`

Esto comitee sobre el mismo commit anterior y además podrías cambiar el nombre del commit.

<br>

3. Añadir nuevos ficheros al último commit sin editar el nombre del commit
`git add nombrechero` Primero hay que añadir el fichero al stage
`git commit --amend --no-edit -m "nombre del commit que ya se ha hecho anteriormente"` importante añadir el --no-edit

Esto comitee sobre el mismo commit anterior, en este caso, sin cambiar el nombre del commit.

<br>


---

# 8.Administración de servidor (Linux)
### Flush DNS

En Ubuntu 24.04, el comando:

`sudo resolvectl flush-caches`

se utiliza para vaciar la caché DNS del sistema.

En los sistemas modernos de Ubuntu, el servicio systemd-resolved almacena temporalmente las respuestas de los servidores DNS para acelerar la resolución de nombres de dominio. Esto significa que cuando accedemos a una web o servicio (por ejemplo, un dominio que apunta a un servidor), el sistema puede recordar la dirección IP sin tener que consultarla de nuevo al servidor DNS externo.

Vaciar la caché DNS es útil en tareas de mantenimiento cuando:

Se han realizado cambios en registros DNS (por ejemplo, migraciones de servidor).

Un dominio apunta a una IP antigua y el sistema sigue resolviendo la dirección anterior.

Existen problemas de conexión aparentemente inexplicables.

Se están realizando pruebas de propagación DNS.

Al ejecutar este comando, el sistema elimina todas las resoluciones almacenadas y obliga a que la próxima consulta DNS se realice directamente al servidor configurado, garantizando que se utilice la información más actualizada.

Es una herramienta básica pero importante en tareas de administración de servidores y diagnóstico de red.

