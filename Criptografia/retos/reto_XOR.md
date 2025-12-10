# XOR Cipher

El archivo cifrado tenía el mismo tamaño que el mensaje original, lo que sugería un XOR byte a byte. Comprobé el patrón repitiendo bytes conocidos:

    xordump = [a ^ b for (a,b) in zip(cipher, cipher[1:])]

El análisis mostró que la clave era de 1 byte (patrón constante). Para recuperar la clave usé:

    key = cipher[0] ^ ord('F')   # suponiendo que el mensaje empieza por "FLAG{"

Una vez identificada la clave, descifré:

    plaintext = bytes([c ^ key for c in cipher])

El resultado fue un mensaje ASCII legible.  
Este ejercicio demuestra conocimiento en la estructura de XOR, análisis de patrones y scripting rápido en Python para descifrado automático.
