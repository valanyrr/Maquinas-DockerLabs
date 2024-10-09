# Maquina PequeñasMentirosas - DockerLabs.es

![alt text](ImagenesMaquinaPequenasMentirosas/image-1.png)

Verificar que la maquina este desplegada correctamente

![alt text](ImagenesMaquinaPequenasMentirosas/image-2.png)

Realizamos un ping a la máquina para verificar la comunicación y confirmamos que la conexión es exitosa.

![alt text](ImagenesMaquinaPequenasMentirosas/image-3.png)

A continuación, realizamos un escaneo de la IP utilizando Nmap.

![alt text](ImagenesMaquinaPequenasMentirosas/image-4.png)

Observamos que el puerto 80 y 22 estan abiertos. Ahora realizamos un escaneo adicional para detectar, enumerar servicios y versiones.

![alt text](ImagenesMaquinaPequenasMentirosas/image-5.png)

En este caso, nos centraremos en el puerto 80 que ejecuta un Apache. Accederemos a la página web alojada en esta máquina utilizando un navegador y veremos lo siguiente.

![alt text](ImagenesMaquinaPequenasMentirosas/image-6.png)

Como vemos solamente es una pagina que tiene un mensaje que dice "Pista: Encuentra la clave para A en los archivos."

Recordemos que esta maquina tiene el puerto 22 abierto que corre un SSH, y segun el mensaje que vemos en la pagina web hay posiblemente un usuario llamado "A" al cual tenemos que encontrar su clave. Usaremos Hydra para hacer ataque por fuera bruta por SSH a ver si obtenemos alguna sesión válida...

![alt text](ImagenesMaquinaPequenasMentirosas/image-7.png)

Vemos que logramos encontrar la contraseña para el usuario "a" la cual es `secret`, ahora accederemos por SSH...

![alt text](ImagenesMaquinaPequenasMentirosas/image-8.png)

Y ya estamos dentro.

## Escalada de Privilegios

Si vemos los usuarios que existen, notaremos que hay uno llamado `spencer`

![alt text](ImagenesMaquinaPequenasMentirosas/image-9.png)

Si hacemos una busqueda de archivos `.txt` de la siguiente manera `find / -name *.txt 2>/dev/null`, veremos que hay algunos muy interesantes...

![alt text](ImagenesMaquinaPequenasMentirosas/image-10.png)

Si vemos el contenido de cada uno de estos archivos con `ls | xargs cat` veremos los siguiente:

![alt text](ImagenesMaquinaPequenasMentirosas/image-11.png)

Si vemos específicamente el contenido del archivo que dice `hash_spencer.txt` nos encontraremos con un hash en `MD5`

![alt text](ImagenesMaquinaPequenasMentirosas/image-12.png)

Si tratamos de romper ese hash con alguna herramienta como `CrackStation` veremos que obtendremos lo siguiente...

![alt text](ImagenesMaquinaPequenasMentirosas/image-13.png)

Si cambiamos al usuario `spencer` y proporcionamos la contraseña `password1`

![alt text](ImagenesMaquinaPequenasMentirosas/image-14.png)

Ahora bien, si ejecutamos un `sudo -l` con este usuario veremos que podemos ejecutar el binario de `/usr/bin/python3` como cualquier usuario sin propocionar contraseña.

![alt text](ImagenesMaquinaPequenasMentirosas/image-15.png)

Nos aprovecharemos de estos permisos de la siguiente manera para escalar privilegios.

`sudo /usr/bin/python3 -c "import os; os.system('/bin/bash')"`

![alt text](ImagenesMaquinaPequenasMentirosas/image-16.png)

Y ya seriamos root!
