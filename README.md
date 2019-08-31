# pruebaDevOps
Presentación de Prueba DevOps

Debido a ue no puedo utilizar la infraestructura de producción, realicé la prueba en un pequeño laboratorio en un notebook dell
para esto genere un cluster docker swarm con vagrant mediante el archivo Vagranfile y como virtualizador Virtualbox 5.2.32
Hice esta misma prueba con Virtualbox de la serie 6, sin embargo Vagrant plugin virtualbox con virtualbox 6, tiene serios problemas con
las virtual net.
Una configuración similar la he provisionado en AWS anteriormente y en xenserver (que si bien es experimental es lo que se encuentra
funcionando en produccion en una cloud privada que implementé


#Implementación infraestructura

La implementación de la infraestructura y la conformación del cluster docker swarm se efectura con la ejecución de vagrant up

$vagrant up

Esto provisiona 4 vm Centos/7 con docker, luego dentro de la lógica del script de vagrant, ejecuta en la vm manager el comando docker init swarm y la captura del token para el join de los workers
los workers a su vez ejecutan el comando docker join como worker
Luego se puede verificar la convergencia de la infraestructura conectandose por ssh al manager

$vagrant ssh manager

dentro de la shell de la vm manager se verifica la covergencia de los workers con comando docker node list

obteniendo un resultado como este:
[vagrant@manager ~]$ docker node list
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
ls0u6j0v4ucm79m9yxjiuqgkd *   manager             Ready               Active              Leader              19.03.1
i4w2k2wjtw5kpjqksxdgvm370     worker01            Ready               Active                                  19.03.1
wlkcmrhskca9vruuq91jgbjat     worker02            Ready               Active                                  19.03.1
sdv1pefo5kncvzhp6gvo88ycj     worker03            Ready               Active                                  19.03.1

Esta provisión de infraestructura es lograble de mismo modo ejecutando un playbook de ansible.

Luego continuando con los deseables de la prueba, se confeccionan los yml dentro de un playground local. 

Para la demostración se puso como objetivo implementar un orquestador muy bueno para trabajar con docker swarm y como complemento para el trabajo de DevOps con los desarrolladores a traves de un CI como Jenkins.

para lo cual se crearon dentro del path ansible_local/provisioning
las recetas ansible que se ejecutan en el siguiente orden:

1. playbook.yml
para instalación de paqueteria centos requerida para docker-compose
1. docker-compose.yml
para la instalación propiamente tal de docker-compose
1. portainer.yml
para la provision del contenedor de portainer, portainer service y portainer agent

El resultado final esperado de la prueba, es la visualización de la aplicación dockerizada portainer

[![portainer funcionado en docker swarm](https://github.com/pmlopez-2019/pruebaDevOps/blob/master/portainer-running-at-docker-swarm.png "portainer funcionado en docker swarm")](https://github.com/pmlopez-2019/pruebaDevOps/blob/master/portainer-running-at-docker-swarm.png "portainer funcionado en docker swarm")
