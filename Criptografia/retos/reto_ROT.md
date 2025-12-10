# ROT/César

El mensaje contenía caracteres únicamente alfabéticos, con estructura sospechosa de frase. Realicé un conteo de frecuencias:

    from collections import Counter
    Counter(ciphertext)

La letra más frecuente coincidía con 'E' en inglés, así que estimé el desplazamiento:

    shift = (ord(most_common_cipherchar) - ord('E')) % 26

Probé desplazamientos del 1 al 26:

    for s in range(26):
        print( ''.join(chr((ord(c)-65-s)%26 + 65) for c in ciphertext) )

El texto plano emergió claramente en el desplazamiento 19.  
Este ejercicio demuestra familiaridad con técnicas de cripto clásicas, análisis de frecuencia y scripting eficiente.
