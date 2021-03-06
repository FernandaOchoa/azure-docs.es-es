---
title: INDEX_OF en el lenguaje de consulta de Azure Cosmos DB
description: Aprenda sobre la función del sistema SQL INDEX_OF en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "71350993"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
 Devuelve la posición inicial de la primera aparición de la expresión de la segunda cadena dentro de la primera expresión de cadena especificada, o -1 si no se encuentra la cadena.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   La expresión de cadena en la que se va a buscar.  
  
*str_expr2*  
   La expresión de cadena que se va a buscar.  

*numeric_expr* Expresión numérica opcional que establece la posición en la que se iniciará la búsqueda. La primera posición en *str_expr1* es 0. 
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se devuelve el índice de varias subcadenas dentro de "abc".  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de cadena Azure Cosmos DB](sql-query-string-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](introduction.md)
