---
title: 'Script de la CLI de Azure: escalado y supervisión de Azure Database for PostgreSQL'
description: 'Ejemplo de script de la CLI de Azure: escalado del servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) a un nivel de rendimiento diferente después de consultar las métricas.'
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: d5f08e764ae44b11ab5838689e7f8a70c89b3122
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708635"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Supervisión y escalado de un solo servidor PostgreSQL mediante la CLI de Azure
Este script de la CLI de ejemplo escala el proceso y el almacenamiento de un servidor individual de Azure Database for PostgreSQL después de consultar las métricas. La capacidad de procesamiento se puede ampliar o reducir. El almacenamiento solo se puede ampliar. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide ejecutar la CLI localmente, este artículo necesita la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para comprobar la versión. Consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli) para instalar o actualizar su versión de la CLI de Azure.

## <a name="sample-script"></a>Script de ejemplo
Actualice el script con el identificador de su suscripción.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Use el comando siguiente para quitar el grupo de recursos y todos los recursos asociados a él después de ejecutarse el script de ejemplo. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los comandos que se describen en la tabla siguiente:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Crea un servidor de PostgreSQL que hospeda las bases de datos. |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | Actualiza las propiedades del servidor PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Especifica el valor de métrica de los recursos. |
| [az group delete](/cli/azure/group) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre el [proceso y el almacenamiento de Azure Database for PostgreSQL](../concepts-pricing-tiers.md)
- Pruebe otros scripts adicionales: [Ejemplos de la CLI de Azure para Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- Más información sobre la [CLI de Azure](/cli/azure)
