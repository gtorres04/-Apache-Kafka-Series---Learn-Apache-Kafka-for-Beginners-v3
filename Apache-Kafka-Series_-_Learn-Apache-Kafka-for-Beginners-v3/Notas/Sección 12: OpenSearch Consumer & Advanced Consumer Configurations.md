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

Es el comportamiento que tendra el consumidor al leer los mensajes de kafka

- `auto.offset.reset=latest`: leera desde el final de la cola.
- `auto.offset.reset=earliest`: leera desde el inicio de la cola.
- `auto.offset.reset=none`: lanzara una exception si no se encuentran offset,

Kafka retiene los mensajes recibidos de los productores en un tiempo determinado por la propiedad `offset.retension.minutes`.

# 84. OpenSearch Consumer Implementation Part 6 - Replaying Data

Ejecutemos un consumidor y verificamos que este consumidor no reciba mensajes.
```
> Task :kafka-consumer-opensearch:OpenSearchConsumer.main()
[main] INFO org.apache.kafka.clients.consumer.ConsumerConfig - ConsumerConfig values: 
	allow.auto.create.topics = true
	auto.commit.interval.ms = 5000
	auto.offset.reset = latest
	bootstrap.servers = [127.0.0.1:9092]
	check.crcs = true
	client.dns.lookup = use_all_dns_ips
	client.id = consumer-consumer-opensearch-demo-1
	client.rack = 
	connections.max.idle.ms = 540000
	default.api.timeout.ms = 60000
	enable.auto.commit = false
	exclude.internal.topics = true
	fetch.max.bytes = 52428800
	fetch.max.wait.ms = 500
	fetch.min.bytes = 1
	group.id = consumer-opensearch-demo
	group.instance.id = null
	heartbeat.interval.ms = 3000
	interceptor.classes = []
	internal.leave.group.on.close = true
	internal.throw.on.fetch.stable.offset.unsupported = false
	isolation.level = read_uncommitted
	key.deserializer = class org.apache.kafka.common.serialization.StringDeserializer
	max.partition.fetch.bytes = 1048576
	max.poll.interval.ms = 300000
	max.poll.records = 500
	metadata.max.age.ms = 300000
	metric.reporters = []
	metrics.num.samples = 2
	metrics.recording.level = INFO
	metrics.sample.window.ms = 30000
	partition.assignment.strategy = [class org.apache.kafka.clients.consumer.RangeAssignor, class org.apache.kafka.clients.consumer.CooperativeStickyAssignor]
	receive.buffer.bytes = 65536
	reconnect.backoff.max.ms = 1000
	reconnect.backoff.ms = 50
	request.timeout.ms = 30000
	retry.backoff.ms = 100
	sasl.client.callback.handler.class = null
	sasl.jaas.config = null
	sasl.kerberos.kinit.cmd = /usr/bin/kinit
	sasl.kerberos.min.time.before.relogin = 60000
	sasl.kerberos.service.name = null
	sasl.kerberos.ticket.renew.jitter = 0.05
	sasl.kerberos.ticket.renew.window.factor = 0.8
	sasl.login.callback.handler.class = null
	sasl.login.class = null
	sasl.login.connect.timeout.ms = null
	sasl.login.read.timeout.ms = null
	sasl.login.refresh.buffer.seconds = 300
	sasl.login.refresh.min.period.seconds = 60
	sasl.login.refresh.window.factor = 0.8
	sasl.login.refresh.window.jitter = 0.05
	sasl.login.retry.backoff.max.ms = 10000
	sasl.login.retry.backoff.ms = 100
	sasl.mechanism = GSSAPI
	sasl.oauthbearer.clock.skew.seconds = 30
	sasl.oauthbearer.expected.audience = null
	sasl.oauthbearer.expected.issuer = null
	sasl.oauthbearer.jwks.endpoint.refresh.ms = 3600000
	sasl.oauthbearer.jwks.endpoint.retry.backoff.max.ms = 10000
	sasl.oauthbearer.jwks.endpoint.retry.backoff.ms = 100
	sasl.oauthbearer.jwks.endpoint.url = null
	sasl.oauthbearer.scope.claim.name = scope
	sasl.oauthbearer.sub.claim.name = sub
	sasl.oauthbearer.token.endpoint.url = null
	security.protocol = PLAINTEXT
	security.providers = null
	send.buffer.bytes = 131072
	session.timeout.ms = 45000
	socket.connection.setup.timeout.max.ms = 30000
	socket.connection.setup.timeout.ms = 10000
	ssl.cipher.suites = null
	ssl.enabled.protocols = [TLSv1.2, TLSv1.3]
	ssl.endpoint.identification.algorithm = https
	ssl.engine.factory.class = null
	ssl.key.password = null
	ssl.keymanager.algorithm = SunX509
	ssl.keystore.certificate.chain = null
	ssl.keystore.key = null
	ssl.keystore.location = null
	ssl.keystore.password = null
	ssl.keystore.type = JKS
	ssl.protocol = TLSv1.3
	ssl.provider = null
	ssl.secure.random.implementation = null
	ssl.trustmanager.algorithm = PKIX
	ssl.truststore.certificates = null
	ssl.truststore.location = null
	ssl.truststore.password = null
	ssl.truststore.type = JKS
	value.deserializer = class org.apache.kafka.common.serialization.StringDeserializer

[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka version: 3.1.0
[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka commitId: 37edeed0777bacb3
[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka startTimeMs: 1656769557772
[main] INFO OpenSearchConsumer - The Wikimedia Index already exits
[main] INFO org.apache.kafka.clients.consumer.KafkaConsumer - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Subscribed to topic(s): wikimedia.recentchange
[main] INFO org.apache.kafka.clients.Metadata - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Resetting the last seen epoch of partition wikimedia.recentchange-0 to 0 since the associated topicId changed from null to DGWkrWKyQACUFXm_XTecqw
[main] INFO org.apache.kafka.clients.Metadata - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Resetting the last seen epoch of partition wikimedia.recentchange-2 to 0 since the associated topicId changed from null to DGWkrWKyQACUFXm_XTecqw
[main] INFO org.apache.kafka.clients.Metadata - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Resetting the last seen epoch of partition wikimedia.recentchange-1 to 0 since the associated topicId changed from null to DGWkrWKyQACUFXm_XTecqw
[main] INFO org.apache.kafka.clients.Metadata - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Cluster ID: Uu9pnoNSRxashKBRdPCtfQ
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Discovered group coordinator 127.0.0.1:9092 (id: 2147483646 rack: null)
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] (Re-)joining group
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Request joining group due to: need to re-join with the given member-id
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] (Re-)joining group
[main] INFO OpenSearchConsumer - Received 0 record(s)
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Successfully joined group with generation Generation{generationId=3, memberId='consumer-consumer-opensearch-demo-1-4cdcf1c2-92c4-443a-9f83-81c741d56067', protocol='range'}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Finished assignment for group at generation 3: {consumer-consumer-opensearch-demo-1-4cdcf1c2-92c4-443a-9f83-81c741d56067=Assignment(partitions=[wikimedia.recentchange-0, wikimedia.recentchange-1, wikimedia.recentchange-2])}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Successfully synced group in generation Generation{generationId=3, memberId='consumer-consumer-opensearch-demo-1-4cdcf1c2-92c4-443a-9f83-81c741d56067', protocol='range'}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Notifying assignor about the new Assignment(partitions=[wikimedia.recentchange-0, wikimedia.recentchange-1, wikimedia.recentchange-2])
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Adding newly assigned partitions: wikimedia.recentchange-2, wikimedia.recentchange-1, wikimedia.recentchange-0
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Setting offset for partition wikimedia.recentchange-2 to the committed offset FetchPosition{offset=235, offsetEpoch=Optional[0], currentLeader=LeaderAndEpoch{leader=Optional[127.0.0.1:9092 (id: 1 rack: null)], epoch=0}}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Setting offset for partition wikimedia.recentchange-1 to the committed offset FetchPosition{offset=252, offsetEpoch=Optional[0], currentLeader=LeaderAndEpoch{leader=Optional[127.0.0.1:9092 (id: 1 rack: null)], epoch=0}}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Setting offset for partition wikimedia.recentchange-0 to the committed offset FetchPosition{offset=257, offsetEpoch=Optional[0], currentLeader=LeaderAndEpoch{leader=Optional[127.0.0.1:9092 (id: 1 rack: null)], epoch=0}}
[main] INFO OpenSearchConsumer - Received 0 record(s)
[main] INFO OpenSearchConsumer - Received 0 record(s)
[main] INFO OpenSearchConsumer - Received 0 record(s)
[main] INFO OpenSearchConsumer - Received 0 record(s)
[main] INFO OpenSearchConsumer - Received 0 record(s)
```


Para restablecer el offset de un grupo de consumidores asi:
```
gerlinorlandotorres@MacBook-Pro-de-Gerlin kafka-stack-docker-compose % kafka-consumer-groups --bootstrap-server localhost:9092 --group consumer-opensearch-demo --reset-offsets --to-earliest --execute --all-topics

Error: Assignments can only be reset if the group 'consumer-opensearch-demo' is inactive, but the current state is Stable.

GROUP                          TOPIC                          PARTITION  NEW-OFFSET     
```
El error anterior ocurre porque existe un consumidor conectado, y para restablecer es necesario que todos los consumidores del grupo esten desconectados. 

Al tener todos los consumidores desconectados volvemos a ejecutar el comando:
```
gerlinorlandotorres@MacBook-Pro-de-Gerlin kafka-stack-docker-compose % kafka-consumer-groups --bootstrap-server localhost:9092 --group consumer-opensearch-demo --reset-offsets --to-earliest --execute --all-topics

GROUP                          TOPIC                          PARTITION  NEW-OFFSET     
consumer-opensearch-demo       wikimedia.recentchange         2          0              
consumer-opensearch-demo       wikimedia.recentchange         1          0              
consumer-opensearch-demo       wikimedia.recentchange         0          0
```
Despues de haber restablecido el offset del grupo de consumidores, ejecutamos un consumidor de este mismo grupo y evidenciaremos que ahora si recibira mensajes:
```
16:06:47: Executing ':kafka-consumer-opensearch:OpenSearchConsumer.main()'...

> Task :kafka-consumer-opensearch:compileJava UP-TO-DATE
> Task :kafka-consumer-opensearch:processResources NO-SOURCE
> Task :kafka-consumer-opensearch:classes UP-TO-DATE

> Task :kafka-consumer-opensearch:OpenSearchConsumer.main()
[main] INFO org.apache.kafka.clients.consumer.ConsumerConfig - ConsumerConfig values: 
	allow.auto.create.topics = true
	auto.commit.interval.ms = 5000
	auto.offset.reset = latest
	bootstrap.servers = [127.0.0.1:9092]
	check.crcs = true
	client.dns.lookup = use_all_dns_ips
	client.id = consumer-consumer-opensearch-demo-1
	client.rack = 
	connections.max.idle.ms = 540000
	default.api.timeout.ms = 60000
	enable.auto.commit = false
	exclude.internal.topics = true
	fetch.max.bytes = 52428800
	fetch.max.wait.ms = 500
	fetch.min.bytes = 1
	group.id = consumer-opensearch-demo
	group.instance.id = null
	heartbeat.interval.ms = 3000
	interceptor.classes = []
	internal.leave.group.on.close = true
	internal.throw.on.fetch.stable.offset.unsupported = false
	isolation.level = read_uncommitted
	key.deserializer = class org.apache.kafka.common.serialization.StringDeserializer
	max.partition.fetch.bytes = 1048576
	max.poll.interval.ms = 300000
	max.poll.records = 500
	metadata.max.age.ms = 300000
	metric.reporters = []
	metrics.num.samples = 2
	metrics.recording.level = INFO
	metrics.sample.window.ms = 30000
	partition.assignment.strategy = [class org.apache.kafka.clients.consumer.RangeAssignor, class org.apache.kafka.clients.consumer.CooperativeStickyAssignor]
	receive.buffer.bytes = 65536
	reconnect.backoff.max.ms = 1000
	reconnect.backoff.ms = 50
	request.timeout.ms = 30000
	retry.backoff.ms = 100
	sasl.client.callback.handler.class = null
	sasl.jaas.config = null
	sasl.kerberos.kinit.cmd = /usr/bin/kinit
	sasl.kerberos.min.time.before.relogin = 60000
	sasl.kerberos.service.name = null
	sasl.kerberos.ticket.renew.jitter = 0.05
	sasl.kerberos.ticket.renew.window.factor = 0.8
	sasl.login.callback.handler.class = null
	sasl.login.class = null
	sasl.login.connect.timeout.ms = null
	sasl.login.read.timeout.ms = null
	sasl.login.refresh.buffer.seconds = 300
	sasl.login.refresh.min.period.seconds = 60
	sasl.login.refresh.window.factor = 0.8
	sasl.login.refresh.window.jitter = 0.05
	sasl.login.retry.backoff.max.ms = 10000
	sasl.login.retry.backoff.ms = 100
	sasl.mechanism = GSSAPI
	sasl.oauthbearer.clock.skew.seconds = 30
	sasl.oauthbearer.expected.audience = null
	sasl.oauthbearer.expected.issuer = null
	sasl.oauthbearer.jwks.endpoint.refresh.ms = 3600000
	sasl.oauthbearer.jwks.endpoint.retry.backoff.max.ms = 10000
	sasl.oauthbearer.jwks.endpoint.retry.backoff.ms = 100
	sasl.oauthbearer.jwks.endpoint.url = null
	sasl.oauthbearer.scope.claim.name = scope
	sasl.oauthbearer.sub.claim.name = sub
	sasl.oauthbearer.token.endpoint.url = null
	security.protocol = PLAINTEXT
	security.providers = null
	send.buffer.bytes = 131072
	session.timeout.ms = 45000
	socket.connection.setup.timeout.max.ms = 30000
	socket.connection.setup.timeout.ms = 10000
	ssl.cipher.suites = null
	ssl.enabled.protocols = [TLSv1.2, TLSv1.3]
	ssl.endpoint.identification.algorithm = https
	ssl.engine.factory.class = null
	ssl.key.password = null
	ssl.keymanager.algorithm = SunX509
	ssl.keystore.certificate.chain = null
	ssl.keystore.key = null
	ssl.keystore.location = null
	ssl.keystore.password = null
	ssl.keystore.type = JKS
	ssl.protocol = TLSv1.3
	ssl.provider = null
	ssl.secure.random.implementation = null
	ssl.trustmanager.algorithm = PKIX
	ssl.truststore.certificates = null
	ssl.truststore.location = null
	ssl.truststore.password = null
	ssl.truststore.type = JKS
	value.deserializer = class org.apache.kafka.common.serialization.StringDeserializer

[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka version: 3.1.0
[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka commitId: 37edeed0777bacb3
[main] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka startTimeMs: 1656770807951
[main] INFO OpenSearchConsumer - The Wikimedia Index already exits
[main] INFO org.apache.kafka.clients.consumer.KafkaConsumer - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Subscribed to topic(s): wikimedia.recentchange
[main] INFO org.apache.kafka.clients.Metadata - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Resetting the last seen epoch of partition wikimedia.recentchange-0 to 0 since the associated topicId changed from null to DGWkrWKyQACUFXm_XTecqw
[main] INFO org.apache.kafka.clients.Metadata - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Resetting the last seen epoch of partition wikimedia.recentchange-2 to 0 since the associated topicId changed from null to DGWkrWKyQACUFXm_XTecqw
[main] INFO org.apache.kafka.clients.Metadata - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Resetting the last seen epoch of partition wikimedia.recentchange-1 to 0 since the associated topicId changed from null to DGWkrWKyQACUFXm_XTecqw
[main] INFO org.apache.kafka.clients.Metadata - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Cluster ID: Uu9pnoNSRxashKBRdPCtfQ
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Discovered group coordinator 127.0.0.1:9092 (id: 2147483646 rack: null)
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] (Re-)joining group
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Request joining group due to: need to re-join with the given member-id
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] (Re-)joining group
[main] INFO OpenSearchConsumer - Received 0 record(s)
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Successfully joined group with generation Generation{generationId=5, memberId='consumer-consumer-opensearch-demo-1-7ef9b828-25e9-4507-b305-50d1430658ce', protocol='range'}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Finished assignment for group at generation 5: {consumer-consumer-opensearch-demo-1-7ef9b828-25e9-4507-b305-50d1430658ce=Assignment(partitions=[wikimedia.recentchange-0, wikimedia.recentchange-1, wikimedia.recentchange-2])}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Successfully synced group in generation Generation{generationId=5, memberId='consumer-consumer-opensearch-demo-1-7ef9b828-25e9-4507-b305-50d1430658ce', protocol='range'}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Notifying assignor about the new Assignment(partitions=[wikimedia.recentchange-0, wikimedia.recentchange-1, wikimedia.recentchange-2])
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Adding newly assigned partitions: wikimedia.recentchange-2, wikimedia.recentchange-1, wikimedia.recentchange-0
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Setting offset for partition wikimedia.recentchange-2 to the committed offset FetchPosition{offset=0, offsetEpoch=Optional.empty, currentLeader=LeaderAndEpoch{leader=Optional[127.0.0.1:9092 (id: 1 rack: null)], epoch=0}}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Setting offset for partition wikimedia.recentchange-1 to the committed offset FetchPosition{offset=0, offsetEpoch=Optional.empty, currentLeader=LeaderAndEpoch{leader=Optional[127.0.0.1:9092 (id: 1 rack: null)], epoch=0}}
[main] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-consumer-opensearch-demo-1, groupId=consumer-opensearch-demo] Setting offset for partition wikimedia.recentchange-0 to the committed offset FetchPosition{offset=0, offsetEpoch=Optional.empty, currentLeader=LeaderAndEpoch{leader=Optional[127.0.0.1:9092 (id: 1 rack: null)], epoch=0}}
[main] INFO OpenSearchConsumer - Received 500 record(s)
[main] INFO OpenSearchConsumer - Inserted 500 record(s).
[main] INFO OpenSearchConsumer - Offsets have been committed!
[main] INFO OpenSearchConsumer - Received 244 record(s)
[main] INFO OpenSearchConsumer - Inserted 244 record(s).
[main] INFO OpenSearchConsumer - Offsets have been committed!
[main] INFO OpenSearchConsumer - Received 0 record(s)
[main] INFO OpenSearchConsumer - Received 0 record(s)
```
# 85. Consumer Internal Threads (Avanzado)
Los consumidores agrupados hablan con un coordinador de grupos de consumidores. El coordinador de grupos de consumidores es un broker en funciones y se usa para detectar si un consumidor esta activo.

Para detectar si un consumidor esta activo, existe 2 mecanismo:
- Heartbeat
- poll

## Consumer Heartbeat Thread (Hilo de los latidos del corazon del consumidor)
Existe un supproceso (Thread) llamado (Heartbeat) que envia datos a Kafka, solo un latido de vez en cuando para decirle al coordinador de consumidores que sigue vivo. 

Por defecto el intervalo del latido del corazon (heartbeat) lo determina una configuracion `heartbeat.interval.ms` (Por defecto 3 segundos), determinando la frecuencia con la que debe enviar latidos.

Existe un parametro que maneja el timeout de la session entre el consumidor y el coordinador, `session.timeout.ms`, es recomendable dar el valor mas bajo posible, pensando principalmente en los rebalances que debe hacerse cada vez que un consumidor se desconecta.

NOTA: es recomendable que el parametro `heartbeat.interval.ms` = `session.timeout.ms`/3

## Consumer Poll Thread (Hilo de la encuesta)

- `max.poll.interval.ms` (Por defecto 5 minutos): 
  Tiempo maximo entre dos llamadas a la funcion `.poll()` antes de pensar que el consumidor esta muerto. Por ejemplo, un consumidor recibio un lote de mensajes y luego los procesa, si dicho procesamiento tarda mas tiempo del configurado en `max.poll.interval.ms` kafka pensara que el consumidor esta atascado.
- `max.poll.records` (Por defecto 500): Es la cantidad maxima de registros que sondea a la vez, es decir, la cantidad de registros que puede recuperarse en un lote de mensajes.
- `fetch.min.bytes` (Por defecto 1): Controla la cantidad minima de datos que quisieras al menos obtener de kafka en cada solicitud.
- `fetch.max.wait.ms` (Por defecto 500): cantidad maxima de tiempo que el broker de kafka se bloqueara antes de responder la solicitud de recuperacion si no hay suficientes bytes de acuerdo a lo especificado en `fetch.min.bytes`. Por ejemplo: Un consumidor realiza una solicitud, kafka se da cuenta que no tiene suficientes bytes (<`fetch.min.bytes`) para dar una respuesta, entonces el broker se tomara tal tiempo (`fetch.max.wait.ms`) a esperas de tener los suficientes bytes para dar una respuesta al consumidor.
- `max.partition.fetch.bytes` (Por defecto 1MB): Es la cantidad maxima de datos por particion que devolvera el servidor, es decir, si la lectura se hace de 100 particiones significa que necesitara al menos 100MB de RAM.
- `fetch.max.bytes` (Por defecto 55MB): Cantidad maxima de datos devueltos para cada solicitud. Si tienes memoria disponible, aumentala para que permitir que su consumidor lea mas datos en cada solicitud.

# 86. Consumer Replica Fetching - Rack Awareness
Resaltar que desde la version 2.4 de Kafka, existe una funcionalidad que si existen particiones en diferentes centros de datos, los consumidores podran leer los mensajes de la particion que este en su mismo centro de datos, esto con la ventaja de que los costos entre los datos que viajan entre distintos centros de datos sean menos.




