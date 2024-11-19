# Proyecto Infraestructura Computacional

### Descripción
Este proyecto tiene como objetivo diseñar una solución de infraestructura tecnológica mediante virtualización, para satisfacer las necesidades de una organización que busca consolidar su infraestructura. Actualmente, la organización cuenta con tres servidores tipo torre, pero ha adquirido un nuevo servidor de mayor capacidad con el fin de optimizar su infraestructura tecnológica.

### Objetivos

1. **Crear contenedores en el nuevo servidor:**
   Se crearán tres contenedores, uno para cada servicio en el nuevo servidor:
   - **Apache:** Servidor web.
   - **MySQL:** Sistema de gestión de bases de datos.
   - **Nginx:** Servidor proxy inverso y servidor HTTP.

2. **Implementar los servicios utilizando Docker y Podman:**
   Cada servicio será implementado utilizando un archivo Dockerfile para construir la imagen correspondiente. Además, se replicará la creación de imágenes utilizando la herramienta Podman.

3. **Configurar el almacenamiento de los contenedores:**
   Se realizarán las siguientes configuraciones de almacenamiento:
   - Crear y configurar tres arreglos RAID de nivel 1 (RAID 1) para redundancia y alta disponibilidad.
   - Cada RAID se utilizará para crear un LVM (Logical Volume Manager), proporcionando flexibilidad en la gestión del almacenamiento.
   - Enlazar los volúmenes LVM a los contenedores creados, garantizando un almacenamiento persistente y eficiente.

4. **Validar el funcionamiento:**
   Se realizarán las siguientes pruebas para asegurar que la infraestructura funciona correctamente:
   - Probar la funcionalidad del servidor Apache y Nginx utilizando archivos web de prueba.
   - Validar la funcionalidad de las bases de datos con MySQL.

### Requisitos Técnicos

- **Sistema Operativo:** Ubuntu

- **Herramientas necesarias:**
  - Docker o Podman
  - Git

- **Conocimientos básicos requeridos:**
  - Administración de sistemas Linux.
  - Configuración de RAID y LVM.
  - Creación de imágenes y gestión de contenedores.


### Desarrollo del Proyecto

#### 1. Preparar el entorno

Este proyecto se desarrolló sobre el sistema operativo Linux con la distribución Ubuntu, por lo tanto, lo ideal es tenerlo instalado para la implementación del mismo.

Antes de proceder con la instalación de otros programas, debemos descargar las últimas actualizaciones o cambios de nuestro SO con el siguiente comando:
~~~
sudo apt update && sudo apt upgrade -y
~~~
Luego procedemos con la instalación de los siguientes programas o herramientas:

- Docker
- Podman
- Lvm2
- Mdadm

### Creación de contenedores con Docker o Podman

Docker y Podman son herramientas para gestionar contenedores, compatibles con las mismas imágenes y comandos básicos. Ambas utilizan los estándares OCI, ofrecen aislamiento y seguridad, y se integran con plataformas de orquestación.

La principal diferencia entre ellas es que Podman no requiere un demonio en segundo plano, lo que mejora la seguridad en comparación con Docker.

### Comandos de instalación de Docker:

1. Instalar Docker:
~~~
sudo apt install docker.io -y
~~~
~~~
sudo systemctl enable docker
~~~
~~~
sudo systemctl start docker
~~~

2. Comandos de instalación Podman:
~~~
sudo apt install podman -y
~~~
### LVM2

LVM2 (Logical Volume Manager 2) es una herramienta de gestión de volúmenes lógicos en Linux. Permite crear, redimensionar, eliminar y administrar particiones de almacenamiento de manera flexible y dinámica, sin depender de las limitaciones de las particiones físicas.

### MDADM

MDADM es una herramienta de Linux para gestionar arreglos RAID (Redundant Array of Independent Disks), permitiendo la creación, configuración y mantenimiento de volúmenes RAID para mejorar la redundancia y el rendimiento del almacenamiento.


### Comando de instalación de LVM2 Y MDADM:
~~~
sudo apt install lvm2 mdadm -y
~~~

### Creación y configuración de los RAID’s y LVM

Dado que cada RAID requiere la utilización de 3 discos de almacenamiento, es crucial verificar la disponibilidad de los discos necesarios para configurar cada arreglo. En total, se necesitan 9 discos, y lo ideal es que todos tengan el mismo tamaño para asegurar un rendimiento y balance óptimos.

### Comando para consultar los discos:
~~~
lsblk
~~~

### Comando para la creación y configuración de RAID’s:
~~~
sudo mdadm --create /dev/md0 --level=1 --raid-devices=3 /dev/sdb /dev/sdc
/dev/sdd

sudo mdadm --create /dev/md1 --level=1 --raid-devices=3 /dev/sde /dev/sdf
/dev/sdg

sudo mdadm --create /dev/md2 --level=1 --raid-devices=3 /dev/sdh /dev/sdi
/dev/sdj
~~~

### Comando para comprobar la creación de RAID’s:
~~~
cat /proc/mdstat
~~~
Se procede a inicializar cada uno de los RAID’s como volúmenes físicos con el siguiente comando:
~~~
sudo pvcreate /dev/md0 /dev/md1 /dev/md2
~~~
### Creación un grupo de volúmenes:
~~~
sudo vgcreate vgRaid /dev/md0 /dev/md1 /dev/md2
~~~

### Creación los volúmenes lógicos:
~~~
sudo lvcreate -L 1G -n Apache vgRaid
sudo lvcreate -L 1G -n MySQL vgRaid
sudo lvcreate -L 1G -n Nginx vgRaid
~~~

### Definición del formato para cada uno de los volúmenes lógicos:
~~~
sudo mkfs.ext4 /dev/vgRaid/Apache
sudo mkfs.ext4 /dev/vgRaid/MySQL
sudo mkfs.ext4 /dev/vgRaid/Nginx
~~~

### Creación de los contenedores para cada servidor
Para dar una mejor organización del proyecto se creó una carpeta para cada uno, con el fin gestionar los recursos de forma independiente. Cada carpeta contendrá un DockerFile con su respectiva configuración y demás archivos necesarios.

#### Dockerfile para Apache:
~~~
FROM httpd:latest
COPY ./html/ /usr/local/apache2/htdocs/
EXPOSE 80
~~~

#### Dockerfile para MySQL:
~~~
FROM mysql:latest
ENV MYSQL_ROOT_PASSWORD=admin*****
ENV MYSQL_DATABASE=dbTMJ
EXPOSE 3306
~~~

#### Dockerfile para Nginx:
~~~
FROM nginx:latest
COPY ./html/ /usr/share/nginx/html/
EXPOSE 80
~~~

#### Creación de archivos HTML de prueba para Apache y Nginx
~~~
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
~~~

### Construir las images y ejecutar los contenedores

#### Comandos para la construcción de las imágenes:
~~~
docker build -t apache_image ./apache
docker build -t nginx_image ./nginx
docker build -t mysql_image ./mysql
~~~

#### Comandos para la construcción de las imágenes:
~~~
docker run -d --name apache_container -p 8080:80 apache_image
docker run -d --name nginx_container -p 8081:80 nginx_image
docker run -d --name mysql_container -p 3306:3306 mysql_image
~~~

#### Construir las images y ejecutar los contenedores
Consulta cada una de las direcciones de los contenedores asignadas con su respectivo puerto:

~~~
Apache: http://localhost:8080/
~~~
~~~
Nginx: http://localhost:3306/
~~~
~~~
MySQL: http://localhost:8081/
~~~

#### Equipo de Trabajo

- Monica Maria Velasquez
- Tatiana Lisbeth Zamora Andrade
- Jhoan Sebastian Urrea Sanchez

### Universidad del Quindío

#### Ingeniería en Sistemas y Computación Nocturna
#### Infraestructura Computacional




