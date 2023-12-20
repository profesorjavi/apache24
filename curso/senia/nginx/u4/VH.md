# Dominios virtuales
Es común que un mismo servidor *web* sirva páginas cuyo nombre de sitio
es distinto. Por ejemplo, un sitio puede ser *sitio1.dominio.org* y otro
distinto *sitio2.dominio.org*. Sin embargo, ambos están alojados en
nuestro servidor web.

Para lograr tal, debemos utilizar tres sentencias:
<a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server"
class="reference external">server</a>,
<a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#listen"
class="reference external">listen</a> y <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name"
class="reference external">server_name</a>. Por ejemplo:

    # cat /etc/nginx/sites-available/sitio1
    server {
       listen  80;
       server_name  sitio1.dominio.org;

       try_files $uri $uri/ =404;
       root /srv/www/sitio1;
    }
    # cat /etc/nginx/sites-available/sitio2
    server {
       listen  80;
       server_name  sitio2.dominio.org;

       try_files $uri $uri/ =404;
       root /srv/www/sitio2;
    }

Por lo general, cada sentencia <span class="pre">`server`</span> se
corresponde con un dominio virtual distinto, y cada dominio se coloca en
un fichero distinto. Además, para que estén habilitados, los ficheros
tienen que estar enlazados en <span class="pre">`sites-enabled`</span>
por lo que es necesario:

    # cd /etc/nginx/sites-enabled
    # ln -fs ../sites-available/sitio1
    # ln -fs ../sites-available/sitio2

## <span class="section-number">7.2.2.2.2.1. </span>server\_name<a href="#server-name" class="headerlink"
title="Enlace permanente a este encabezado">¶</a>

No obstante lo anterior, las cosas pueden complicarse hasta el punto de
que <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name"
class="reference external">server_name</a> tiene un
<a href="http://nginx.org/en/docs/http/server_names.html"
class="reference external">apartado dedicado en exclusiva a su
explicación</a><a href="#id5" id="id1" class="footnote-reference brackets"
role="doc-noteref"><span class="fn-bracket">[</span>1<span
class="fn-bracket">]</span></a>. De hecho, <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name"
class="reference external">server_name</a> no tiene por qué contener un
nombre sin más, sino que puede contener:

-   Un nombre, como
    <span class="kbd kbd docutils literal notranslate">sitio1.dominio.org</span>.

-   Un nombre con comodín al comienzo (p.e.
    <span class="kbd kbd docutils literal notranslate">\*.dominio.org</span>)
    o al final (p.e.
    <span class="kbd kbd docutils literal notranslate">www.dominio.\*</span>).

-   Una <a href="../../../../02.conbas/10.texto/01.regex.html#regex"
    class="reference internal"><span class="std std-ref">expresión
    regular</span></a> (con sintaxis PCRE), en cuyo caso debe comenzarse
    la expresión con una virgulilla, como
    <span class="kbd kbd compound docutils literal notranslate"><span class="kbd kbd docutils literal notranslate">~</span>^<span class="kbd kbd docutils literal notranslate">www.(\[</span>^<span class="kbd kbd docutils literal notranslate">.\]</span>+<span class="kbd kbd docutils literal notranslate">.\[</span>^<span class="kbd kbd docutils literal notranslate">.\]</span>+<span class="kbd kbd docutils literal notranslate">)$</span></span>.

-   Una combinación cualquiera de las tres posibilidades anteriores:

        server_name   dominio.org
                    *.dominio.org;

Nota

La documentación nos advierte de que el nombre
<span class="kbd kbd docutils literal notranslate">.dominio.org</span>
se trata de forma especial y equivale a la combinación de nombres usada
justamente encima de esta nota:

    server_name  .dominio.org;

Las *expresiones regulares*, además de permitir definir patrones
bastantes complicados de coincidencia, permiten almacenar parte del
nombre en una variable y usarlo luego en la configuración. Por ejemplo:

    server {
       listen  80;
       server_name ~^([^.]+)\..*;

       root /srv/www/$1;
       try_files $uri $uri/ =404;
    }

Advertencia

Cuando una *expresión regular* contiene llaves de apertura o cierre, o
punto y coma es preciso encerrarla entre comillas dobles.

no sólo eso, sino que se puede usar la
<a href="../../../../02.conbas/10.texto/01.regex.html#regex-pcre"
class="reference internal"><span class="std std-ref">captura con
nombres</span></a> para facilitar la lectura de la configuración:

    server {
       listen  80;
       server_name ~^(?P<vhost>[^.]+)\..*;

       root /srv/www/$vhost;
       try_files $uri $uri/ =404;
    }

Nota

Como valor se puede usar también una *ip* o una *ip* con comodín:

    server_name 172.22.0.2 192.168.*;

Nota

Hay algunas apuntes en la documentación oficial sobre la forma en que
**nginx** procesa y compara el campo <span class="pre">`Host:`</span>
con los valores de la directiva y nos explica que, obviamente, es más
eficiente la comparación de nombres exactos y luego de nombres con
comodines que la comparación con expresiones regulares. Además, nos
advierte de que, si a un servidor se accede mayoritariamente con un
nombre, es mejor declarar explícitamente ese nombre. Por tanto es mejor
desde el punto de vista del rendimiento, esto:

    server_name www.dominio.org  dominio.org *.dominio.org;

que esto otro:

    server_name .dominio.org;

## <span class="section-number">7.2.2.2.2.2. </span>Selección del dominio virtual<a href="#seleccion-del-dominio-virtual" class="headerlink"
title="Enlace permanente a este encabezado">¶</a>

Conocido cómo escribir la directiva <a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name"
class="reference external">server_name</a>, lo interesante es saber cuál
es el algoritmo que sigue **nginx** para seleccionar cuál es el bloque
<a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server"
class="reference external">server</a> que usará para responder a una
petición. El algoritmo es el siguiente:

1.  Se seleccionan aquellos bloques cuya directiva
    <a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#listen"
    class="reference external">listen</a> esté en consonancia con la
    petición.

2.  Para los bloques anteriores, se escoge aquel en que el valor de la
    directiva <a
    href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name"
    class="reference external">server_name</a> concuerde con el campo
    <span class="pre">`Host:`</span> de la petición

    -   Si son varios los bloques, entonces la precedencia en la
        concordancia con <a
        href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name"
        class="reference external">server_name</a> es la siguiente:

        1.  Nombre exacto.

        2.  Nombre con comodín al principio. Cuanto más largo, más
            precedente.

        3.  Nombre con comodín al final. Cuanto más largo, más
            precedente.

        4.  Expresión regular. Si hay concordancia con varias
            expresiones regulares se coge el bloque
            <a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server"
            class="reference external">server</a> concordante que
            aparece en primer lugar dentro del
            fichero<a href="#f1" id="id2" class="footnote-reference brackets"
            role="doc-noteref"><span class="fn-bracket">[</span>2<span
            class="fn-bracket">]</span></a>.

    -   Si no hay bloque seleccionado por falta de concordancia, se toma
        aquel que incluya el parámetro
        <span class="pre">`default_server`</span> en su sentencia
        <a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#listen"
        class="reference external">listen</a>:

            listen  80 default_server;

        y, si ningún bloque tenía una sentencia
        <a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#listen"
        class="reference external">listen</a> que incluyera el
        parámetro, se toma el primer
        <a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#server"
        class="reference external">server</a>
