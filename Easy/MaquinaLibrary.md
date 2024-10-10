# Maquina Library - DockerLabs.es

![alt text](ImagenesMaquinaLibrary/image.png)

Verificar que la maquina este desplegada correctamente

![alt text](ImagenesMaquinaLibrary/image-1.png)

Realizamos un ping a la máquina para verificar la comunicación y confirmamos que la conexión es exitosa.

![alt text](ImagenesMaquinaLibrary/image-2.png)

A continuación, realizamos un escaneo de la IP utilizando Nmap.

![alt text](ImagenesMaquinaLibrary/image-3.png)

Observamos que el puerto 80 y 22 estan abiertos. Ahora realizamos un escaneo adicional para detectar, enumerar servicios y versiones.

![alt text](ImagenesMaquinaLibrary/image-5.png)

En este caso, nos centraremos en el puerto 80 que ejecuta un Apache. Accederemos a la página web alojada en esta máquina utilizando un navegador y veremos lo siguiente.

![alt text](ImagenesMaquinaLibrary/image-6.png)

Al realizar un análisis de Fuzzing, encontramos varios archivos, entre ellos el `index.php`. Al acceder a este archivo, se presenta el siguiente mensaje: `JIFGHDS87GYDFIGD`.

![alt text](ImagenesMaquinaLibrary/image-7.png)

![alt text](ImagenesMaquinaLibrary/image-8.png)

Si recordamos el escaneo que hicimos teniamos el puerto 22 abierto, podemos intentar hacer un ataque por fuerza bruta con Hydra utilizando este mensaje como posible nombre de usuario o contraseña... En este caso para adelantar les diré que es la contraseña y tendremos que atacar el usuario utilizando el diccionario.

![alt text](ImagenesMaquinaLibrary/image-9.png)

Hemos obtenido el usuario asociado a la contraseña. Ahora procederemos a acceder por SSH utilizando las credenciales encontradas.

![alt text](ImagenesMaquinaLibrary/image-10.png)

Y listo, estamos dentro.

## Escalada de Privilegios

Al ejecutar `sudo -l`, observamos que se nos permite ejecutar un script en Python ubicado en la ruta `/opt/script.py` utilizando el binario `/usr/bin/python3`, y esto se puede hacer con cualquier usuario sin necesidad de proporcionar una contraseña.

![alt text](ImagenesMaquinaLibrary/image-11.png)

Si accedemos a ese script y revisamos los permisos, notaremos que nosotros somos los propietarios del mismo.

![alt text](ImagenesMaquinaLibrary/image-13.png)

Con esto en mente, se me ocurre otorgar permisos de escritura al script y modificar su código para que devuelva una `/bin/bash`. Procederemos de la siguiente manera:

```
chmod +w script.py
echo -e 'import os \nos.system("/bin/bash")' > script.py
```

![alt text](ImagenesMaquinaLibrary/image-14.png)

Al ejecutar el script con los permisos que tenemos, obtendremos una shell en modo root.

`sudo /usr/bin/python3 /opt/script.py`

![alt text](ImagenesMaquinaLibrary/image-15.png)
