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


[1]: images/Taller3Strace1.PNG
[2]: images/Taller3StraceOpen.PNG
[3]: images/Taller3StraceRead.PNG
[4]: images/Taller3StraceWrite.PNG
[5]: images/Taller3StraceAccess.PNG
[6]: images/Taller3StraceGetDents.PNG
