# HANDBOOK TÉCNICO INTERNO

Manual Operativo Oficial -- Departamento de Desarrollo

Versión: 1.0 Alcance: WordPress, Sage, Prestashop, Entornos Locales,
Gestión de PHP, Node y Yarn Uso: Onboarding de nuevos desarrolladores y
referencia operativa interna

------------------------------------------------------------------------

# 1. Arquitectura de Entornos

## 1.1 Filosofía del Entorno

El equipo trabaja bajo el siguiente principio:

-   PHP gestionado exclusivamente por LAMPP + LVM
-   Node gestionado exclusivamente por NVM
-   Yarn como gestor estándar de dependencias frontend
-   Nunca instalar versiones globales no controladas

------------------------------------------------------------------------

## 1.2 Diagrama General de Entorno

``` mermaid
flowchart TD
    A[Repositorio Git] --> B[Clonado en htdocs]
    B --> C[LAMPP]
    C --> D[LVM selecciona versión PHP]
    B --> E[NVM selecciona versión Node]
    E --> F[Yarn instala dependencias]
    F --> G[Entorno listo para desarrollo]
```

------------------------------------------------------------------------

# 2. Gestión Profesional de PHP

## 2.1 Norma obligatoria

Nunca instalar PHP a nivel sistema. Siempre usar versiones controladas
mediante LVM.

## 2.2 Flujo de cambio de versión

``` mermaid
flowchart LR
    A[lvm disable] --> B[lvm use X.X]
    B --> C[sudo lampp restart]
    C --> D[Verificación entorno]
```

## 2.3 Comandos oficiales

``` bash
lvm disable
lvm use 7.4
sudo lampp restart
```

------------------------------------------------------------------------

# 3. Gestión Profesional de Node y Yarn

## 3.1 Gestión con NVM

Instalar y usar versión:

``` bash
nvm install 16
nvm use 16
node -v
```

## 3.2 Instalación estándar de Yarn

### Método recomendado

``` bash
corepack enable
corepack prepare yarn@stable --activate
```

### Alternativa

``` bash
npm install -g yarn
```

Nunca instalar cmdtest.

------------------------------------------------------------------------

## 3.3 Flujo completo frontend

``` mermaid
flowchart TD
    A[Entrar en tema] --> B[nvm use versión]
    B --> C[yarn]
    C --> D[yarn dev]
    D --> E[Desarrollo activo]
```

------------------------------------------------------------------------

# 4. WordPress -- Proceso Estándar de Arranque

## 4.1 Base de datos

``` bash
echo "create database wp_proyecto" | mysql -u root -p
mysql -u root -p wp_proyecto < archivo.sql
```

## 4.2 Enlace simbólico

``` bash
unlink web.local
ln -s nombre-proyecto/ web.local
```

## 4.3 Validación final

-   Acceso frontend
-   Acceso admin
-   Navegación correcta
-   Formularios funcionales

------------------------------------------------------------------------

# 5. Sage -- Arquitectura y Workflows

## 5.1 Identificación de versión

  Indicador en package.json   Versión
  --------------------------- ---------
  @roots/sage                 Sage 10
  laravel-mix                 Sage 9

## 5.2 Compatibilidad Node

  Sage   Node recomendado
  ------ ------------------
  9      12
  10     16 / 18 LTS

## 5.3 Workflow Oficial

``` bash
yarn
yarn dev
yarn build
```

------------------------------------------------------------------------

# 6. Migración de Gulp a Yarn

## 6.1 Arquitectura recomendada

``` text
mi-tema/
 ├── css/
 │   ├── sass/
 │   ├── css/
 │   └── min.css
 ├── package.json
 └── postcss.config.js
```

## 6.2 Instalación dependencias

``` bash
yarn init -y
yarn add -D sass postcss postcss-cli postcss-import cssnano autoprefixer concurrently
```

## 6.3 Flujo compilación

``` mermaid
flowchart LR
    A[SASS] --> B[all.css]
    B --> C[PostCSS]
    C --> D[min.css]
    D --> E[WordPress enqueue]
```

------------------------------------------------------------------------

# 7. Prestashop -- Arquitectura Local

## 7.1 Diagrama de entorno

``` mermaid
flowchart TD
    A[Repositorio] --> B[LAMPP vhost]
    B --> C[Certificado SSL]
    C --> D[Base de datos]
    D --> E[Proyecto funcional]
```

## 7.2 Limpieza caché

``` bash
rm -rf var/cache/*
```

## 7.3 Activar debug

Editar: /config/define.inc.php

Cambiar:

    _PS_MODE_DEV_ = true;

------------------------------------------------------------------------

# 8. Seguridad y Anti-Spam

Checklist obligatorio:

-   Honeypot
-   reCAPTCHA
-   Akismet
-   Flamingo

Flujo revisión:

``` mermaid
flowchart LR
    A[Configurar herramientas] --> B[Monitorizar 5-7 días]
    B --> C[Evaluar reducción spam]
    C --> D[Cerrar tarea]
```

------------------------------------------------------------------------

# 9. Tipos de Mantenimiento

  Tipo          Frecuencia   Nivel
  ------------- ------------ --------------------
  Básico        Mensual      Operativo
  Profesional   Trimestral   Auditoría completa

------------------------------------------------------------------------

# 10. Checklist Global de Validación de Proyecto

## Entorno

-   PHP correcto vía LVM
-   Node correcto vía NVM
-   Yarn instalado correctamente
-   Dependencias instaladas

## Proyecto

-   Base de datos conectada
-   Navegación correcta
-   Formularios funcionales
-   Caché limpia
-   Logs revisados

------------------------------------------------------------------------

Fin del documento.
