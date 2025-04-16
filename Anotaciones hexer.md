# INDICE
- [1. Mantenimiento de proyectos](#mantenimiento-de-proyectos)
- [2. Sage](#sage)
  - [Yarn](#he-de-ejecutar-yarn-antes-de-nada)
  - [Node](#tengo-la-versión-correcta-de-node)
- [3. Revisión de Spam](#revisión-de-spam)
- [4. Prestashop](#prestashop)

<br><br>
=

# MANTENIMIENTO DE PROYECTOS 

#### PASO 1. Descargar repositorio en htdocs

Utilizamos la directiva `git clone https://github.com/hexer-dev/dob-wordpress-xxxxxxxxxx.git` (sutituimos el nombre del repositorio por el del proyecto a editar). <br>

#### PASO 2. Seleccionar la version de php del proyecto

1. Desmontar la versión previa utilizada

`lvm disable`

2. Seleccionar la versión de lampp pertinente para el proyecto.

`lvm use 7.4 (por ejemplo)`

3. Reiniciamos el servicio en lampp.
`sudo lampp restart`
<br>

#### PASO 3. Crear una base de datos y enlazarla a mysql.  

1. Creación de base de datos con terminal 

- Opción 1:
`echo "create database wp_xxxxxxxxx" | mysql -u root -p`

- Opción 2:
Entrar en mysql con:
```mysql 
mysql -u root -p
create database wp_xxxxxx.sql > mysql
```
<br>      
                                             
#### PASO 4. Enlazar la base de datos.zip o sql a la base de datos de mysql   
- Opción 1:       
`pv xxxxxxxxxxxxxxxx.sql | mysql -u root -p wp_xxxxxxxxxx`
El comando pv permite ver un progreso en la terminal.

##### Posibles errores: 
Puede que de un error en la línea 1 del sql porque se haya inyectado código comentado. Se tiene que borrar, guardar y volver a repetir el paso.

- Opción 2: 
`mysql -u root -p wp_xxxxxx < /home/ruta/de/la/bbdd/xxxxxxx.sql`  

<br>

#### PASO 5. DESFIJAR WEB.LOCAL Y ASOCIARLO AL NUEVO PROYECTO

Antes de unir otro proyecto y poder ser visualizado en web.local se ha de desvincular el anterior. Para ello se empleará el siguiente comando en terminal.

`unlink web.local`

Seguidamente se enlazará el siguiente proyecto

`ln -s dob-wordpress-ejemplo/ web.local`

<br>

#### PASO 6. Cargar el config.php en el proyecto

Importante tener en cuenta:
- Base de datos que carga el proyecto
- Mirar si la base de datos tiene algún prefijo

<br>


#### PASO 7. Abrir en host virtual

Nos dirigimos al navegador y abirmos el proyecto en web.local


#### Navegación fallida entre páginas web

Puede darse el caso que los enlaces dentro del sitio web no funcione bien. Para ello hay que ir al wordpress y, dentro de **AJUSTES** hay que pulsar **Enlaces permanentes** y guardar. De ese modo se regeneran los enlaces y el fichero .htaccess.

<br><br><br>
=


# SAGE

Para utilizar SAGE debemos ver qué versión de node necesita el proyecto. 
<br>


### ¿Cómo saber qué versión de Sage tiene un proyecto?
Abrimos el fichero package.json y revisamos la siguiente línea:
`"@roots/sage": "^6.12.3"`

En este caso concreto, significa que se está usando Sage 10, ya que:

- La versión 6.x.x de @roots/sage corresponde a Sage 10.
- Sage 9 usaba Laravel Mix, no Bud, y no tenía el sistema moderno basado en @roots.

  **Para SAGE 9 tenemos que utilizar una versión entre la 8 y la 12 de node.*
<br>


### ¿He de ejecutar yarn antes de nada?

Sí, se ha de ejecutar yarn (o npm install) al iniciar el proyecto por primera vez, o si se acaba de clonar/descargar el repositorio.

Esto hace:

- Instala todas las dependencias del package.json.
- Prepara los comandos yarn dev, yarn build, etc.
- Deja listo Bud (el compilador) y otros plugins para trabajar.

Para realizar la ejecución de **Yarn** nos situamos sobre la carpeta del proyecto wp_content > themes > nombre_proyecto en la terminal e introducimos el comando `yarn` para cargar las dependencias. 

Es importante saber que yarn se instala a nivel de versión de node, por lo que si se instala en la versión 12, para la 23 también habría que volver a instalarlo. 

Además también se ha de tener en cuenta que se puede instalar de forma local, -global o de sistema operativo. Nosotros vamos a instalarlo en global dentro de la versión para que pueda ser utilizado en cualquier desde cualquier carpeta donde estemos situados. 

<br>

#### Posibles errores de yarn
Puede ser que cuando escribamos _yarn_, el sistema no encuentre el comando, y sugiera instalar cmdtest. ¡NO lo hagas! Ese paquete no es yarn de Node, sino otra herramienta completamente diferente que tiene el mismo nombre.

###### ¿Cómo solucionarlo correctamente?

- Opción 1: **Instalar yarn usando corepack (la más moderna y recomendada si tienes Node ≥16.10)**
  Lo realizamos con la utilidad corepack, que gestiona herramientas como yarn con:
  ```
  corepack enable
  corepack prepare yarn@stable --activate
  ```
  Y ahora volvermos a comprobar que funciona yarn con `yarn -v` <br>

- Opción 2: **Instalarlo globalmente con npm**
  Escribimos `npm install -g yarn` y verificamos la versión instalada con `yarn -v`.

  
<br>
Por otro lado, deberemos mirar en el package.json los scripts que acepta yarn a fin de que pueda realizar la inyección y el volcado del codigo que se vaya a modificar. Dependiendo de la versión de Sage, los scripts que se utilicen serán diferentes.
Así pues, para inicializarlo habrá que realizar un `yarn start` o un `yarn dev` dependiendo de la versión utilizada. 
<br><br>

**SAGE 9**
```json
scripts: {
    "build": "webpack --progress --config resources/assets/build/webpack.config.js",
    "build:production": "webpack --env.production --progress --config resources/assets/build/webpack.config.js",
    "build:profile": "webpack --progress --profile --json --config resources/assets/build/webpack.config.js",
    "start": "webpack --hide-modules --watch --config resources/assets/build/webpack.config.js",
    "rmdist": "rimraf dist",
    "lint": "npm run -s lint:scripts && npm run -s lint:styles",
    "lint:scripts": "eslint resources/assets/scripts resources/assets/build",
    "lint:styles": "stylelint \"resources/assets/styles/**/*.{css,sass,scss,sss,less}\"",
    "test": "npm run -s lint"
  }
```


Una vez se hayan realizado las modificaciones habrá que realizar un `yarn build` y posteriormente `yarn build:production`
<br>

**SAGE 10**

```json
scripts: {
    "dev": "bud dev",
    "build": "bud build",
    "translate": "yarn translate:pot && yarn translate:update",
    "translate:pot": "wp i18n make-pot . ./resources/lang/sage.pot --include=\"app,resources\"",
    "translate:update": "wp i18n update-po ./resources/lang/sage.pot ./resources/lang/*.po",
    "translate:compile": "yarn translate:mo && yarn translate:js",
    "translate:js": "wp i18n make-json ./resources/lang --pretty-print",
    "translate:mo": "wp i18n make-mo ./resources/lang ./resources/lang"
  }
```
Una vez se hayan realizado las modificaciones habrá que realizar un `yarn dev` y posteriormente `yarn build`
<br>

<br>

### ¿Qué es nvm y para qué sirve?

NVM significa Node Version Manager. Es una herramienta que te permite:

- Instalar múltiples versiones de Node.js.
- Cambiar de versión fácilmente entre proyectos.

Sage (y muchos proyectos modernos) requiere una versión específica de Node, y con nvm puedes asegurarte de usar la correcta sin romper otros proyectos.

<br>

### ¿Tengo la versión correcta de Node?
Para visualizar esto necesitamos introducir el comando `node -v`

En el package.json se especifica:
```json
"engines": {
  "node": ">=X.X.X" (Donde X es la versión aconsejada de node)
}
```
Sage 10 y Bud suelen funcionar mejor con versiones LTS estables, como v18.x o v20.x. La v23 aún es muy reciente y experimental. Así que, en caso de tener una versión muy alta, tendremos que bajarla para no romper el proyecto.

<br>

Nos vamos a la carpeta del proyecto y abrimos una nueva terminal sobre el directorio de wp_content > themes > nombre_proyecto. 

Una vez dentro del directorio en la terminal insertamos `nmv -v` para conocer la versión que tenemos corriendo.

Insertamos `nvm use x` (donde x es la versión que queremos saber si está) para ver si está instalado. En caso de no estarlo, introduciremos el comando de instalación y el de uso de la versión con:
```json
nvm install 12
nvm use 12
```
Acto seguido ahora volvemos a correr yarn para la versión de node especifica y se carguen las dependencias necesarias. 
<br>




Por último, realizamos un `git add .`, `git commit -m "mensaje con el commit hecho T:xxxxxx"` indicando la tarea y realizar un `git push`.



<br><br><br>
=


### Revisión de SPAM

Se ha de comprobar que tengan métodos anti spam los formularios (recaptcha y honeypot como mínimo en cf7 y forminator con recaptcha también). Para ello, se deberá tener en condideración que cuenten con:

- Honeypot
- Akismet
- Flamingo

En tareas de revisión de Spam, hay que dejar la tarea en "REVISANDO" durante unos días y avisar en la tarea. Se ha de inspeccionar durante los próximos días para garantizar que el Spam ha desaparecido o ha disminuido drásticamente.
la clave de captcha va asociada a un grupo de dominio.
<br>

### Caché
Se ha de **borrar** la caché de WP Rocket

<br>

### Logs
Se ha de REVISAR `/var/logs` antes de subir al repositorio para evitar subirlo con muchos errores. 

<br><br><br>
=

# PRESTASHOP

Para poder montar un proyecto en prestashop (falseando dominio) hay que seguir los siguientes pasos:
<br>

### 1. Descarga del repositorio

### 2. Falsear Dominio para cargarlo en local
Para realizar este proceso vamos a tener que tener en cuenta lo siguiente:
- Modificación del fichero `/opt/lampp/etc/extra/httpd-vhosts.conf`
  Ahí introduciremos el siguiente código e introduciremos los datos del proyecto:
```xml
######################### URL PROYECTO XXX #########################

<VirtualHost *:80>
    ServerAdmin xxxxxxx@hexer.dev
    DocumentRoot "/opt/lampp/htdocs/dob-prestashop-xxxxxxxx"
    ServerName xxxxxxx.xxx (se debe cambiar x por el dominio del proyecto)
    ServerAlias www.xxxxxxxx.xxx  (se debe cambiar x por el dominio del proyecto)
    ErrorLog "logs/web.local-error_log"
</VirtualHost>

<VirtualHost *:443>
    DocumentRoot "/opt/lampp/htdocs/dob-prestashop-xxxxxxxxx"
    ServerName xxxxxxx.xxx (se debe cambiar x por el dominio del proyecto)
    ServerAlias xxxxxxx.xxx  (se debe cambiar x por el dominio del proyecto)

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/xxxxxxx.xxx-selfsigned.crt  (se sustituirá x por el nombre del certificado puesto)
    SSLCertificateKeyFile /etc/ssl/private/xxxxxxxx.xxx-selfsigned.key
</VirtualHost>
```
<br>

Ahora crearemos los certificados para el navegador. Incluiremos el siguiente código

  ```xml
  sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/xxxxxxx.xxx-selfsigned.key -out /etc/ssl/certs/xxxxxxx.xxx-selfsigned.crt
  ```
Se deben sustituir las xxxxxx por el nombre del certificado que le hayamos dado en el fichero `/opt/lampp/etc/extra/httpd-vhosts.conf`

<br>
También ha de editarse el fichero **/etc/hosts**
y agregar una nueva línea con **127.0.0.1** seguido del nombre del proyecto `xxxxxxx.com www.xxxxxxxx.com` (o .es según sea) deberá quedar tal que así:

```xml
127.0.0.1 ejemplo.com www.ejemplo.com 
```
Donde ejemplo.com se sistituirá por el dominio del proyecto que se está trabajando.
Una vez concluido el trabajo en el proyecto se deberá comentar la línea para ver el resultado en producción.

<br>
De manera opcional, si queremos montar la web en local sin falsear el dominio deberemos modificar el fichero hosts e incluir una nueva línea con el dominio que vamos a crear ejemplo `127.0.0.1 proyecto.local www.proyecto.local`.
Seguidamente en el fichero .htaccess deberemos cambiar todos los enlaces que apuntan al dominio de la web por el dominio que hemos creado en local.
Por último, buscaremos en la base de datos de phpmyadmin el archivo `xxxxx.ps_shop_url`, y cambiar el dominio de la web por el nuestro local. 

<br>

### 3. Montar proyecto 
Para hacer que el proyecto pueda abrir la base de datos, tenemos que irnos al fichero **app > config > parameters.php** y modificar los parámetros pertinentes para ello. 

#### Posibles errores.
Si nos da un error 500 puede ser que esté cacheado. Para ello nos vamos a dirigir a **proyecto > var > cache** y eliminamos la carpeta **prod**.
Ahora volvemos a intentar acceder a la web y ya debería funcionar. 
