- **Esteganografía**
  - Ocultar información dentro de archivos.
  - Muy común en CTFs de OSINT/Forense.

- **Primeros pasos**
  - Identificar archivo:
    - `file archivo`,
    - revisar cabeceras hex.
  - Buscar contenido oculto:
    - `strings`,
    - patrones extraños.

- **Herramientas**
  - **steghide**
    - `steghide extract -sf imagen.jpg`
  - **binwalk**
    - `binwalk -e archivo`
    - extrae archivos embebidos.
  - **zsteg**
    - ideal para PNG.
  - **exiftool**
    - metadatos.

- **Técnicas frecuentes**
  - Datos ocultos en LSB.
  - Archivos ZIP añadidos al final (EOF).
  - Texto en canales de color.
  - Manipulación de bits.

- **Análisis manual**
  - Revisar tamaño anómalo.
  - Comparar histogramas.
  - Visualizar canales RGB por separado.

- **Tipos de archivos más comunes**
  - **PNG/BMP** → muy usados para LSB.
  - **JPG** → menos efectivo por compresión con pérdidas.

- **Flujo recomendado**
  - `file`
  - `exiftool`
  - `strings`
  - `binwalk -e`
  - `zsteg`
  - probar extracción con steghide.
