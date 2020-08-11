# Instalación de STACK ELK
Steps to install ELK on ubuntu Bionic

# Objetivo
Instalar ELK en un servidor Ubuntu

# Distribucion
Ubuntu 18.04

# Requerimientos
Área de trabajo con privilegios root

# Instalacion de dependencias
```sh
$ sudo apt install openjdk-8-jre apt-transport-https wget 
```

# Configuración de JAVA

Validamos la version de JAVA, lo ideal es tener un JAVA 8 (Si tienes la version java 1.8 salta este paso):
```sh
$ java -version
```
openjdk version "1.8.0_191"
OpenJDK Runtime Environment (build 1.8.0_191-8u191-b12-2ubuntu0.18.10.1-b12)
OpenJDK 64-Bit Server VM (build 25.191-b12, mixed mode)

En el caso de tener otra version de JAVA y queremos usar el JAVA 8, con el siguiente comando escojemos a JAVA 8 por defecto:

```sh
$ sudo update-alternatives --config java
```

# Adicionar el repositorio de Elastic
Elastic proporciona un completo repositorio para los sistemas basados en Debian que incluye Elastic y Kibana. Sólo tienes que añadirlo a tu sistema. Comienza importando su clave GPG.
```sh
$ wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
Lo siguiente, adicionar el repositorio. Creamos el archivo en la ruta con el nombre /etc/apt/sources.list.d/elastic.list, 

```sh
$ sudo touch /etc/apt/sources.list.d/elastic.list
```
pegamos la siguiente línea en el archivo “elastic.list” usando nano o vim:

deb https://artifacts.elastic.co/packages/6.x/apt stable main

Guardamos, salimos. Actualizamos el Apt.
```sh
$ sudo apt update
```
# Instalar Elasticsearch y Kibana
Ya tenemos listo el ambiente para instalar  Elasticsearch y Kibana. Ya se encuentra disponible en el repositorio de nuestro servidor, entonces ejecutamos el comando:
```sh
$ sudo apt install elasticsearch kibana
```
Vamos a necesitar configurar los archivos /etc/kibana/kibana.yml para indicar la IP y puerto donde expondremos a Kibana. descomentamos:

server.port: 5601
server.host: "x.x.x.x"
elasticsearch.hosts: ["http://x.x.x.x:9200"]

Luego en el archivo /etc/elastisearch/elasticsearch.yml descomentamos y configuramos la IP y puerto que corresponde a nuestro server:

network.host: x.x.x.x
http.port: 9200

Reiniciamos Kibana and iniciamos Elasticsearch, y ambos servicios ya estan listos.
```sh
$ sudo systemctl restart kibana
$ sudo systemctl start elasticsearch
```
# Instalar Logstash
Como parte del procesador de información del stack instalamos Logstash. Entonces con el siguiente comando desde el administrador de paquetes apt instalamos:
```sh
$ sudo apt install logstash
```
# Testing Kibana y Elastic
Abrimos nuestro browser, y introducimos la IP y puerto que definimos en el archivo de configuracion de Kbana: http:x.x.x.x:5601

Probamos el servicio elasticsearch:

```sh
$ curl http:x.x.x.x:9200
```
# Configurando nuestro primer Logstash:
Para ello nos vamos a la ruta de configuracion:

$ cd /etc/logstash/conf.d

y creamos nuestro primer archivo de configuracion 'logstash.conf' para recibir data y enviarsela a Elastic:

input {
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["http://192.168.0.17:9200"]
    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
    #user => "elastic"
    #password => "changeme"
  }
}

