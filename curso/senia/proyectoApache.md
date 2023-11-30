# Proyecto Apache
## Entorno
Trabajaremos en una máquina ubuntu server con IP estática.

## Introducción a los servidores WEB
* Paso 1. Crear una página WEB que simule una empresa con 2 imagenes que estarán en el directo /var/www/html/img/ la página se llamará  /var/www/html/senia.html (más adelante accederemos con www.seniatic.com)
* Paso 2. Copia el archivo de configuración base 000-default.conf y llamarlo senia.conf
* Paso 3. Habilitar vuestro sitio WEB y deshabilitar el sitio por defecto. Utiliza para ello los comandos `a2ensite` y `a2dissite`.
* Paso 4. Reiniciar y probar. http://ip/senia.html
* Paso 5. Estudia la directiva DirectoryIndex y modifica tu configuración para poder acceder sin indicar la página. Probar con http://ip/
* Paso 6. Describe que ves en los archivos `/var/log/apache2/error.log` y  `/var/log/apache2/access.log` 

## Virtual hosting en Apache 2.4
Crear dos nuevos sitio web, el primero, www.blogseniaTIC.com y el segungo www.intranetseniatic.com. Además de estos ya creamos en /var/www/html el sitio WEB que ahora se corresponderá con www.seniaTIC.com
* Paso 1. Crear un nuevo directorio en /var/www/blogseniaTIC
* Paso 2. Seguir los pasos para crear el sitio WEB www.blogseniaTIC.com configurándolo para acceder por nombre
* Paso 3. Crear un nuevo directorio en /var/www/intranetseniaTIC
* Paso 4. Seguir los pasos para crear el sitio WEB www.intranetseniaTIC.com configurándolo para acceder por nombre pero en el puerto 8080