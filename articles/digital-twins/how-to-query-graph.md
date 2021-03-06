---
title: Consulta del grafo de gemelos
titleSuffix: Azure Digital Twins
description: Vea cómo consultar el grafo gemelo de Azure Digital Twins para información.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 127fd9a9e47a85479018524998e33f44b0a65ba8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078483"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Consulta del grafo gemelo de Azure Digital Twins

En este artículo se ofrecen ejemplos y más detalles sobre el uso del [lenguaje de consultas de Azure Digital Twins](concepts-query-language.md) para consultar el [grafo de gemelos](concepts-twins-graph.md) y obtener información. Las consultas se ejecutan en el grafo con las [**API de consulta**](how-to-use-apis-sdks.md) de Azure Digital Twins.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

En el resto de este artículo se proporcionan ejemplos de cómo usar estas operaciones.

## <a name="query-syntax"></a>Sintaxis de consulta

Esta sección incluye consultas de ejemplo que muestran la estructura del lenguaje de consulta y realizan las operaciones de consulta posibles en [Digital Twins](concepts-twins-graph.md).

### <a name="show-all-existing-digital-twins"></a>Mostrar todos los gemelos digitales existentes

Esta es la consulta básica que devolverá una lista de todos los gemelos digitales de la instancia:

```sql
SELECT *
FROM DIGITALTWINS
```

### <a name="select-top-items"></a>Selección de los elementos principales

Puede seleccionar los diversos elementos "principales" en una consulta mediante la cláusula `Select TOP`.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="count-items"></a>Recuento de elementos

Puede contar el número de elementos de un conjunto de resultados mediante la cláusula `Select COUNT`:

```sql
SELECT COUNT() 
FROM DIGITALTWINS
``` 

Agregue una cláusula `WHERE` para contar el número de elementos que cumplen determinados criterios. Estos son algunos ejemplos de recuento con un filtro aplicado basado en el tipo de modelo gemelo (para obtener más información sobre esta sintaxis, vea [*Consulta por modelo*](#query-by-model) a continuación):

```sql
SELECT COUNT() 
FROM DIGITALTWINS 
WHERE IS_OF_MODEL('dtmi:sample:Room;1') 
SELECT COUNT() 
FROM DIGITALTWINS c 
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

También puede usar `COUNT` junto con la cláusula `JOIN`. Esta es una consulta que cuenta todas las bombillas incluidas en los paneles de luz de los salones 1 y 2:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="specify-return-set-with-projections"></a>Especificación del conjunto de devoluciones con proyecciones

Con las proyecciones, puede elegir las columnas que devolverá una consulta. 

>[!NOTE]
>En este momento, no se admiten las propiedades complejas. Para asegurarse de que las propiedades de proyección son válidas, combine las proyecciones con una comprobación de `IS_PRIMITIVE`. 

Este es un ejemplo de una consulta que usa una proyección para devolver gemelos y relaciones. La siguiente consulta proyecta los valores *Consumidor*, *Fábrica* y *Perimetral* de un escenario en el que una *Fábrica* con un identificador de *ABC* está relacionado con el *Consumidor* mediante una relación de *Factory.customer*, y esa relación se presenta como *Perimetral*.

```sql
SELECT Consumer, Factory, Edge 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
```

También puede usar la proyección para devolver una propiedad de un gemelo. La siguiente consulta proyecta la propiedad *Nombre* de los *Consumidores* que están relacionados con la *Fábrica* con un identificador de *ABC* mediante una relación de *Factory.customer*. 

```sql
SELECT Consumer.name 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Consumer.name)
```

También puede usar la proyección para devolver una propiedad de una relación. Al igual que en el ejemplo anterior, la siguiente consulta proyecta la propiedad *Nombre* de los *Consumidores* relacionados con la *Fábrica* con un identificador de *ABC* mediante una relación de *Factory.customer*; pero ahora también devuelve dos propiedades de esa relación, *Prop1* y *prop2*. Para ello, se asigna un nombre a la relación *Perimetral* y se recopilan sus propiedades.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

También puede utilizar alias para simplificar las consultas con proyección.

La siguiente consulta realiza las mismas operaciones que en el ejemplo anterior, pero asigna alias a los nombres de propiedades para `consumerName`, `first`, `second` y `factoryArea`. 
 
```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)" 
```

Esta es una consulta similar que consulta el mismo conjunto que en el caso anterior, pero solo proyecta la propiedad *Consumer.name* como `consumerName` y proyecta la *Fábrica* completa como gemela. 

```sql
SELECT Consumer.name AS consumerName, Factory 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) 
```

### <a name="query-by-property"></a>Consulta por propiedad

Obtenga instancias de Digital Twins por **propiedades** (incluidos el identificador y los metadatos):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> El identificador de un gemelo digital se consulta con el campo de metadatos `$dtId`.

También puede obtener instancias de Digital Twins en función de **si una propiedad determinada está definida**. Esta es una consulta que obtiene instancias de Digital Twins que tienen una propiedad *Location* definida:

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Esto le puede ayudar a obtener instancias de Digital Twins por sus propiedades de *etiqueta*, como se describe en [Incorporación de etiquetas a gemelos digitales](how-to-use-tags.md). Esta es una consulta que obtiene todas las instancias de Digital Twins etiquetadas con *red*:

```sql
select * from digitaltwins where is_defined(tags.red) 
```

También puede obtener instancias de Digital Twins en función del **tipo de una propiedad**. Esta es una consulta que obtiene instancias de Digital Twins cuya propiedad *Temperature* es un número:

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>Consulta por modelo

El operador `IS_OF_MODEL` se puede usar para filtrar en función del [**modelo**](concepts-models.md) de la instancia de Digital Twins. Admite la herencia y tiene varias opciones de sobrecarga.

El uso más simple de `IS_OF_MODEL` solo toma un parámetro `twinTypeName`: `IS_OF_MODEL(twinTypeName)`.
A continuación, se muestra un ejemplo de consulta que pasa un valor en este parámetro:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Para especificar una colección gemela para buscar cuando hay más de uno (como cuando se usa `JOIN`), agregue el parámetro `twinCollection`: `IS_OF_MODEL(twinCollection, twinTypeName)`.
A continuación, se muestra un ejemplo de consulta que agrega un valor en este parámetro:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

Para realizar una coincidencia exacta, agregue el parámetro `exact`: `IS_OF_MODEL(twinTypeName, exact)`.
A continuación, se muestra un ejemplo de consulta que agrega un valor en este parámetro:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

También puede pasar los tres argumentos juntos: `IS_OF_MODEL(twinCollection, twinTypeName, exact)`.
A continuación, se muestra un ejemplo de consulta que especifica un valor para los tres parámetros:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Consulta basada en relaciones

Al realizar consultas basadas en **relaciones** de Digital Twins, el lenguaje de consultas de Azure Digital Twins tiene una sintaxis especial.

Las relaciones se extraen en el ámbito de la consulta en la cláusula `FROM`. Una diferencia importante de los lenguajes de tipo SQL "clásico" es que cada expresión de esta cláusula `FROM` no es una tabla; en su lugar, la cláusula `FROM` expresa un recorrido de relación entre entidades y se escribe con una versión de Azure Digital Twins de `JOIN`. 

Recuerde que, con las funcionalidades del [modelo](concepts-models.md) de Azure Digital Twins, las relaciones no existen de forma independiente de los gemelos. Esto significa que la operación `JOIN` del lenguaje de consultas de Azure Digital Twins es un poco diferente de la operación `JOIN` general de SQL, ya que las relaciones aquí no se pueden consultar de forma independiente y deben estar vinculadas a un gemelo.
Para incorporar esta diferencia, se usa la palabra clave `RELATED` en la cláusula `JOIN` para hacer referencia a un conjunto de relaciones de un gemelo. 

En la siguiente sección se proporcionan varios ejemplos de lo que se ve a continuación.

> [!TIP]
> Conceptualmente, esta característica imita la funcionalidad centrada en documentos de CosmosDB, donde se puede llevar a cabo `JOIN` en los objetos secundarios dentro de un documento. CosmosDB usa la palabra clave `IN` para indicar que `JOIN` está pensado para recorrer en iteración los elementos de la matriz en el documento de contexto actual.

#### <a name="relationship-based-query-examples"></a>Ejemplos de consultas basadas en relaciones

Para obtener un conjunto de resultados que incluya relaciones, use una única instrucción `FROM` seguida de N instrucciones `JOIN`, donde las instrucciones `JOIN` expresan relaciones en el resultado de una instrucción `FROM` o `JOIN` anterior.

A continuación se muestra un ejemplo de consulta basada en relaciones. Este fragmento de código selecciona todos los gemelos digitales con una propiedad *ID* "ABC" y todos los gemelos digitales relacionados con estos gemelos digitales a través de una relación *contains*. 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> El desarrollador no necesita poner en correlación esta operación `JOIN` con un valor de clave en la cláusula `WHERE` (ni especificar un valor de clave insertado con la definición de `JOIN`). El sistema calcula esta correlación automáticamente, ya que las propias propiedades de la relación identifican la entidad de destino.

#### <a name="query-the-properties-of-a-relationship"></a>Consulta de las propiedades de una relación

Del mismo modo que los gemelos digitales tienen propiedades que se describen a través de DTDL, las relaciones también pueden tener propiedades. Puede consultar instancias de Digital Twins **en función de las propiedades de sus relaciones**.
El lenguaje de consultas de Azure Digital Twins permite filtrar y proyectar relaciones mediante la asignación de un alias a la relación dentro de la cláusula `JOIN`. 

Como ejemplo, considere una relación *servicedBy* que tiene una propiedad *reportedCondition*. En la consulta siguiente, a esta relación se le asigna el alias "R" para hacer referencia a su propiedad.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

En el ejemplo anterior, observe cómo *reportedCondition* es una propiedad de la propia relación *servicedBy* (NO de un gemelo digital que tiene una relación *servicedBy*).

### <a name="query-with-multiple-joins"></a>Consulta con varias combinaciones JOIN

Actualmente en versión preliminar, una consulta única admite hasta cinco `JOIN`. Esto le permite atravesar varios niveles de relaciones a la vez.

Este es un ejemplo de una consulta de varias combinaciones, que obtiene todas las bombillas contenidas en los paneles de luz de las salas 1 y 2.

```sql
SELECT LightBulb 
FROM DIGITALTWINS Room 
JOIN LightPanel RELATED Room.contains 
JOIN LightBulb RELATED LightPanel.contains 
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1') 
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1') 
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="other-compound-query-examples"></a>Otros ejemplos de consultas compuestas

Puede **combinar** cualquiera de los tipos de consulta anteriores mediante operadores de combinación con el fin de incluir más detalles en una sola consulta. A continuación se muestran algunos ejemplos adicionales de consultas compuestas que realizan consultas para más de un tipo de descriptor de instancia de Digital Twins a la vez.

| Descripción | Consultar |
| --- | --- |
| De entre los dispositivos que tiene *Room 123*, se devuelven los dispositivos MxChip que tienen el rol de operador. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Se obtienen las instancias de Digital Twins que tienen una relación denominada *Contains* con otra instancia que tiene un identificador *id1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Se obtienen todas las salas de este modelo de sala contenidos en *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="reference-expressions-and-conditions"></a>Referencia: Expresiones y condiciones

Esta sección contiene la referencia de los operadores y las funciones disponibles al escribir consultas de Azure Digital Twins.

### <a name="operators"></a>Operadores

Se admiten los siguientes operadores:

| Familia | Operadores |
| --- | --- |
| Lógicos |AND, OR, NOT |
| De comparación |=, !=, <, >, <=, >= |
| Contiene | IN, NIN |

### <a name="functions"></a>Functions

Se admiten las funciones de conversión y comprobación de tipos siguientes:

| Función | Descripción |
| -------- | ----------- |
| IS_DEFINED | Devuelve un valor booleano que indica si se ha asignado un valor a la propiedad. Solo se admite cuando el valor es de tipo primitivo. Los tipos primitivos son cadena, booleano, numérico o `null`. No se admiten los valores DateTime, los tipos de objeto ni las matrices. |
| IS_OF_MODEL | Devuelve un valor booleano que indica si el gemelo especificado coincide con el tipo de modelo especificado. |
| IS_BOOL | Devuelve un valor booleano que indica si el tipo de la expresión especificada es un valor booleano. |
| IS_NUMBER | Devuelve un valor booleano que indica si el tipo de la expresión especificada es un número. |
| IS_STRING | Devuelve un valor booleano que indica si el tipo de la expresión especificada es una cadena. |
| IS_NULL | Devuelve un valor booleano que indica si el tipo de la expresión especificada es nulo. |
| IS_PRIMITIVE | Devuelve un valor booleano que indica si el tipo de la expresión especificada es un tipo primitivo (cadena, booleano, numérico o `null`). |
| IS_OBJECT | Devuelve un valor booleano que indica si el tipo de la expresión especificada es un objeto JSON. |

Se admiten las siguientes funciones de cadena:

| Función | Descripción |
| -------- | ----------- |
| STARTSWITH(x, y) | Devuelve un valor booleano que indica si la primera expresión de cadena empieza con la segunda. |
| ENDSWITH(x, y) | Devuelve un valor booleano que indica si la primera expresión de cadena finaliza con la segunda. |

## <a name="run-queries-with-an-api-call"></a>Ejecución de consultas con una llamada a la API

Una vez que haya decidido una cadena de consulta, puede ejecutarla realizando una llamada a la **API de consulta**.
En el fragmento de código siguiente se muestra esta llamada desde la aplicación cliente:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Esta llamada devuelve los resultados de la consulta en forma de un objeto QueryResult. 

Las llamadas de consulta admiten la paginación. Este es un ejemplo completo con control de errores y paginación:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Limitaciones de las consultas

Puede haber un retraso de hasta 10 segundos antes de que los cambios en la instancia se reflejen en las consultas. Por ejemplo, si completa una operación como la creación o la eliminación de gemelos con la API de DigitalTwins, es posible que el resultado no se refleje inmediatamente en las solicitudes de la API de consulta. Para que se resuelva debería bastar con esperar un poco.

Hay otras limitaciones en el uso de `JOIN` durante la versión preliminar.
* No se admiten subconsultas en la instrucción `FROM`.
* No se admite la semántica `OUTER JOIN`, lo que significa que, si la relación tiene un rango de cero, se elimina toda la "fila" del conjunto de resultados de salida.
* Durante la versión preliminar, la profundidad transversal del grafo está restringida a cinco niveles `JOIN` por consulta.
* El origen de las operaciones `JOIN` está restringido: la consulta debe declarar los gemelos donde comienza la consulta.

## <a name="query-best-practices"></a>Procedimientos recomendados sobre las consultas

A continuación se muestran algunas sugerencias para realizar consultas con Azure Digital Twins.

* Considere el patrón de consulta durante la fase de diseño del modelo. Intente asegurarse de que las relaciones que deben responderse en una sola consulta se modelan como una relación de un solo nivel.
* Diseñe las propiedades de forma que se eviten grandes conjuntos de resultados del recorrido del grafo.
* Puede reducir significativamente el número de consultas que necesita si crea una matriz de gemelos y consulta con el operador `IN`. Por ejemplo, considere un escenario en el que *Buildings* contenga a *Floors* y *Floors* contenga a *Rooms*. Para buscar las habitaciones que estén activas dentro de un edificio, puede:

    1. Buscar pisos en el edificio en función de la relación `contains`
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. Para buscar las habitaciones, en lugar de tener que considerar los pisos uno por uno y ejecutar una consulta `JOIN` para buscar las habitaciones de cada uno, puede realizar consultas con una colección de los pisos del edificio (con el nombre *Floor* en la consulta siguiente).

        En la aplicación cliente:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        En la consulta:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* Los nombres y valores de propiedad distinguen mayúsculas de minúsculas, por lo que debe tener cuidado de usar los nombres exactos definidos en los modelos. Si los nombres de propiedad están mal escritos o usan las mayúsculas de forma incorrecta, el conjunto de resultados está vacío y no se devuelven errores.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las [API y los SDK de Azure Digital Twins](how-to-use-apis-sdks.md), incluida la API de consulta que se usa para ejecutar las consultas de este artículo.
