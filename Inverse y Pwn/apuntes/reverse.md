# Ingeniería Inversa (Reverse Engineering)

La ingeniería inversa permite comprender el comportamiento interno de un programa analizando su estructura, instrucciones y lógica. Su objetivo es reconstruir cómo funciona una pieza de software sin necesidad de disponer del código fuente. En CTF es fundamental para entender validadores, desencriptar entradas, reconstruir flags o alterar programas.

## 1. Binarios y estructura interna

Los ejecutables contienen varias secciones:

- `.text`: código ejecutable.
- `.rodata`: cadenas de solo lectura.
- `.data`: variables globales inicializadas.
- `.bss`: variables sin inicializar.

Herramientas básicas:

- `strings`: revela texto incrustado.
- `file`: muestra arquitectura y formato.
- `ltrace`, `strace`: muestran llamadas a librerías y sistema.

## 2. Análisis Estático

### Herramientas comunes

- **Ghidra**: descompilación avanzada.
- **IDA Free**: interfaz sólida y reconocida.
- **Radare2 / Cutter**: potente CLI y GUI.

Objetivos del análisis:

- Identificar funciones críticas.
- Seguir el flujo de ejecución.
- Entender algoritmos.
- Hallar comparaciones clave.
- Detectar cifrados simples.

### Reconociendo validadores

Muchos crackmes y retos tienen funciones como:

- bucles que recorren cada carácter del input,
- operaciones matemáticas sobre ASCII,
- XOR con constantes,
- comparaciones con arrays ocultos.

Comprender estos patrones permite reconstruir la clave original.

## 3. Análisis Dinámico

Usamos GDB para observar el comportamiento en tiempo real.

Técnicas:

- Añadir breakpoints antes de comparaciones.
- Examinar registros (`info registers`).
- Observar memoria (`x/20x`).
- Trazar el flujo paso a paso (`si`, `ni`).

Ejemplo: detener justo antes de `strcmp` permite inspeccionar qué valor espera el programa.

## 4. Reversing de algoritmos

La mayoría de retos incluyen algoritmos simples:

- XOR con clave fija.
- ROT/shift.
- sumas acumuladas.
- permutaciones de bytes.

Analizando estas funciones, podemos invertirlas para obtener la flag.

Ejemplo típico:

```
for(i=0; i<len; i++){
    output[i] = input[i] ^ 0x37;
}
```

Inversión: aplicar XOR con el mismo valor.

## 5. Patching de binarios

Permite alterar el programa para:

- saltarse validaciones,
- evitar bloques de código,
- modificar comparaciones.

En Radare2:

```
r2 -w binario
s <direccion>
wa nop
```

Cambiar un `jne` por `je` es un bypass clásico.

En Ghidra:

- Localizar instrucción.
- Editar bytes.
- Exportar binario parcheado.

## 6. Anti-debugging

Los binarios más elaborados intentan detectar depuradores.

Métodos:

- uso de `ptrace`,
- comprobación de tiempo (sleep inconsistentes),
- lectura de `/proc/self/status`.

Soluciones:

- Parchear la llamada problemática.
- Engañar al programa mediante cambios de memoria.
- Modificar el binario para anular comprobaciones.

## 7. Ingeniería inversa de autenticadores

Patrones comunes:

- funciones que generan buffers internos,
- comparaciones carácter a carácter,
- cálculos acumulativos.

Proceso general:

1. Localizar función principal del validador.
2. Seguir cómo procesa el input.
3. Reconstruir la lógica inversa.
4. Generar el valor correcto o parchear el binario.

## 8. Packed binaries

Herramientas como UPX comprimen binarios.

Detección:

- `file` muestra "packed".
- Secciones anómalas.

Desempaquetado:

```
upx -d binario
```

## 9. Metodología general para reversing en CTF

1. Ejecutar binario con inputs sencillos.
2. Examinar cadenas internas.
3. Abrir en Ghidra/IDA.
4. Renombrar funciones y variables.
5. Seguir validadores.
6. Ejecutar con GDB si es necesario.
7. Invertir algoritmo o parchear binario.
8. Obtener flag final.