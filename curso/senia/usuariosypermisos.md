
# Usuarios y permisos de los sevicios WEB

## Usuario www-data
El servidor web Apache inicia como usuario root en sistemas Linux para acceder a puertos por debajo de 1000, como el puerto 80 o 443 para HTTP o HTTPS.

Al arrancar, Apache crea procesos hijos con un usuario y grupo más seguro (www-data) para manejar solicitudes y servir páginas. 

El proceso inicial, como root, no atiende solicitudes directas por razones de seguridad. Los procesos hijos gestionan las solicitudes bajo el usuario www-data, limitando el impacto de posibles vulnerabilidades en scripts CGI.

Para verificar, puedes usar el comando `ps -aux`.
```sh
javier@javier:~$ ps -aux | grep apache2
root        1832  0.0  1.0 204884 21856 ?        Ss   09:36   0:00 /usr/sbin/apache2 -k start
www-data    1833  0.0  0.4 205416  9852 ?        S    09:36   0:00 /usr/sbin/apache2 -k start
www-data    1834  0.0  0.4 205416  9852 ?        S    09:36   0:00 /usr/sbin/apache2 -k start
www-data    1835  0.0  0.5 205456 11288 ?        S    09:36   0:00 /usr/sbin/apache2 -k start
```

En apache depende la distribución puede aparecer esta configuración en sitios distintos puedes probar con `grep -r "www-data" /etc/apache2/`

En la distribución que utilizamos en las prácticas.
```sh
javier@javier:~$ apache2 -v
Server version: Apache/2.4.52 (Ubuntu)
Server built:   2023-10-26T13:44:4

javier@javier:~$ sudo cat /etc/apache2/envvars | grep www-data
export APACHE_RUN_USER=www-data
export APACHE_RUN_GROUP=www-data

javier@javier:~$ sudo cat  /etc/apache2/apache2.conf | grep APACHE_RUN_USER
User ${APACHE_RUN_USER}

```

## Configuración del usuario
Por defecto, una vez instalado el servidor web apache, el dueño del grupo y el directorio por defecto pertenecen a root. 
Podemos cambiar el dueño del directorio y el grupo, al usuario por defecto de apache, llamado: www-data.

    chown -R www-data:www-data /var/www/html

Y realizar esta acción para los distintos sitios web que se creen.

## Configuración de las carpetas
**Las carpeta** de los sitios WEB deberían tener el permiso 755 (nunca el permiso 777). Con el permiso 755, el propietario tiene todos los privilegios (lectura, escritura y ejecución), el grupo y el resto de usuarios solo tendrán permisos de lectura y de ejecución en la misma carpeta.

    drwxr-xr-x

**Los ficheros** de los sitios WEB debería de tener los permisos 644

    -rw-r--r--


## Referencias.
https://usuariodebian.blogspot.com/2020/09/apache-permisos-usuario-y-grupo-www-data.html