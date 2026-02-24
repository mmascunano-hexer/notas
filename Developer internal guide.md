# Guía Interna de Desarrollo

Manual operativo unificado para mantenimiento, entornos locales y
workflows técnicos.

> Este documento consolida toda la información relacionada con **Yarn,
> Node, PHP y gestión de entornos** para evitar duplicidades y
> contradicciones.

------------------------------------------------------------------------

# Índice

1.  Entornos y Configuración Base
2.  Gestión Unificada de PHP (LAMPP + LVM)
3.  Gestión Unificada de Node y Yarn
4.  WordPress -- Configuración y Mantenimiento
5.  Sage (según versión)
6.  Migración de Gulp a Yarn
7.  Prestashop -- Entorno Local
8.  Revisión de Spam
9.  Tipos de Mantenimiento

------------------------------------------------------------------------

# 1. Entornos y Configuración Base

## Clonado de repositorios

``` bash
git clone https://github.com/hexer-dev/nombre-proyecto.git
```

Trabajar siempre dentro de `htdocs` si es WordPress o Prestashop en
LAMPP.

------------------------------------------------------------------------

# 2. Gestión Unificada de PHP (LAMPP + LVM)

## Regla del equipo

NO instalar PHP a nivel de sistema. Siempre usar PHP gestionado por
**LAMPP + LVM**.

## Cambiar versión de PHP

``` bash
lvm disable
lvm use 7.4
sudo lampp restart
```

## Si PHP se instala a nivel sistema por error

1.  Desinstalar PHP del sistema
2.  Asegurar que los binarios apuntan a LAMPP
3.  Volver a activar versión con LVM

------------------------------------------------------------------------

# 3. Gestión Unificada de Node y Yarn

Toda la gestión de Node debe hacerse mediante **NVM**.

## Instalar versión requerida

``` bash
nvm install 16
nvm use 16
```

Ver versión activa:

``` bash
node -v
```

------------------------------------------------------------------------

## Instalación correcta de Yarn

### Opción recomendada (Node ≥16)

``` bash
corepack enable
corepack prepare yarn@stable --activate
yarn -v
```

### Alternativa

``` bash
npm install -g yarn
```

Nunca instalar `cmdtest`.

------------------------------------------------------------------------

## Procedimiento estándar al clonar un proyecto con frontend

``` bash
cd wp-content/themes/nombre_tema
nvm use <version_requerida>
yarn
```

------------------------------------------------------------------------

# 4. WordPress -- Configuración y Mantenimiento

## Base de datos

Crear:

``` bash
echo "create database wp_proyecto" | mysql -u root -p
```

Importar:

``` bash
mysql -u root -p wp_proyecto < archivo.sql
```

## Enlace simbólico

``` bash
unlink web.local
ln -s nombre-proyecto/ web.local
```

## wp-config.php (ejemplo)

``` php
define('DB_NAME', 'wp_proyecto');
define('DB_USER', 'root');
define('DB_PASSWORD', '');
define('DB_HOST', 'localhost');
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
```

------------------------------------------------------------------------

# 5. Sage

## Identificar versión

En `package.json`:

-   `@roots/sage` → Sage 10
-   `laravel-mix` → Sage 9

## Compatibilidad Node

  Sage   Node recomendado
  ------ ------------------
  9      12
  10     16 o 18 LTS

## Workflow estándar

``` bash
yarn
yarn dev     # desarrollo
yarn build   # producción
```

------------------------------------------------------------------------

# 6. Migración de Gulp a Yarn

## Instalar dependencias

``` bash
yarn init -y
yarn add -D sass postcss postcss-cli postcss-import cssnano autoprefixer concurrently
```

## Scripts recomendados

``` json
"scripts": {
  "scss": "sass css/sass/all.sass css/css/all.css --no-source-map",
  "minify": "postcss css/css/all.css -o css/min.css",
  "build": "yarn scss && yarn minify",
  "watch": "concurrently \"sass --watch css/sass/all.sass:css/css/all.css --no-source-map\" \"postcss css/css/all.css -o css/min.css --watch\""
}
```

Uso diario:

``` bash
yarn build
yarn watch
```

------------------------------------------------------------------------

# 7. Prestashop -- Entorno Local

## Activar debug

Editar `/config/define.inc.php`:

    _PS_MODE_DEV_ = true;

## Limpiar caché

``` bash
rm -rf var/cache/*
```

------------------------------------------------------------------------

# 8. Revisión de Spam

Checklist:

-   Honeypot
-   reCAPTCHA
-   Akismet
-   Flamingo

Monitorizar 5-7 días antes de cerrar tarea.

------------------------------------------------------------------------

# 9. Tipos de Mantenimiento

  ------------------------------------------------------------------------
  Tipo             Frecuencia                     Incluye
  ---------------- ------------------------------ ------------------------
  Básico           Mensual                        Plugins menores,
                                                  formularios, backups

  Profesional      Trimestral                     Seguridad, rendimiento,
                                                  auditoría completa
  ------------------------------------------------------------------------
