# Tutorial Instalar Littlest-JupyterHub en VPS

En este tuorial vamos a ver como instalar JupyterHub en un VPS las herramientas que utilizamos son:

* Distro de Ubuntu 20.04 LTS instalada en Windows con WSL

  * Windows 10 (mi SO local)
* Un VPS contratado de Hostinger con las siguientes caracteristicas:

  * 1GB RAM
  * 20GB HDD
  * 2 nucleos

    * Estas caracteristicas son las minimas que provee hostinger para contratar un servicio de VPS.
  * Una vez que tenemos esto Hostinger nos provee una IP con la cual vamos a poder conectarnos desde nuestro SO local por medio de una conexion ssh

    ## Pasos a Seguir para la instalacion
  * El primero paso va a ser abrir una terminal de Ubuntu corriendo en windows con WSL y desde ahi ver la gestion de memoria de nuestra SO local corriendo

    `$ htop -s PERCENT_MEM`
  * Tambien vamos a actualizar nuestro version de Ubuntu desde WSL corriendo ` $ sudo apt-get update` `$ sudo apt-get upgrade` ``
