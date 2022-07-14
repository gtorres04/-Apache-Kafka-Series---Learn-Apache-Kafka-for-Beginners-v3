# The need of schemas in kafka
## The need for a schema registry
- Kafka toma bytes como una entrada y los publica.
- Kafka no realiza ningun tipo de verirficacion de la entrada.
- Kafka no conoce si lo que entra es un String, JSON etc. Solo recibe un BYTE.
- Kafka entregas a los consumidores el bytes tal cual.

Esto nos da para preguntarnos lo siguiente:
- ¿Que pasa si el productor envia algun dato mal?
- ¿Que si algun campo fue renombrado?
- ¿Que si el formato del dato ha cambiado de un dia para el otro?
La respuesta es que los consumidores se paran. Entonces todo la capacidad de realtime se para. 

Entonces es necesario:
- Que el dato se describa el mismo.
- Ser capas de que el dato evolucione sin para a los consumidores.

En conclusion es necesario trabajar con SCHEMAS y un registro de SCHEMAS. 

## Schema Registry
- Es un componente separado.
- Los productores y consumidores son capas de hablar con el registro de esquema.
- Es capas de rechazar un dato malo.
- Se acuerda un formato comun del dato (Apache Avro).
  - Soportando Schema
  - Soprtando la evolucion de los schemas
  - Y es liviano.

# Architecture for kafka with the schema registry and REST Proxy

## Kafka Ecosystem: Confluence Schema Registry

El paso a paso general de lo que sucede es:

1. El productor escribe en el cluster de kafka informacion en formato Avro.
2. El Productor registra el schema en Kafka Schema Registry.
3. El consumidor lee nuestra informacion desde kafka, y ademas recupera el schema del Kafka Registry.

## Kafka Ecosystem: Coonfluence REST Proxy


