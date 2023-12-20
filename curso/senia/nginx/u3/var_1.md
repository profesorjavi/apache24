#Variables predefinidas

**nginx** tiene predefinidas
<a href="http://nginx.org/en/docs/varindex.html"
class="reference external">un gran número de variables</a>. Aquí
trataremos solamente algunas de las más recurrentes:

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_args"
class="reference external">$args</a>  
Contiene los argumentos pasados a través de la URL. Es decir, si la
petición fue:

    GET /form.php?name=Pepe&age=21

el valor de esta variable será
``name=Pepe&edad=21``

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_arg_"
class="reference external">`$arg_xx`</a>  
Contiene el valor del argumento *xxx* pasado a través de la URL. En el
caso de la petición anterior,
<span class="kbd kbd docutils literal notranslate">$arg\_age</span>
valdría **21**.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_cookie_"
class="reference external">$cookie_xxx</a>  
Contiene el valor de la *cookie* «xxx».

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_host"
class="reference external">$host</a>  
Nombre que se usa para conectar al servidor. Véase el siguiente apartado
para más información.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_https"
class="reference external">$https</a>  
Vale *on* si la petición se hizo a través de HTTPs.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_http_"
class="reference external">$http_xxx</a>  
Valor del campo de cabecera *xxx*.

<a
href="http://nginx.org/en/docs/http/ngx_http_referer_module.html#var_invalid_referer"
class="reference external">$invalid_referer</a>  
Vale 1 si el <span class="pre">`Referer`</span> de la petición es
inválido, y la cadena vacía si es correcto. Para definir la validez se
usa la directiva <a
href="http://nginx.org/en/docs/http/ngx_http_referer_module.html#valid_referers"
class="reference external">valid_referers</a>. Por ejemplo:

    server {
       # Configuración...

       location ~ \.(:?jpe?g|png|gif)$ {
          valid_referers  server_names;
          if ($invalid_referer) {
             return 403;
          }
       }
    }

El código impide que se hagan enlaces calientes desde otros sitios web a
las imágenes de nuestro sitio.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_is_args"
class="reference external">$is_args</a>  
Vale *?* si la línea de petición contenía argumentos y la cadena vacía
si no los contenía. Es útil en redirecciones. Por ejemplo, si se quiere
redirigir una petición no segura a segura:

    return 301 https://$host$uri$is_args$args;

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_remote_addr"
class="reference external">$remote_addr</a>  
Dirección IP del cliente original. Tal dirección, si media un
<a href="../../../05.proxies/index.html#proxies"
class="reference internal"><span class="std std-ref">proxy</span></a> no
tiene por qué coincidir con la de la máquina que hace conexión con el
servidor (véase
<a href="nginx-clientip" class="reference external">cliente original</a>
más adelante). Para obtener la IP de la máquina que hace conexión
directa existe <a
href="http://nginx.org/en/docs/http/ngx_http_realip_module.html#var_realip_remote_addr"
class="reference external">realip_remote_addr</a>.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_request"
class="reference external">$request</a>  
Línea original de petición.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_request_method"
class="reference external">$request_method</a>  
Método de petición.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_request_uri"
class="reference external">$request_uri</a>  
Línea original de petición, incluyendo argumentos.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_uri"
class="reference external">$uri</a>  
URL de la petición modificada por las redirecciones internas operadas
por **nginx**.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_scheme"
class="reference external">$scheme</a>  
Protocolo. Puede valer *http* o *https*.

<a
href="http://nginx.org/en/docs/http/ngx_http_core_module.html#var_server_name"
class="reference external">$server_name</a>  
Nombre del servidor

-------

Las tres variables refieren el nombre del servidor con que puede ser
algo confuso:

-   <span class="pre">`$http_host`</span> es, tal cual, el valor del
    campo <span class="pre">`Host`</span>. Por tanto, puede incluir el
    puerto de conexión.

-   <span class="pre">`$server_name`</span> es el valor de la directiva
    <span class="pre">`server_name`</span> que provocó la elección del
    bloque <span class="pre">`server`</span>.

-   <span class="pre">`$host`</span> es el nombre como aparece en la
    primera línea de la petición (la que incluye el método) o, si no
    aparece ahí, el nombre que aparece en el campo
    <span class="pre">`Host`</span> o, si no, el valor de la variable
    <span class="pre">`$server_name`</span>. A diferencia de
    <span class="pre">`$http_host`</span> jamás incluirá el puerto de
    conexión.
