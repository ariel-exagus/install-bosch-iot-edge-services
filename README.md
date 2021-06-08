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
1. Es importante [verificar las conexiones entre los distintos servicios](https://docs.bosch-iot-suite.com/device-management/Check-your-connections.html) que nos ofrece la plataforma de Bosch IoT Suite, para ello, en la pantalla de Sucripción, hacemos click en _Go to Developer Console_ y en el menú derecho, seleccionamos _Connections_. Allí debemos asegurarnos que las conexiones están activas (El icono enchufado). Las dos conexiones que deben estar presentes son las de _Device Management_ y _Devices via Bosch IoT Hub_.

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
 ./install.sh.
```
7. Una vez que terminada la instalación, podemos correr el agente ejecutando el script 

```
./start.sh
```

8. Si todo se realizó correctamente, debemos ver en la consola del _Bosch IoT Suite_ y en el _Dashboard de Device Management_ el simbolo verde que indica conexión con el Gateway. Se va a crear además un nuevo digital twins para el manejador de los containers. Si no aparece el simbolo de conectado, puede ser que no se haya habilitado en la sección _Conections_ del servicio _Devices via Bosch IoT Hub_ el checkbox que habilita a que se refleje el estado de la conexión (_Reflect device connectivity status in thing_).


