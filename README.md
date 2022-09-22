# Tutorial Instalar The Littlest-JupyterHub en un VPS

En este tuorial vamos a ver como instalar  The Littlest JupyterHub en un VPS las herramientas que utilizaremos seran:

* Distro de Ubuntu 20.04 LTS instalada en Windows con WSL. [Ver](https://learn.microsoft.com/es-es/windows/wsl/install)
* Un VPS contratado de Hostinger con las siguientes caracteristicas:

  * 1GB RAM
  * 20GB HDD
  * 2 nucleos

  **Estas caracteristicas son las minimas que provee hostinger para contratar un servicio de VPS.**
  **Una vez que contratamos el servicio de VPS de Hostinger este nos provee una IP publica con la cual vamos a poder conectarnos desde nuestro SO local por medio de una conexion ssh, si quieres contratar una puedes hacerlo desde [aqui](https://www.hostinger.com.ar/vps-argentina)**
* Algo de conocimiento con la consola de Linux. Si necesitas ayuda puedes verla desde [aqui](https://www.guru99.com/linux-commands-cheat-sheet.html)

## Pasos a Seguir para la instalacion

Podemos dividir la Instalacion en Diferentes Pasos:

1. Acceder al Sevidor de Forma Segura.
2. Instalar las Herramientas.
3. Apuntar el proyecto a nuestro Dominio.
4. Customizar nuestra version de TLJH.

### 1_Acceder al Servidor de Forma Segura

1. Vamos a abrir una terminal de Ubuntu corriendo en Windows con WSL y desde ahi ver la gestion de memoria de nuestra SO local corriendo el comando:

   `$ htop -s PERCENT_MEM`
2. Luego abriremos otra terminal desde donde vamos a conectarnos a nuestro VPS con el comando:

  `$ ssh <username>@<ip-address or hostname>`

* Donde `<username>` es el usuario con el que vamos a acceder a nuestro VPS. Esta por ser nuestra primera vez el `<username>` va a ser `root`
* Donde `<ip-address or hostname>` sera la ip publica que nos asigno Hostinger para poder acceder a nuestra VPS.
* La primera vez que nos conectemos nos pedira contraseña, es la que escojiste a la hora de configurara tu VPS como se ve en la imagen.
  ![conectar vps](source\conectar_vps.png)
* Como podras ver la linea de comandos cambia a color blanco para diferenciarla de la de wsl.

3. Una vez dentro de tu VPS puedes actualizarlo con los siguientes comandos:

* `$ sudo apt-get update`
* `$ sudo apt-get upgrade`
* `$ sudo reboot`
* Luego de esto necesitaras volver a acceder a tu VPS siguiendo el paso 2.

4. Ahora vamos a crear y copiar las claves de accesos ssh para acceder de esa forma a nuestro VPS.

* Abriremos una nueva terminal y desde esta vamos a correr el comando para poder generar la clave ssh y guardarla en nuestro ordenador:

  `$ ssh-keygen -t rsa`

![generar clave ssh](source\generar_clave_ssh.png)

* Ahora deberemos ponerla en un formato para que podamos añadirla a nuestro VPS con el comando:

  `$ cat ~/.ssh/id_rsa.pub`

![ver clave ssh](source\ver_clave_ssh.png)

* Ahora solo copiaremos lo que nos entrego ese comando y lo pegaremos en el panel de gestion de claves ssh VPS de Hostinger.
* Primero haciendo click en el boton:

![agregar clave ssh](source\agregar_clave_ssh_1.png)

* Luego ponemos un nombre y pegamos el contenido que copiamos previamente.

![agregar clave ssh](source\agregar_clave_ssh_2.png)

* Una vez agregada tu clave ssh ya no te va a pedir ingresar con contraseña, simplemente ingresara al correr el comando: `$ ssh <root>@<ip-address>`

5. Vamos a crear un usuario con permisos de administrador para luego restingir el acceso como root a nuestro VPS.

* Desde el usuario root de nuestro VPS vamos a correr el comando:

  `$ adduser <nombre_nuevo>`

![add usser](source\add_usser.png)

* Luego de correr el comando nos pedira colocar 2 veces la contraseña para este nuevo usuario que creamos.
* Nos pedira tambien un poco de informacion adicional, que pueden saltar simplemente dando en Enter.
* Vamos a salir de la consola del usuario root con el comando: `$ logout` y desde nuestro wsl continuaremos.
* Vamos a copiar las claves de acceso ssh a este nuevo usuario corriendo el comando:
  `$ ssh-copy-id <tu_nombre_usuario_nuevo>@<tu_ip_server>`

![add clave ssh new usser](source\agregar_clave_ssh_new_usser.png)

