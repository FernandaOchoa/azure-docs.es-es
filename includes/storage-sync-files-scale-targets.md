---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 95c553d26a3e79b53106b933c629c5884c3e004c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "84466893"
---
| Recurso | Destino | Límite máximo |
|----------|--------------|------------|
| Servicios de sincronización de almacenamiento por región | 100 servicios de sincronización de Storage | Sí |
| Grupos de sincronización por servicio de sincronización de almacenamiento | 200 grupos de sincronización | Sí |
| Servidores registrados por servicio de sincronización de almacenamiento | 99 servidores | Sí |
| Puntos de conexión en la nube por grupo de sincronización | 1 punto de conexión en la nube | Sí |
| Puntos de conexión del servidor por grupo de sincronización | 50 puntos de conexión del servidor | No |
| Puntos de conexión del servidor por servidor | 30 puntos de conexión de servidor | Sí |
| Objetos del sistema de archivos (archivos y directorios) por grupo de sincronización | 100 millones de objetos | No |
| Número máximo de objetos del sistema de archivos (archivos y directorios) en un directorio | 5 millones de objetos | Sí |
| Tamaño máximo del descriptor de seguridad de (archivos y directorios) del objeto | 64 KiB | Sí |
| Tamaño de archivo | 100 GiB | No |
| Tamaño mínimo de un archivo que se va a organizar en niveles | V9: basado en el tamaño del clúster del sistema de archivos (tamaño del clúster del sistema de archivos doble). Por ejemplo, si el tamaño del clúster del sistema de archivos es 4 Kb, el tamaño mínimo del archivo será de 8 Kb.<br> V8 y anteriores: 64 KiB  | Sí |

> [!Note]  
> Un punto de conexión de Azure File Sync puede escalar verticalmente hasta el tamaño de un recurso compartido de archivos de Azure. Si se alcanza el límite de tamaño de recurso compartido de archivos de Azure, la sincronización no podrá funcionar.
