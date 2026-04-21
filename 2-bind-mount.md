# BIND MOUNT
En un bind mount mapeamos (montar) un directorio o archivo específico del sistema de archivos del host con una parte del sistema de ficheros del contenedor.

```
docker run -d --name <nombre contenedor> -v <ruta carpeta host>:<ruta carpeta contenedor> <imagen> 
```
ó
```
docker run -d --name <nombre contenedor> --mount type=bind,source=<ruta carpeta host>,target=<ruta carpeta contenedor> <imagen>
```
- destination, dst, target: La ruta donde se monta el archivo o directorio en el contenedor.
- source, src: El origen del montaje.
  
### En tu computador crear una carpeta llamada nginx y dentro de esta carpeta crea otra llamada html. Como se aprecia en la figura.
![Volúmenes](directorio.PNG)

### Crear un contenedor con la imagen nginx:alpine, mapear todos por puertos, para la ruta carpeta host colocar el directorio en donde se encuentra la carpeta html en tu computador y para la ruta carpeta contenedor: /usr/share/nginx/html (esta ruta se obtiene al revisar la documentación de la imagen)
![Volúmenes](volumen-host.PNG)
```bash
docker run -d -P --name mi-nginx-bind -v "/$(pwd)/nginx/html://usr/share/nginx/html" nginx:alpine
```

### ¿Qué sucede al ingresar al servidor de nginx?
Se muestra un error 403 Forbidden (o el directorio vacío si estuviera habilitado). Esto ocurre porque la carpeta del host está vacía y Nginx no encuentra un archivo index.html para servir.

### ¿Qué pasa con el archivo index.html del contenedor?
El archivo original de la imagen queda oculto (efecto conocido como shadowing). Al realizar un montaje de tipo bind, el directorio del host se superpone y reemplaza temporalmente el contenido original del directorio en el contenedor.

### Ir a https://html5up.net/ y descargar un template gratuito, descomprirlo dentro de tu computador en la carpeta html
### ¿Qué sucede al ingresar al servidor de nginx?
El sitio web se actualiza inmediatamente mostrando la plantilla descargada. Esto demuestra que cualquier cambio realizado en la carpeta del host se refleja en tiempo real dentro del contenedor, lo cual es ideal para entornos de desarrollo.

### Eliminar el contenedor
```bash
docker rm -f mi-nginx-bind
```

### ¿Qué sucede al crear nuevamente un contenedor montado al directorio definidos anteriormente?
El sitio web sigue funcionando y mostrando la plantilla sin pérdida de información. Esto comprueba la persistencia de datos: la información sobrevive al ciclo de vida del contenedor porque reside de forma segura en el sistema de archivos del host.


