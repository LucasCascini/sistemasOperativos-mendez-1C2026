## ¿Qué es?
> Es la capa de software que maneja los recursos de una computadora. Para lograr esto y facilitarlo, lo hace mediante virtualizacion.

## ¿Qué hace? 
> Provee servicios a los programas de usuario mediante una interfaz

# El kernel
Es la barrera entre las aplicaciones de usuario y el hardware. Es un programa especial que proporciona servicios a los programas (procesos), llamados system calls (Syscalls).

## File Descriptors

Cuando un programa abre un archivo, el sistema operativo le asigna un numero entero llamado file descriptor, que se usa para leer, escribir o cerrar el archivo

> int fd = open("salida.txt", O_WRONLY | O_CREAT, 0644);

Por convención, 
0 = standard input
1 = standard output
2 = standard error