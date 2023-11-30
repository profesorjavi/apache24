# Configuración NO-IP 

## Alta en no-ip
El primer paso es darnos de alta en la plataforma y conseguir nuestro subdominio.


https://www.noip.com


## Configurar instancia AWS
En la instancia de AWS en ubuntu server

Note: If you do not have privileges on the machine you are on, you may add the “sudo” command in front of steps (5 and 6).
```
$ sudo sudo su -
```
1. cd /usr/local/src
1. wget http://www.no-ip.com/client/linux/noip-duc-linux.tar.gz
1. tar xzf noip-duc-linux.tar.gz
1. cd noip-2.1.9-1
1. make
1. make install

Configurar el cliente
```sh
/usr/local/bin/noip2 -C   (dash capital C, this will create the default config file)
```
Lanzar el programa
```sh
/usr/local/bin/noip2
```
Podemos comprobar que funciona con nu ping a nuestro subdominio


Crear un servicio para que al reinicial la instancia siga funcionando (https://www.blackmoreops.com/2020/11/18/how-to-install-the-noip2-on-ubuntu-and-run-via-systemd-systemctl-noip-dynamic-update-client/)


1. Crea el fichero `/etc/systemd/system/noip2.service`
```sh
root@ubuntu:/usr/local/src/noip-2.1.9-1# vi /etc/systemd/system/noip2.service
```
2. Copia y pega.

        [Unit]
        Description=noip2 service

        [Service]
        Type=forking
        ExecStart=/usr/local/bin/noip2
        Restart=always

        [Install]
        WantedBy=default.target


3. Reiniciar el demonio de systemctl
```sh
root@ubuntu:/usr/local/src/noip-2.1.9-1# sudo systemctl daemon-reload
```
4. Habilitar el servio no-ip
```
root@ubuntu:/usr/local/src/noip-2.1.9-1# sudo systemctl enable noip
```

5. Reiniciar la máquina y comprobar que funciona