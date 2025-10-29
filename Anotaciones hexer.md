# ÍNDICE
- [1. Mantenimiento de proyectos](#mantenimiento-de-proyectos)
  - [1.1 Actualización de Wordpress y PHP](#actualizacion-de-wordpress-y-php)
  - [1.2 Mantenimientos preventivos](#mantenimientos-preventivos)
- [2. Sage](#sage)
  - [Yarn](#he-de-ejecutar-yarn-antes-de-nada)
  - [Node](#tengo-la-versión-correcta-de-node)
  - [**NUEVO:** Migración de Gulp a Yarn](#migración-de-gulp-a-yarn)
- [3. Revisión de Spam](#revisión-de-spam)
- [4. Prestashop](#prestashop)
- [5. Creación de entornos - nuevos proyectos](#creación-de-entornos)
- [6. PHP](#php)

---

# MANTENIMIENTO DE PROYECTOS 

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

### **SAGE 10** (usa Bud)
```json
{
  "scripts": {
    "dev": "bud dev",
    "build": "bud build",
    "translate": "yarn translate:pot && yarn translate:update"
  }
}
```

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

---

## 🆕 MIGRACIÓN DE GULP A YARN

### ¿Cómo detectar un proyecto con Gulp?
Buscar estos archivos en el proyecto:
- `gulpfile.js`
- `gulp.config.js`
- En `package.json`: dependencias como `gulp`, `gulp-sass`, etc.

### Proceso de migración paso a paso:

#### 1. Análisis del proyecto actual
```bash
# Revisar estructura actual
ls -la  # buscar gulpfile.js
cat package.json  # revisar dependencias de gulp
```

#### 2. Backup del proyecto
```bash
# Crear rama para la migración
git checkout -b migracion-gulp-to-yarn
```

#### 3. Instalación de Sage/Bud (recomendado)
```bash
# Opción A: Migrar a Sage 10 con Bud
npm install @roots/bud --save-dev

# Opción B: Usar Webpack directamente
npm install webpack webpack-cli --save-dev
```

#### 4. Configuración nueva (ejemplo con Bud)
Crear `bud.config.js`:
```javascript
export default async (bud) => {
  bud
    .entry('app', ['@scripts/app', '@styles/app'])
    .entry('editor', ['@scripts/editor', '@styles/editor'])
    .assets(['images'])
    .copyDir('images')
    .setUrl('http://localhost:3000')
    .setProxyUrl('http://web.local')
    .watch(['resources/views', 'app']);
};
```

#### 5. Actualizar package.json
Reemplazar scripts de Gulp:
```json
{
  "scripts": {
    "dev": "bud dev",
    "build": "bud build",
    "build:production": "bud build --mode=production"
  }
}
```

#### 6. Migrar archivos de assets
```bash
# Mover archivos según nueva estructura
mkdir -p resources/{scripts,styles,images}
# Mover archivos de assets/* a resources/*
```

#### 7. Testing y verificación
```bash
# Instalar dependencias
yarn

# Probar compilación
yarn dev

# Verificar que todo funciona
yarn build
```

#### 8. Limpieza
```bash
# Eliminar archivos obsoletos de Gulp
rm gulpfile.js
rm gulp.config.js
# Limpiar dependencias obsoletas del package.json
```

### Errores comunes en migración:
- **Rutas incorrectas**: Verificar que las rutas de assets coincidan
- **Dependencias faltantes**: Instalar loaders específicos (sass-loader, etc.)
- **Configuración proxy**: Ajustar URLs locales en la config

---

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

# CREACIÓN DE ENTORNOS

## Tipos de mantenimiento

### Mantenimiento Profesional (Nivel 2)
**Frecuencia**: Trimestral
**Incluye**:
- Auditoría completa de seguridad
- Optimización de rendimiento
- Actualización completa de componentes
- Análisis de logs detallado
- Backup y restauración test
- Informe técnico completo

### Mantenimiento Básico (Nivel 1)  
**Frecuencia**: Mensual
**Incluye**:
- Actualización de plugins menores
- Verificación de funcionamiento básico
- Limpieza de spam
- Backup básico
- Verificación de formularios

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

---

# PHP

Se puede dar el caso que un proyecto, en un momento concreto, nos pida la instalación de PHP, bien porque lo requiere al hacer un composer update o por lo que sea. Tenemos que decirle que no queremos hacer esta instalación y, en su lugar, hacer que apunte a la carpeta lampp ya que tiene creados enlaces que apuntan a la versión de lampp levantada para el proyecto concreto con LVM. 

Si por casualidad se nos instalase PHP en una versión a nivel de sistema, tendremos que eliminarlo y hacer que el enlace apunte a la carpeta de lampp para que administre de manera automática la versión que levante lvm.
