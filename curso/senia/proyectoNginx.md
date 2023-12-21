# Proyecto Nginx

## Entorno
Trabajaremos en una máquina ubuntu server con IP estática. Deshabilitar el servicio de apache en el caso que utilicemos la misma página.

## Introducción a los servidores WEB
* Paso 1. Instalación de nginx 
* Paso 2. Crear un directorio de trabajo /var/www/nginx/newsenia. 
* Paso 3. Crear una página WEB personal accesible por el puerto 8099. http://IP_nginx:8099
* Paso 4. Crear un nuevo sitio en /var/www/nginx/newblogsenia
* Paso 5. Configurar nginx para hacer accesible los dos sitios atendiendo a sus nombres http://www.newseniatic.com y http://www.newblogseniatic.com 
* Paso 6 . Configurar la seguridad de los sitios. https://www.newseniatic.com y https://www.newblogseniatic.com
  -----------------
* Paso 7. Configurar un nuevo servidor NGINX para realizar un balanceo de carga sobre dos servidores con la configuración anterior