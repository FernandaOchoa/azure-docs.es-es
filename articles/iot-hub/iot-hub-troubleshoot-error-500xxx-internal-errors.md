---
title: Solución de problemas de errores internos 500xxx de Azure IoT Hub
description: Corrección de errores internos 500xxx
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f83ec4d09389d91c2dd427c9840885c0b829eafb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150306"
---
# <a name="500xxx-internal-errors"></a>500xxx Errores internos

En este artículo se describen las causas y las soluciones de los **errores internos 500xxx**.

## <a name="symptoms"></a>Síntomas

La solicitud a IoT Hub produce un error que comienza con 500 y un tipo de "error de servidor". Las posibilidades son las siguientes:

* **500001 ServerError**: IoT Hub genera un error en el servidor.

* **500008 GenericTimeout**: IoT Hub no pudo completar la solicitud de conexión antes de agotar el tiempo de expiración.

* **ServiceUnavailable (sin código de error)** : IoT Hub encontró un error interno.

* **InternalServerError (sin código de error)** : IoT Hub encontró un error interno.

## <a name="cause"></a>Causa

Puede haber varias causas para una respuesta de error 500xxx. En todos los casos, lo más probable es que el problema sea transitorio. Aunque el equipo de IoT Hub trabaja duro para mantener [el SLA (Acuerdo de Nivel de Servicio)](https://azure.microsoft.com/support/legal/sla/iot-hub/), hay pequeños subconjuntos de nodos de IoT Hub que, en ocasiones, pueden experimentar errores transitorios. Cuando el dispositivo trata de conectarse a un nodo que está teniendo problemas, recibirá este error.

## <a name="solution"></a>Solución

Para mitigar los errores 500xxx, ejecute un reintento desde el dispositivo. Para [administrar automáticamente los reintentos](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), asegúrese de usar la versión más reciente de los [SDK de Azure IoT](./iot-hub-devguide-sdks.md). Consulte [Control de errores transitorios](/azure/architecture/best-practices/transient-faults) para ver procedimientos recomendados sobre reintentos y cómo controlar errores transitorios.  Si el problema persiste, compruebe [Resource Health](./iot-hub-monitor-resource-health.md#use-azure-resource-health) y [Estado de Azure](https://status.azure.com/) para ver si IoT Hub tiene un problema conocido. También puede usar la [característica de conmutación por error manual](./tutorial-manual-failover.md). Si no hay ningún problema conocido y el problema continúa, [póngase en contacto con el equipo de soporte técnico](https://azure.microsoft.com/support/options/) para que lo investigue.