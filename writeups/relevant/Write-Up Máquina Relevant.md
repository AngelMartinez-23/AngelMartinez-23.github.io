# Relevant

<br>

## Escaneo de puertos

Como primer paso, realizamos un escaneo de puertos con **Nmap** para identificar los servicios en la máquina.

![1.png](_resources/1.png)

Posteriormente, ejecutamos un escaneo más detallado sobre el puerto **445** (SMB) utilizando los scripts "*smb-enum-shares.nse*" y "*smb-enum-users.nse*" para enumerar los recursos compartidos y usuarios disponibles.

![2.png](_resources/2.png)

## Enumeración con enum4linux

A continuación, empleamos la herramienta **Enum4linux** para recopilar información detallada sobre la configuración y estructura del sistema objetivo.

![3.png](_resources/3.png)

<br>

## Enumeración de SMB

Utilizamos el protocolo SMB para explorar los recursos compartidos mediante los comandos: "*smbap*" y "*smbclient*"

![4.png](_resources/4.png)

![5.png](_resources/5.png)

Identificamos un usuario con acceso y descargamos el archivo "*password.txt*".

![6.png](_resources/6.png)

Contenido del archivo:

![7.png](_resources/7.png)

Las credenciales encontradas están codificadas en *Base64*. Utilizamos la herramienta **CyberChef** para su decodificación.

![8.png](_resources/8.png)

![9.png](_resources/9.png)

<br>

## Escalada de privilegios 

Generamos una reverse shell en **ASPX** para cargarla en el servidor y obtener acceso remoto.

![10.png](_resources/10.png)

Subimos el archivo y establecemos conexión con la reverse shell.

![11.png](_resources/11.png)

![12.png](_resources/12.png)

![13.png](_resources/13.png)

Identificamos y obtenemos la primera flag ubicada en el archivo "*user.txt*".

![14.png](_resources/14.png)

Verificamos los privilegios disponibles en la sesión y encontramos la vulnerabilidad "*SeImpersonatePrivilege*", la cual nos permite explotar un método conocido.

![15.png](_resources/15.png)

Para explotarlo, clonamos el siguiente repositorio que contiene el exploit necesario:

![16.png](_resources/16.png)

Y subimos el siguiente .exe al servidor:

![17.png](_resources/17.png)

Para que se encuentre correctamente el archivo debemos de ejecutar el **smbclient** desde la carpeta **printspoofer**, y ya una vez dentro podemos subirlo.

![18.png](_resources/18.png)

Ejecutamos el exploit y obtenemos acceso como **NT AUTHORITY\\SYSTEM**.

![19.png](_resources/19.png)

Una vez con privilegios de administrador, localizamos el archivo "*root.txt*" y obtenemos nuestra segunda flag.

![20.png](_resources/20.png)