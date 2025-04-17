# Bounty Hacker

<br>

## Escaneo de puertos

Realizaremos un análisis inicial de la máquina objetivo utilizando **Nmap** para identificar puertos abiertos y servicios en ejecución.

![1.png](./_resources/1.png)

Como podemos observar en la imagen los puertos abiertos son:

- **80/TCP -** HTTP
- **22/TCP -** SSH
- **21/TCP** - FTP  

<br>

Comenzaremos analizando el sitio web a través del puerto **80**, en busca de información que pueda resultar útil.

![2.png](./_resources/2.png)

El sitio web resultó ser una página estática sin contenido relevante ni enlaces de interés, por lo que procedimos a examinar el siguiente puerto disponible.

Se intentó establecer una conexión con el servidor **FTP** utilizando acceso anónimo.

![3.png](./_resources/3.png)

Se identificaron dos archivos que podrían ser relevantes, por lo que procedimos a descargarlos y analizar su contenido para evaluar su posible utilidad.

![4.png](./_resources/4.png)

A continuación, se revisaron los archivos `locks.txt` y `task.txt`.

![5.png](./_resources/5.png)

![6.png](./_resources/6.png)

Ahora que tenemos un posible nombre de usuario identificado y el servicio **SSH** accesible a través del puerto **22**, procedimos a realizar un ataque de fuerza bruta utilizando la herramienta `Hydra`, junto con una lista de contraseñas predefinida.

![7.png](./_resources/7.png)

<br>

## Primera flag

Una vez obtenidas las credenciales, establecemos una conexión **SSH** utilizando dichos datos de acceso.

![8.png](./_resources/8.png)

El siguiente paso consiste en localizar la primera **flag** dentro del sistema comprometido.

![9.png](./_resources/9.png)

<br>

## Escalada de privilegios

Para comenzar con la escalada de privilegios, verificamos los permisos del usuario **lin** mediante el comando `sudo -l`.

![10.png](./_resources/10.png)

Tal como se muestra en la imagen anterior, el binario `tar` puede ser explotado para obtener acceso como **root**. Consultando la referencia en [GTFOBins](https://gtfobins.github.io/gtfobins/tar/#sudo), identificamos el comando apropiado para llevar a cabo esta acción.

![11.png](./_resources/11.png)

![12.png](./_resources/12.png)

Por último, una vez obtenidos los privilegios de **root**, procederemos a buscar la flag en el sistema.

![13.png](./_resources/13.png)