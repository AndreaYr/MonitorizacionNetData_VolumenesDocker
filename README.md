# Bitácora del Proyecto - Infraestructura Computacional  
## 👥 Integrantes 
- Yuri Andrea Ramírez Reyes
- Juan Pablo Buitrago
- Nicolas Trejos

## 🏗️ Arquitectura de Almacenamiento
- **Total discos**: 9 discos
- **Configuración RAID**: 3 arreglos RAID 1
- **Discos por RAID**: 3 discos cada uno
- **Asignación**:
  - RAID 0 (md0): 3 discos
  - RAID 1 (md1): 3 discos
  - RAID 2 (md2): 3 discos

## 📝 Pasos Realizados:

**Creación de los 9 Discos:**

Se crearon 9 discos duros virtuales con la siguiente distribución de tamaños:

- RAID 1 (md0):

/dev/sdb - 2GB
/dev/sdc - 2GB
/dev/sdh - 1GB
Total físico: 5GB

- RAID 2 (md1):

/dev/sdd - 2GB
/dev/sde - 2GB
/dev/sdi - 1GB
Total físico: 5GB

- RAID 3 (md2):
/dev/sdf - 2GB
/dev/sdg - 2GB
/dev/sdj - 1GB
Total físico: 5GB

Para la verificación de los discos utilizamos el comando: lsblk

<img width="301" height="144" alt="image" src="https://github.com/user-attachments/assets/353a9e30-f3c6-4eee-a6aa-c2841dd836c5" />

# 🔧 Creación de los RAIDS

* ## RAID 1 (md0)
Paso 1: Creación del RAID:
### Comando utilizado
sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=3 /dev/sdb /dev/sdc /dev/sdh

Parámetros Configurados:
* --level=1: RAID 1 (Espejo)
* --raid-devices=3: 3 discos en el arreglo

Paso 2: Vericación del estado:
### Comando utilizado
sudo mdadm --detail /dev/md0

**En el estado obtenemos resultados:**

<img width="348" height="311" alt="image" src="https://github.com/user-attachments/assets/4a3b4923-1550-4893-ac5f-63d90a514816" />


* ## RAID 2 (md1)
Paso 1: Creación del RAID:
### Comando utilizado
sudo mdadm --create --verbose /dev/md1 --level=1 --raid-devices=3 /dev/sdd /dev/sde /dev/sdi

Parámetros Configurados:
--level=1: RAID 2 (Espejo)
--raid-devices=3: 3 discos en el arreglo

Paso 2: Vericación del estado:
### Comando utilizado
sudo mdadm --detail /dev/md1

**En el estado obtenemos resultados:**

<img width="372" height="332" alt="image" src="https://github.com/user-attachments/assets/a8fa501b-8683-42c6-824c-8aca7456d824" />


* ## RAID 3 (md2)
Paso 1: Creación del RAID:
### Comando utilizado
sudo mdadm --create --verbose /dev/md2 --level=1 --raid-devices=3 /dev/sdf /dev/sdg /dev/sdj

Parámetros Configurados:
--level=1: RAID 3 (Espejo)
--raid-devices=3: 3 discos en el arreglo

Paso 2: Vericación del estado:
### Comando utilizado
sudo mdadm --detail /dev/md2

**En el estado obtenemos resultados:**

<img width="395" height="332" alt="image" src="https://github.com/user-attachments/assets/245a068c-ef9e-456a-a599-f2587dee1a4d" />

Una vez creados los 3 RAIDS editamos el archivo de configuración de mdadm para agregar la información de los RAIDS con el siguiente comando:
sudo nano /etc/mdadm/mdadm.conf

<img width="437" height="324" alt="image" src="https://github.com/user-attachments/assets/091dd9e2-93f9-481f-a91f-684b8e531aaf" />

## **🗂️ Configuración de Sistema de Archivos EXT4**
Configuración del sistema de archivos EXT4 en los arreglos RAID para permitir el almacenamiento estructurado de datos.

### 🔧 Comando Ejecutado
- sudo mkfs.ext4 /dev/md0
- sudo mkfs.ext4 /dev/md1
- sudo mkfs.ext4 /dev/md2

## 📂 Creación de Directorios para Montaje de RAID
Se crea directorios específicos para montar cada RAID.
### 🔧 Comando Ejecutado para montar cada RAID
- sudo mount /dev/md0 /mnt/apache
- sudo mount /dev/md1 /mnt/mysql
- sudo mount /dev/md2 /mnt/nginx

<img width="292" height="45" alt="image" src="https://github.com/user-attachments/assets/1584ed37-a418-4866-bb3c-99279afcf06e" />

# 🏗️ Configuración de Volúmenes Físicos LVM
Crear los Physical Volumes (Volúmenes Físicos) sobre los arreglos RAID para preparar la configuración de LVM.

## 🔧 Comandos Ejecutados
### Creación de Volúmenes Físicos:
- sudo pvcreate /dev/md0
- sudo pvcreate /dev/md1
- sudo pvcreate /dev/md2

  ### Mostrar dispositivos fisicos
  - sudo pvs
  <img width="255" height="80" alt="image" src="https://github.com/user-attachments/assets/bc793ac2-06e1-44ca-8986-683356bbd60e" />

# 🏗️ Configuración de Grupo de Volúmenes
Se crea los grupos de volúmenes que agruparán los volúmenes físicos para permitir la gestión flexible del almacenamiento.

## 🔧 Comandos Ejecutados
### Creación de Grupo de Volúmenes:
- sudo vgcreate vgproyecto /dev/md0 /dev/md1 /dev/md2

### Mostrar Grupo de Volúmenes
- sudo vgs
<img width="253" height="65" alt="image" src="https://github.com/user-attachments/assets/e06e08ac-8cb3-4562-9721-e90843b181ca" />

# 🏗️ Configuración de Volúmenes Lógicos
Crear 3 volúmenes lógicos del mismo tamaño, uno para cada contenedor (Apache, MySQL, Nginx).

## 🔧 Comandos Ejecutados
### Creación de los Volúmenes Lógicos:
- sudo lvcreate -L 996M -n lvmapache vgproyecto
- sudo lvcreate -L 996M -n lvmmysql vgproyecto
- sudo lvcreate -L 996M -n lvmnginx vgproyecto

### Mostrar los Volúmenes Lógicos
- sudo lvs
<img width="488" height="70" alt="image" src="https://github.com/user-attachments/assets/01717d4c-9c90-4f24-9768-47968f404177" />

## 🗂️ Formateo y Configuración de Volúmenes Lógicos
Formatear los volúmenes lógicos con sistema de archivos EXT4, crear directorios de montaje y enlazar los volúmenes para su uso por los contenedores.

### 🔧 Dar Formato con EXT4 para los Volúmenes Lógicos
- sudo mkfs.ext4 /dev/vgproyecto/lvmapache
- sudo mkfs.ext4 /dev/vgproyecto/lvmmysql
- sudo mkfs.ext4 /dev/vgproyecto/lvmnginx

### 🗂️ Creación de Directorios para el Montaje
- mkdir apache
- mkdir mysql
- mkdir nginx

### 🗂️ Enlazar Directorios con los Volumenes Lógicos
- sudo mount /dev/vgproyecto/lvmapache /home/yuriandrea/apache
- sudo mount /dev/vgproyecto/lvmmysql /home/yuriandrea/mysql
- sudo mount /dev/vgproyecto/lvmnginx /home/yuriandrea/nginx

### 🗂️ Verificar Montaje
Para verificar que este enlazado correctamente utilizamos el comando "mount"

<img width="426" height="38" alt="image" src="https://github.com/user-attachments/assets/95910632-5e1e-4bf3-9d71-509f028432cd" />

# DockerFiles
## Crear Estructura de Carpetas para DockerFile
Para crear la estructura de carpetas utilizamos el comando:

mkdir -p docker/{apache,mysql,nginx}

## Crear DockerFiles
### 🔧 Comandos Ejecutados
Crear:
- nano docker/apache/Dockerfile
- nano docker/mysql/Dockerfile
- nano docker/nginx/Dockerfile
  
Mostrar:
- cat docker/apache/Dockerfile
- cat docker/mysql/Dockerfile
- nano docker/nginx/Dockerfile

<img width="345" height="241" alt="image" src="https://github.com/user-attachments/assets/3becfbf9-88cc-4904-a94a-737eb2a3e4ce" />

## Construir Imágenes Docker
Primero creamos las imagenes y déspues las mostramos

## Imagenes Docker
### 🔧 Comandos Ejecutados
- sudo docker build -t miapache -f docker/apache/Dockerfile .
- sudo docker build -t mysql -f docker/mysql/Dockerfile .
- sudo docker build -t minginx -f docker/nginx/Dockerfile .

- sudo docker images
<img width="332" height="104" alt="image" src="https://github.com/user-attachments/assets/f0e14e28-878b-43ad-99f2-820b79bb3a46" />

## Ejecutar Contenedores
Ejecutar los contenedores Docker utilizando las imágenes personalizadas creadas y conectarlas con los volúmenes LVM configurados.

### 🔧 Comandos Ejecutados
- sudo docker run -d --name apachecontainer -p 8080:80 -v /home/yuriandrea/apache:/usr/local/apache2/htdocs miapache:latest
- docker run -d --name mysqlcontainer -p 3306:3306 -v /home/yuriandrea/mysql:/var/lib/mysql mimysql:latest
- docker run -d --name nginxcontainer -p 8081:80 -v /home/yuriandrea/nginx:/usr/share/nginx/html minginx:latest

- sudo docker ps
<img width="407" height="146" alt="image" src="https://github.com/user-attachments/assets/d549ef5d-abcb-4367-9ad1-01bb0c844c60" />

## 🔧 Pruebas
Se realizan las pruebas de funcionamiento de los contenedores.

### Prueba Apache
- echo "<h1>Apache Funcionando Correctamente</h1>" > /home/yuriandrea/apache/index.html
  
- curl http://localhost:8080

<img width="410" height="38" alt="image" src="https://github.com/user-attachments/assets/34d3fde9-90d9-4c8b-ba9c-d22fd2155e07" />

### Prueba Mysql
sudo docker exec -it mysqlcontainer mysql -u root -p

CREATE DATABASE proyecto_final;
USE proyecto_final;
CREATE TABLE usuarios (id INT, nombre VARCHAR(50));
INSERT INTO usuarios VALUES (1, 'Usuario Prueba');
SELECT * FROM usuarios;
EXIT;

<img width="410" height="200" alt="image" src="https://github.com/user-attachments/assets/356f2352-9e2e-4e58-bf54-a80a1c9eaa55" />

### Prueba Nginx
- echo "<h1>Nginx Funcionando Correctamente</h1>" > /home/yuriandrea/nginx/index.html
- curl http://localhost:8081
<img width="406" height="132" alt="image" src="https://github.com/user-attachments/assets/97d42dc5-f3e7-4620-b386-cc815acfbc30" />

### Prueba de Persistencia
1. Reiniciamos todos los contenedores con el siguiente comando:
docker restart apachecontainer mysqlcontainer nginxcontainer
2. Verificar que los contenedores están activos nuevamente con el código:
sudo docker ps
3. Verificar que los datos persisten después del reinicio con los siguientes comandos:
   - curl http://localhost:8080
   - curl http://localhost:8081
   - docker exec -it mysql-container mysql -u root -p -e "USE proyecto_final; SELECT * FROM usuarios;"


<img width="409" height="149" alt="image" src="https://github.com/user-attachments/assets/3f99bfbe-7fb3-4ea5-aceb-ff5484812271" />

# Podman
## Estructura de Carpetas
mkdir -p podman/{apache,mysql,nginx}

## Crear Containerfiles
- nano podman/apache/Containerfile
  
En el archivo se agrego:
FROM httpd:2.4
VOLUME /usr/local/apache2/htdocs
EXPOSE 80
CMD ["httpd-foreground"]

- nano podman/mysql/Containerfile
  
En el archivo se agrego:
FROM mysql:8.0
VOLUME /var/lib/mysql
ENV MYSQL_ROOT_PASSWORD=root123
EXPOSE 3306
CMD ["mysqld"]

- nano podman/nginx/Containerfile

En el archivo se agrego:
FROM nginx:latest
VOLUME /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

## Construir imagenes con podman
### 🔧 Comandos Ejecutados
- podman build -t miapachepodman -f podman/apache/Containerfile .
- podman build -t mysqlpodman -f podman/mysql/Containerfile .
- podman build -t minginxpodman -f podman/nginx/Containerfile .
<img width="400" height="95" alt="image" src="https://github.com/user-attachments/assets/9af82f93-109f-42ef-aee3-02f831bec6b5" />

## Ejecutar Contenedores
### 🔧 Comandos Ejecutados
- podman run -d --name apachepodman -p 8082:80 -v /home/yuriandrea/apache:/usr/local/apache2/htdocs miapachepodman
- podman run -d --name nginxpodman -p 8083:80 -v /home/yuriandrea/nginx:/usr/share/nginx/html minginxpodman
- podman run -d --name mysqlpodman -p 3307:3306 -v /home/yuriandrea/mysql:/var/lib/mysql mysqlpodman

- podman ps
<img width="412" height="97" alt="image" src="https://github.com/user-attachments/assets/45a01911-12e6-45ed-8f31-3b9a5a8cedff" />

2 contenedores Podman ejecutándose:
  - Apache: http://localhost:8082 
  - Nginx: http://localhost:8083
  - Contenedor MySQL creado (imagen construida, contenedor existente) pero no       inicia

## Pruebas
### Apache Podman
** Verifica que Apache Podman responde en puerto 8082: **
- curl http://localhost:8082

** Crea archivo HTML específico en volumen LVM compartido **
- echo "<h1>Apache Podman funcionando</h1>" > /mnt/apache-data/index-podman.html

** Confirma que el archivo es servido por Podman y la persistencia funciona  **
- curl http://localhost:8082/index-podman.html

  <img width="407" height="152" alt="image" src="https://github.com/user-attachments/assets/83f1863b-1570-4cad-9a87-46414a38ca74" />

### Apache Nginx
** Verifica que Apache Podman responde en puerto 8083: **
- curl http://localhost:8083

** Crea archivo HTML específico en volumen LVM compartido **
- echo "<h1>Apache Podman funcionando</h1>" > /mnt/apache-data/index-podman.html

** Confirma que el archivo es servido por Podman y la persistencia funciona  **
- curl http://localhost:8082/index-podman.html

<img width="430" height="180" alt="image" src="https://github.com/user-attachments/assets/96b2ce8a-1833-40b3-aeda-6107e11fb68a" />

## Prueba de Containerfiles
Verificamos que los Containerfiles existen y son correctos

### Comandos Utilizados
cat podman/apache/Containerfile
cat podman/mysql/Containerfile
cat podman/nginx/Containerfile

<img width="320" height="199" alt="image" src="https://github.com/user-attachments/assets/f14de504-f10a-4de6-9f47-fbf4aa8d67ba" />

### 🔧 OBSERVACIÓN TÉCNICA:
MySQL con Podman presenta una limitación técnica conocida: servicios de base de datos requieren configuración adicional específica en entornos Podman rootless. Esto no afecta la demostración exitosa de la herramienta.


# Implementación de NetData
## Pasos Realizados
1. Creación del archivo DockerCompose
- nano docker-compose.yml
2. Se agrego el siguiente código
  netdata:
  image: netdata/netdata
  container_name: netdata
  hostname: yurlandrea
  ports:
    - "19999:19999"
  cap_add:
    - SYS_PTRACE
  security_opt:
    - apparmor=unconfined
  volumes:
    - netdata_lib:/var/lib/netdata
    - netdata_cache:/var/cache/netdata
    - /etc/passwd:/host/etc/passwd:ro
    - /etc/group:/host/etc/group:ro
    - /proc:/host/proc:ro
    - /sys:/host/sys:ro
    - /etc/os-release:/host/etc/os-release:ro
    - /var/run/docker.sock:/var/run/docker.sock:ro
3. Despliegue del contenedor
  - sudo docker-compose up -d
<img width="287" height="32" alt="image" src="https://github.com/user-attachments/assets/bd52fa05-5eb7-4a06-a9d3-7f5c9846f42f" />
4. Verificamos el estado
  - sudo docker-compose ps
<img width="425" height="78" alt="image" src="https://github.com/user-attachments/assets/229be40b-ca33-4a97-bcf0-16cd6cba3e30" />

# Acceso a la interfaz Web
http://localhost:19999

##Secciones Principales de la Interfaz:
1. Dashboard Principal
 - Métricas recolectadas: 4,048 en tiempo real
 - Nodos monitoreados: 1 nodo activo
 - Resolución de datos: 1s, 1m, 1h
 - Retención: 14 días a 2 años
<img width="602" height="380" alt="image" src="https://github.com/user-attachments/assets/05c04235-1b3b-402e-9866-8b47d9b21bc6" />

<img width="605" height="383" alt="image" src="https://github.com/user-attachments/assets/c3180428-09d1-4429-aabd-f3b89fbc37a5" />

<img width="613" height="342" alt="image" src="https://github.com/user-attachments/assets/c7aab127-176a-4c94-90d4-352a11f1d3bf" />

<img width="607" height="388" alt="image" src="https://github.com/user-attachments/assets/4866cd03-0b85-4149-bebf-abc2effffd39" />


