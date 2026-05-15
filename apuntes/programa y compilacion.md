# Programa
### Edicion
Se crean y editan los archivos `programita.c` por el programador
### Compilacion
Transforma el codigo del lenguaje a codigo assembler.
### Ensamblaje
El ensamblador traduce el archivo de assembly `.s` a verdadero lenguaje de maquina `.o` que pertenece al formato ELF
### Linker y ELF
Concatena de cada archivo `.o` los diferentes segmentos.

el formato ejecutable `ELF` (Executable and Linking Format) posee toda la informacion para construir un proceso en memoria

- Instrucciones del lenguaje de maquina
- Direccion del punto de entrada al programa
- Datos (data segment)
- Simbolos y tablas de realocacion
- Bibliotecas Compartidas o Dinamicas
- Otra informacion
