# Easypeasyctf

<br>

## Escaneo de puertos

Realizaremos un análisis inicial de la máquina objetivo utilizando **Nmap** para identificar puertos abiertos y servicios en ejecución.

![1.png](./_resources/1.png)

Se utiliza la herramienta **Gobuster** para realizar un escaneo y descubrir directorios no visibles en el sistema.

![2.png](./_resources/2.png)

Ingresamos al directorio denominado `/hidden`, sin embargo, no se encuentran elementos de interés.

![3.png](./_resources/3.png)

Realizamos una búsqueda de directorios ocultos dentro del nuevo directorio descubierto.

![4.png](./_resources/4.png)

Exploramos el directorio `/whatever`, donde, al analizar el código fuente, encontramos una cadena codificada en base 64.

![5.png](./_resources/5.png)

Al decodificar la cadena, revelamos la primera flag.

![6.png](./_resources/6.png)

Procedemos a buscar información en el archivo `robots.txt`, detectado mediante el escaneo de Nmap en el puerto **65524**. En su contenido, se encuentra un hash MD5 de una cadena."

![7.png](./_resources/7.png)

Al desencriptar el hash, se obtiene la segunda flag.

![8.png](./_resources/8.png)

Examinamos la página alojada en ese puerto, podemos visualizar la tercera flag.

![9.png](./_resources/9.png)

Revisamos el código fuente y encontramos otra cadena, esta vez codificada en **base 62**.

![10.png](./_resources/10.png)

Desencriptada la cadena, se obtiene la ubicación del directorio oculto.

![11.png](./_resources/11.png)

Exploramos el directorio, aparece una nueva cadena codificada.

![12.png](../../_resources/12.png)

Para obtener la contraseña, se emplea la herramienta ***John the Ripper***, utilizando el archivo proporcionado en el ejercicio como apoyo para obtener el resultado.

![13.png](./_resources/13.png)

En el código fuente de la página recientemente analizada, se observa una imagen que podría resultar útil.

![14.png](./_resources/14.png)

Obtenemos la imagen de la página web utilizando el comando `wget`.

![15.png](./_resources/15.png)

Utilizamos la herramienta **Steghide** para extraer el archivo `.txt` oculto dentro de la imagen.

![16.png](./_resources/16.png)

Abrimos el archivo, podemos observar que contiene un usuario y una contraseña en formato binario.

![17.png](./_resources/17.png)

Tras desencriptar el código, se revela la contraseña.

![18.png](./_resources/18.png)

Accedemos al sistema a través de **SSH** utilizando el usuario y la contraseña obtenidos.

![19.png](./_resources/19.png)

En el sistema aparece un archivo llamado `user.txt`, que incluye la flag del usuario, aunque está codificada en **ROT13**.

![20.png](./_resources/20.png)

Una vez descifrada, encontramos la flag.

![21.png](./_resources/21.png)

Dado que el usuario no dispone de permisos **sudo**, se revisa el archivo `/etc/crontab`, donde se encuentra una entrada para el archivo **.mysecretcronjob.sh**, el cual tiene permisos de **root**.

![22.png](./_resources/22.png)

Para configurar una shell reversa, primero se habilita un puerto en escucha en el equipo.

![23.png](./_resources/23.png)

Luego, ejecutamos el siguiente comando en la terminal de la máquina donde se van a escalar los privilegios.

![24.png](./_resources/24.png)

Después de unos segundos, se obtiene acceso a la línea de comandos con privilegios de **root**, donde se ejecutó el comando de escucha.

![25.png](./_resources/25.png)

Por último, al ejecutar el comando **ls -la**, se listan los archivos, entre los cuales se encuentra `root.txt`. Al visualizar su contenido con el comando **cat**, se obtiene la flag.

![26.png](./_resources/26.png)