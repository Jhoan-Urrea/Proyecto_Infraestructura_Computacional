<h1>Proyecto Infraestructura Computacional</h1>

<h5>Descripción</h5>
<p> Este proyecto tiene como objetivo crear una solución utilizando virtualización para satisfacer las necesidades de una organización que busca consolidar su infraestructura tecnológica. Actualmente, la organización posee tres servidores tipo torre, pero ha adquirido un nuevo servidor con capacidades mayores para optimizar su infraestructura.
<br>
<h5>Objetivos:</h5>
1.Crear tres contenedores en el nuevo servidor para proporcionar los siguientes servicios:
     -Apache: Servidor web.
     -MySQL: Sistema de gestión de bases de datos.
     -Nginx: Servidor proxy inverso y servidor HTTP.
2.Implementar los servicios utilizando contenedores Docker:
     -Cada servicio se construirá utilizando un archivo Dockerfile.
     -Replicar la creación de estas imágenes utilizando la herramienta Podman.
3.Configurar el almacenamiento de los contenedores:
     -Crear y configurar 3 RAID’s de nivel 1 (RAID 1).
     -Con cada RAID, crear un LVM (Logical Volume Manager).
     -Enlazar los LVM a los volúmenes creados dentro de los contenedores.
4.Validar el funcionamiento:
     -Realizar pruebas con archivos web para el servidor Apache y Nginx.
     -Probar la funcionalidad de bases de datos con MySQL.
<br>
<h5>Desarrollo del Proyecto</h5>
<br>
Este proyecto fue desrrollado en el sistema operativo Linux con la distribucion de Ubintu, se requiere tener istalado para poderse implemmentar.
<br>
primero que nada debemos descargar las ultimas actualizaciones o cambios de nuestro SO con el siguiente codigo: sudo apt update && sudo apt upgrade -y

  
  crear 3 RAIDS de nivel 1 con tres discos dos de 2GB y uno de 1GB, luego inicializamos los Raids como volumenes fisicos, creamos un grupo de volumenes para almacenarlos, y finalmente creamos los volumenes logicos.
<br>
<h3>Creacion de contenedores con Docker o Podman</h3>
<br>


</p>



