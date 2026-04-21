## Esquema para el ejercicio
![Imagen](esquema-ejercicio3.PNG)

### Crear red net-wp
```bash
docker network create net-wp
```

### Para que persista la información es necesario conocer en dónde mysql almacena la información.
En el esquema del ejercicio carpeta del contenedor (a) es /var/lib/mysql

Ruta carpeta host: .../ejercicio3/db

### ¿Qué contiene la carpeta db del host?
Inicialmente estaba vacía, pero tras ejecutar el contenedor se llenó con los archivos binarios, esquemas y tablas del sistema de MySQL (como ibdata1, las carpetas mysql y performance_schema). Docker mapeó el directorio interno del contenedor hacia el host.

### Crear un contenedor con la imagen mysql:8  en la red net-wp, configurar las variables de entorno: MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER y MYSQL_PASSWORD
```bash
docker run -d --name db-wp --network net-wp -v "/$(pwd)/ejercicio3/db://var/lib/mysql" -e MYSQL_ROOT_PASSWORD=admin -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wpuser -e MYSQL_PASSWORD=wppass mysql:8
```

### ¿Qué observa en la carpeta db que se encontraba inicialmente vacía?
Se observan todos los archivos estructurales de la base de datos generados por el motor de MySQL en su primer arranque. El bind mount ha externalizado exitosamente el almacenamiento.

### Para que persista la información es necesario conocer en dónde wordpress almacena la información.
En el esquema del ejercicio la carpeta del contenedor (b) es /var/www/html

Ruta carpeta host: .../ejercicio3/www

### Crear un contenedor con la imagen wordpress en la red net-wp, configurar las variables de entorno WORDPRESS_DB_HOST, WORDPRESS_DB_USER, WORDPRESS_DB_PASSWORD y WORDPRESS_DB_NAME (los valores de estas variables corresponden a los del contenedor creado previamente)
```bash
docker run -d --name mi-wordpress -p 9500:80 --network net-wp -v "/$(pwd)/ejercicio3/www://var/www/html" -e WORDPRESS_DB_HOST=db-wp -e WORDPRESS_DB_USER=wpuser -e WORDPRESS_DB_PASSWORD=wppass -e WORDPRESS_DB_NAME=wordpress wordpress
```

### Personalizar la apariencia de wordpress y agregar una entrada

### Eliminar el contenedor y crearlo nuevamente, ¿qué ha sucedido?
Al destruir y recrear el contenedor, el sitio web se restablece exactamente en el mismo estado (mismo tema, plugins y entradas). Esto demuestra que al usar bind mounts tanto para la base de datos (contenido lógico) como para la carpeta web (contenido estático y multimedia), logramos una persistencia total, desacoplando completamente la información vital del ciclo de vida de los contenedores efímeros. 

