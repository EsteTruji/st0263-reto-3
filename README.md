
# Info de la materia: ST0263 - Tópicos Especiales en Telemática

### Estudiantes:
  - Esteban Trujillo Carmona, etrujilloc@eafit.edu.co
  - Viviana Hoyos Sierra, vhoyoss@eafit.edu.co
  - Damian Duque López, daduquel@eafit.edu.co
  - Valentina Morales Villada, vmoralesv@eafit.edu.co

### Profesor: Edwin Montoya, emontoya@eafit.edu.co

# Reto 3 

# Video sustentación
[![Video sustentacion](https://i9.ytimg.com/vi_webp/gDpiXgXeUiM/mq3.webp?sqp=CNC6krEG-oaymwEmCMACELQB8quKqQMa8AEB-AH-CYAC0AWKAgwIABABGGUgTyg6MA8=&rs=AOn4CLCb5bybaUKPS6t3rYw_oJ573G7SMg)]([https://youtu.be/gDpiXgXeUiM])

- Enlace: **_https://youtu.be/gDpiXgXeUiM_**

## 1. Breve descripción de la actividad
Se desplegó un CMS wordpress (app monolítica) en una arquitectura distribuida haciendo uso de tecnologías como Docker, proveedores de dominio como GoDaddy y servicios de AWS.

En este reto 3 utiliza nginx como balanceador de cargas (LB) de la capa de aplicación del wordpress.
Además de lo anterior, se utilizan 2 servidores adicionales, uno para la base de datos (DBServer) y otro para archivos (FileServer). El DBServer utiliza la BD en docker.El FileServer implementa un NFSServer.
A nivel de recursos, se desplegaron (5) maquinas virtuales en aws academy.



### 1.1. Que aspectos cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

**NO FUNCIONALES**

-	**DISPONIBILIDAD:**
    -  Despliegue de  aplicación wordpress dockerizada en varios nodos.
    -  Implementar un balanceador de cargas basado en nginx que reciba el tráfico web https de Internet con múltiples instancias de procesamiento.
   

**FUNCIONALES**
-	Almacenamiento en la capa de datos con una instancia de bases de datos mysql.
- Almacenamiento de archivos con una instancia de archivos distribuidos implementado con servicio NFS.
- Conexion de servicios de wordpress con servicio NFS.
- Utilización de https.



### 1.2. Que aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

Se cumplieron con todos los requisitos propuestos en el reto por el profesor.

## 2. Información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas.

Se implementó para este proyecto la siguiente arquitectura basada en la propuesta del profesor.

[Diagrama](https://drive.google.com/file/d/11rlnW3-tqprr7dtQRbYrXvi0q4SP7CP8/view?usp=sharing)

![Reto3-TT drawio](https://github.com/EsteTruji/st0263-reto-3/assets/81714113/2c14edb4-a5c9-40be-aeb5-a9f91cfdc248)

## 3. Descripción del ambiente de ejecución lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.


IPs correspondientes a cada una de las 6 instancias utilizadas en el reto 3:

IPs Privadas:

- WordPress: 10.0.129.104
- WordPress2: 10.0.138.82
- Load balancer: 10.0.10.150
- DataBase: 10.0.134.183
- NFS: 10.0.138.240
- Bastion Host: 10.0.3.155

IPs Públicas:
- Load balancer: 3.234.42.123
- Bastion Host: 35.175.224.118

Para garantizar el correcto desarrollo y cumplimiento de los requisitos del reto 3 se instaló Docker dentro de 4 instancias, las cuales fueron: las 2 instancias dedicadas a WordPress, la instancia del load balancer y la instancia donde se desplegó la base de datos.

Por último, también se instalaron los siguientes paquetes dentro de la instancia utilizada para el load balancer:
- certbot 2.10.0
- letsencrypt 2.10.0
- nginx 1.25.5



## 4. Guía paso a paso de instalación y configuración

Para este proyecto, se realizó el despliegue de varias instancias según lo solicitado en la descripción. Esta es una pequeña guía del proceso de instalación y configuración de estas instancias.

- Se crea la VPC donde van a estar almacenadas las instancias que crearemos. Esta está compuesta de dos subredes, una pública y otra privada.

![screenshot 2024-04-20 (1)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/7eed552d-e3c2-4d62-90dd-553dfa44900a)


![screenshot 2024-04-16 (1)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/32d1a6fd-c3f9-452d-a998-247eab15263b)


![screenshot 2024-04-16 (2)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/5c4979cf-aea1-4bda-9837-f9f4e31fd479)


- Se crean 3 instancias dentro de esta subred privada, cada una para los siguientes servicios Database, Wordpress, NFS. Todas estas con ips privadas.

![screenshot 2024-04-16 (7)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/14adb39b-56dd-44bf-9b66-ca42b792de4f)


![screenshot 2024-04-16 (8)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/83e7de4a-8fd9-4114-9821-dcfbb21636b9)


![screenshot 2024-04-16 (9)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/05e73e45-d973-4c66-8023-46a7696f4744)


- Dentro de la subred pública de la vpc se crean 2 instancias.
    - La instancia del Load Balancer y se le asocia una ip pública elástica.

    ![screenshot 2024-04-16 (10)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/c4a54706-e013-420c-a415-b420371d3ef9)


    - Se crea la instancia de Bastion Host y se le asocia una ip pública.

- Así ya tenemos las cinco instancias dentro de la VPC que creamos inicialmente.

![screenshot 2024-04-16 (11)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/5f18a288-8880-4a66-8960-2e8214ade7ca)


![screenshot 2024-04-16 (12)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/d29c8fc6-d030-4be8-9258-076b68f72617)



- Ahora procedemos a agregar el dominio (con el valor de la IP elástica del LB)  el subdominio en el DNS de igual forma y finalmente el registro CNAME. Dentro de la configuración de registros del proveedor, se vería de la siguiente manera: 

![screenshot 2024-04-16 (13)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/50ab8642-165b-44cc-90b7-10de4dd2b5c8)



```
A        @        IP elástica del balanceador
A        reto3    IP elástica del balanceador
CNAME    www      reto3.toysnt.shop
```

- Modificamos las reglas del security group de la instancia de Database para asociarla con una base de datos MySQL.

![screenshot 2024-04-16 (14)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/7f2eeafa-80fa-4d80-a454-489bd2baf83d)


![screenshot 2024-04-16 (15)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/76d2d17d-fe38-4b1e-b9d2-e4a8302fbb44)


![screenshot 2024-04-16 (16)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/19861801-9158-402b-816c-c20abff10ed9)



- Ahora abrimos la consola de Ubuntu 22.04 localmente, y accedemos mediante la clave .pem, a la instancia remota del Bastion Host.

![screenshot 2024-04-16 (17)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/39397958-6d58-423d-b80f-a8f389c7b35e)


![screenshot 2024-04-16 (19)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/1b0560de-ac44-4171-882f-085a227c861f)



- Ahora estando dentro de la instancia del Bastion Host, accedemos a la instancia de Database mediante la clave .pem
  
  * Debemos ejecutar el siguiente comando para darle permisos al usuario para escribir y leer el archivo .pem

  ```
  chmod 400 "reto-3.pem"                    
  ```

  * Editamos el archivo reto-3.pem con la clave que descargamos.

![screenshot 2024-04-16 (20)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/f722fb5d-2d4b-469b-8871-22561400ea10)


![screenshot 2024-04-16 (21)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/15f24aa7-543b-4211-a562-2017697c76ea)




- Instalamos Docker en la instancia de Database y editamos el archivo de docker compose para declarar las variables de entorno que nos permitirán conectarnos a la base de datos MySQL.

![screenshot 2024-04-16 (23)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/a4807018-4120-4310-8bf3-08c3530685de)


![screenshot 2024-04-16 (22)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/4d24deb0-963a-4a71-9d4a-74f4cc63eddc)



- Y finalmente ejecutamos docker compose up para correr la instancia de base de datos.

![screenshot 2024-04-16 (24)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/220a66bb-5738-45d0-b883-bc8558b3a3fb)


![screenshot 2024-04-16 (25)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/48ec14ab-fa93-4017-8272-8ac6d818d276)

### Configuración NFS.

- Ahora vamos a configurar la instancia de NFS (Network File System), para esto debemos escoger primero dentro del security group el puerto 2049 NFS y adicionalmente debemos configurar un *firewall* ya que como en el servidor NFS se estarán almacenando archivos, debemos incluir una capa extra de seguridad. Por esto lo que debemos habilitar también el puerto 22 correspondiente al SSH.

- Para la instalación del NFS server, se deben utilizar los siguientes comandos:

a. Para instalar el paquete ```nfs-kernel.server```, así:
```
sudo apt update
sudo apt install nfs-kernel-server -y
```

- Al finalizar esta instalación, tendremos creado el directorio mnt que es donde se guardarán los archivos de las instancias de Wordpress de la siguiente manera:
a. Se crea el directorio de Wordpress:
```
sudo mkdir -p /mnt/wordpress
```

b. Se modifica el propietario del directorio:

```
sudo chown nobody:nogroup /mnt/wordpress
```

c. Se modifican los permisos del dorectorio creado:

```
 sudo chmod 777 /mnt/wordpress
```

d. Se cambia la configuración de los directorios a exportar a través de NFS:

```
sudo nano /etc/exports

/mnt/wordpress *(rw,sync,no_subtree_check)
```

e. Se activa el firewall_

```
sudo ufw enable
```

f. Se agrega la regla para permitir conexión por NFS y SSH:

```
sudo ufw allow nfs

sudo ufw allow ssh
```

### Configuración Wordpress.

- Ahora procedemos con la configuración de la instancia de Wordpress, que luego más adelante estaremos duplicando para asignar ambas instancias al load balancer y que este pueda distribuir las peticiones entre las dos instancias de Wordpress.

- Configuramos el security group de la instancia con el puerto 80 correspondiente a HTTP.

- Instalamos docker:

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

```
# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Luego pasamos a configurar el NFS Client ya que esta instancia es la que actuará como cliente del NFS. Esto lo podemos hacer con los siguientes comandos:

a. Se instala ```nfs-common```:

```
sudo apt update
sudo apt install nfs-common -y
```

b. Se crea el directorio de montaje:

```
 sudo mkdir -p /mnt/wordpress
```

c. Se monta el directorio:

```
sudo mount <IP-PRIVADA-NFS>:/mnt/wordpress /mnt/wordpress
```

- Una vez realizado este paso, vamos a editar el docker-compose.yml y le ponemos la información necesaria para poder conectarnos con la base de datos (el archivo docker compose correspondiente se encuentra en la carpeta Wordpress de este repositorio).

- Ahora podemos ejecutar el comando docker compose up para dejar la instancia de Wordpress corriendo correctamente y que podamos editar la página desde la página de administración de contenido.

```
sudo docker compose up -d
```

![screenshot 2024-04-20 (6)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/b7de5512-d1b1-4f4f-a1f9-015746c13da0)


![screenshot 2024-04-20 (7)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/8cf628c1-65a8-4010-b2ee-2f1071f2dff4)


### Configuración Load Balancer.

- Finalmente, vamos a configurar la instancia de load balancer, que como ya habíamos mencionado, nos permitirá distribuir las cargas de las peticiones entre las dos instancias de Wordpress que tenemos. Para este proyecto utilizaremos como servidor a NGINX.

- Para esto primero escogemos en el security group agregramos los puertos 80 y 443 para el acceso a HTTP y HTTPS respectivamente.

- Ahora vamos a instalar docker como en las demás instancias que lo requieren.

- Instalamos docker:

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

```
# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

- Además se deben instalar los paquetes letsencrypt, certbot y nginx, de la siguiente manera:

```
sudo apt update
sudo add-apt-repository ppa:certbot/certbot
sudo apt install letsencrypt -y
sudo apt install nginx -y
```

- Se procede a ejecutar el comando de las librerías de certbot y letsencrypt. Esto genera el nombre (acme-challenge) y el valor es una cadena de caracteres encriptada, estos se ingresan en el registro TXT en el proveedor. Antes de continuar, hay que esperar a que se realice la propagación en los servidores del provedor, para validar la disponibilidad del registro se puede ingresar a el "Google Admin Tool Box".

```
sudo mkdir -p /var/www/letsencrypt
sudo certbot --server https://acme-v02.api.letsencrypt.org/directory -d *.toysnt.shop --manual --preferred-challenges dns-01 certonly
```
_Fíjese que se está utilizando la wild card (*) para obtener el certificado SSL._


![screenshot 2024-04-20 (3)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/1840646d-9455-4a2f-b666-71b825572764)


![screenshot 2024-04-20 (5)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/bed13529-482b-4008-97c0-6a2cf67bb46c)



- Cuando en el toolbox se ingrese el nombre del registro y lo encuentre (devolviendo el valor correspondiente) este ya se encontrará disponible y procedemos con la operación en consola (con la tecla enter).
- Ahora, se debe crear un directorio de nombre **loadbalancer** y dentro de este otro de nombre **ssl** en donde se almacenarán los archivos asociados al certificado y la llave para https, de esta manera:

```
mkdir loadbalancer
mkdir loadbalancer/ssl
```

- Después, se copian los certificados creados:

```
sudo su
cp /etc/letsencrypt/live/toysnt.shop/* /home/ubuntu/loadbalancer/ssl
```

- Así, al ingresar a la carpeta ssl podrá visualizar las claves y certificados recién copiados.

```
.
└── loadbalancer
    └── ssl
        ├── cert.pem
        └── chain.pem
        └── fullchain.pem
        └── privkey.pem

```

- Salimos del directorio ssl, y en la carpet raíz se procede a crear el archivo de configuración para SSL con el siguiente comando:

```
nano loadbalancer/ssl.conf
```
_El contenido que deberá ir dentro de este archivo se encuentra en este repositorio en la carpeta Load-balancer. Proceda a copiarlo y pegarlo entonces._

- Qudará entonces así el direcotorio loadbalancer, por el momento:

```
└───loadbalancer
    │   ssl.conf
    │
    └───ssl
```

En esta configuración se especifican valores para el acceso a los archivos generados con anterioridad.

![screenshot 2024-04-20 (8)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/a7b4cc8a-3c4e-4bfb-9849-50ac8fcf2055)


![screenshot 2024-04-20 (10)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/79ebb9c4-88e7-4c86-8122-798d3eecc4af)


- Ahora vamos a crear el archivo de configuración nginx.conf para el load balancer, así:

```
nano loadbalancer/nginx.conf
```
_El contenido que deberá ir dentro de este archivo se encuentra en este repositorio en la carpeta Load-balancer. Proceda a copiarlo y pegarlo entonces._


- Posteriormente, ya dentro de este archivo se especifica la IP de la instancia de Wordpress que tenemos hasta el momento. Se debe ubicar en el campo indicado a continuación como ```<wordpress_ip_1>```:

```
upstream backend {
      server <wordpress_ip_1>;
   }
```

- Ahora, la carpeta loadbalancer estará de la siguiente manera:
  
```
└───loadbalancer
    │   nginx.conf
    │   ssl.conf
    │
    └──ssl
```


![ss12 copia](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/2897b03e-bff1-467f-b3a6-97ff3624320a)



- Después, creamos el archivo docker-compose.yml en donde insertaremos la estructura base para nginx necesaria.

```
nano loadbalancer/docker-compose.yml
```
_El contenido que deberá ir dentro de este archivo se encuentra en este repositorio en la carpeta Load-balancer. Proceda a copiarlo y pegarlo entonces._

- Luego, se debe detener cualquier proceso o ejecución activa de NGINX en la instancia para evitar conflictos:

```
ps ax | grep nginx
netstat -an | grep 80

sudo systemctl disable nginx
sudo systemctl stop nginx
exit
```

- Luego ejecutamos el comando de docker compose up para correr la instancia del load balancer nginx y que funcione correctamente.

```
cd loadbalancer
docker compose up -d
```

### Configuración Wordpress 2.

- Ahora podemos clonar la instancia que tenemos de Wordpress y nombrarla 'wordpress-2', la cual tendrá la misma configuración que la instancia 'wordpress'.
- 
- Se actualiza entonces el archivo nginx.conf con la ip privada de la nueva instancia 'wordpress-2'.
  
![screenshot 2024-04-20 (12)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/57ebceef-d34c-4803-9836-07be325ddc16)


- Finalmente se recarga la página y así quedarían las instancias dentro de la VPC.

![screenshot 2024-04-20 (2)](https://github.com/EsteTruji/st0263-reto-3/assets/83479274/b3c498ed-9984-4280-91cd-ccbac816592f)

- Ahora, al acceder al enlace **https://reto3.toysnt.shop** podrá visualizar la página web desplegada y funcionando sin problema (tenga presente que para el momento de la revisión puede que las instancias que alojan todos los recursos y procesos previamente enunciados no estén en ejecución, y por lo tanto no esté disponible la página web).


## Referencias

[How To Install and Use Docker on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)


[How To Set Up an NFS Mount on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-20-04)


