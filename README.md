# Pasos para la instalación de Bosch IoT Edge Services en una Raspberry Pi

_Detallaremos los pasos necesarios para instalar los servicios de Bosch IoT Edge en una plataforma linux, en este caso en una Raspberry Pi con Raspbian_

## Requerimientos
Estos son los requerimientos necesarios para poder instalar el software Bosch IoT Edge Services en una plataforma Linux.

* Hardware: Es necesario contar con una plataforma de Hardware que pueda correr un sistema Linux, en nuestro caso utilizaremos una _Raspberry Pi 4B_. Además ya debe contar con un sistema operativo, esta guía fue realizada con la Raspberry corriendo _Raspbian_.
* Software: Debemos tener instalado Docker en la plataforma, en nuestro caso la Raspberry pi, para poder crear imagenes y contenedores. También es necesario tener una cuenta en [Docker Hub](https://hub.docker.com/) para poder subir las imagenes.

> **Nota:** Este es un enlace con los pasos para [instalar Docker en una Raspberry pi](https://phoenixnap.com/kb/docker-on-raspberry-pi)


## Comenzando
_Empezaremos creando una suscrpción a Bosch IoT Suite para poder descargarnos el software del Bosch IoT Edge Services_

### Crear una Suscripción a Bosch IoT Suite

1. Nos dirigimos a la página de inicio de [Bosch IoT Suite](https://accounts.bosch-iot-suite.com) y creamos una cuenta. Luego realizamos una Suscripción a los servicios de _Bosch IoT Suite for Device Management_. Aquí hay una pequeña guía de como [realizar una nueva Suscripción](https://docs.bosch-iot-suite.com/device-management/Subscribe-a-service-instance.html). Debemos esperar unos segundos a que se active nuestra suscripción.

2. Es importante [verificar las conexiones entre los distintos servicios](https://docs.bosch-iot-suite.com/device-management/Check-your-connections.html) que nos ofrece la plataforma de Bosch IoT Suite, para ello, en la pantalla de Sucripción, hacemos click en _Go to Developer Console_ y en el menú derecho, seleccionamos _Connections_. Allí debemos asegurarnos que las conexiones están activas (El icono enchufado). Las dos conexiones que deben estar presentes son las de _Device Management_ y _Devices via Bosch IoT Hub_.

### Agregar un Namespace
3. Volvemos a la consola de desarrollo y [creamos un Namespace](https://docs.bosch-iot-suite.com/device-management/Register-a-namespace.html). Este nombre nos permite organizar los Objetos creados en distintos proyectos.

### Registrar un dispositivo Gateway
4. Necesitamos crear la representación digital de nuestro Gateway en la plataforma. Debemos volver a la consola de desarrollo y seleccionar la sección _Provisioning_ y realizar los siguientes pasos:

    - En la sección de _presets_ seleccionar _Start from scratch (default)_.
    - Seleccionar _Gateway Device_.
    - Activar el _Enable Automatic provisioning of edge devices_ en Gateway Authorities.
    - Seleccionar el Namespace previamente configurado.
    - Ingresar el nombre del Gateway
    - Seleccionar autenticación por Username y Password
    - Ingresar el password
    - En la sección de _Preview_ oprimir _Send Request_
    - Descargar el archivo `provisioning.json` haciendo click en _Save Response_

![Crear Gateway](/images/dm_getStarted_provisionDevConsole1.png)

### Descargar el Agente
5. Descargar el Agente, el cual nos va a permitir conectarnos a la plataforma de Bosch. Para ello debemos volver a la página de suscripción e ir a la sección _Download Edge Agent_. Se van a generar los links de descarga, los cuales tienen una licencia por tres meses. Descargar el archivo según la plataforma. Para Raspberry con Raspbian descargar el Edge Agent para Linux y arquitectura ARM.

6. Acceder a la Raspberry pi y crear un directorio donde copiar el archivo descargado. Utilizar el comando `tar -xvf com.bosch.iot.edge.agent-linux-arm.tar.gz` para descomprimir los archivos. En la misma carpeta colocar el archivo de `provisioning.json` que se obtuvo en el paso anterior. Una vez hecho esto, podemos ejecutar el script de instalación:

```
$ mkdir bosch_iot_edge_agent
$ cd bosch_iot_edge_agent
```
Descar el archivo `com.bosch.iot.edge.agent-linux-arm.tar.gz` y copiar en el mismo directorio el archivo `provisioning.json`
```
$ tar -xvf com.bosch.iot.edge.agent-linux-arm.tar.gz
$ ls -al
total 24104
drwxr-xr-x  4 pi pi     4096 Jun  5 10:35 .
drwxr-xr-x 35 pi pi     4096 Jun  6 23:44 ..
drwxr-xr-x  4 pi pi     4096 May 24 22:52 cloud.connector.generic
-rw-r--r--  1 pi pi 24167039 May 24 21:36 com.bosch.iot.edge.agent-linux-arm.tar.gz
drwxr-xr-x  3 pi pi     4096 Mar 10 17:30 containers
-rw-r--r--  1 pi pi      101 May 24 22:45 edge.config
-rwxr-xr-x  1 pi pi    18410 Mar 19 07:34 install.sh
-rw-r--r--  1 pi pi    10282 Mar 19 07:34 LICENSE
-rw-r--r--  1 pi pi     2255 May 24 22:04 provisioning.json
-rwxr-xr-x  1 pi pi     3535 Mar 19 07:34 start.sh
-rwxr-xr-x  1 pi pi     2215 Mar 19 07:34 stop.sh
-rw-r--r--  1 pi pi   438261 Mar 19 07:34 THIRDPARTYLICENSEREADME.txt
-rwxr-xr-x  1 pi pi     2706 Mar 19 07:34 uninstall.sh
-rw-r--r--  1 pi pi      502 Mar 19 07:34 version
```
Instalamos el agente corriendo el script `install.sh`
```
$ ./install.sh.
```
7. Una vez que terminada la instalación, podemos correr el agente ejecutando el script 

```
$ ./start.sh
```

8. Si todo se realizó correctamente, debemos ver en la consola del _Bosch IoT Suite_ y en el _Dashboard de Device Management_ el simbolo verde que indica conexión con el Gateway. Se va a crear además un nuevo digital twins para el manejador de los containers. Si no aparece el simbolo de conectado, puede ser que no se haya habilitado en la sección _Conections_ del servicio _Devices via Bosch IoT Hub_ el checkbox que habilita a que se refleje el estado de la conexión (_Reflect device connectivity status in thing_).

>**Nota:** Podemos detener el agente corriendo el script `./stop.sh`

### Descargar IoT Edge Services
Lo siguiente que debemos hacer es descargar el Edge Services, el cual posee contenedores listos para ejecutarse y poder realizar pruebas. Antes de descargar el Edge Services, debemos tener instalado Docker en nuestro Raspberry pi (Observa En la sección de _Requerimientos_ para ver como instalar Docker).

9. Para descargar el Edge Services, nos dirigimos a la página de [Suspcripción](https://accounts.bosch-iot-suite.com/subscriptions/) y seleccionamos _Edge Download_. Allí buscamos descargar el Edge Services para la plataforma Raspbian, que es el sistema operativo que utilizamos en la Raspberry pi. El archivo debe tener el siguiente nombre, o uno muy similar: `	target-image-iot-edge-services-linux-arm-raspbian.tar`

10. Creamos un directorio donde poder descargar y descomprimir la imagen, por ejemplo:

```
$ mkdir /home/pi/bosch_iot_edge_services
$ cd /home/pi/bosch_iot_edge_services
```
Descomprimimos el archivo utilizando el comando `tar`
```
$  tar -xvf target-image-iot-edge-services-linux-arm-raspbian.tar
```
Al terminar la descompresión, debemos tener un directorio como el siguiente:
```
$ ls -al
```

### Compilar la Imagen del Edge Services
Una vez que tenemos el Edge Services en nuestra plataforma, debemos compilar la imagen y registrarla, para poder hacer uso de las herramientas que nos provee el gestor de contenedores del Edge Agent. Utilizaremos Docker para compilar en una imagen el Edge Services.

11. En la carpeta donde descomprimimos los archivos del Edge Service, nos vamos a encontrar con un archivo llamado DockerFile, el cual tiene las instrucciones para generar la imagen. Como la arquitectura de Hardware del Raspberry pi es un ARM, vamos a compilar la imagen usando el argumento `FROM_IMAGE=arm32v7/openjdk:8-slim`. La instrucción para generar la imagen quedaría de la siguiente manera (No omitor el detalle del punto final):

```
$ docker build -t <new_name> --build-arg FROM_IMAGE=arm32v7/openjdk:8-slim .
```
12. Podemos visualizar la imagen creada con la instrucción `docker images`.

```
$ docker images
REPOSITORY                  TAG       IMAGE ID       CREATED         SIZE
edge-service-image          latest    67864cd9c565   2 minutes ago   388MB
arm32v7/openjdk             8-slim    2c883270745f   2 years ago     189MB
```

### Registrar la imagen
13. Para poder hacer uso del manager de contenedores que nos provee el Edge Agent, debemos registrar la imagen del conenedor, en este caso la registraremos a docker hub, que es la manera más simple. Para ello previamente debemos contar con una cuenta en  [Docker Hub](https://hub.docker.com/). Realizamos un login para ingresar nuestras credenciales de Docker Hub.

```
$ docker login
```
14. Hacemos un tag de la imagen de la siguiente manera: `docker tag <image_id> <docker_id>/image_name:version`. Por ejemplo:
```
$  docker tag edge-service-image:latest exagus/edge-service-image:v2
```

15. Subimos la imagen al Docker Hub haciendo un `push`:
```
$ docker push exagus/edge-service-image:v1
```
Para direccionar a la imagen registrada en Docker Hub debemos utilizar el siguiente nombre: `docker.io/exagus/edge-service-image:v2`

### Crear el Contenedor
Para crear el contenedor, haremos uso del manejador de contenedores que nos provee el Bosch Iot Edge Agent que descargamos anteriormente. Esta herramienta es el `edgectr`

16. Utilizamos el comando edgectr para crear las imagen, vamos a agregar algunas opciones para poder conectarnos a la consola Web; `--hosts="ctrhost:host_ip" --ports=80:80`. Además si queremos utilizar el modulo Bluetooth de la Raspberry pi para comunicarnos con los dispositivos, debemos tener instalado `BlueZ` y darle acceso al contendor al bus del sistema, que lo podemos hacer con el siguiente argumento: `--privileged --mp=/var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket`. El comando para crear la imagen nos quedara de la siguiente manera:

```
$ sudo edgectr create <my-image-registry-host>/<my-edge-services-image-name>:<my-edge-services-image-tag> --hosts="ctrhost:host_ip" --i --t --ports=80:80 --privileged --mp=/var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket
```
En donde además con las opciones `--i --t` obtenemos acceso a la terminal del contenedor. Para nuestro ejemplo, creamos la imagen con el siguiente comando:

```
$ sudo edgectr create docker.io/exagus/edge-service-image:v2 --hosts="ctrhost:host_ip" --i --t --ports=80:80 --privileged --mp=/var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket
```

17. Una vez que creamos el contenedor, podemos ver el listado de contenedores con el siguiente comando:

```
$ sudo edgectr list
ID |NAME |IMAGE |STATUS |FINISHED AT |EXIT CODE |

------------------------------------- |-------------- |------------------------------------- |--------- |-------------------------------- |---------- |

59f3a330-9508-4458-9168-3398ea65d2f1 |my-container |docker.io/exagus/edge-service-image:v2 |Created |2020-06-09T11:54:23.657504761Z |0 |
```

### Correr el contenedor
18. Una ves que tenemos listo el contenedor, podemos correrlo con el siguiente comando:

```
sudo edgectr start <container-id> --i --a
```
Donde el container ID es el resultado de la primer columna que nos lista el comando `edgectr list` y donde las opciones `--i --a` nos permite interactuar con la terminal, si previamente lo especificamos al crear la imagen. Un ejemplo del comando sería:

```
$ sudo edgectr start 59f3a330-9508-4458-9168-3398ea65d2f1 --i --a
```
Con esto ya tenemos el Edge Services corriendo en nuestra Raspberry pi. Podemos acceder a la consola Web para configurar y ver las aplicaciones corriendo, para ello abrimos un navegador y accedemos a la siguiente dirección `http://<IP-Local-Raspberry>/system/console`

19. Por ultimo, podemos parar la ejecución del contenedor con el comando `edgectr stop`
```
$ sudo edgectr stop 59f3a330-9508-4458-9168-3398ea65d2f1
```


