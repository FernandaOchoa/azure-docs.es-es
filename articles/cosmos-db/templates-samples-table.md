---
title: Plantillas de Resource Manager para Azure Cosmos DB Table API
description: Use las plantillas de Azure Resource Manager para crear y configurar Table API de Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: c77c917cf6063b787dc2972f5ee7db3329e0e743
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283466"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Administrar recursos de Table API de Azure Cosmos DB con plantillas de Azure Resource Manager

En este artículo, aprenderá a usar plantillas de Azure Resource Manager para ayudarle a implementar y administrar las cuentas, bases de datos y contenedores de Azure Cosmos DB.

Este artículo solo contiene ejemplos de las cuentas de Table API. Para buscar ejemplos de otras cuentas de tipo API, consulte los artículos sobre el uso de plantillas de Azure Resource Manager con la API de Azure Cosmos DB para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md) y [SQL](manage-sql-with-resource-manager.md).

> [!IMPORTANT]
>
> * Los nombres de cuenta están limitados a 44 caracteres, todo en minúsculas.
> * Para cambiar los valores de rendimiento, vuelva a implementar la plantilla con las RU/s actualizadas.
> * Cuando agrega o elimina ubicaciones en una cuenta de Azure Cosmos, no puede modificar otras propiedades simultáneamente. Estas operaciones se deben realizar por separado.

Para crear cualquiera de los siguientes recursos de Azure Cosmos DB, copie la siguiente plantilla de ejemplo en un archivo JSON nuevo. También puede crear un archivo JSON de parámetros para usar al implementar varias instancias del mismo recurso con distintos nombres y valores. Hay muchas maneras de implementar plantillas de Azure Resource Manager, como, [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), la [CLI de Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) y [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Para habilitar rendimiento compartido cuando se usa Table API, habilite el rendimiento de nivel de cuenta en Azure Portal.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Cuenta de Azure Cosmos para Table con rendimiento de escalabilidad automática

Esta plantilla creará una cuenta de Azure Cosmos para Table API con una tabla con rendimiento de escalabilidad automática. Esta plantilla también está disponible para implementar con un solo clic desde la galería de plantillas de inicio rápido de Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementación en Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Cuenta de Azure Cosmos para Table con rendimiento aprovisionado estándar

Esta plantilla creará una cuenta de Azure Cosmos para Table API con una tabla con rendimiento estándar. Esta plantilla también está disponible para implementar con un solo clic desde la galería de plantillas de inicio rápido de Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementación en Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Pasos siguientes

Estos son algunos recursos adicionales:

* [Documentación de Azure Resource Manager](/azure/azure-resource-manager/)
* [Esquema del proveedor de recursos de Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Plantillas de inicio rápido de Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Solución de errores comunes de implementación de Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
