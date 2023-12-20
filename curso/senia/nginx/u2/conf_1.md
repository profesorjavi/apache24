
# Principios de configuración

## Instalación

    # apt install nginx



## Ficheros de configuración
La configuración de **nginx** está incluida toda dentro de
`/etc/nginx`. Los ficheros y directorios
relevantes son los siguientes:

    /etc/nginx
       +--- conf.d/
       +--- modules-available/
       +--- modules-enabled/
       +--- nginx.conf
       +--- sites-available/
               +--- default
       +--- sites-enabled/
               +--- default -> ../sites-available/default
       +--- snippets/
               +--- fastcgi-php.conf
               +--- snakeoil.conf

cuyo significado podemos desglosar así:

 `nginx.conf`   
Es, en sí, el fichero de configuración. el resto de configuración
existente en este directorio se aplica, bien porque este fichero la
carga directamente, bien porque un fichero cargado por él, la carga a su
vez.

 `conf.d`   
Contiene ficheros con trozos de configuración que
 `nginx.conf`  siempre carga si su extensión es
 `*.conf` .

 `snippets`   
Es un directorio destinado a contener también ficheros con trozos de configuración, pero a diferencia de los contenidos en el directorio anterior, sólo se aplicarán si son expresamente citados dentro de otros ficheros de configuración. *Debian* trae dos configuraciones ya definidas que podemos aprovechar:

-    `fastcgi-php.conf`  para la configuración de
    PHP.

-    `snakeoil.conf`  que incluye las líneas
    necesarias para usar el certificado autofirmado del servidor.

Para aplicar una configuración incluida en este directorio debe utilizar
en la línea en que queramos que se aplique la directiva
 `include` :

    include snippets/snakeoil.conf;

 `sites-available`   
Un mismo servidor web puede alojar distintos sitios *web* mediante el
mecanismo dedominios
virtuales . Este directorio está destinado a albergar la
definición de todos los *dominios virtuales*. Ya se verá más adelante
esto con mayor profundidad.

 `sites-enabled`   
Contiene los *dominios virtuales* habilitados. Los que no se encuentren
aquí, no forman parte de la configuración y por tanto es como si no se
hubieran definido. Para habilitar dominios basta, simplemente, con hacer
enlaces simbólicos que apuntes a las definiciones incluidas en
 `sites-available` .

 `modules-available`   
Para cargar dinámicante un módulo la documentación de **nginx** indica
que debe incluirse en la configuración una línea así:

    load_module modules/nonbre_del_modulo.so

Para simplificarlo, *Debian* incluye un fichero de configuración para
cada módulo con su línea  `load_module` 
correspondiente, con lo que cargar el fichero de configuración, implica
cargar el módulo. Como *Debian* prefiere alojar para sus paquetes estos
ficheros en
 `/usr/share/nginx/modules-available/` , en este
directorio sólo cabría crear ficheros de configuración para módulos que
hubiéramos compilado nosotros mismos.

 `modules-enabled`   
Son enlaces simbólicos a los módulos que realmente se cargan al arrancar
**nginx**. Cada vez que se instala en el sistema un paquete
correspondiente a un módulo, *Debian* crea el enlace simbólico que
apunta al fichero de configuración correspondiente en
 `/usr/share/nginx/modules-available` . Por
supuesto, si algún módulo no lo usamos, podemos aligerar **nginx**
eliminando el enlace (y reiniciando el servidor, claro).

En la práctica:

-    `nginx.conf`  se modifica si se desea
    alterar alguna directiva citada en él.

-   Se escribe algún fichero  `conf.d` , si se
    desea añadir configuración predeterminada a la labor HTTP de
    **nginx**.

-   Nuestra tarea se basa fundamentalmente en crear sitios cuya
    configuración se creará en un fichero dentro de
     `sites-available`  que luego se enlazará
    desde  `sites-enabled` .

-   Si algún trozo de configuración es recurrente en varios sitios, o
    bien deseamos apartarla para tenerla mejor controlada y organizada,
    podemos crear un *snippet* para ella e incluir tal *snippet* con la
    directiva  `include`  en el fichero de
    configuración del sitio correspondiente.


