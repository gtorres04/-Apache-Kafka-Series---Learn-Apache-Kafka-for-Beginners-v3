# Avro Primitive Types
- Los tipos primitivos son los tipos del soporte base:
  - `null`: no value
  - `boolean`: valor binario, true o false.
  - `int` (32-bit): numerico
  - `float` (32-bit): numerico punto flotante (IEEE 754)
  - `double` (64-bit): numerico punto flotante (IEEE 754)
  - `bytes`: Secuence of 8-bit unsigned bytes
  - `string`: secuencia de caracteres unicode.

Ejemplo de como se declararia un tipo de datos primitivo:

0-primitive-schema.avsc
```
{"type": "string"} 
```
# Avro Record Schema Definition
- Avro Record Schemas son definidos usando JSON
- Esto son algunos de los campos comunes:
  - `Name`: Nombre del schema
  - `Namespace`: Un Espacio de nombre para el esquema
  - `Doc`: Documentacion del schema
  - `Aliases` (Opcional): Otros nombres para tu esquema
  - `Fields`: Es un Arreglo, donde se especifica los campos del schema
    - `Name`: Nombre del campo
    - `Doc`: Documentacion del campo
    - `Type`: Tipo de datos del campo
    - `Default`: Valor por defecto del campo
## Practice 1
Definir un schema Avro para registros de tipo cliente con los siguientes atributos:
- First Name
- Last Name
- Age
- Height
- Weight
- Automated email turned on (Boolean, default true)

Solucion:
```
{
     "type": "record",
     "namespace": "com.example",
     "name": "Customer",
     "doc": "Avro Schema for our Customer",     
     "fields": [
       { "name": "first_name", "type": "string", "doc": "First Name of Customer" },
       { "name": "last_name", "type": "string", "doc": "Last Name of Customer" },
       { "name": "age", "type": "int", "doc": "Age at the time of registration" },
       { "name": "height", "type": "float", "doc": "Height at the time of registration in cm" },
       { "name": "weight", "type": "float", "doc": "Weight at the time of registration in kg" },
       { "name": "automated_email", "type": "boolean", "default": true, "doc": "Field indicating if the user is enrolled in marketing emails" }
     ]
}
```
# Avro Complex Types

In Avro hay tipos complejos, tal como:
- Enums
```
{ "name": "type", "type": "enum", "symbols": ["PO BOX", "RESIDENTIAL", "ENTERPRISE"] }
```
- Arrays
```
{ "name": "emails", "type": "array", "items": "string"}
```
- Maps
```
{ "name": "maps", "type": "map", "values": "string"}
```
- Unions
Permite que un valor de un campo tome tipos de datos diferentes. Si es especificado el campo `default` este debe coincidir estrictamente con el primer tipo de dato especificado.

El caso mas comun podria ser este:
```
{ "name": "middle_name", "type": ["null","string"], "default": null}
```
NOTA: Para el campo `default` su valor no se escribe con comillas, debe ser escrito sin comillas, asi no es confundido por un valor de tipo string.
- Llamadas a otros esquemas como tipos.

fijese en el campo `customer_address`
```
[
  {
      "type": "record",
      "namespace": "com.example",
      "name": "CustomerAddress",
      "fields": [
        { "name": "address", "type": "string" },
        { "name": "city", "type": "string" },
        { "name": "postcode", "type": ["string", "int"] },
        { "name": "type", "type": "enum", "symbols": ["PO BOX", "RESIDENTIAL", "ENTERPRISE"] }
      ]
  },
  {
     "type": "record",
     "namespace": "com.example",
     "name": "Customer",
     "fields": [
       { "name": "first_name", "type": "string" },
       { "name": "middle_name", "type": ["null", "string"], "default": null },       
       { "name": "last_name", "type": "string" },
       { "name": "age", "type": "int" },
       { "name": "height", "type": "float" },
       { "name": "weight", "type": "float" },
       { "name": "automated_email", "type": "boolean", "default": true },
       { "name": "customer_emails", "type": "array", "items": "string", "default": []},
       { "name": "customer_address", "type": "com.example.CustomerAddress" }
     ]
}]
```
Sin embargo podria hacerse asi tambien:
```
[
  {
     "type": "record",
     "namespace": "com.example",
     "name": "Customer",
     "fields": [
       { "name": "first_name", "type": "string" },
       { "name": "middle_name", "type": ["null", "string"], "default": null },       
       { "name": "last_name", "type": "string" },
       { "name": "age", "type": "int" },
       { "name": "height", "type": "float" },
       { "name": "weight", "type": "float" },
       { "name": "automated_email", "type": "boolean", "default": true },
       { "name": "customer_emails", "type": "array", "items": "string", "default": []},
       { "name": "customer_address", "type": 
          {
            "type": "record",
            "name": "CustomerAddress",
            "fields": [
              { "name": "address", "type": "string" },
              { "name": "city", "type": "string" },
              { "name": "postcode", "type": ["string", "int"] },
              { "name": "type", "type": "enum", "symbols": ["PO BOX", "RESIDENTIAL", "ENTERPRISE"] }
            ]
          }
        }
     ]
}]
```
# Avro Logical Types
Avro tiene el concepto de tipo con logica para dar mas significado a los tipos de datos primitivos.

Los mas conmunmente usados son:
- `decimals`
- `date` (int)
- `time-millis` (long): numero de milisegundos despues de la medianoche
- `timestamp-millis` (long): el numero de milisegundos desde unix epoch (1 January 1970 00:00:00.000 UTC)

## Como usar la logica de tipos

```
{ "name": "signup_ts", "type": "long", "logicalType": "timestamp-millis" }
```
NOTA: los tipos con logica estan desde la version 1.7.7, no es soportado por todos lo lenguajes y no aplica para los tipos UNIOS. 