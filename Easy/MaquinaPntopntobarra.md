# Maquina Pntopntobarra - DockerLabs.es

![alt text](ImagenesMaquinaPntopntobarra/image.png)

Verificar que la maquina este desplegada correctamente

![alt text](ImagenesMaquinaPntopntobarra/image-1.png)

Realizamos un ping a la máquina para verificar la comunicación y confirmamos que la conexión es exitosa.

![alt text](ImagenesMaquinaPntopntobarra/image-2.png)

A continuación, realizamos un escaneo de la IP utilizando Nmap.

![alt text](ImagenesMaquinaPntopntobarra/image-3.png)

Observamos que el puerto 80 y 22 estan abiertos. Ahora realizamos un escaneo adicional para detectar, enumerar servicios y versiones.

![alt text](ImagenesMaquinaPntopntobarra/image-4.png)

En este caso, nos centraremos en el puerto 80 que ejecuta un Apache. Accederemos a la página web alojada en esta máquina utilizando un navegador y veremos lo siguiente.

![alt text](ImagenesMaquinaPntopntobarra/image-5.png)

Y tambien veremos lo siguiente al entrar en `Ejemplos de computadoras infectadas`

![alt text](ImagenesMaquinaPntopntobarra/image-6.png)

Si aplicamos Fuzzing a la web encontraremos algunas cosas interesantes como `t.php`

![alt text](ImagenesMaquinaPntopntobarra/image-7.png)

Después de experimentar con diversas técnicas, decidí aplicar Fuzzing utilizando Wfuzz en `t.php` con el objetivo de identificar algún parámetro susceptible de ser explotado para llevar a cabo un LFI. Durante esta prueba, descubrí un parámetro llamado `images`.

![alt text](ImagenesMaquinaPntopntobarra/image-8.png)

Al ejecutar el comando `curl "http://172.17.0.2/t.php?images=/etc/passwd"`, podemos observar que se muestra el contenido de `/etc/passwd`.

![alt text](ImagenesMaquinaPntopntobarra/image-9.png)

Vemos que en la lista de usuarios hay uno llamado `nico`, recordemos que el puerto 22 esta abierto, entonces haremos un ataque por fuerza bruta con Hydra a SSH a ver si logramos encontrar la contraseña para dicho usuario...

Vemos que encontramos la contraseña para el usuario.

![alt text](ImagenesMaquinaPntopntobarra/image-10.png)

Ahora procederemos a iniciar sesión por SSH utilizando el usuario `nico` y la contraseña `lovely`.

![alt text](ImagenesMaquinaPntopntobarra/image-11.png)

Y ya estamos dentro.

## Escalada de Privilegios

Al ejecutar `sudo -l`, observamos que podemos ejecutar el binario `/bin/env` como cualquier usuario sin necesidad de proporcionar una contraseña.

![alt text](ImagenesMaquinaPntopntobarra/image-12.png)

Aprovecharemos estos permisos para escalar privilegios de la siguiente manera:

`sudo /bin/env /bin/bash`

![alt text](ImagenesMaquinaPntopntobarra/image-13.png)

Y obtendremos una shell en modo root.
