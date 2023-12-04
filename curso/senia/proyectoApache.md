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
* Paso 1. Modificar el sitio de senia.conf para que atienda al nombre www.seniaTIC.com
* Paso 2. Probar `curl www.seniaTIC.com` 
* Paso 3. Crear un nuevo directorio en /var/www/blogseniaTIC
* Paso 4. Seguir los pasos para crear el sitio WEB www.blogseniaTIC.com configurándolo para acceder por nombre
* Paso 5. Probar `curl www.blogseniaTIC.com`
* Paso 6. Crear un nuevo directorio en /var/www/intranetseniaTIC
* Paso 7. Seguir los pasos para crear el sitio WEB www.intranetseniaTIC.com configurándolo para acceder por nombre pero en el puerto 8080
* Paso 8. Probar `curl www.intranetseniaTIC.com:8080`

## Mapeo de URL
* Paso 1. Crear el direcotrio /var/www/blogseniaTIC/docs y añadirle un par de archivos.
* Paso 2. Configurar el sitio web para que cuando se acceda http://www.blogseniaTIC.com/docs se liste los archivos del directorio
* Paso 3. Añadir una redirección en la página oficial del IES desde la página de senia.html. Se accedera con www.seniaTIC.com/oficial
* Paso 4. Crear una página de error 404 personalizada para el sitio www.seniaTIC.com
* 