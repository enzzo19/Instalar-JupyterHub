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
  ![conectar vps](https://github.com/enzzo19/Instalar-JupyterHub/blob/master/source/conectar_vps.png)
* Como podras ver la linea de comandos cambia a color blanco para diferenciarla de la de wsl.

1. Una vez dentro de tu VPS puedes actalizarlo con los siguientes comandos:

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

* Mos pedira la contraseña que elegimos para este nuevo usuario.

* Ahora podemos volver a conectarnos a nuestro usuario root para darle permiso de administrador a este nuevo usuario que acabamos de crear.

* Para eso desde el root de nuestro VPS vamos a correr el comando:

   `$ usermod <tu-nombre-usuario_nuevo> -G sudo`

* Para comprobar que este nuevo usuario tiene permisos de administrador puede probar el comando:
  
   `$ su <tu-nombre-usuario_nuevo>`

* Y luego dentro de este nuevo usuario corre el comando:

   `$ sudo ls /`

* Solo deberia pedir la contraseña de este nuevo usuario y mostrar la estructura de directorios.

![new usser as sudo](source\new_usser_as_sudo.png)

* Salir de ese superusuario y regresar a root: `$ exit`

6. Ahora vamos a bloquear el acceso root y el acceso por contraseña a nuestro VPS. [Tutorial de VIM](https://victorhckinthefreeworld.com/2017/06/14/como-salir-del-editor-vim/)

* Desde el usuario root vamos a buscar el archivo para editar los permisos con el comando:

   `$ vim /etc/ssh/sshd_config`

* Y aqui especificamente buscaremos las opciones:
  * Para no permitir el acceso root:
    * `PermitRootLoogin <no>` (cambiar)
  * Para no permitir el acceso por contraseña
    * `PasswordAuthetication <no>` (cambiar)

* Desde Vim para:
  * Entrar en modo edicion presionamos `i`
  * Guardar un cambio `:w`
  * Salir del editor `:q`

* Para guardar los cambios en el sistema:
  * `$ systemctl restart ssh`

* Para salir del usuario root:
  * `$ logout`

* En este punto no deberias poder acceder como usuario root ni tampoco acceder a otro usuario con contraseña  a tu sevidor como se ve en la imagen.

![security access](source\security_acces.png)

* Entonces ya dejamos un VPS con las configuraciones MINIMAS de seguridad y vamos a acceder a el a travez del comando: 
   `$ ssh <tu_nombre_usuario_nuevo>@<tu_ip_server>`

### 2. Instalar las Herramientas

* Ahora que nuestro VPS tiene las configuraciones MINIMAS de seguridad vamos a empezar a instalar ahora si The Littlest Jupyter Hub dentro de el.

1. Primero en tu navegador vas a copiar la direccion IP de tu VPS y ver el contenido que esta te entrega:

* Aqui pueden pasar 2 cosas:
  * 1. El puerto esta liberado y la pagina no tiene ningun contenido, ni siquiera accede (Esto seria bueno y te llevaria directamente al paso 2).
  * 2. El puerto esta ocupado con un proceso y se debe matar el proceso antes de continuar.

* Desde Hostinger sucede lo segundo ya que si colocamos la direccion ip en nuestro navegador obtenemos:

![kill port vps](source\kill_port_vps.png)

* Entonces vamos a encontrar y matar el proceso para liberar el puerto que necesitamos

* Primero vamos a listar los puertos con el comando:
  * `$ sudo netstat -tupln`
  
![find port and proccess](source\find_port_and_procces.png)

* Aqui buscar el puerto 80 y el numero de PID que tiene luego matarlo con:
  * `$ sudo kill -<numero-PID-proceso> PID`

* Ahora una vez que matamos el proceso corriendo en ese puerto al acceder a la ip desde nuestro navegador deberiamos ver algo como:
  
![content page after kill](source\content_page_after_kill.png)

* Y asi exactamente es como la necesitabamos.

2. Probar de levantar un servidor

* Si estas en este punto puede probar de crer un archivo con:
  
  * `$ touch HOLA`
  * `$ ls`

* Intenta levantar un servidor de python el puerto 80 (puerto por defecto para protocolo http):
  
  * `$ sudo python3 - m http.server 80`

![step to make python server](source\step_to_make_python_server.png)

* Una vez realizados estos pasos podemos volver a acceder a nuestra ip desde el navegador y veremos algo como:

![content of python server](source\content_python_server.png)

* Para detenerlo simplemente apretar laconvinacion de teclas `CTRL + C` en la consola donde se estaba corriendo.

3. Ahora vamos a instalar TLJH siguiendo la [guia](https://tljh.jupyter.org/en/latest/install/custom-server.html)

* Asegúrese de tener `python3` y `python3-dev`instalados. `curl` `git`:

  * `$ sudo apt install python3 python3-dev git curl`

![install package](source\install_package.png)

* Copie el texto a continuación y péguelo en la terminal. Reemplace `<admin-user-name>` con el nombre del primer usuario administrador de este JupyterHub. Elija el nombre que desee (¡no olvide quitar los corchetes!).
  `$ curl -L https://tljh.jupyter.org/bootstrap.py | sudo -E python3 - --admin <admin-user-name>`
* Este usuario administrador puede iniciar sesión después de configurar JupyterHub y puede configurarlo según sus necesidades.
* ¡Recuerda agregar tu nombre de usuario !
* Esto puede tardar un poco dependiendo de tu coneccion de red.

![install tljh](source\install_tljh.png)

* Copie la IP pública de su servidor e intente acceder `http://<public-ip>` desde su navegador. Si todo salió bien, esto debería darle una página de inicio de sesión de JupyterHub.

![content tljh web](source\content_tljh_web.png)

* Inicie sesión con el nombre de usuario administrador que utilizó en el paso anterior. Puede elegir cualquier contraseña que desee. Use una contraseña segura y anótela en algún lugar, ya que esta será la contraseña para la cuenta de usuario administrador a partir de ahora.

* ¡Felicitaciones, tiene un JupyterHub en funcionamiento!

### 3_Habilitar HTTPS y apuntar a nuestro Dominio

1. Primero Vamos a conseguir un dominio para apuntarlo a nuestro servidor, esto tambien lo puden hacer desde Hostinger en [dominio](https://www.hostinger.com.ar/registrar-dominio).

![apuntar dominio](source\apuntar_dominio.png)

* Donde esta tapado por rojo va nuestra direccion ip.

2. Ahora siguiendo el [tutorial](https://tljh.jupyter.org/en/latest/howto/admin/https.html#howto-admin-https) vamos a habilitar HTTPS y apuntar tljh a nuestro dominio.

* Para habilitar HTTPS a través de letsencrypt:
  * `$ sudo tljh-config set https.enabled true`
  * `$ sudo tljh-config set https.letsencrypt.email you@example.com`
  * `$ sudo tljh-config add-item https.letsencrypt.domains yourhub.yourdomain.edu`
  
  * Donde `you@example.com` está su dirección de correo electrónico y `yourhub.yourdomain.edu` es el dominio donde se ejecutará su Hub.

![apuntar hub dominio](source\apuntar_hub_dominio.png)

* Finalmente, puede volver a cargar el proxy para cargar la nueva configuración con el comando: 

   `$ sudo tljh-config reload proxy`

![reload_proxy](source\reload_proxy.png)

* En este punto, el proxy debe negociar con Let's Encrypt para configurar un certificado HTTPS de confianza para usted. El proxy puede tardar un momento en negociar con Let's Encrypt para obtener sus certificados, después de lo cual puede acceder a su Hub de forma segura en `<https://yourhub.yourdomain.edu>`.

* Estos certificados tienen una validez de 3 meses. El proxy los renovará automáticamente antes de que caduquen.
