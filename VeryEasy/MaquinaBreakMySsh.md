# Maquina BreakMySSH - DockerLabs.es

![Logo Maquina](ImagenesMaquinaBreakMySsh/image.png)

Primero, debemos verificar que nuestra máquina esté correctamente desplegada.

![alt text](ImagenesMaquinaBreakMySsh/image-2.png)

Realizamos un ping a la máquina para verificar la comunicación y confirmamos que la conexión es exitosa.

![alt text](ImagenesMaquinaBreakMySsh/image-1.png)

A continuación, realizamos un escaneo de la IP utilizando Nmap.

![alt text](ImagenesMaquinaBreakMySsh/image-14.png)

Observamos que el puerto 22 está abierto. Ahora realizamos un escaneo adicional para detectar, enumerar servicios y versiones.

![alt text](ImagenesMaquinaBreakMySsh/image-15.png)

El puerto 22 está ejecutando el servicio SSH en su versión `OpenSSH 7.7`, la cual presenta una vulnerabilidad conocida asociada al `CVE CVE-2018–15473`. Esta vulnerabilidad permite a usuarios remotos determinar nombres de usuario válidos en el sistema objetivo mediante la enumeración de usuarios.

Con esta información, podemos crear o buscar exploits en plataformas como ExploitDB o en otros recursos en línea para explotar esta vulnerabilidad.

En mi caso, he optado por utilizar un exploit disponible en el siguiente repositorio: https://github.com/Sait-Nuri/CVE-2018-15473.

Para utilizar este exploit, primero es necesario otorgarle permisos de ejecución con el comando `chmod +x nombreExploit.py`. Luego, ejecutamos el exploit pasando la IP de la máquina vulnerable junto con la opción `-w` para indicar un diccionario de nombres de usuario (en mi caso, utilicé el diccionario `rockyou`).

![alt text](ImagenesMaquinaBreakMySsh/image-16.png)

Al ejecutar el exploit, observamos que devuelve tanto nombres de usuarios válidos como no válidos. Para filtrar únicamente los usuarios válidos, podemos utilizar el comando `grep -v "invalid"`, lo que nos permitirá obtener solo los nombres de usuario correctos.

![alt text](ImagenesMaquinaBreakMySsh/image-17.png)

Hemos encontrado un usuario válido llamado "lovely". A partir de esto, podríamos intentar realizar un ataque de fuerza bruta utilizando Hydra contra el puerto 22 por SSH, empleando el nombre de usuario que hemos descubierto.

![alt text](ImagenesMaquinaBreakMySsh/image-18.png)

Hemos logrado encontrar la contraseña del usuario "lovely", la cual es "rockyou". Ahora, solo queda conectarnos a la máquina víctima mediante SSH utilizando estas credenciales.

![alt text](ImagenesMaquinaBreakMySsh/image-19.png)

## Escalada de Privilegios

Estuve explorando diferentes métodos, y uno de ellos fue realizar un ataque de fuerza bruta utilizando Hydra contra el usuario root. El comando que utilicé fue el siguiente:

![alt text](ImagenesMaquinaBreakMySsh/image-11.png)

Elegí este usuario por intuición, pero también se puede intentar encontrar otros usuarios realizando una enumeración con un diccionario, utilizando el exploit mencionado anteriormente.

![alt text](ImagenesMaquinaBreakMySsh/image-20.png)

Ahora solo queda iniciar sesión como "root" a través de SSH.

![alt text](ImagenesMaquinaBreakMySsh/image-21.png)

Y como vemos ya logramos obtener una shell en modo root!
