# Configuracion de Agentes agiles en endpoinst 
Para tener data de los clientes o endpoints se debe configurar de lado del cliente o endpoint:
  - agentes agiles: Beats
  - Protocolos: SNMP
 
Con esta inforamcion podemos tener data que sera representada por Kibana.

# Instalando agentes Beats para System Logs

El system Filebeat module colecta and parsea logs creado para sistemas logging service comunmente basado en distros Unix/Linux.
El modulo no esta disponible para sistemas Windows.

Para instalacion en Debian, descargamos e instalamos el agente:

```sh
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-6.8.11-amd64.deb
sudo dpkg -i filebeat-6.8.11-amd64.deb
```

# Configurando el Agente

Modificamos /etc/filebeat/filebeat.yml para envio de data a logstash y configuracion de agente:

filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/*.log
filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false
setup.template.settings:
  index.number_of_shards: 3  
setup.kibana:
  host: "192.168.0.17:5601"
  space.id: Default
output.elasticsearch:
  hosts: ["192.168.0.17:9200"]
processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~

Habilitamos el agente:

sudo filebeat modules enable system

Y, Iniciamos el agente y la configuracion establecida:

sudo filebeat setup
sudo service filebeat start

Para Logstash eviamos directo desde UDP TCP, para eso configramos el rsyslog desde el cliente:
