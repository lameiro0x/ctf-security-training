- **Forense Digital (orientado a CTF)**
  - Análisis de datos estáticos.
  - Objetivo: recuperar evidencias, extraer archivos, reconstruir actividad.

- **Primeros pasos**
  - Identificar tipo de archivo → `file`.
  - Extraer cadenas → `strings archivo`.
  - Representación hex → `xxd`, `hexdump -C`.

- **Carving**
  - Recuperación de archivos embebidos.
  - Herramientas:
    - `binwalk -e`,
    - `foremost`,
    - `scalpel`.

- **Análisis de logs**
  - Buscar:
    - accesos,
    - cambios de permisos,
    - comandos ejecutados.
  - Herramientas:
    - `journalctl`,
    - `last`,
    - `.bash_history`.

- **Memoria (niveles avanzados)**
  - Extracción de:
    - procesos,
    - sockets,
    - strings residentes.
  - Herramientas:
    - Volatility.

- **Red**
  - Analizar PCAP con:
    - Wireshark,
    - tshark.
  - Buscar:
    - credenciales,
    - ficheros transferidos,
    - sesiones HTTP,
    - tráfico sospechoso.

- **Buenas prácticas**
  - Trabajar con copias.
  - Verificar integridad (hashes).
  - Documentar todos los pasos.
  - No modificar la evidencia original.
