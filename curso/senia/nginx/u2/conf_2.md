
# Configuración básica

Para ilustrar la configuración, tomemos una muy básica que sirve
contenido estático y analicémosla. En principio, editemos
 `/etc/nginx/sites-available/default`  y
dejémoslo con este contenido:

    server {
       listen  80;
       server_name _;

       root /srv/www;
       try_files $uri $uri/ =404;
    }

Cada directiva
`server` nos permite configurar un *dominio
virtual* y es lo que hemos hecho. Por ahora:

-   sólo nos preocuparemos de definir una que escucha por el puerto
    **80**
   `listen`
-   en el que no preocupa cuál sea el nombre (la cabecera
     `Host` , de ahí el valor de ``server_name``

-   en que todos los ficheros compartidos a través del servidor se
    sitúan bajo
     `/srv/www` , merced a la directiva
    ``root``

-   la directiva ``try_files`` le indica al servidor qué recursos debe intentar devolver al realizarse una petición. En la     configuración propuesta, primero se intenta devolver el propio recurso; si éste no es un fichero, debe comprobarse si es un directorio y en tal caso tratarlo como tal; y, si tampoco es un directorio, devolver un error **404**. Una alternativa podría haber  sido:

        try_files $uri $uri/ /

    en este caso, cuando el recurso no se encontró como fichero ni como
    directorio, no se genera un error, sino que se devuelve la página
    principal. Por tanto en esta configuración, jamás se devolverá al
    cliente un error de recurso inexistente.

Nota

Si miramos dónde se inserta este fichero dentro de
 `nginx.conf` , comprobaremos que lo hace en un
bloque  `http` . O sea que un bloque
 `server`  se inserta dentro de un bloque
 `http` . Esto es importante tenerlo en cuenta
porque la configuración de **nginx** se estructura en contextos: el
global, el  `http` , el
 `server` , etc.\` que se anidan unos dentro de
otros. Cuando consultemos la documentación oficial, podremos comprobar
que para cada directiva se define en qué contextos es válida.

Para rematar la configuración, debemos probarla. Para ello, podemos
escribir una página principal simple en
 `/srv/www/index.html` :

    <!DOCTYPE html>
    <html lang="es">
       <title>Página principal</title>
       <meta charset="utf-8">
       <h1>Página principal</h1>
    </html>

Y, por último, usar un navegador para acceder a lá página usando, por
ejemplo, la propia *IP* de la máquina en la que hayamos instalado el
servidor.

Sobre esta configuración básica podemos añadir algunas modificaciones:

-   La directiva  `listen`  puede incluir
    explicitamente la interfaz en la que queremos que escuche. Por
    ejemplo:

    >     listen localhost:80;

    ya que cuando se indica únicamente el puerto se sobreentiende que
    escuchará en todas las disponibles.

-   Esta misma directiva puede usarse repetidamente para esscuchar en
    múltiples puertos o múltiples interfaces, de modo que:

    >     listen  80;
    >     listen  8080;

    hará que el servidor escuche tanto en el puerto **80** como en el
    **8080**.

-   Si en el navegador hemos usado esta dirección (cambiése la *ip* por
    la que toque):

        http://192.168.1.11

    para pedir la página, deberíamos ser conscientes de lo que ha
    ocurrido. No hemos incluido ruta alguna para especificar el recurso,
    así que **nginx** no encuentra el «no recurso» (por llamarlo de
    alguna forma). Siguiendo la directiva
     `try_files`  añade una barra y entonces
    prueba a pedir el directorio raíz e, inexplicablemente, se devuelve
    el recurso  `/index.html` . Obviamente el
    suceso tiene explicación. Cuando **nginx** se encuentra con que se
    le pide un directorio, revisa una directiva llamada
    <a href="http://nginx.org/en/docs/http/ngx_http_index_module.html#index"
    class="reference external">index</a> para saber cuáles son los
    fichero que se consideran de índice y que se mostrarán. El valor
    predeterminado de esta directiva es:

    >     index index.html;

    así que esa es la razón por la que se sirve la página simple que
    preparamos. La directiva, por supuesto, admite que le cambiemos el
    valor o incluso que pongamos varios para que **nginx** pruebe su
    existencia ordenadamente. Por ejemplo:

        index index.html index.htm;

    probaría si existen  `index.html`  y, si no
    es así, prueba  `index.htm` . Ahora bien,
    ¿qué ocurre si no existen tales ficheros de índice? En ese caso,
    **nginx** mostrará el contenido del directorio, pero sólo si se
    añade la directiva <a
    href="http://nginx.org/en/docs/http/ngx_http_autoindex_module.html#autoindex"
    class="reference external">autoindex</a>:

        autoindex on;
        autoindex_exact_size off;  # Muestra unidades k, m, M, etc. y no bytes siempre

    que por defecto tiene valor *off*.

La configuración, además, admite algunos añadidos interesantes:

-   **nginx** registra los accesos y los accesos fallidos dentro de los
    ficheros  `access.log`  y
     `error.log`  del directorio
     `/var/log/nginx` , ya que así de define
    dentro  `nginx.conf`  mediante las directivas
    <a
    href="http://nginx.org/en/docs/http/ngx_http_log_module.html#access_log"
    class="reference external">access_log</a> y <a
    href="http://nginx.org/en/docs/http/ngx_http_log_module.html#error_log"
    class="reference external">error_log</a>. A este respecto es
    importante tener claras dos cosas:

    -   Es común definir distintos ficheros de registro para distintos
        dominios virtuales, así que podríamos incluir dentro del bloque
         `server`  estas dos directivas con
        ficheros alternativos.

    -   Si en un mismo contexto se definen varios
         `access_log`  (o
         `error_log` ), se escribirán registros
        en los ficheros que definen todas las directivas. Sin embargo,
        cuando en un contexto inferior se define la directiva, ésta
        sobreescribe las definiciones que pudieran haberse hecho en un
        contexto superior.

    -   Estos ficheros se escriben directamente y no pasan por el gestor
        de registros (**systemd** o **rsyslog**, según el caso). Para
        propiciar que **nginx** use el gestor, vea más adelante
        cómo.

-   **nginx** tiene unas páginas predefinidas para informar al cliente
    de los errores (**403**, **404**, **502**, etc.). Podemos, no
    obtante, crear páginas personalizadas y hacer que **nginx** las
    envíe al cliente en vez de las predefinidas:

        error_page 404                      /errors/404.html;
        error_page 500 502 503 504          /errors/50X.html;





