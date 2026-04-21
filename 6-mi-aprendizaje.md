El concepto de Volúmenes era completamente nuevo para mí. Aprender a desacoplar el almacenamiento de la vida útil del contenedor me parece muy importante. Ahora entiendo cómo garantizar que las bases de datos (como PostgreSQL) y los archivos de los proyectos no se pierdan al actualizar o destruir un contenedor, lo cual parece ser una pieza arquitectónica crucial para llevar a cabo la dockerización de proyectos a nivel profesional.

## Principales Aprendizajes Técnicos

**Bind Mounts vs. Volúmenes Nombrados:** Comprendí la diferencia práctica entre ambos enfoques. Los Bind Mounts son ideales para el desarrollo en vivo, reflejando cambios del código fuente desde el host inmediatamente, mientras que los Volúmenes Nombrados son el estándar profesional delegado a Docker para gestionar bases de datos en entornos más seguros y estables.

**Persistencia Multi-Contenedor:** El despliegue de CMS (WordPress y Drupal) demostró que la persistencia no solo aplica a la base de datos, sino también a los archivos estáticos de la aplicación (temas, plugins). Lograr que toda una infraestructura web sobreviva a la destrucción de sus contenedores valida el concepto de infraestructura inmutable.

## Resolución de Problemas

Durante esta práctica me enfrenté a un problema crítico de depuración que requirió ir más allá de los comandos básicos:

Al intentar levantar el contenedor `server-postgres` vinculado a su volumen nombrado, el contenedor "crasheaba", apagándose en menos de un segundo y desapareciendo al ejecutar `docker ps`. Como resultado, el cliente pgAdmin no podía encontrar la base de datos en la red. La clave para resolverlo no fue adivinar configuraciones de Windows, sino utilizar el comando `docker logs server-postgres`.

La lectura de los logs reveló un *breaking change* en la imagen oficial de PostgreSQL (versión 18+ que se descarga por defecto con la etiqueta `latest`). Esta actualización cambió la estructura de directorios internos requerida y ya no permite montar volúmenes directamente en la ruta clásica `/var/lib/postgresql/data` sin generar conflictos de compatibilidad.

Para no alterar la estructura de las rutas y garantizar la compatibilidad con el resto del despliegue, la solución más limpia fue fijar, con *pinning*, la versión de la imagen a una anterior estable. Cambiamos el comando para forzar el uso de `postgres:16`, lo que estabilizó el contenedor instantáneamente y permitió culminar con éxito la instalación.