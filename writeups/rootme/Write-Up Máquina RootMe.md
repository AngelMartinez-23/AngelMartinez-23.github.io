# RootMe

<br>

## Escaneo de puertos

Para comenzar, realizaremos un escaneo utilizando **Nmap** con el objetivo de identificar los puertos abiertos en el sistema.

![1.png](./_resources/1.png)

Como podemos observar en la imagen los puertos abiertos son:

- **80/TCP -** HTTP
- **22/TCP -** SSH  

<br>

Ahora utilizaremos la herramienta **Gobuster** para realizar una exploración de directorios en el servidor web, con el objetivo de identificar rutas ocultas que no están visibles directamente desde la página principal.

![2.png](./_resources/2.png)

Una vez identificado el directorio oculto (`/panel/`), procedemos a acceder a él. Dentro de esta sección, se nos proporciona la opción de cargar archivos en el servidor.

![3.png](./_resources/3.png)

El siguiente paso consiste en generar una *reverse shell* en PHP, configurando en ella nuestra dirección IP y el puerto que utilizaremos para la escucha. Sin embargo, al intentar subir este archivo al servidor, notamos que el sistema no permite su carga directamente.

![4.png](./_resources/4.png)

![5.png](./_resources/5.png)

Para sortear las restricciones del servidor y lograr que acepte el archivo, modificamos la extensión de nuestra *reverse shell* de `.php` a `.phtml`. Con este cambio, el archivo puede subirse sin inconvenientes.

![6.png](./_resources/6.png)

![7.png](./_resources/7.png)

Una vez completada la carga del archivo, procedemos a iniciar un proceso de escucha utilizando **Netcat** en el puerto previamente configurado.

![8.png](./_resources/8.png)

Simultáneamente, ejecutamos el script haciendo doble clic sobre él, ubicado en el directorio de *uploads*.

![9.png](./_resources/9.png)

Tras la ejecución, el sistema debería establecer una *reverse shell* estable. Para garantizar su estabilidad, procedemos a ejecutar el siguiente comando:

![10.png](./_resources/10.png)

A continuación, procederemos a la búsqueda de la *flag*. Para ello, nuestro objetivo es localizar el archivo `user.txt`.

![11.png](./_resources/11.png)

A continuación, ejecutaremos el siguiente comando para revisar los permisos relevantes. Al hacerlo, observamos que existe un archivo en el directorio `/usr/bin/python` que no pertenece al sistema.

![12.png](./_resources/12.png)

<br>

## Escalada de privilegios

Para realizar la escalada de privilegios, podemos consultar la página de [<ins>GTFOBins</ins>](https://gtfobins.github.io/gtfobins/python/#reverse-shell) en busca de posibles comandos de escalada para Python. El comando que utilizaremos es el siguiente:

![13.png](./_resources/13.png)

![14.png](./_resources/14.png)

Por último, procederemos de la misma manera que lo hicimos con el archivo `user.txt`, pero en este caso buscaremos el archivo `root.txt`.

![15.png](./_resources/15.png)