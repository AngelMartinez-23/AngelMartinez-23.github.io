# Sudo Agent

<br>

## Escaneo de puertos

El análisis comienza ejecutando un escaneo con **Nmap**, con el objetivo de identificar los puertos abiertos en la máquina y obtener información sobre el sistema operativo que se encuentran.

![1.png](./_resources/1.png)

El escaneo revela que los puertos están abiertos son:

- **80/TCP -** HTTP
- **21/TCP -** FTP
- **22/TCP -** SSH  
    <br/>

Como primer paso, accedemos al sitio web objetivo y observamos el siguiente mensaje en pantalla:

![2.png](./_resources/2.png)

El mensaje sugiere que, para acceder correctamente al sitio, es necesario personalizar el valor del encabezado **User-Agent**, utilizando un identificador específico. En este caso, se nos indica que utilicemos nuestro propio "código", firmado como **Agente R**.

Como primera prueba, se procede a establecer el **User-Agent** como `"R"` al realizar la solicitud al sitio.

![3.png](./_resources/3.png)

Se realizan múltiples solicitudes utilizando diferentes valores en el encabezado **User-Agent** a través de la herramienta `curl`. Como resultado de estas pruebas, se obtiene una nueva respuesta del servidor al utilizar `"C"` como **User-Agent**, lo que indica un posible cambio en el comportamiento del sitio según el agente utilizado.

![4.png](./_resources/4.png)

Como siguiente paso en el proceso de enumeración, se intenta obtener la contraseña del usuario identificado mediante un ataque de fuerza bruta. Para ello, se utiliza la herramienta **Hydra**, aprovechando su capacidad para automatizar intentos de autenticación contra el servicio expuesto.

![5.png](./_resources/5.png)

Una vez obtenidas las credenciales válidas, se establece una conexión con el servidor a través del protocolo **FTP**. Procedemos a listar y descargar todos los archivos disponibles, con el objetivo de analizarlos localmente en busca de información relevante.

![6.png](./_resources/6.png)

![7.png](./_resources/7.png)

A continuación, se examina el contenido de uno de los archivos obtenidos.

![8.png](./_resources/8.png)

En el siguiente paso, se procede a inspeccionar posibles datos ocultos dentro de archivos de imagen utilizando herramientas especializadas. En esta ocasión, se emplea **binwalk** para analizar el archivo **cutie.png**. El resultado revela que la imagen contiene un archivo comprimido en formato **ZIP** incrustado en su estructura.

![9.png](./_resources/9.png)

Se procede a extraer el archivo ZIP oculto dentro de la imagen utilizando **binwalk**. Sin embargo, al intentar acceder a su contenido, se detecta que el archivo está protegido por contraseña. Para intentar recuperarla, se recurre a un ataque de fuerza bruta utilizando la herramienta **John the Ripper**, que permite descifrar contraseñas a partir de hashes obtenidos del archivo.

![10.png](./_resources/10.png)

El proceso comienza utilizando la herramienta **zip2john** para convertir el archivo ZIP en un formato compatible con **John the Ripper**, generando así el hash necesario para el ataque. Una vez obtenido, se utiliza **john** para intentar descifrar el contenido protegido del archivo.

![11.png](./_resources/11.png)

Una vez que se ha logrado descifrar la contraseña, se procede a descomprimir el archivo ZIP y acceder a su contenido. Dentro de él, se encuentra un archivo de texto que contiene un mensaje directo del **Agente R**, el cual podría ser crucial para avanzar en el análisis.

![12.png](./_resources/12.png)

![13.png](./_resources/13.png)

Utilizando la contraseña obtenida, se procede a recuperar la información oculta en otra imagen, **cute-alien.jpg**. Gracias a este procedimiento, se logran extraer credenciales válidas correspondientes a un usuario denominado **james**.

![14.png](./_resources/14.png)

Con las credenciales obtenidas de **james**, se establece una conexión remota a la máquina a través del servicio **SSH**.

![15.png](./_resources/15.png)

<br>

## Primera flag

Una vez dentro del sistema a través de la conexión SSH, se realiza una navegación por el directorio del usuario y se localiza el archivo **user_flag.txt**, en el cual se encuentra la **flag** correspondiente.

![16.png](./_resources/16.png)

Al realizar una búsqueda, descubrimos que el nombre del incidente relacionado con la imagen es **Roswell Alien Autopsy**.

<br>

## Escalada de privilegios

Lo primero que vamos a ver es que permisos tiene el usuario james con el comando \`**sudo -l**\`.

![17.png](./_resources/17.png)

Buscando tal cual ‘**(ALL, !root) /bin/bash**’ encontramos esta [<ins>**vulnerabilidad**</ins>](https://www.exploit-db.com/exploits/47502) . Para realizar la escalada utilizamos el siguiente comando: `sudo -u#-1 /bin/bash`

![18.png](./_resources/18.png)