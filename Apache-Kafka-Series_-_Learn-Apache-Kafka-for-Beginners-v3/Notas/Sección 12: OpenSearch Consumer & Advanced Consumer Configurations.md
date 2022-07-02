# 71. OpenSearch Consumer - Project Overview
Se implementara un consumidor que lea los mensajes de Kafka y los envia a una base de datos elastica OpenSearch.
# 72. OpenSearch Consumer - Project Setup
Se deja el proyecto configurado y listo para iniciar la implementacion.
# 73. Setting up OpenSearch on Docker

se ejecuta el siguiente docker compose
```
version: '3.7'
services:
  opensearch:
    image: opensearchproject/opensearch:1.2.4
    environment:
      discovery.type: single-node
      plugins.security.disabled: true # disable https and logins
      compatibility.override_main_response_version: true
    ports:
      - 9200:9200
      - 9600:9600 # required for Performance Analyzer

  # console at http://localhost:5601/app/dev_tools#/console
  opensearch-dashboards:
    image: opensearchproject/opensearch-dashboards:1.2.0
    ports:
      - 5601:5601
    environment:
      OPENSEARCH_HOSTS: '["http://opensearch:9200"]'
      DISABLE_SECURITY_DASHBOARDS_PLUGIN: "true"
```
Abrimos un navegador y escribimos `http://localhost:9200` y nos aparecera un json como el siguiente:
```
{
  "name" : "127309b72557",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "vNcbE_qpQXSQxVpqBS773A",
  "version" : {
    "number" : "7.10.2",
    "build_type" : "tar",
    "build_hash" : "e505b10357c03ae8d26d675172402f2f2144ef0f",
    "build_date" : "2022-01-14T03:38:06.881862Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```
Despues abrimos en el navegador `http://localhost:5601` cargando el dashboard de paneles de busqueda.
# 74. Setting up OpenSearch on the Cloud
# 75. OpenSearch 101 (e.g. ElasticSearch)

Practicaremos la busqueda abierta que es lo mismo que la busqueda elastica:
Abrimos la siguiente gui rapido https://opensearch.org/docs/latest/#docker-quickstart y ademas en el navegador entramos a la consola del opensearch de docker http://localhost:5601/app/dev_tools#/console:

1. Para crear nuestro primer index escribimos `PUT /my-first-index` y ejecutamos dandonos como resultado lo siguiente:
```
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "my-first-index"
}
```
2. Creamos nuestro primer documento ejecutando 
```
PUT /my-first-index/_doc/1
{"Description": "To be or not to be, that is the question."}
```
Resultando
```
{
  "_index" : "my-first-index",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```
3. Recuperamos el documento creado anteriormente, ejecutando

```
GET /my-first-index/_doc/1
```
Resultando
```
{
  "_index" : "my-first-index",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "Description" : "To be or not to be, that is the question."
  }
}
```
4. Borramos el documento, ejecutando

```
DELETE /my-first-index/_doc/1
```
Resultado
```
{
  "_index" : "my-first-index",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 2,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}
```
Si intentamos recuperar el documento borrado verificando que este borrado, ejecutamos nuevamente
```
GET /my-first-index/_doc/1
```
Resultado
```
{
  "_index" : "my-first-index",
  "_type" : "_doc",
  "_id" : "1",
  "found" : false
}
```
5. Si deseamos tambien podemos borrar el index, ejecutando:

```
DELETE /my-first-index 
```
Resultado
```
{
  "acknowledged" : true
}
```
Consultamos el indx para validar el borrado, ejecutando:

```
GET /my-first-index
```
Resultado
```
{
  "error" : {
    "root_cause" : [
      {
        "type" : "index_not_found_exception",
        "reason" : "no such index [my-first-index]",
        "index" : "my-first-index",
        "resource.id" : "my-first-index",
        "resource.type" : "index_or_alias",
        "index_uuid" : "_na_"
      }
    ],
    "type" : "index_not_found_exception",
    "reason" : "no such index [my-first-index]",
    "index" : "my-first-index",
    "resource.id" : "my-first-index",
    "resource.type" : "index_or_alias",
    "index_uuid" : "_na_"
  },
  "status" : 404
}
```
# 76. OpenSearch Consumer Implementation - Part 1 
# 77. OpenSearch Consumer Implementation Part 2
# 78. Consumer Delivery Semantics (Semanticas de entrega)
## Delivery Semantics - At Most Once (Como Maximo una vez)
los offsets de los mensajes que llegan en el lote, son confirmados inmediatamente el lote de mensaje es recivido por el consumidor. Si el procesamiento de un mensaje da error, este mensaje no se volvera a procesar ya que su offset fue confirmado al llegar en el lote.

Con este mecanismo lo que sucede es lo siguiente:
1. El consumidor lee el lote de mensajes.
2. Confirma el offset del ultimo mensaje del lote leido.
3. El consumir procesa cada mensaje del lote.

## Delivery Semantics - At Least Once (Al menos una vez)

Los offsets de los mensajes que llegan en el lote, son confirmados inmediatamente despues que se han procesados. Si el procesado de algun mensaje da error, el mensaje sera leido otra vez. Esto puede resultar en procesamiento de mensajes duplicados. Para este mecanismo debemos hacegurarnos que nuestro procesamiento de mensajes es IDEMPOTENTE.

Con este mecanismo lo que sucede es lo siguiente:
1. El consumidor lee el lote de mensajes.
2. El consumidor procesa cada mensaje del lote.
3. Confirma el offset del ultimo mensaje del lote leido.

Hay que tener en cuenta que si sucede una interrupcion (reinicio del micro) del procesado de un lote mensaje, dejando mensajes del lote sin procesar, al arrancar de nuevo volvera a procesar todo el lote, procesando nuevamente por sgunda vez los mensajes que alcanzo a procesar previamente a su interrupcion (Esto es debido a los offsets se confirman por lotes y no por mensajes)

## Delivery Semantics - Exactly Once (Exactamente una vez)
Puede ser logrado por kafka, mediante la API transactional (Facil con el API de Kafka Streams).

# 79. OpenSearch Consumer Implementation Part 3 - Idempotence

En este apartado haremos idempotente el procesamiento de mensajes que recibimos de Kafka y enviamos a OpenSearch.

Lograremos esto enviando a OpenSearch un Id que nos hara unico el documento en OpenSearh, permitiendo que OpenSearch al enviar dos veces el mismo documento no lo duplique.

# 80. Consumer Offsets Commit Strategies (Estrategias de confirmacion de los offsets de un consumidor)

## ¿Cuales son las estrategias de commits de los offsets disponibles en los consumidores?

Existen 2 patrones comunes para cometer los offsets en una aplicacion:
- (easy) `enable.auto.commit=true`: procesado sincrono de lotes de mensajes.
- (mediun) `enable.auto.commit=false`: confirmacion manual de los offsets.

## Kafka Consumer - Auto Offset Commit Beahavior (Comportamiento automatico de las confirmaciones de los offsets)
- En la API de Java para los consumidores, los offset son regularmente confirmados automaticamente.
- Por defecto se habilita la lectura y confirmacion de los offset AL MENOS UNA VEZ, bajo ciertas condiciones.
- Los offsets son confirmados cuando la funcion `.poll()` es llamada y `auto.commit.interval.ms` ha trascurrido. Es necesario asegurarse que los mensajes se procesen con exito antes de llamar a la funcion `.poll()`.

## Consumer Offset Commits Strategies: `enable.outo.commit=true` procesando el lote de mensajes sincronamente
```
while (true) {
  ConsumerRecords<String, String> batch = consumer.poll(Duration.ofMillis(100));
  dosomethingSynchronous(batch);
}
```
la primera vez que se llame a la funcion `.poll()` se obtendra un lote de mensajes, los cuales se procesaran en la siguiente linea `dosomethingSynchronous(batch)`, garantizando que la proxima vez que se llame a la funcion `.poll()` se procesaran los mensajes con exitos.

- Con las confirmaciones automaticas, los offsets seran confirmados automaticamente para ti en intervalos regulares dado por la propiedad `auto.commit.interval.ms=5000` (valor por defecto) cada vez que se llame a la funcion `.poll()`.

- Si tu no usas prosamiento sincrono, tu tendras un comportamiento "al-most-once" porque los offsets seran confirmados antes que tus datos sean procesados.

## Consumer Offset Commits Strategies: `enable.outo.commit=false` procesando el lote de mensajes sincronamente

Puede darse el caso en que no se quisiera confirmar los offsets automaticamente cada cierto tiempo, ya que al integrarse con una base de datos se ve impactado el rendimiento del procesado de mensajes, sobre todo en casos en los que un lote de mensaje viene con solo 1 o 2 mensajes, por lo tanto necesita acomular una cantidad importante de mensajes y procesarlos conectandoce con la base de datos las menos veces posibles. 

El codigo que podria soportar este escenario:
```
List<ConsumerRecords<String, String>> batchs = new ArrayList<>();
while (true) {
  batchs.add(consumer.poll(Duration.ofMillis(100)));
  if(isReady(batchs)){
    doSomethingSynchronous(batchs);
    consumer.commitAsync();
    batchs.clear()
  }
}
```
- Controlas cuando tus offsets son confirmados y cual es la condicion para confirmalos, como por ejemplo la cantidad de mensajes acumulados minima para procesar los mensajes.

# 81. OpenSearch Consumer Implementation Part 3 - Delivery Semantics
Se agrega la propiedad para no confirmar los offset automaticamente y ademas se sincroniza.
```
...
properties.setProperty(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "false");
...
...
// commit offsets after the batch is consumed
consumer.commitSync();
log.info("Offsets have been committed!");
...
```
# 82. OpenSearch Consumer Implementation Part 5 - Batching Data
Enviamos de manera masiva a OpenSearch los mensajes recividos en un lote de mensajes.
# 83. Consumer Offset Reset Behavior (Comportamiento de Restablecimiento de los offsets del consumidor)

