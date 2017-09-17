### Taller 2
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Estudiante:** Juan Camilo Swan  
**Tema:** Llamadas al sistema (syscalls)  
### Objetivos
* Conocer y explicar la función de las llamadas al sistema en un sistema operativo
### Prerrequisitos
* Virtualbox o WMWare
* Máquina virtual con sistema operativo CentOS7
* Aplicativo strace
### Descripción
El tercer taller del curso sistemas operativos trata sobre las llamadas al sistema y su importancia para el sistema operativo
### Actividades
1. Empleando el aplicativo **strace** obtenga 5 llamadas al sistema para uno o varios comandos de linux. Explique por qué los comandos seleccionados emplean las llamadas al sistema encontradas, para ello debe emplear los manuales de Linux en Internet o del sistema operativo (comando **man**). Debe incluir la explicación de los parámetros que reciben las llamadas al sistema encontradas. Consigne capturas de pantalla donde muestre las llamadas al sistema obtenidas (sugerencia: emplear -etrace para filtrar los resultados)
  
**Para este primer punto se utilizó los llamados al sistemas del comando 'ls'.**  
   ![][1]  
* **open(fichero, tipo):** La llamada al sistema open () se utiliza para convertir un nombre de ruta en un descriptor de archivo (un entero pequeño, no negativo para uso en las I/O subsiguientes), En este caso todas las rutas les asigna un número que para el comando ls empleado es el descriptor con un valor de 3. Cuando la llamada es correcta, el descriptor de archivo devuelto será el descriptor de archivo más bajo que no está abierto actualmente para el proceso.  
   Los indicadores de parámetros son O_RDONLY, O_WRONLY o O_RDWR que solicitan abrir el archivo sólo lectura, sólo escritura o lectura/escritura, respectivamente. Para este caso el comando ls emplea solo lectura.  
   ![][2]  
* **read(fichero, buffer, tamaño):** La llamada al sistema read se realiza desde un proceso de usuario y  permite la lectura de datos de un archivo.
 Parámetros:  
	* Fd: descriptor de fichero, creado en una llamada previa (numero asignado por el llamado open().)  
	* buf :puntero al buffer de caracteres donde se recibirán los datos leídos.  
	* Count: número máximo de bytes que se van a almacenar en el buffer de caracteres.  
La salida del read es el número de bytes que se leyeron del archivo.  
   ![][3]  
* **write(Fichero, buffer, tamaño):** Escritura de datos que un proceso puede realizar en un archivo.  
Parametros:  
	* Fd: descriptor del fichero en el que se quiere escribir.  
	* Buf: puntero  que    apunta  al  buffer donde  se  encuentran  los  datos  a  escribir.  
	* Count: indica el número de bytes que contiene el buffer.  
Esta llamada puede retornar: 
	* Si  se  ha  ejecutado  correctamente:  el  número  de  bytes  que  fueron  transferidos.  
	* Si ha habido algún error: -1.  
   ![][4]  
* **Access(Fichero, permisos):** Sirve para comprobar los permisos sobre un determinado fichero. Access puede comprobar cualquier combinación de permisos lectura, escritura y ejecución.   
Tiene 2 argumentos el primero es la ruta al fichero cuyos permisos queremos comprobar el segundo es un campo de bits tipo OR que puede contener R_OK, W_OK, X_OK que corresponden a verificar los permisos de lectura, escritura y ejecución.  
El valor de retorno es 0 si tiene todos los permisos especificados por el segundo parámetro. Si el fichero existe pero no tiene todos los permisos especificados devuelve -1.  
     ![][5]  
* **getdents(fildes, buf,s nbyte):** Permite saber saber el nombre de los ficheros que hay en un subdirectorio.  
Parametros:  
	* Fildes: será un descriptor de fichero del subdirectorio que queremos examinar. Lo obtendremos con open. (Se puede notar que en el ejemplo este valor es 3, que fue el obtenido con la llamada al sistema open analizada al inicio.)  
	* Buf: Se trata de un buffer de caracteres cuyo tamaño ha de ser un múltiplo del tamaño del bloque del sistema de ficheros.(Esta información se puede obtener haciendo un stat sobre el directorio; como se puede ver en la primera foto el comando ls también hace un llamado stat.)  
	* Nbyte: es el tamaño del buffer anterior, para no estar escribiendo fuera del área reservada para dicho buffer.  
     ![][6]  

  

2. Realice la compilación del código fuente adjunto y su ejecución empleando el aplicativo **strace**. Identifique las llamadas al sistema encargadas de enviar y recibir datos a través de la red. A partir de los manuales de Linux en Internet o del sistema operativo explique las llamadas al sistema encontradas y sus parámetros.  

* Para completar este punto primero se descargaron las librerias correspondientes y luego se implementó y complió el algoritmo brindado curl.c. posteriormente con la herramienta strace se analizaron sus syscallls.  
   ![][7]  
  
* **connect(sockfd, struct sockaddr serv_addr, addrlen):** sockfd es nuestro famoso descriptor de fichero de socket, serv_addr es una estructura struct sockaddr que contiene el puerto y la dirección IP de destino, y a addrlen le podemos asignar el valor del tamaño del serv_addr.  
   ![][8]  
* **socket(int domain, int type, int protocol):** Devuelve el descriptor del fichero tal y como lo hacía la llamada al sistema open.  
	*Domain: describe una dirección de la familia de direcciones con la que se hace conexión, una dirección perteneciente a AF_INET.  
	*Type: Le dice al sistema operativo que tipo de socket es este.  
	*Protocol: le dice al sistema operativo median que protocolo se abrirá el socket.    
   ![][9]  
* **recvfrom(sockfd, buf, len, flags, struct sockaddr from, fromlen):** sockfd es el descriptor del fichero del que se va a leer, buff es el buffer donde se va a depositar la información leida, len es la longitud máxima del buffer, y flags, son las banderas del mensaje; que habitualmente van en cero, from es un puntero a una estructura struct sockaddr local que será rellenada con la dirección IP y el puerto de la máquina de origen. fromlen es un puntero a un int local que tiene el tamaño del from. Devuelve el número de bytes recibidos, o -1 en caso de error.  
   ![][10]  
* **getpeername(sockfd, struct sockaddr addr, addrlen):** Dirá quién está al otro lado de un socket de flujo conectado.
sockfd es el descriptor del socket de flujo conectado, addr es un puntero a una estructura struct sockaddr que guardará la información acerca del otro lado de la conexión, y addrlen es un puntero a un int, que almacena el tamaño del addr. La función devuelve -1 en caso de error.  
  ![][11]  
* **sendto(sockfd, msg,  len, flags, to, tolen):** sockfd es el descriptor de socket al que quieres enviar datos. msg es un puntero a los datos que quieres enviar, y len es la longitud de esos datos en bytes, flags son las banderas del mensaje; que habitualmente van en cero, to es un puntero a una estructura struct sockaddr que contiene la dirección IP y el puerto de destino, tolen es el tamaño que puede tomar la estructura to.  
Devuelve el número de bytes que realmente se enviaron  o -1 en caso de error.  
  ![][12]  
* **Getsockname(sockname, size):** hostname es un puntero a una cadena de carácteres donde se almacenará el nombre del socket cuando la función retorne, y size es la longitud en bytes de esa cadena de caracteres.
La función devuelve 0 si se completa sin errores, y -1 en caso contrario.  
  ![][13]  
   
   

**Nota:** Cuando compile programas tenga en cuenta que estos pueden necesitar la instalación de librerías para su compilación y ejecución.
 	
**Debian**
	
```
# apt-get install libcurl4-openssl-dev
$ gcc -o curl curl.c -lcurl
```

**CentOS**

```
# yum install libcurl-devel
$ gcc -o curl curl.c -lcurl
```
### Nota
El informe debe ser entregado en formato README.md y debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-workshop3 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe.  
## Referencias
* http://man7.org/linux/man-pages/man2/syscalls.2.html  
* https://jvns.ca/blog/2014/09/18/you-can-be-a-kernel-hacker/  
* http://avellano.usal.es/~labssoo/sesion2.htm  
* http://sopa.dis.ulpgc.es/ii-dso/leclinux/miscelaneos/llamadas/LEC_llamadas.pdf  
* http://sopa.dis.ulpgc.es/ii-dso/leclinux/fs/readwrite/LEC20_READWRITE.pdf  
* https://www.lifewire.com/linux-command-open-4091951  
*http://www.tyr.unlu.edu.ar/tyr/TYR-trab/satobigal/documentacion/beej/syscalls.html  


[1]: images/Taller3Strace1.PNG
[2]: images/Taller3StraceOpen.PNG
[3]: images/Taller3StraceRead.PNG
[4]: images/Taller3StraceWrite.PNG
[5]: images/Taller3StraceAccess.PNG
[6]: images/Taller3StraceGetDents.PNG
[7]: images/Taller3StraceCurl.PNG
[8]: images/Taller3Connect.PNG
[9]: images/Taller3Socket.PNG
[10]: images/Taller3Recvfrom.PNG
[11]: images/Taller3getpeername.PNG
[12]: images/Taller3sendto.PNG
[13]: images/Taller3getsockname.PNG
