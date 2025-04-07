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

<br>


# SAGE

Para utilizar SAGE debemos ver qué versión de node necesita el proyecto. 
Para SAGE 9 tenemos que utilizar una versión entre la 8 y la 12 de node.

<br>

### Instalación de Node (SAGE 9)

Nos vamos a la carpeta del proyecto y abrimos una nueva terminal sobre el directorio de wp_content > themes > nombre_proyecto. 

Una vez dentro del directorio en la terminal insertamos `nmv -v` para conocer la versión que tenemos corriendo.

Insertamos `nvm use 12` para ver si está instalado. En caso de no estarlo, introduciremos el comando `nvm install 12` para instalarlo y volveremos a comprobar la versión de node que está corriendo. 

<br>

### Instalación de Yarn

Es importante saber que yarn se instala a nivel de versión de node, por lo que si se instala en la versión 12, para la 23 también habría que volver a instalarlo. 

Además también se ha de tener en cuenta que se puede instalar de forma local, -global o de sistema operativo. Nosotros vamos a instalarlo en global dentro de la versión para que pueda ser utilizado en cualquier desde cualquier carpeta donde estemos situados. 

Para realizar la instalación de **Yarn** nos situamos sobre la carpeta del proyecto wp_content > themes > nombre_proyecto e introducimos el comando `yarn` para cargar las dependencias. 

Deberemos mirar en el package.json los scripts que acepta yarn a fin de que pueda realizar la inyección y el volcado del codigo que se vaya a modificar. Dependiendo de la versión de Sage, los scripts que se utilicen serán diferentes.
Así pues, para inicializarlo habrá que realizar un `yarn start` o un `yarn dev` dependiendo de la versión utilizada. 

**SAGE 9**
```json

scripts": {
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

Por último, realizamos un `git add .`, `git commit -m "mensaje con el commit hecho T:xxxxxx"` indicando la tarea y realizar un `git push`.



<br><br><br>
# Revisión de páginas

### SPAM

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
