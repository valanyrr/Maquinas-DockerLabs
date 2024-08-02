<!-- ![[Pasted image 20240802000343.png]] -->

# Maquina Upload - DockerLabs.es

![hola](Imagenes/Pasted%20image%2020240802000343.png)

Primero, debemos verificar que nuestra máquina esté correctamente desplegada.

<!-- ![[Pasted image 20240802000549.png]] -->

![hola](Imagenes/Pasted%20image%2020240802000549.png)

Realizamos un ping a la máquina para verificar la comunicación y confirmamos que la conexión es exitosa.

<!-- ![[Pasted image 20240802000649.png]] -->

![hola](Imagenes/Pasted%20image%2020240802000649.png)

A continuación, realizamos un escaneo de la IP utilizando Nmap.

<!-- ![[Pasted image 20240802000847.png]] -->

![hola](Imagenes/Pasted%20image%2020240802000847.png)

Observamos que el puerto 80 está abierto. Ahora realizamos un escaneo adicional para detectar, enumerar servicios y versiones.

<!-- ![[Pasted image 20240802001115.png]] -->

![hola](Imagenes/Pasted%20image%2020240802001115.png)

Ahora accedemos a la página web alojada en esa máquina.

<!-- ![[Pasted image 20240802001251.png]] -->

![hola](Imagenes/Pasted%20image%2020240802001251.png)

Intentaremos cargar un recurso y luego acceder a él a través de la misma página web.

<!-- ![[Pasted image 20240802001354.png]] -->

![hola](Imagenes/Pasted%20image%2020240802001354.png)

Ahora, para identificar dónde están alojados los recursos, realizaremos un fuzzing en la web para detectar posibles carpetas ocultas.

<!-- ![[Pasted image 20240802001817.png]] -->

![hola](Imagenes/Pasted%20image%2020240802001817.png)

Ahora podemos visualizar y acceder a los recursos alojados.

<!-- ![[Pasted image 20240802001913.png]] -->

![hola](Imagenes/Pasted%20image%2020240802001913.png)

<!-- ![[Pasted image 20240802001923.png]] -->

![hola](Imagenes/Pasted%20image%2020240802001923.png)

Ahora intentaremos subir una shell utilizando código PHP o algún otro método que nos permita ejecutar comandos directamente en el sistema operativo. Luego, configuraremos el archivo para que envíe una reverse shell a nuestra IP y puerto.

<!-- ![[Pasted image 20240802004303.png]] -->

![hola](Imagenes/Pasted%20image%2020240802004303.png)

<!-- ![[Pasted image 20240802002347.png]] -->

![hola](Imagenes/Pasted%20image%2020240802002347.png)

Nos ponemos a la escucha con Netcat en el puerto 4444 y luego cargamos el recurso desde la web.

<!-- ![[Pasted image 20240802004543.png]] -->

![hola](Imagenes/Pasted%20image%2020240802004543.png)

## (Opcional) Tratamiento de la TTY

Una vez estemos dentro ejecutamos el siguiente comando:
`script /dev/null -c bash`

Luego presionamos: `Ctrl + Z` para suspender el proceso

A continuación, escribimos:
`stty raw -echo; fg`

Despues ingresamos:
`reset`

Cuando se nos pregunte:
"Terminal type?"
Ingresamos `xterm`.

Finalmente, exportamos las siguientes variables de entorno:
`export TERM=xterm`
`export SHELL=bash`

Y listo!

## Escalada de Privilegios

Ejecutamos `sudo -l` para revisar los permisos del usuario. Observamos que podemos ejecutar el binario `/usr/bin/env` como usuario root sin necesidad de proporcionar una contraseña.

<!-- ![[Pasted image 20240802005534.png]] -->

![hola](Imagenes/Pasted%20image%2020240802005534.png)

Para explotar esta vulnerabilidad, realizaremos los siguientes pasos:
`sudo env /bin/bash`

<!-- ![[Pasted image 20240802005710.png]] -->

![hola](Imagenes/Pasted%20image%2020240802005710.png)

¡Con eso, ya tendríamos una sesión de Bash con privilegios de root!
