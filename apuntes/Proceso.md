# Proceso

Es una instancia de un programa en ejecución

Cada proceso tiene un Process ID (PID) que es único, una tabla de file descriptors (fds) y memoria: code segment, data segment, stack y heap.

Cerrar un file descriptor en un proceso no lo cierra en el otro.

## Resumen de funciones de procesos

- fork(): crea un nuevo proceso identico al actual excepto por el pid, y devuelve su pid. El hijo hereda los fds del proceso padre.
- wait(): el proceso actual se queda esperando a que uno de sus hijos muera, devuelve el id del proceso hijo que murio.  
- getpid(): devuelve el id del proceso actual.   
- getppid(): devuelve el pid del padre del proceso actual
- exec(): carga y ejecuta un nuevo programa que pisa al actual, presenta muchas variantes. ver https://dashdash.io/3/exec 
- exit(): termina el proceso y devuelve un exit code al SO
- kill(pid): termina el proceso identificado por pid
- pipe(): crea un canal de comunicacion UNIDIRECCIONAL entre dos procesos, con un extremo de escritura y otro de lectura. Hay que pasarle un array de 2 enteros. el index 0 es de lectura, y el index 1 es de escritura.
![pipe](./imagenes/pipe.png)  
- dup(): duplica un fd, creando una copia exacta.
- dup2(): lo mismo que dup pero mas facil de manejar porque tiene dos parametros

## Estructura de un proceso
El proceso tiene un contexto, puede pensarse como el estado completo del proceso conformado por:
- Espacio de direcciones de memoria o `address space`
- Registros del procesador
- Estructuras del kernel

### Trampoline y trapframe:
- Trampoline: codigo para guardar los registros y finalmente llama al kernel.
    - Guarda todos los registros en el traframe
    - Cambia el registro satp, cambiando la tabla de paginas y el address space
    - indica en el registro sp apuntando al kstack del proc actual
    - invoca usertrap() y salta al kernel

- Trapframe: lugar en donde se guardan los registros
