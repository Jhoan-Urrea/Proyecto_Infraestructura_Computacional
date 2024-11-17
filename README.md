<h1>Proyecto Infraestructura Computacional</h1>

<h3>Descripción</h3>
<p> Este proyecto tiene como objetivo crear una solución utilizando virtualización para satisfacer las necesidades de una organización que busca consolidar su infraestructura tecnológica. Actualmente, la organización posee tres servidores tipo torre, pero ha adquirido un nuevo servidor con capacidades mayores para optimizar su infraestructura.
<br>
<h3>Objetivos:</h3>
<br>
1.Crear tres contenedores en el nuevo servidor para proporcionar los siguientes servicios:  
<br>     
-Apache: Servidor web.
<br>
-MySQL: Sistema de gestión de bases de datos.
<br>
-Nginx: Servidor proxy inverso y servidor HTTP.
<br>
<br>
 
2.Implementar los servicios utilizando contenedores Docker:
<br> 
-Cada servicio se construirá utilizando un archivo Dockerfile.
<br>
-Replicar la creación de estas imágenes utilizando la herramienta Podman.
<br>


3.Configurar el almacenamiento de los contenedores:
<br>
-Crear y configurar 3 RAID’s de nivel 1 (RAID 1).
<br>
-Con cada RAID, crear un LVM (Logical Volume Manager).
<br>
-Enlazar los LVM a los volúmenes creados dentro de los contenedores.
<br>

4.Validar el funcionamiento:
<br>
-Realizar pruebas con archivos web para el servidor Apache y Nginx.
<br>
-Probar la funcionalidad de bases de datos con MySQL.

<br>
<h3>Requisitos Técnicos</h3>
<br>
1.Sistema Operativo: Ubuntu
<br>
<br>
2. Herramuentas necesarias: Docker o Podman yGit
<br>
<br>
3. Conocimientos basicos en:
<br>
-Administración de sistemas Linux.
<br>
-Configuración de RAID y LVM.
<br>
-Creación de imágenes y gestión de contenedores.
<br>

<h3>Desarrollo del Proyecto</h3>
<br>
<h4>1.Preparar el entorno</h4>
<br>
Este proyecto se desarrolló sobre el sistema operativo Linux con la distribución Ubuntu, por lo tanto lo ideal es tenerlo instalado para la implementación del mismo.
<br>
Antes de proceder en la instalación de otros programas debemos descargar las ultimas actualizaciones o cambios de nuestro SO con el siguiente comando:
<br>
<h4>"sudo apt update && sudo apt upgrade -y"</h4>
<br>
<br>
Luego procedemos en la instalación de los siguientes programas o herramientas, estos son: Docker, Podman, Lvm2 y Mdadm.
<br>
<h3>Creacion de contenedores con Docker o Podman</h3>
<br>
Estas son herramientas para gestionar contenedores, compatibles con las mismas imágenes y comandos básicos. Ambas utilizan los estándares OCI, ofrecen aislamiento y seguridad, y se integran con plataformas de orquestación. La principal diferencia es que Podman no requiere un demonio en segundo plano, lo que mejora la seguridad en comparación con Docker.
<br>
<h3>Comandos de instalación docker:</h3>
<br>
sudo apt install docker.io -y 
<br>
sudo systemctl enable docker
<br>
sudo systemctl start docker
<br>
<h3>Comandos de instalación Podman:</h3>
<br>
sudo apt install podman -y

<h3>LVM2:</h3>
<br>
LVM2 (Logical Volume Manager 2) es una herramienta de gestión de volúmenes lógicos en Linux, que permite crear, redimensionar, eliminar y administrar particiones de almacenamiento de manera flexible y dinámica, sin depender de las limitaciones de las particiones físicas.
<br>
<h3>MDADM</h3>
<br>
MDADM es una herramienta de Linux para gestionar arreglos RAID (Redundant Array of Independent Disks), permitiendo la creación, configuración y mantenimiento de volúmenes RAID para mejorar la redundancia y el rendimiento del almacenamiento

<h3>Comando de instalación de LVM2 Y MDADM:</h3>
<br>
sudo apt install lvm2 mdadm -y

<h4>2.Creación y configuración de los Raid’s y LVM</h4>
<br>
Dado que cada RAID requiere la utilización de 3 discos de almacenamiento, es crucial verificar la disponibilidad de los discos necesarios para configurar cada arreglo. En total, se necesitan 9 discos, y lo ideal es que todos tengan el mismo tamaño para asegurar un rendimiento y balance óptimos.
<br>
<h3>Comando para consultar los discos:</h3>
<br>
lsblk
<br>
<h3>Comando para la creación y configuración de RAID’s:</h3>
<br>
sudo mdadm --create /dev/md0 --level=1 --raid-devices=3 /dev/sdb /dev/sdc
/dev/sdd
<br>
sudo mdadm --create /dev/md1 --level=1 --raid-devices=3 /dev/sde /dev/sdf
/dev/sdg
<br>
sudo mdadm --create /dev/md2 --level=1 --raid-devices=3 /dev/sdh /dev/sdi
/dev/sdj
<br>
<h3>Comando para comprobar la creación de RAID’s:</h3>
cat /proc/mdstat
<br>
<br>
Se procede a inicializar cada uno de los RAID’s como volúmenes físicos con el siguiente comando:
<br>
sudo pvcreate /dev/md0 /dev/md1 /dev/md2
<br>
<h3>Creación un grupo de volúmenes:</h3>
sudo vgcreate vgRaid /dev/md0 /dev/md1 /dev/md2

<br>
<h3>Creación los volúmenes lógicos:</h3>
sudo lvcreate -L 1G -n Apache vgRaid
<br>
sudo lvcreate -L 1G -n MySQL vgRaid
<br>
sudo lvcreate -L 1G -n Nginx vgRaid
<br>
<h3>Definición del formato para cada uno de los volúmenes lógicos:</h3>
sudo mkfs.ext4 /dev/vgRaid/Apache
<br>
sudo mkfs.ext4 /dev/vgRaid/MySQL
<br>
sudo mkfs.ext4 /dev/vgRaid/Nginx

<h4>3.Creación de los contenedores para cada servidor</h4>
Para dar una mejor organización del proyecto se creó una carpeta para cada uno, con el fin gestionar los recursos de forma independiente. Cada carpeta contendrá un DockerFile con su respectiva configuración y demás archivos necesarios.
<br>
<br>
<h3>Dockerfile para Apache:</h3>
FROM httpd:latest
<br>
COPY ./html/ /usr/local/apache2/htdocs/
<br>
EXPOSE 80
<br>
<h3>Dockerfile para MySQL:</h3>
FROM mysql:latest
<br>
ENV MYSQL_ROOT_PASSWORD=admin*****
<br>
ENV MYSQL_DATABASE=dbTMJ
<br>
EXPOSE 3306
<br>
<h3>Dockerfile para Nginx:</h3>
FROM nginx:latest
<br>
COPY ./html/ /usr/share/nginx/html/
<br>
EXPOSE 80
<br>
<h3>Creación de archivos HTML de prueba para Apache y Nginx</h3>
<h4>Apache/index.html:</h4>
<html>
<br>
  <head><title>Servidor Apache</title></head>
<br>
  <body><h1>¡Hola desde Apache!</h1></body>
<br>
</html>
<br>
<h4>Nginx/index.html:</h4>
<html>
<br>
  <head><title>Servidor Nginx</title></head>
<br>
  <body><h1>¡Hola desde Nginx!</h1></body>
<br>
</html>
<br>
<h4>4. Construir las images y ejecutar los contenedores</h4>
<h3>Comandos para la construcción de las imágenes:</h3>
docker build -t apache_image ./apache
<br>
docker build -t nginx_image ./nginx
<br>
docker build -t mysql_image ./mysql
<br>

<h3>Comandos para la construcción de las imágenes:</h3>
docker run -d --name apache_container -p 8080:80 apache_image
<br>
docker run -d --name nginx_container -p 8081:80 nginx_image
<br>
docker run -d --name mysql_container -p 3306:3306 mysql_image
<br>
<h4>5. Construir las images y ejecutar los contenedores</h4>
Consulta cada una de las direcciones de los contenedores asignadas con su respectivo puerto:
<br>
<br>
Apache: http://localhost:8080/
<br>
Nginx: http://localhost:3306/
<br>
MySQL: http://localhost:8081/
<br>
<br>

<h4>Equipo de Trabajo</h4>

Monica Maria Velasquez
<br>
Tatiana Lisbeth Zamora Andrade
<br>
Jhoan Sebastian Urrea Sanchez
<br>

</p>
<h3>Universdad del Quindío</h3>
<h4>Ingenieria en Sistemas y Computacion Nocturna</h4>
<h4>Infraestructura Computacional</h4>



