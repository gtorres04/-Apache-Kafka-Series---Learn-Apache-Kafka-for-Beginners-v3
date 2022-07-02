# 87. Kafka Extended APIs - Overview
Elñ Ecosistema de kafka no solo se basa en productores y consumidores. Con el tiempo se ha enriquesido el ecosistema con otras herramientas como:
- `Kafka Connect`: Resuelve los problema de integrar fuentes externas -> kafka y desde Kafka -> External Sink
- `Kafka Stream`: Resuelve problemas de transformacion de mensajes de un topico a otro topico, evitando usar consumidores y productores de por medio.
- `Schema Registry`: Ayuda al uso de Schemas en Kafka

Ademas veremos como los datos de Wikimedia se enviaran a Kafka mediante un conector, despues mediante kafka stream haremos una aplicacion de contador y calcularemos algunas estadisticas sobre nuestros conjunto de datos y despues usaremos Kafka Connect ElasticSearch Sink para enviar datos a OpenSearch.
# 88. Kafka Connect Introduction
Referencias:

https://www.confluent.io/product/connectors/

## ¿Porque Kafka Connect?
- Los programadores siempre quieren importar datos siempre de las misma fuentes 

# 89. Kafka Connect Hands On: Warning
# 90. Kafka Connect Wikimedia & ElasticSearch Hands On
Referencias:

https://www.confluent.io/hub/

https://github.com/conduktor/kafka-connect-wikimedia


# 91. Kafka Streams Introduction
# 92. Kafka Streams Hands-On
# 93. Kafka Schema Registry Introduction
# 94. Kafka Schema Registry Hands On
# 95. Which Kafka API should I use?

NOTA: No se toma nota ya que es introductorio toda la sesion