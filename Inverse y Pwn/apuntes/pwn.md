# Explotación Binaria (Pwn)

La explotación binaria es una de las áreas más técnicas en seguridad informática. Consiste en analizar cómo interactúa un programa con la memoria, identificar vulnerabilidades y forzar comportamientos no previstos por el desarrollador. En un CTF, esto se traduce en obtener ejecución arbitraria de código, leer memoria, desviar el flujo de control o acceder a funciones ocultas.

## 1. Arquitectura y Memoria del Proceso

Para explotar un binario es imprescindible comprender cómo se organiza la memoria.

Un proceso típico contiene:

- **text**: código ejecutable.
- **data**: variables globales inicializadas.
- **bss**: variables globales sin inicializar.
- **heap**: memoria dinámica gestionada por `malloc`, `free`.
- **stack**: donde se almacenan variables locales, direcciones de retorno y frames de funciones.

El *stack* es fundamental en las primeras fases del aprendizaje de explotación. Funciona como una pila LIFO, donde una función guarda su `RBP` (base pointer) y su `RIP` (dirección de retorno). Si se puede sobrescribir parte del stack, se puede alterar el flujo del programa.

### Registros importantes

- **RIP**: instrucción actual; si lo controlamos, controlamos el programa.
- **RSP**: puntero de pila.
- **RBP**: base pointer usado para referencias internas.
- **RDI, RSI, RDX, RCX, R8, R9**: registros donde se pasan los argumentos.

El calling convention en Linux x86_64 usa estos registros para los parámetros, lo cual es clave para ataques ret2win o ROP.

## 2. Buffer Overflow – Concepto Fundamental

Un buffer overflow ocurre cuando una función copia más datos en un buffer del tamaño que debería. Las funciones peligrosas incluyen `gets`, `strcpy`, `scanf("%s")`, `sprintf`.

Si un buffer local tiene 32 bytes pero copiamos 200, la memoria más allá del buffer será sobrescrita. Esto incluye `RBP` y finalmente `RIP`.

### Hallando el offset al RIP

Existen técnicas para encontrar cuántos bytes necesitamos escribir antes de sobrescribir el puntero de retorno:

- patrones generados con herramientas (“`cyclic`” de pwntools).
- inspección en GDB tras un crash.
- comandos como `pattern_offset`.

### Construyendo un ret2win

Muchos binarios de CTF contienen una función que imprime la flag. La idea es sobrescribir el RIP para que apunte a esta función.

Payload típico:

```
padding hasta RIP + dirección de win()
```

## 3. Protección: Stack Canary

Los canarios se colocan entre el buffer y el puntero de retorno. Si el overflow sobrescribe el canario, el programa aborta.

Bypass típicos:

- Leak mediante format strings.
- Leak por vulnerabilidad de lectura.
- Leer memoria usando funciones inseguras.

## 4. NX (No-eXecute) y ROP

NX evita ejecutar código en la pila. Para sortearlo se usa **ROP (Return Oriented Programming)**, es decir, gadgets ya presentes en el binario.

Gadget común:

```
pop rdi ; ret
```

Permite pasar argumentos a funciones como `system("/bin/sh")`.

## 5. ASLR y PIE

ASLR randomiza direcciones de memoria. PIE randomiza también el segmento de código.

Cómo saltarlo:

- Obtener leaks desde printf.
- Leer la GOT.
- Derivar offsets de libc.

## 6. ret2libc

Consiste en llamar directamente a funciones de libc.

Pasos:

1. Obtener leak de `puts()`.
2. Calcular base de libc.
3. Crear payload ROP que llame a `system("/bin/sh")`.

## 7. Format String Vulnerabilities

Cuando el usuario controla el formato de `printf`, puede:

- Leer memoria con `%p`.
- Leer cadenas con `%s`.
- Escribir memoria con `%n`.

Es común sobrescribir la GOT de `puts` o `exit`.

## 8. Heap Exploitation (Introducción)

Conceptos:

- fastbins  
- tcache  
- double free  
- use after free  

Permite fabricar primitivas de lectura y escritura arbitrarias.

## 9. PwnTools

Framework esencial para automatizar exploits.

Funciones:

- `cyclic`
- `ELF()`
- `ROP()`
- `process()`, `remote()`
- `p64()`

## 10. GDB y debugging

Comandos útiles:

- `info registers`
- `x/40x $rsp`
- `break *0x...`
- `si`, `ni`

Extensiones:

- pwndbg
- peda
- gef

## 11. Metodología general para Pwn

1. Ejecutar `checksec`.
2. Analizar protecciones.
3. Buscar vulnerabilidad.
4. Encontrar offset.
5. Preparar payload.
6. Automatizar en pwntools.
7. Obtener shell o flag.
