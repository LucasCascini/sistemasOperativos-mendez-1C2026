##### Hay varias cosas, sobretodo de la clase filesystem II, de las que no tomé apunte porque no salen en el parcial, y si no salen en el parcial no me importan 

# Filesystem
Un filesystem puede pensarse como un sistema de nombres, agrega nombres a una porcion de bytes del disco para que puedan accederse y modificarse por el usuario.  
  
Los programas ven a los archivos como flujos de bytes, una secuencia de bytes y nada más.  

Los archivos tienen asociados permisos  
3 permisos: read, write, execute  
3 clases de usuarios: owner, grupo, todos los demás  

Un archivo es un dentry apuntando a un inodo. 

## Dentry (Directory Entry)
Representa la relacion entre el nombre de un archivo y su `inodo` correspondiente. 
**Los directorios son listas de dentries**  
Su funcion principal es ayudar a resolver nombres de archivos y rutas. Cuando el filesystem busca un archivo, utiliza las dentries para traducir la ruta hasta el inodo correspondiente.  
Un dentry solo apunta a inodos. Pero un dentry puede apuntar a un inodo tipo directorio, que apunta a datos y los datos contienen una lista de dentries, asi se logra el sistema jerarquico.

## Inodo
Un inodo contiene metadatos del archivo, pero no el contenido en sí del archivo. Siempre apunta a datos.
Incluye:
- Tamaño del archivo
- ID del propietario y grupo
- Permisos del archivo
- Tiempos de acceso, modificacion y cambio del inodo
- Numero de enlaces duros al archivo
- Punteros a bloques de disco donde se almacena el contenido real del archivo 

### Tipos de Inodo
Los tipos de inodo en Linux nos dan idea de la variedad de objetos que podemos anclar al fs

- S_IFSOCK -> socket
- S_IFLINK -> link simbolico
- S_IFREG -> archivo regular
- S_IFBLK -> dispositivo de bloque
- S_IFDIR -> directorio
- S_IFCHR -> dispositivo de caracteres
- S_IFIFO -> FIFO o pipe

## VIRTUAL FILESYSTEM

Cada filesystem tiene que proveer implementaciones para una especie de interfaz:
- implementaciones de write, read, etc
- estructuras que modelan el filesystem, denominados objetos:
    - el superbloque, representa a un sistema de archivos
    - el inodo, que representa a un archivo
    - el dentry, que representa una entrada de directorio, que es un componente simple de un path
    - el file que representa a un archivo asociado a un determinado proceso

## DISPOSITIVOS
Se representan como archivos especiales de tipo DEVICE. Se usan las mismas syscalls que para operar con archivos normales. El VFS interactua con una capa de drivers

#### Tipos
- Dispositivo de bloque: ej discos duros, usb, etc.
- Dispositivo de caracteres: ej la terminal

Los dispositivos de I/O tienen un par de numeros asociados: maior number y minor number

Major number: identifica al driver
Minor number: cada dispositivo especifico dentro del grupo de dispositivos gestionado por el mismo number

Como conectan los dispositivos al fs

Los dispositivos no son mas que archivos especiales de tipo DEVICE que se crean usando la syscall mknod

sudo mknod /dev/ttyS1 c 4 65

/dev/ttyS1 es el nombre del archivo
C indica que es un dispositivo de caracteres, no bloques
4 es el major
65 es el minor

### Links
#### Soft Links o Symbolic Link
Es un archivo especial que su contenido es el path al que esta apuntando. No apunta al inodo ni a informacion.

#### Hard Links
Puede haber más de un Dentry apuntando a un mismo inodo, esto se llama Hard link. Si se elimina un hard link, no se cierra el archivo, porque al igual que la open file table, tiene un contador de referencias abiertas.  
**No se puede hacer un hard link hacia otra particion o disco**  

##### La syscall unlink()
La syscall unlink() elimina el nombre de un archivo y, si fue el ultimo, se elimina de la open file table tambien.

## Open file table
Cada archivo al que se hace open, agrega una entrada en la Open file table, con el offset que lleva por writes, los flags de dicho archivo y un puntero al inodo de dicho archivo.
No se elimina una entrada de esta tabla hasta que se haga close de dicho archivo desde todas sus referencias. Tambien lleva la cuenta de cuantos file descriptors tienen abierto el archivo

### Tipos de kernel
#### Monolitico
El nucleo y la mayor parte del SO esta en kernel space. Esto es muy rapido en cuanto a acceso y rendimiento, pero si un driver tiene un bug, puede colapsar todo el sistema, ya que todo corre en ring 0.
> ej linux, unix

#### Microkernel
Minimiza la funcionalidad del kernel, reduciendolo a:
- Comunicacion entre procesos
- Scheduling
- Gestion de interrupciones y memoria basica

Todo lo demas esta en user space  
Mejora la estabilidad y seguridad pero tiene un costo en rendimiento por la sobrecarga de comunicacion entre componentes

ej: MINIX3, QNX, GNU Hurd  

#### Kernel Hibrido

- Se mantiene un solo espacio de kernel como en el modelo monolitico
- Se modulariza el diseño y algunos componentes (como drivers o partes del filesystem) pueden moverse o tratarse de forma mas aislada  

El objetivo es mantener rendimiento mientras se mejora algo la modularidad y robustez

Ej: windows XP, 10, macOS