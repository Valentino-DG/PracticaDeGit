# Trabajo Práctico 6-SO2

## Alumno: Marco Valentino Di Giannantonio.
## LinkRepo: https://github.com/ICOMP-UNC/2023---soii---laboratorio-6-Valentino-DG

## Introducción

El objetivo de este trabajo práctico es desarrollar una implementación básica de una RESTful API en un sistema embebido. Este sistema incluye dos servicios principales escritos en Go: un servicio de usuarios y un servicio de procesamiento. Estos servicios están protegidos y expuestos a través de un servidor Nginx que direcciona las solicitudes a los servicios correspondientes, basándose en la URL de destino.

## Arquitectura del Sistema

La arquitectura del sistema se compone de los siguientes componentes:

* **Servidor Nginx**: Actúa como proxy inverso para direccionar las solicitudes a los servicios correspondientes.
* **Base de Datos MySQL**: Almacena los datos de usuarios y sensores.
* **Servicio de Usuarios y Servicio de Procesameinto**.
   

Ambos servicios (servicio de usuarios y servicio de procesamiento) exponen una REST API con Media Type application/json. Las solicitudes a estos servicios se enrutan a través de Nginx de la siguiente manera:

**dashboard.com** para el servicio de usuarios.<br>
**sensors.com** para el servicio de procesamiento.

## Detalle de los Servicios

### Servicio de Usuarios

El servicio de usuarios maneja las siguientes funcionalidades:

* **Crear Usuarios**: Permite la creación de nuevos usuarios. Los usuarios creados pueden acceder al sistema mediante SSH.
* **Listar Usuarios**: Devuelve una lista de todos los usuarios registrados.
* **Autenticar Usuarios**: Autentica a los usuarios y les proporciona un token JWT para acceder a las funciones protegidas.
* **Contar Usuarios**: Devuelve la cantidad total de usuarios registrados.

Endpoints y ejemplos de solicitudes de los mismos:

<u>Crear usuario</u>:

    
    POST http://dashboard.com/api/users/createuser 
    
&#8203;

    curl --request POST \
         --url http://dashboard.com/api/users/createuser \
         -u USER:SECRET \
         --header 'accept: application/json' \
         --header 'content-type: application/json' \
         --data '{"username": <"usr1">, "password": <"pw1">}'

<br>
<u>Listado de todos los Usuarios registrados</u>: <br><br>
    
    GET http://dashboard.com/api/users/listall
    
&#8203;

    curl --request GET \
         --url http://dashboard.com/api/users/listall \
         -u USER:SECRET \
         --header 'accept: application/json' \
         --header 'content-type: application/json' \
         --header 'Authorization: <token>'
         
<br>
<u>Logueo (autenticacion)</u>:<br><br>

    POST http://dashboard.com/api/users/login
    
&#8203;
    
    curl --request POST \
         --url http://dashboard.com/api/users/login \
         -u USER:SECRET \
         --header 'accept: application/json' \
         --header 'content-type: application/json' \
         --data '{"username": <"usr1">, "password": <"pw1">}'

<br>
<u>Obtención de la Cantidad de Usuarios registrados</u>:<br><br>
    
    GET http://dashboard.com/api/users/count
&#8203;   

    curl --request GET \
         --url http://dashboard.com/api/users/count \
         -u USER:SECRET \
         --header 'accept: application/json' \
         --header 'content-type: application/json' \
         --header 'Authorization: <token>'

<br>
**<u>Seguridad y Autenticación</u>**

Para acceder a las funcionalidades del servicio de usuarios como lo son obtener la lista y cantidad de usuarios registrados, es necesario estar autenticado. La autenticación se realiza mediante tokens JWT, que se generan al momento del login exitoso.

**<u>Acceso SSH</u>**

El servicio de usuarios también crea usuarios en el sistema, permitiendo que estos puedan logearse via SSH y acceder al servidor. Esta creación de usuarios en el sistema se realiza automáticamente cada vez que se registra un nuevo usuario.

Una vez creado el usuario, para acceder al servidor via SSH es necesario ejecutar:

    ssh nombreDeUsuario@ipServidor
nombreDeUsuario -> Nombre de usuario registrado por el servicio de usuarios.<br>

ipServidor -> "localhost" (ya que estamos en modo de prueba y estamos accediendo al servidor con los usuarios, en la misma maquina donde levantamos el servidor).<br>

Luego se solicitará la contraseña con la cual registramos el usuario.<br><br>

### Servicio de Procesamiento

El servicio de procesamiento se encarga de recibir y procesar datos de sensores IoT. Proporciona dos funcionalidades principales:

* **Enviar Datos del Sensor**: EL servidor recibe datos enviados por sensores y los almacena en la base de datos. Estos datos son la memoria ram libre del sensor, memoria swap libre, y carga del sistema.
* **Resumen de los Sensores:** Calcula y devuelve un resumen de los datos de todos los sensores almacenados.


Endpoints y ejemplos de solicitudes de los mismos:

<u>Envio de datos del sensor:</u><br>

    POST http://sensors.com/api/processing/submit
    
&#8203;

    curl --request POST \
         --url http://sensors.com/api/processing/submit \
         -u USER:SECRET \
         --header 'accept: application/json' \
         --header 'content-type: application/json' \
         --data '{"sensor_id": <1>, "memory_free_GB": <0.5>, "swap_free_GB": <0.1>, "system_load": <0.6>}'
<br>
<u>Obtención del resumen de los datos de los sensores:</u><br><br>

    GET http://sensors.com/api/processing/summary
    
&#8203;

    curl --request GET \
         --url http://sensors.com/api/processing/summary \
         -u USER:SECRET \
         --header 'accept: application/json' \
         --header 'content-type: application/json'

### Configuración de Nginx

Se ha configurado Nginx para enrutar las solicitudes a los servicios adecuados. A continuación se muestra una explicación de como hacerlo:

Primero creamos un mensaje de bienvenida para cada servicio:

    echo "Bienvenido al servicio de usuarios" | sudo tee /var/www/html/dashboard.html
    echo "Bienvenido al servicio de procesamiento" | sudo tee /var/www/html/sensors.html

Lo anterior es solo para mejorar la visual. Para cuando se acceda a **http://dashboard.com** o **http://sensors.com** (o con triple W) aparezca el mensaje de bienvenida correspondiente. 

En segundo lugar creamos los archivos de configuracion de Nginx para ambos servicios:

Para el servicio de usuarios ejecutamos:

    sudo nano /etc/nginx/sites-available/dashboard.conf
    
y pegamos el siguiente texto:

    server {
      listen 80;
      server_name dashboard.com www.dashboard.com;

     location / {
        root /var/www/html;
        index dashboard.html;
    }

    location /api/ {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location ~ /.well-known {
        allow all;
    }
    }

Para el servicio de procesamiento ejecutamos:

    sudo nano /etc/nginx/sites-available/sensors.conf
    
y pegamos el siguiente texto:
    
    server {
      listen 80;
      server_name sensors.com www.sensors.com;

     location / {
        root /var/www/html;
        index sensors.html;
    }

    location /api/ {
        proxy_pass http://localhost:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location ~ /.well-known {
        allow all;
    }
    }

Para el localhost ejecutamos:

    sudo nano /etc/nginx/sites-available/localhost.conf
    
y pegamos el siguiente texto:

    server {
      listen 80;
      server_name localhost;

     location / {
        return 200 'Esta es la página por defecto para localhost';
        add_header Content-Type text/plain;
     }
    }
    
    
Luego creamos un enlace simbólico desde sites-available a sites-enabled para activar la configuración:

    sudo ln -s /etc/nginx/sites-available/dashboard.conf /etc/nginx/sites-enabled/
    sudo ln -s /etc/nginx/sites-available/sensors.conf /etc/nginx/sites-enabled/
    sudo ln -s /etc/nginx/sites-available/localhost.conf /etc/nginx/sites-enabled/

Eliminamos la configuracion default de Nginx:

    sudo rm /etc/nginx/sites-enabled/default
    
Verificamos que la configuración de Nginx no tenga errores de sintaxis:

    sudo nginx -t

Si la verificación es exitosa, reinicia Nginx para aplicar los cambios:

    sudo systemctl restart nginx

Por último Editamos el archivo Hosts:

    sudo nano /etc/hosts

Agregamos:
    
    127.0.0.1 www.dashboard.com dashboard.com
    127.0.0.1 www.sensors.com sensors.com
    
<br>

### Antes de pasar a la configuracion de SystemD o antes de ejecutar el código:
Es necesario editar el archivo sudoers ya que el código del servicio de usuario utiliza dos comandos que necesitan ejecutarse con SUDO, entonces desabilitamos que "SUDO" pida contraseña para esos dos comandos.

Ejecutamos:

    sudo visudo

Agregamos la siguiente linea en el archivo:

    go_user ALL=(ALL) NOPASSWD: /usr/sbin/useradd, /usr/sbin/chpasswd

go_user -> usuario que ejecuta el servicio de Go (el que ejecuta el código del servicio de usuario).<br><br>

### Configuración de SystemD

Se han configurado servicios SystemD para gestionar la ejecución de ambos servicios de manera eficiente. Los archivos de configuración de SystemD permiten iniciar, detener y reiniciar los servicios de manera controlada.

Necesitamos crear un archivo de configuracion de SystemD para cada servicio.

Para el servicio de usuarios ejecutamos:

    sudo nano /etc/systemd/system/dashboard.service
    
y pegamos el siguiente texto:

    [Unit]
    Description=dashboard.com

    [Service]
    ExecStart=/path/hacia/el/ejecutable
    WorkingDirectory=/path/hacia/el/directorio/de/trabajo
    User=usuario
    Group=sudo
    Restart=always
    RestartSec=3

    [Install]
    WantedBy=default.target


Para el servicio de procesamiento ejecutamos:

    sudo nano etc/systemd/system/sensors.service

y pegamos el siguiente texto:

    [Unit]
    Description=sensors.com

    [Service]
    ExecStart=/path/hacia/el/ejecutable
    WorkingDirectory=/path/hacia/el/directorio/de/trabajo
    User=usuario
    Group=sudo
    Restart=always
    RestartSec=3

    [Install]
    WantedBy=default.target


Luego ejecutamos lo siguiente con el fin de hacer un reload de los archivos de configuración de SystemD y empezar los servicios:

    sudo systemctl daemon-reload
    sudo systemctl start dashboard
    sudo systemctl enable dashboard
    sudo systemctl start sensors
    sudo systemctl enable sensors

Por último verificamos que los servicios esten activos :
    
    sudo systemctl status dashboard
    sudo systemctl status sensors



* Algunos comandos utilies:

Cómo detener un servicio:

    sudo systemctl stop nombre_del_servicio

Cómo reiniciar un servicio:

    sudo systemctl restart nombre_del_servicio
    sudo systemctl restart sensors.service

Cómo habilitar un servicio para que se inicie automáticamente al arrancar el sistema:

    sudo systemctl enable nombre_del_servicio

Cómo deshabilitar un servicio para que no se inicie automáticamente al arrancar el sistema:

    sudo systemctl disable nombre_del_servicio

Cómo ver el estado de un servicio:

    sudo systemctl status nombre_del_servicio
<br>

## Conclusión
Este proyecto ha proporcionado una visión completa de una implementación básica de una RESTful API para un sistema embebido con servicios en Go, utilizando JWT para la autenticación y Nginx para el enrutamiento. Además, se ha añadido la funcionalidad de crear usuarios que pueden acceder al sistema mediante SSH y gestión de servicios SystemD, cumpliendo así con los requisitos planteados en el trabajo práctico
