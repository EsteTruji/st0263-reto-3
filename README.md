
# Info de la materia: ST0263 - Tópicos Especiales en Telemática

### Estudiantes:
  - Esteban Trujillo Carmona, etrujilloc@eafit.edu.co
  - Viviana Hoyos Sierra, vhoyoss@eafit.edu.co
  - Damian Duque López, daduquel@eafit.edu.co
  - Valentina Morales Villada, vmoralesv@eafit.edu.co

### Profesor: Edwin Montoya, emontoya@eafit.edu.co

# Reto 3 

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
-	Almacenamiento en la capa de datos con una instancia de bases de datos mysql
- Almacenamiento de archivos con una instancia de archivos distribuidos implementado con servicio NFS.
- Conexion de servicios de wordpress con servicio NFS.
- Utilización de https



### 1.2. Que aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)

Se cumplieron con todos los requisitos propuestos en el reto por el profesor.

## 2. Información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas.

Se implementó para este proyecto la siguiente arquitectura basada en la propuesta del profesor.

[Diagrama](https://drive.google.com/file/d/11rlnW3-tqprr7dtQRbYrXvi0q4SP7CP8/view?usp=sharing)


## 3. Descripción del ambiente de ejecución lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

- IP o nombres de dominio en nube o en la máquina servidor.

Para la instancia de Load Balancer:
  - .



## 4. Guía paso a paso de instalación y configuración

Para este proyecto, se realizó el despliegue de varias instancias según lo solicitado en la descripción. Esta es una pequeña guía del proceso de instalación y configuración de estas instancias.

- Se crea la VPC donde van a estar almacenadas las instancias que crearemos. Esta está compuesta de dos subredes, una pública y otra privada.


ss1 20
ss 1 
ss2

- Se crean 3 instancias dentro de esta subred privada, cada una para los siguientes servicios Database, Wordpress, NFS. Todas estas con ips privadas.

 ss7 ss8 ss9
- Dentro de la subred pública de la vpc se crean 2 instancias.
    - La instancia del Load Balancer y se le asocia una ip pública elástica.

    ss10 

    - Se crea la instancia de Bastion Host y se le asocia una ip pública.

- Así ya tenemos las cinco instancias dentro de la VPC que creamos inicialmente.

s11 s12

- Ahora procedemos a agregar el dominio (con el valor de la IP elástica del LB)  el subdominio en el DNS de igual forma y finalmente el registro CNAME. Dentro de la configuración de registros del proveedor, se vería de la siguiente manera: 

s13


```
A        @        IP elástica del balanceador
A        reto3    IP elástica del balanceador
CNAME    www      reto3.toysnt.shop
```

- Modificamos las reglas del security group de la instancia de Database para asociarla con una base de datos MySQL.


s14 s15 s16

- Ahora abrimos la consola de Ubuntu 22.04 localmente, y accedemos mediante la clave .pem, a la instancia remota del Bastion Host.

s17 s19

- Ahora estando dentro de la instancia del Bastion Host, accedemos a la instancia de Database mediante la clave .pem
  
  * Debemos ejecutar el siguiente comando para darle permisos al usuario para escribir y leer el archivo .pem

  ```
  chmod 400 "reto-3.pem"                    
  ```

  * Editamos el archivo reto-3.pem con la clave que descargamos.

s20 s21

- Instalamos Docker en la instancia de Database y editamos el archivo de docker compose para declarar las variables de entorno que nos permitirán conectarnos a la base de datos MySQL.

s23 s22

- Y finalmente ejecutamos docker compose up para correr la instancia de base de datos.

s24 s25

- Ahora vamos a configurar la instancia de NFS (Network File System), para esto debemos escoger primero dentro del security group el puerto 2049 NFS y adicionalmente debemos configurar un *firewall* ya que como en el servidor NFS se estarán almacenando archivos, debemos incluir una capa extra de seguridad. Por esto lo que debemos habilitar también el puerto 22 correspondiente al SSH.

- Para la instalación del NFS server, se deben utilizar los siguientes comandos:

```
....
```

- Al finalizar esta instalación, tendremos creado el directorio mnt que es donde se guardarán los archivos de las instancias de Wordpress.

- Ahora procedemos con la configuración de la instancia de Wordpress, que luego más adelante estaremos duplicando para asignar ambas instancias al load balancer y que este pueda distribuir las peticiones entre las dos instancias de Wordpress.

- Configuramos el security group de la instancia con el puerto 80 correspondiente a HTTP. 
- Instalamos docker, y luego pasamos a instalar el NFS Client ya que esta instancia es la que actuará como cliente del NFS. Esto lo podemos hacer con los siguientes comandos:


```
....
```

- Una vez realizado este paso, vamos a editar el docker-compose.yml y le ponemos la información necesaria para poder conectarnos con la base de datos. 

- Ahora podemos ejecutar el comando docker compose up para dejar la instancia de Wordpress corriendo correctamente y que podamos editar la página desde la página de administración de contenido.

ss6 ss7 20

- Finalmente, vamos a configurar la instancia de load balancer, que como ya habíamos mencionado, nos permitirá distribuir las cargas de las peticiones entre las dos instancias de Wordpress que tenemos. Para este proyecto utilizaremos el balanceador NGINX.

- Para esto primero escogemos en el security group agregramos los puertos 80 y 443 para el acceso a HTTP y HTTPS respectivamente.

- Ahora vamos a instalar docker como en las demás instancias que lo requieren. Además se deben instalar las librerías de letsencrypt, certbot y nginx.
- Se procede a ejecutar el comando de las librerías de certbot y letsencrypt. Esto genera el nombre (acme-challenge) y el valor es una cadena de caracteres encriptada, estos se ingresan en el registro TXT en el proveedor. Antes de continuar, hay que esperar a que se realice la propagación en los servidores del provedor, para validar la disponibilidad del registro se puede ingresar a el "Google Admin Tool Box".

ss3 ss5 20

- Cuando en el toolbox se ingrese el nombre del registro y lo encuentre (devolviendo el valor correspondiente) este ya se encontrará disponible y procedemos con la operación en consola (con la tecla enter) y este genera un directorio ssl en donde se almacenarán los archivos asociados al certificado y la llave para https, de esta manera:
```
.
└── loadbalancer
    └── ssl
        ├── cert.pem
        └── chain.pem
        └── fullchain.pem
        └── privkey.pem

```
Salimos del directorio y nos dirigimos a la raíz, en el cual se crea el archivo de configuración para SSL
```
└───loadbalancer
    │   ssl.conf
    │
    └───ssl
```
En esta configuración se especifican valores para el acceso a los archivos generados con anterioridad.

ss8 ss10 20


- Ahora vamos a crear y posteriormente ingresar al archivo de configuración nginx.conf para especificar la ip de la única instancia de Wordpress que tenemos hasta el momento. 
```
└───loadbalancer
    │   nginx.conf
    │   ssl.conf
    │
    └──ssl
```


ss12 20 copia

- Creamos el archivo docker-compose.yml en donde insertaremos la estructura base para nginx necesaria.

- Luego ejecutamos el comando de docker compose up para correr la instancia del load balancer nginx y que funcione correctamente. 
- Ahora podemos clonar la instancia que tenemos de Wordpress y nombrarla 'wordpress-2' que tendrá la misma configuración que la instancia 'wordpress'.
- Se actualiza el archivo nginx.conf con la ip privada de la nueva instancia 'wordpress-2'

ss12 20

- Finalmente se recarga la página.




## Referencias
[How To Install and Use Docker on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)


[How To Set Up an NFS Mount on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-20-04)

