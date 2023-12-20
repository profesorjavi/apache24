# alias

Ya introdujimos la directiva
``root``, que permite indicar cuál es el
directorio en el sistema de ficheros del servidor bajo el cual se sitúan
los ficheros accesibles. Por tanto, si su valor es
<span class="pre">`/srv/www`</span> y la ruta del recurso requirida es
<span class="pre">`/themes/classic/main.css`</span>, el recurso se
encuentra en el servidor en la ruta
<span class="pre">`/srv/www/themes/classic/main.css`</span>, esto es, la
concatenación del directorio raíz y la ruta expresada en la URL.

Pero hay otra forma de indicar cuál la ruta en el servidor del recurso y
que requiere el uso <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a>:
<a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#alias"
class="reference external">alias</a>. Con ella, la ruta absoluta del
recurso en el servidor es la concatenación su valor y la ruta de la URL
descontada la coincidencia con el patrón de <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#location"
class="reference external">location</a>. Por ejemplo:

    location /images/ {
       alias /usr/share/nginx/media/;
    }

En este caso, si se pidiera el recurso
*http://www.example.net/images/foto.jpg*, su ruta en el servidor sería
<span class="pre">`/usr/share/nginx/media/foto.jpg`</span> y no
<span class="pre">`/usr/share/nginx/media/images/foto.jpg`</span>, como
habría ocurrido si se hubiera usado <span class="pre">`root`</span>.

<span id="nginx-perfiles"></span>

## Sitios personales

Es una práctica común, cuando en un servidor se desea que sus usuarios
tengan sitio personal en el que puedan colgar contenidos, que se acceda
a ellos a través de la dirección:

    http://www.example.net/~USUARIO/ruta/hacia/el/recurso

donde *USUARIO* es el nombre de usuario; y que los contenidos se sitúen
en el servidor bajo <span class="pre">`~/public_html`</span>. Para
lograr esto, en **nginx** basta con incluir en el dominio virtual (o
sea, el bloque <span class="pre">`server`</span>) adecuado, lo
siguiente:

    # Sitios personales (contenido estático)
    location ~ ^/~([^/]+)(.*)$ {
       alias /home/$1/public_html$2;
       autoindex on;
    }

que logra lo que pretendemos.

Nota

Este trozo de configuración es muy apropiado para incluirlo como
contenido de un fichero llamado
<span class="pre">`snippets/personal.conf`</span> e incluirlo después
dentro del bloque <span class="pre">`server`</span> adecuado mediante la
directiva <span class="pre">`include`</span>:

    include snippets/personal.conf;

Notas al pie

<span class="label"><span class="fn-bracket">\[</span><a href="#id1" role="doc-backlink">1</a><span class="fn-bracket">\]</span></span>

Aunque, en realidad, la máquina se toma del valor del campo
<span class="pre">`Host`</span>, no de la URL, en la práctica los
navegadores copian como valor del campo la máquina que introduzcamos en
la barra de direcciones.

<span class="label"><span class="fn-bracket">\[</span><a href="#id2" role="doc-backlink">2</a><span class="fn-bracket">\]</span></span>

Ese recurso es el icono que usa el navegador para adornar el título de
la ventana y la pestaña. Todos los navegadores nos lo pedirán y, si
nosotros no lo hemos definido, se generará un enfadoso error en el
registro. Para evitarlo, nada mejor que las líneas sugeridas. Con la
directiva
<span class="kbd kbd compound docutils literal notranslate"><span class="kbd kbd docutils literal notranslate">access\_log</span>
<span class="kbd kbd docutils literal notranslate">off</span></span> no
se registrá nunca la petición, incluso si sí hemos creado un *favicon*
para nuestro sitio.

<span class="label"><span class="fn-bracket">\[</span><a href="#id3" role="doc-backlink">3</a><span class="fn-bracket">\]</span></span>

Con todo parece que –al menos– *Chrome* no presta mucho caso a ello y
sigue pidiendo el *favicon*.
