# Location

Ya se ha discutido que a grandes rasgos una URL está compuesta por
``maquina/ruta``
u cómo **nginx** selecciona el dominio virtual adecuado dependiendo de
la máquina. Sin embargo, ya elegido este, **nginx**
permite también alterar el comportamiento dependiendo de cuál sea la
ruta. Para ello existe la directiva <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a>. Por ejemplo:

    server {
       listen 80;
       server_name  _;

       root /srv/www;

       location /favicon.ico {
          log_not_found off;
          access_log off;
          return 410;
       }
    }

En este caso, cuando un navegador pide el recurso
<span class="pre">`/favicon.ico`</span>, se aplica una directiva que
evita que se registre en los *logs* la petición del
recurso y, además, se devuelva un código **410**
para que el navegador no vuelva a pedirlo
más. Por supuesto, si se pide cualquier otro
recurso, la petición sí se registrará y no se devolverá un error.

Advertencia

No entienda <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a> como una
sentencia condicional que sirve para
aplicar unas directivas particulares a la URL que cumpla su patrón, de
manera que si varias localizaciones son adecuadas, se aplicarán las
directivas particulares de todas esas localizaciones. Los bloques <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a> son bloques terminales: cada
petición acaba por aplicar la configuración de un único bloque <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a> (más, obviamente, la
configuración común del bloque): y en caso de que varios bloques
concuerden con la URL se escogerá uno según las
reglas de precedencia que se verán más
adelante. La única forma de que una petición salga de su bloque <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a> y acabe aplicando otra
configuración es quie se encuentre dentro con un
<a href="04.rewrite.html#ngx-rewrite" class="reference internal"><span
class="std std-ref">redirección</span></a> exitosa.

Nota

Pueden anidarse localizaciones, excepto las localicaciones nominales (las que usan @) que no pueden anidar ni ser anidadas.

## Expresión de las rutas

La directiva <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a> permite expresar el patrón a
comparar con la ruta de diversos modos:

-   Sin modificador, implica que el patrón debe concordar con el
    principio de la ruta:

        location /docs/ {
           autoindex on;
        }

    de esta manera, direcciones como
    *http://www.example.net/docs/uno.html* o
    *http://www.example.net/docs/subdir/dos.html* concuerdan con el
    patrón.

-   Con el modificador
    <span class="kbd kbd docutils literal notranslate">=</span>, el
    patrón debe concordar exactamente con la ruta:

        location = /errors/404.html {
           internal;
        }

-   Con el modificador
    <span class="kbd kbd docutils literal notranslate">~</span>, el
    patrón es una expresión regular:

        location ~ \.(jpg|jpeg|png)$ {
           expires max;
        }

    En este caso, la directiva <span class="pre">`expires`</span> se
    aplica a las imágenes.

    Nota

    La expresión regular también puede escribirse entre comillas:

        location ~ "\.(jpg|jpeg|png)$" {
           expires max;
        }

    lo cual cobra especial importancia cuando contiene algún carácter
    que puede malinterpretar **nginx** como la llave de apertura
    (»*{*«).

-   Con el modificador
    <span class="kbd kbd docutils literal notranslate">~\*</span>, el
    patrón es una expresión regular en que no atienden mayúsculas o
    minusculas:

        location ~* \.(jpe?g|png)$ {
           expires max;
        }

    En este caso una imagen de nombre
    <span class="pre">`foto.JPG`</span> también concordaría.

-   Con el modificador
    <span class="kbd kbd docutils literal notranslate">^~</span>, el
    patrón no es una expresión regular, sino que debe concordar con el
    principio de la ruta exactamente igual que cuando no hay
    modificador. La diferencia la veremos al tratar el orden de
    precedencia.

-   Con el modificador
    <span class="kbd kbd docutils literal notranslate">@</span>, el
    patrón no es un patrón para rutas, sino un nombre que puede usarse
    en redirecciones. Por ejemplo:

        try_files $uri $uri/ @nombre;

        location @nombre {
           rewrite ^(.*\.(?:jpe?g|png))$ /images$1 last;
        }

Nota

Como en el caso de <span class="pre">`server_name`</span> es posible
capturar grupos para usarlos luego en directivas incluidas dentro del
bloque. Véase un ejemplo más abajo al
<a href="#nginx-perfiles" class="reference internal"><span
class="std std-ref">habilitar los perfiles de usuario</span></a>.

<span id="ngx-location-prec"></span>

## Precedencia

Es muy habitual que dentro de un mismo bloque virtual, haya varias
localizaciones. Una petición sólo puede aplicar las directivas incluidas
dentro de una localización (o de ninguna), por lo que hay un orden de
precedencia para determinar cuál de los bloques <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a> concordante es aplicable. La
precedencia es la siguiente:

1.  La localización con modificador
    <span class="kbd kbd docutils literal notranslate">=</span>.

2.  La localización más larga con modificador
    <span class="kbd kbd docutils literal notranslate">^~</span>.

3.  La primera localización con expresión regular (indistintamente
    <span class="kbd kbd docutils literal notranslate">~</span> y
    <span class="kbd kbd docutils literal notranslate">~\*</span>).

4.  La localización más larga sin modificador.

Ver también

La página <a href="https://nginx.viraptor.info/"
class="reference external">nginx.viraptor.info</a> calcula, dada una
configuración, cuál es el bloque <span class="pre">`server`</span> y,
dentro de él, el bloque <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a> aplicable para una URL
determinada.


