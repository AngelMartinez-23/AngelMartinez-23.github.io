# Retro

La máquina **Retro** presenta una estructura similar a la de *Blaster*, con la diferencia de que no proporciona una guía detallada en su resolución. La explotación sigue un camino parecido, con algunas variaciones en la escala de privilegios.

<br>

## Escaneo de puertos

Se inicia con un escaneo de puertos de la máquina objetivo, empleando el parámetro "*\-Pn*", dado que no responde a pings.

![1.png](_resources/1.png)

Al analizar el puerto *80*, encontramos el index por defecto de **Microsoft IIS**. 

![2.png](_resources/2.png)

<br>

## Investigación de la página

Se realiza un fuzzing de directorios y se identifica */retro*, un blog personal de Wade con contenido sobre videojuegos, películas y otros temas de interés.

![3.png](_resources/3.png)

En el blog, un post sobre **Ready Player One** revela un posible error recurrente del usuario al escribir el nombre de su avatar. Revisando los comentarios, se obtiene una *contraseña*.

![4.png](_resources/4.png)

![5.png](_resources/5.png)
![6.png](_resources/6.png)

Con las credenciales en mano, intentamos acceder al sistema utilizando la herramienta **xfreerdp**.

![7.png](_resources/7.png)

Al lograr la conexión, encontramos el archivo "*user.txt*", que contiene la primera flag. En la papelera de reciclaje, se identifica el ejecutable "*hhupd.exe*", que permitirá la escalada de privilegios.

![8.png](_resources/8.png)

Dentro del *"user.txt"* encontraremos la primera flag

![9.png](_resources/9.png)

Como lo dijimos en la máquina *blaster* el ejecutable esta asociado al **CVE-2019-1388**, de todas maneras investigando un poco podemos encontrar dentro del google chrome en el apartado de marcadores lo siguiente:  

![10.png](_resources/10.png)

<br>

## Escalada de privilegios

El ejecutable está vinculado a la vulnerabilidad **CVE-2019-1388**. No obstante, al intentar explotarla, surge un error que impide el uso de Google Chrome o Internet Explorer para continuar. Investigando, se encuentra que esto es un bug del sistema Windows Server.

Realizamos los mismos pasos que en la máquina *blaster*, pero con la excepción del bug.

![11.png](_resources/11.png)

![12.png](_resources/12.png)

En este punto, deberíamos seguir los pasos que hicimos en la room de *Blaster*, pero aparece lo siguiente:

![13.png](_resources/13.png)

Para solventarlo, se busca una alternativa y se identifica la vulnerabilidad [CVE-2017-0213](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0213), que permite la escalada de privilegios mediante la ejecución de una aplicación específica.

![14.png](_resources/14.png)

Se descarga el exploit correspondiente desde este [repositorio](https://github.com/WindowsExploits/Exploits/tree/master/CVE-2017-0213). Dado que Windows clasifica el archivo como potencialmente peligroso, es necesario autorizar su descarga de manera manual. Tras completarla, se procede a descomprimirlo y transferirlo a la máquina objetivo utilizando Python.

![15.png](_resources/15.png)

![16.png](_resources/16.png)

Una vez descargado el archivo, procederemos a ejecutarlo y, como podemos observar en la imagen, nos encontramos en la interfaz de administrador.  

![17.png](_resources/17.png)

Finalmente, navegando entre los directorios, se localiza "*root.txt*", completando así la explotación de la máquina Retro.

![18.png](_resources/18.png)