- **OSINT (Open Source Intelligence)**
  - Recolección de información pública accesible.
  - **Principio clave:** nada de intrusión; solo análisis de fuentes abiertas.
  - En CTF se usa para:
    - buscar datos filtrados,
    - encontrar información oculta,
    - rastrear dominios y máquinas.

- **Fuentes principales**
  - Motores de búsqueda: Google, Bing, DuckDuckGo.
  - Archivos históricos: Wayback Machine.
  - Metadatos: imágenes, PDFs.
  - Redes sociales: LinkedIn, Instagram, Twitter/X.
  - Registros técnicos: WHOIS, DNS, Shodan, Censys.

- **Dorks útiles**
  - `site:dominio.com` limitar búsqueda.
  - `filetype:pdf` documentos.
  - `intitle:index.of` directorios expuestos.
  - `inurl:login` páginas de acceso.

- **WHOIS / DNS**
  - WHOIS → info del dueño del dominio.
  - DNS:
    - `dig A`,
    - `dig MX`,
    - `dig ANY`,
  - Identifica IPs, servidores, subdominios.

- **Shodan / Censys**
  - Motores que indexan servicios expuestos.
  - Información útil:
    - **banners**,
    - **versiones vulnerables**,
    - **puertos abiertos**.
  - Buscar dispositivos:
    - cámaras,
    - IoT,
    - routers.

- **Metadatos**
  - Extraer con:
    - `exiftool`,
    - `strings archivo`.
  - Buscar:
    - autores,
    - fechas,
    - versiones de software,
    - rutas internas.

- **Huellas en redes sociales**
  - Análisis de:
    - fotos,
    - ubicaciones,
    - amistades,
    - dispositivos reflejados.

- **Geolocalización (IMINT/GEOINT)**
  - Comparación con Google Maps / Earth.
  - Señales:
    - sombras,
    - vegetación,
    - formas de edificios.

- **Buenas prácticas**
  - Documentar todo.
  - Verificar fuentes.
  - No exponer información sensible.
