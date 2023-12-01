# Práctica 1.4

Implantación de Aplicaciones Web

José Juan Sánchez Hernández

Curso 2023/2024

------------------------------------------------------------------------

# Índice

-   <a
    href="#https.-creación-y-configuración-de-un-certificado-ssltls-autofirmado-en-apache"
    id="toc-https.-creación-y-configuración-de-un-certificado-ssltls-autofirmado-en-apache"><span
    class="toc-section-number">1</span> HTTPS. Creación y configuración de
    un certificado SSL/TLS autofirmado en Apache</a>
    -   <a href="#instalación-del-servidor-web-apache"
        id="toc-instalación-del-servidor-web-apache"><span
        class="toc-section-number">1.1</span> Instalación del servidor web
        Apache</a>
    -   <a href="#creación-del-certificado-autofirmado"
        id="toc-creación-del-certificado-autofirmado"><span
        class="toc-section-number">1.2</span> Creación del certificado
        autofirmado</a>
    -   <a href="#cómo-automatizar-la-creación-de-un-certificado-autofirmado"
        id="toc-cómo-automatizar-la-creación-de-un-certificado-autofirmado"><span
        class="toc-section-number">1.3</span> Cómo automatizar la creación de un
        certificado autofirmado</a>
    -   <a href="#cómo-consultar-la-información-del-sujeto-del-certificado"
        id="toc-cómo-consultar-la-información-del-sujeto-del-certificado"><span
        class="toc-section-number">1.4</span> Cómo consultar la información del
        sujeto del certificado</a>
    -   <a href="#cómo-consultar-la-fecha-de-caducidad-del-certificado"
        id="toc-cómo-consultar-la-fecha-de-caducidad-del-certificado"><span
        class="toc-section-number">1.5</span> Cómo consultar la fecha de
        caducidad del certificado</a>
    -   <a
        href="#configuración-de-un-virtualhost-con-ssltsl-en-el-servidor-web-apache"
        id="toc-configuración-de-un-virtualhost-con-ssltsl-en-el-servidor-web-apache"><span
        class="toc-section-number">1.6</span> Configuración de un VirtualHost
        con SSL/TSL en el servidor web Apache</a>
    -   <a href="#tareas-a-realizar" id="toc-tareas-a-realizar"><span
        class="toc-section-number">1.7</span> Tareas a realizar</a>
    -   <a href="#entregables" id="toc-entregables"><span
        class="toc-section-number">1.8</span> Entregables</a>
        -   <a href="#documento-técnico" id="toc-documento-técnico"><span
            class="toc-section-number">1.8.1</span> Documento técnico</a>
        -   <a href="#scripts-de-bash" id="toc-scripts-de-bash"><span
            class="toc-section-number">1.8.2</span> Scripts de Bash</a>
-   <a href="#referencias" id="toc-referencias"><span
    class="toc-section-number">2</span> Referencias</a>
-   <a href="#licencia" id="toc-licencia"><span
    class="toc-section-number">3</span> Licencia</a>

------------------------------------------------------------------------

# <span class="header-section-number">1</span> HTTPS. Creación y configuración de un certificado SSL/TLS autofirmado en Apache

En esta práctica vamos a crear un **certificado SSL/TLS autofirmado**
con la herramienta [`openssl`](https://www.openssl.org). Una vez creado
vamos a configurar el [servidor web Apache](https://httpd.apache.org)
para que utilice dicho certificado.

El proceso de creación de un certificado autofirmado consta de los
siguientes pasos:

1.  Crear una clave privada y un certificado autofirmado.
2.  Configurar la clave privada y el certificado autofirmado en el
    servidor web.

Tenga en cuenta que cuando un cliente acceda desde un navegador web a un
sitio web que utiliza un certificado autofirmado, se mostrará un mensaje
de advertencia indicando que **el certificado no es de confianza, porque
no ha sido emitido por una [autoridad de certificación
(CA)](https://es.wikipedia.org/wiki/Infraestructura_de_clave_p%C3%BAblica)**.

Si el cliente acepta el certificado, el navegador web lo almacenará en
su almacén de certificados y no volverá a mostrar el mensaje de
advertencia.

No se recomienda utilizar certificados autofirmados en sitios web
públicos, por lo tanto, esta práctica se utiliza con fines educativos
para conocer con detalle cómo funciona el proceso de creación y
configuración de un certificado autofirmado.

## <span class="header-section-number">1.1</span> Instalación del servidor web Apache

En primer lugar deberemos tener instado un [servidor web
Apache](https://httpd.apache.org) en nuestra máquina. Si todavía no lo
hemos instalado, podemos hacerlo con los siguientes comandos:

    sudo apt update
    sudo apt install apache2 -y

## <span class="header-section-number">1.2</span> Creación del certificado autofirmado

Para crear un certificado autofirmado vamos a utilizar la utilidad
[`openssl`](https://www.openssl.org).

Este es el comando que vamos a utilizar:

    sudo openssl req \
      -x509 \
      -nodes \
      -days 365 \
      -newkey rsa:2048 \
      -keyout /etc/ssl/private/apache-selfsigned.key \
      -out /etc/ssl/certs/apache-selfsigned.crt

Vamos a explicar cada uno de los parámetros que hemos utilizado.

-   `req`: El subcomando `req` se utiliza para crear solicitudes de
    certificado en formato `PKCS#10`. También puede utilizarse para
    crear certificados autofirmados, que será el uso que le daremos en
    esta práctica.

-   `-x509`: Indica que queremos crear un certificado autofirmado en
    lugar de una solicitud de certificado, que se enviaría a una
    autoridad de certificación.

-   `-nodes`: Indica que la clave privada del certificado no estará
    protegida por contraseña y estará sin encriptar. Esto permite a las
    aplicaciones usar el certificado sin tener que introducir una
    contraseña cada vez que se utilice.

-   `-days 365`: Este parámetro indica la validez del certificado. En
    este caso hemos configurado una validez de 365 días.

-   `-newkey rsa:2048`: Este parámetro indica que queremos generar una
    nueva clave privada `RSA` de 2048 bits junto con el certificado. La
    longitud de clave de 2048 bits es un estándar razonable para la
    seguridad en la actualidad.

-   `-keyout /etc/ssl/private/apache-selfsigned.key`: Indica la
    ubicación y el nombre del archivo donde se guardará la clave privada
    generada. En este caso, hemos seleccionado que se guarde en la ruta
    `/etc/ssl/private/apache-selfsigned.key`.

-   `-out /etc/ssl/certs/apache-selfsigned.crt`: Indica la ubicación y
    el nombre del archivo donde se guardará el certificado. En este
    caso, hemos seleccionado que se guarde en la ruta
    `/etc/ssl/certs/apache-selfsigned.crt`.

Al ejecutar el comando tendremos que introducir una serie de datos por
teclado que se añadirán al certificado. Los datos que tenemos que
introducir son los siguientes:

-   **Código del país** de 2 caracteres que identifica el país donde se
    emite el certificado. Por ejemplo, `ES` para España.
-   **Provincia** donde se emite el certificado.
-   **Localidad** donde se emite el certificado.
-   **Nombre de la organización** para la que se emite el certificado.
-   **Nombre de la unidad o sección** de la organización.
-   **Nombre del dominio** para el que se emite el certificado.
-   **Email**.

**Ejemplo:**

Este ejemplo muestra la salida que obtendremos al ejecutar el comando
anterior para crear el certificado autofirmado.

    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    State or Province Name (full name) [Some-State]:
    Locality Name (eg, city) []:
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:
    Organizational Unit Name (eg, section) []:
    Common Name (e.g. server FQDN or YOUR name) []:
    Email Address []:

## <span class="header-section-number">1.3</span> Cómo automatizar la creación de un certificado autofirmado

Para automatizar la creación de un certificado autofirmado desde un
script de *Bash*, podemos hacer uso del parámetro `-subj` que nos
permite pasar los datos se adjuntan al certificado como argumentos desde
la línea de comandos.

**Ejemplo:**

    #!/bin/bash
    set -x

    # Configuramos las variables con los datos que necesita el certificado
    OPENSSL_COUNTRY="ES"
    OPENSSL_PROVINCE="Almeria"
    OPENSSL_LOCALITY="Almeria"
    OPENSSL_ORGANIZATION="IES Celia"
    OPENSSL_ORGUNIT="Departamento de Informatica"
    OPENSSL_COMMON_NAME="practica-https.local"
    OPENSSL_EMAIL="admin@iescelia.org"

    # Creamos el certificado autofirmado
    sudo openssl req \
      -x509 \
      -nodes \
      -days 365 \
      -newkey rsa:2048 \
      -keyout /etc/ssl/private/apache-selfsigned.key \
      -out /etc/ssl/certs/apache-selfsigned.crt \
      -subj "/C=$OPENSSL_COUNTRY/ST=$OPENSSL_PROVINCE/L=$OPENSSL_LOCALITY/O=$OPENSSL_ORGANIZATION/OU=$OPENSSL_ORGUNIT/CN=$OPENSSL_COMMON_NAME/emailAddress=$OPENSSL_EMAIL"

## <span class="header-section-number">1.4</span> Cómo consultar la información del sujeto del certificado

    openssl x509 -in /etc/ssl/certs/apache-selfsigned.crt -noout -subject

## <span class="header-section-number">1.5</span> Cómo consultar la fecha de caducidad del certificado

    openssl x509 -in /etc/ssl/certs/apache-selfsigned.crt -noout -dates

## <span class="header-section-number">1.6</span> Configuración de un VirtualHost con SSL/TSL en el servidor web Apache

**Paso 1**

Editamos el archivo de configuración del virtual host donde queremos
habilitar el tráfico HTTPS.

En nuestro caso, utilizaremos el archivo de configuración que tiene
Apache por defecto para SSL/TLS, que está en la ruta:
`/etc/apache2/sites-available/default-ssl.conf`.

El contenido del archivo será el siguiente:

    <VirtualHost *:443>
        #ServerName practica-https.local
        DocumentRoot /var/www/html
        DirectoryIndex index.php index.html

        SSLEngine on
        SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
        SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
    </VirtualHost>

Las directivas que hemos configurado son:

-   `<VirtualHost *:443>`: Indica que este virtual host escuchará en el
    puerto `443` (HTTPS).

-   `ServerName`: Indica el nombre de dominio y se utiliza para indicar
    al servidor web Apache qué peticiones debe servir para este virtual
    host. En nuestro ejemplo estamos utilizando el dominio
    `practica-https.local`.

-   `DocumentRoot`: Es la ruta donde se encuentra el directorio raíz del
    host virtual.

-   `SSLEngine on`: Configuramos que este virtual host utilizará
    SSL/TLS.

-   `SSLCertificateFile`: Indica la ruta donde se encuentra el
    certificado autofirmado.

-   `SSLCertificateKeyFile`: Indica la ruta donde se encuentra la clave
    privada del certificado autofirmado.

**Paso 2**

Habilitamos el virtual host que acabamos de configurar.

    sudo a2ensite default-ssl.conf

Tenga en cuenta que estamos utilizando el nombre de archivo
`default-ssl.conf` porque estamos utilizando el archivo que tiene Apache
por defecto para configurar un virtual host con SSL/TLS, pero en su caso
puede ser otro.

**Paso 3**

Habilitamos el módulo SSL en Apache.

    sudo a2enmod ssl

**Paso 4**

Configuramos el virtual host de HTTP para que redirija todo el tráfico a
HTTPS.

En nuestro caso, el virtual host que maneja las peticiones HTTP está en
el archivo de configuración que utiliza Apache por defecto para el
puerto `80`: `/etc/apache2/sites-available/000-default.conf`.

El contenido del archivo será el siguiente:

    <VirtualHost *:80>
        #ServerName practica-https.local
        DocumentRoot /var/www/html

        # Redirige al puerto 443 (HTTPS)
        RewriteEngine On
        RewriteCond %{HTTPS} off
        RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
    </VirtualHost>

Las directivas que hemos configurado son:

-   `RewriteEngine On`: Habilita el motor de reescritura de URLs y nos
    permite usar reglas de reescritura.

-   `RewriteCond %{HTTPS} off`: Esta directiva es una condición que
    comprueba si la petición recibida utiliza HTTPS o no. Si se cumple
    esta condición, entonces se ejecuta la siguiente línea.

-   `RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]`: Las
    reglas de reescritura tienen la siguiente sintaxis
    `RewriteRule Pattern Substitution [flags]`.

    -   `Pattern`: Es el patrón que se debe cumplir en la URL solicitada
        para que la regla de reescritura se aplique. En este caso, `^`
        coincide con el principio de la URL, por lo que se aplicará a
        todas las solicitudes.
    -   `Substitution`: Es la URL a la que se redirige la solicitud. En
        este caso, se utiliza el valor
        `https://%{HTTP_HOST}%{REQUEST_URI}` y por lo tanto se redirige
        la solicitud a HTTPS manteniendo el mismo nombre de dominio y
        URI.
    -   `flags`: Son los flags que se pueden utilizar para modificar el
        comportamiento de la regla de reescritura. En este caso, el flag
        `[L,R=301]` indica que es una redirección permanente (Código de
        estado: 301).

Las directivas utilizan las siguientes **variables del servidor** que se
obtienen de la cabecera de la petición HTTP:

-   `%{HTTPS}`: Contiene el texto `on` si la conexión utiliza SSL/TLS o
    `off` en caso contrario.

-   `%{HTTP_HOST}`: Contiene el nombre de dominio que se ha utilizado en
    la petición del cliente para acceder al sitio web.

-   `%{REQUEST_URI}`: Contiene la URI que ha utilizado el cliente para
    acceder al sitio web. Por ejemplo, `/index.html`. Si la petición
    incluye parámetros éstos estarán almacenados en la variable
    `%{QUERY_STRING}`.

[En la documentación oficial del módulo `mod_rewrite` de
Apache](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html) puede
encontrar más información sobre estas directivas.

**Paso 5**

Para que el servidor web Apache pueda hacer la redirección de HTTP a
HTTPS es necesario habilitar el módulo `rewrite` en Apache.

    sudo a2enmod rewrite

**Paso 6**

Reiniciamos el servicio de Apache

    sudo systemctl restart apache2

**Paso 7**

Una vez llegado a este punto, es necesario comprobar que el puerto `443`
está abierto en las reglas del firewall para permitir el tráfico HTTPS.

**Paso 8**

Accede desde un navegador web al nombre de dominio que acabas de
configurar. En nuesro caso será: `https://practica-https.local`.

## <span class="header-section-number">1.7</span> Tareas a realizar

En esta práctica tendremos que realizar la instalación de la [pila
LAMP](https://josejuansanchez.org/iaw/practica-01-01/index.html) y la
**configuración de un certificado SSL/TLS autofirmado** en el [servidor
web Apache](https://httpd.apache.org), en una instancia EC2 de [Amazon
Web Services (AWS)](https://aws.amazon.com/es/) con la última versión de
[Ubuntu Server](https://ubuntu.com/server).

A continuación se describen **muy brevemente** algunas de las tareas que
tendrá que realizar.

1.  Crea una instancia EC2 en AWS.

2.  La **Amazon Machine Image (AMI)** que vamos a seleccionar para esta
    práctica será una **Community AMI** con la última versión de
    **Ubuntu Server**.

3.  Cuando esté creando la instancia deberá configurar los puertos que
    estarán abiertos para poder conectarnos por SSH y para poder acceder
    por HTTP/HTTPS.

    -   SSH (TCP)
    -   HTTP (TCP)
    -   HTTPS (TCP)

4.  Crea un par de claves (pública y privada) para conectar por SSH con
    la instancia. También puedes hacer uso de las claves que te
    proporciona AWS Academy (*vockey.pem*).

5.  Crea una dirección **IP elástica** y asígnala a la instancia EC2.

6.  Una vez que haya iniciado su instancia deberá hacer uso de los
    **scripts de bash** que diseñó en las prácticas anteriores para
    automatizar la instalación de la pila LAMP.

7.  Automatice la creación y configuración de un certificado autofirmado
    SSL/TLS con la utilidad [`openssl`](https://www.openssl.org) para el
    servidor web Apache.

8.  Busque cuál es la dirección IP elástica de su instancia y compruebe
    que puede acceder a ella desde una navegador web.

## <span class="header-section-number">1.8</span> Entregables

Deberá crear un repositorio en [GitHub](https://github.com) con el
nombre de la práctica y añadir al profesor como colaborador.

El repositorio debe tener el siguiente contenido:

-   Un **documento técnico** con la descripción de todos los pasos que
    se han llevado a cabo.
-   Los **scripts de Bash** que se han utilizado para automatizar la
    creación y configuración de un certificado SSL/TLS autofirmado en el
    servidor web Apache.

Además del contenido anterior puede ser necesario crear otros archivos
de configuración. A continuación se muestra un ejemplo de cómo puede ser
la estructura del repositorio:

    .
    ├── README.md
    ├── conf
    │   ├── 000-default.conf
    │   └── default-ssl.conf
    └── scripts
        ├── .env
        ├── install_lamp.sh
        └── setup_selfsigned_certificate.sh

### <span class="header-section-number">1.8.1</span> Documento técnico

El documento técnico `README.md` tiene que estar escrito en
[Markdown](https://es.wikipedia.org/wiki/Autoridad_de_certificaci%C3%B3n)
y debe incluir **como mínimo** los siguientes contenidos:

-   Descripción del proceso de creación y configuración del certificado
    SSL/TLS autofirmado en el servidor web Apache.

### <span class="header-section-number">1.8.2</span> Scripts de Bash

El directorio `scripts` debe incluir los siguientes archivos:

-   `.env`: Este archivo contiene todas las variables de configuración
    que se utilizarán en los scripts de Bash.

-   `install_lamp.sh`: Script de Bash con la automatización del proceso
    de instalación de la pila LAMP.

-   `setup_selfsigned_certificate.sh`: Script de Bash con la
    automatización del proceso de creación y configuración de un
    certificado SSL/TLS autofirmado en Apache.

# <span class="header-section-number">2</span> Referencias

-   [¿Qué es
    HTTPS?](https://www.cloudflare.com/es-es/learning/ssl/what-is-https/).
    Documentación oficial de Cloudflare.
-   [Book: OpenSSL
    Cookbook](https://www.feistyduck.com/library/openssl-cookbook/).
    Ivan Ristić.
-   [Infraestructura de clave pública
    (PKI)](https://es.wikipedia.org/wiki/Infraestructura_de_clave_p%C3%BAblica).
    Wikipedia.
-   [Autoridad Certificadora
    (CA)](https://es.wikipedia.org/wiki/Autoridad_de_certificaci%C3%B3n).
    Wikipedia.
-   [X.509](https://es.wikipedia.org/wiki/X.509). Wikipedia.
-   [RSA](https://es.wikipedia.org/wiki/RSA). Wikipedia.
-   [Módulo `mod_rewrite` de
    Apache](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html).
    Documentación oficial de Apache.

# <span class="header-section-number">3</span> Licencia

<a href="http://creativecommons.org/licenses/by-nc-sa/4.0/"
rel="license"><img
src="data:image/png;base64,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"
style="border-width:0" alt="Licencia de Creative Commons" /></a>  
Esta página forma parte del curso [Implantación de Aplicaciones
Web](https://josejuansanchez.org/iaw) de [José Juan Sánchez
Hernández](https://josejuansanchez.org) y su contenido se distribuye
bajo una <a href="http://creativecommons.org/licenses/by-nc-sa/4.0/"
rel="license">licencia Creative Commons
Reconocimiento-NoComercial-CompartirIgual 4.0 Internacional</a>.
