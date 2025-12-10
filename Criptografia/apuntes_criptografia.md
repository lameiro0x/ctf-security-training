# **Criptografía – Apuntes para Obsidian**

- **Representación de datos**
  - ASCII → cada carácter tiene un valor numérico.
  - **Hexadecimal** → útil para representar bytes.
  - **Binario** → forma más básica de datos.
  - Conversión típica en CTF:
    - hex → ascii
    - base64 → texto
    - binario → decimal

- **Codificaciones**
  - **Base64**
    - Se reconoce por `=` al final.
    - Uso típico: ocultar datos simples, no cifrar.
  - **Base32 / Base58 / Base85**
    - Menos comunes pero aparecen en CTF.
    - Base58 evita caracteres ambiguos (0/O, 1/l).

- **ROT y cifrados clásicos**
  - **ROT13 / ROT-N**
    - Desplaza letras del alfabeto.
    - No segura → se revierte fácilmente.
  - **Vigenère**
    - Usa clave repetida.
    - Vulnerable a ataques de frecuencia si la clave es corta.

- **XOR**
  - Operación fundamental en criptografía.
  - Propiedades:
    - `A xor A = 0`
    - `A xor 0 = A`
    - Reversible: `C = P xor K` → `P = C xor K`
  - Usos:
    - Cifrados de flujo.
    - Ofuscación en malware.
  - Ataque típico:
    - Probar claves cortas.
    - Detectar patrones repetidos.

- **Hashes**
  - Funciones unidireccionales.
  - Propiedades:
    - **Preimage Resistance (PR)**
    - **Second Preimage (SPR)**
    - **Collision Resistance (CR)**
  - Hashes comunes:
    - **MD5, SHA1** → inseguros.
    - **SHA256** → seguro.
  - Usos:
    - Integridad.
    - Almacenar contraseñas (con **salt**).
  - Ataques:
    - Diccionario.
    - Fuerza bruta.
    - Rainbow tables.
    - Hashcat.

- **Hashcat**
  - Ataques:
    - **Brute-force**
    - **Dictionary**
    - **Mask attack**
    - **Hybrid**
  - Ejemplo:
    - `hashcat -m 0 hash.txt wordlist.txt`
  - Optimizado para GPU.

- **Criptografía simétrica**
  - Dos tipos:
    - **Bloque** (AES, DES).
    - **Flujo** (RC4).
  - Modos de operación:
    - **ECB** → inseguro, patrones visibles.
    - **CBC** → más seguro, usa IV.
    - **CTR / OFB** → modos de flujo basados en bloque.
  - AES:
    - Rápido y seguro.
    - Tamaños: 128/192/256 bits.

- **Ataques a ECB**
  - Si se cifra una imagen, se ve el patrón original.
  - Byte-at-a-time attack:
    - Controlando input se puede descubrir clave/secretos.

- **Criptografía asimétrica**
  - Usa **clave pública** y **privada**.
  - **RSA**
    - N = p*q
    - φ(N) = (p−1)(q−1)
    - Claves:
      - Pública: (e, N)
      - Privada: (d, N)
    - Cifrado:
      - C = M^e mod N
    - Descifrado:
      - M = C^d mod N

- **Ataques a RSA**
  - **Exponente e pequeño** → posible descifrado.
  - **N muy pequeño** → factorización rápida.
  - **p y q cercanos** → Fermat attack.
  - **N compartido entre usuarios** → ataque común en CTF.
  - Herramientas:
    - `rsactftool`
    - `factordb`

- **Conceptos clave para CTF**
  - Revisar siempre:
    - ¿Es codificación o cifrado?
    - ¿Hay patrones visibles?
    - ¿El archivo parece XOR?
    - ¿El hash es de un tipo crackeable?
  - Automatizar:
    - pruebas de decodificación masiva,
    - XOR con claves cortas,
    - ataques de hash.
