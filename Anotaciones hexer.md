# TRABAJAR CON PROYECTOS 

### PASO 1. Descargar repositorio en htdocs

### PASO 2. Seleccionar la version de php del proyecto

1. Desmontar la versión previa utilizada

`lvm disable`

2. Seleccionar la versión de lampp pertinente para el proyecto.

`lvm use 7.4 (por ejemplo)`

3. Reiniciamos el servicio en lampp.
`sudo lampp restart`

### PASO 3. Crear una base de datos y enlazarla a mysql.  

1. Creación de base de datos con terminal 

- Opción 1:
`echo "create database wp_xxxxxxxxx" | mysql -u root -p`

- Opción 2:
Entrar en mysql con:
```mysql 
mysql -u root -p
create database wp_xxxxxx.sql > mysql
```

      
                                             
### PASO 4. Enlazar la base de datos.zip o sql a la base de datos de mysql   
- Opción 1:       
`pv xxxxxxxxxxxxxxxx.sql | mysql -u root -p wp_xxxxxxxxxx`
El comando pv permite ver un progreso en la terminal.

- Opción 2: 
`mysql -u root -p wp_xxxxxx < /home/ruta/de/la/bbdd/xxxxxxx.sql`  

<br>

### PASO 4. DESFIJAR WEB.LOCAL Y ASOCIARLO AL NUEVO PROYECTO

Antes de unir otro proyecto y poder ser visualizado en web.local se ha de desvincular el anterior. Para ello se empleará el siguiente comando en terminal.

`unlink web.local`

Seguidamente se enlazará el siguiente proyecto

`ln -s dob-wordpress-ejemplo/ web.local`

### PASO 5. Cargar el config.php en el proyecto

### PASO 6. Abrir web.local



# Navegación fallida entre páginas web

Puede darse el caso que los enlaces dentro del sitio web no funcione bien. Para ello hay que ir al wordpress y, dentro de **AJUSTES** hay que pulsar **Enlaces permanentes** y guardar. De ese modo se regeneran los enlaces y el fichero .htaccess.

honeypot
akismet
conctact form  - flamingo

dejo la tarea en revisando

la clave de captcha va asociada a un grupo de dominio



## SAGE

Para utilizar SAGE debemos ver qué versión de node necesita el proyecto. 
Para SAGE 9 tenemos que utilizar una versión entre la 8 y la 12 de node.

### Instalación de Node (SAGE 9)

Nos vamos a la carpeta del proyecto y abrimos una nueva terminal sobre el directorio de wp_content > themes > nombre_proyecto. 

Una vez dentro del directorio en la terminal insertamos `nmv -v` para conocer la versión que tenemos corriendo.

Insertamos `nvm use 12` para ver si está instalado. En caso de no estarlo introduciremos el comando `nvm install 12` para instalarlo, volveremos a comprobar la versión de node que está corriendo. 

### Instalación de Yarn

Es importante saber que yarn se instala a nivel de versión de node, por lo que si se instala en la versión 12, para la 23 también habría que volver a instalarlo. 

Además también se ha de tener en cuenta que se puede instalar de forma local, -global o de sistema operativo. Nosotros vamos a instalarlo en global dentro de la versión para que pueda ser utilizado en cualquier desde cualquier carpeta donde estemos situados. 