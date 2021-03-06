---
title: Descarga y notas de la versión del emulador de Azure Cosmos
description: Obtenga las notas de la versión del emulador de Azure Cosmos para distintas versiones e información de descarga.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: f2a40744053ccc804b2513faf1bc2879d4fde902
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777180"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Emulador de Azure Cosmos: notas de la versión e información de descarga.

En este artículo se muestran las notas de la versión del emulador de Azure Cosmos con una lista de actualizaciones de las características que se realizaron en cada versión. También muestra la versión más reciente del emulador que se va a descargar y usar.

## <a name="download"></a>Descargar

| | |
|---------|---------|
|**Descarga de MSI**|[Centro de descarga de Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Introducción**|[Uso del emulador de Azure Cosmos para desarrollo y pruebas locales](local-emulator.md)|

## <a name="release-notes"></a>Notas de la versión

### <a name="2116-6-october-2020"></a>2.11.6 (6 de octubre de 2020)

 - Esta versión soluciona un problema relacionado con la simultaneidad que permite crear varios contenedores al mismo tiempo. En tales casos, los datos del emulador se quedan en un estado dañado y las siguientes solicitudes de API al punto de conexión del emulador podrían generar errores del tipo "servicio no disponible", que requieren un reinicio y un restablecimiento de los datos locales del emulador.

### <a name="2115-23-august-2020"></a>2.11.5 (23 de agosto 2020)

En esta versión se agregan dos nuevas opciones de inicio del emulador de Cosmos: 

* "/EnablePreview": habilita las características en versión preliminar del emulador. Características en versión preliminar que todavía están en desarrollo y a las que se puede acceder a través de la escritura de ejemplos y de CI.
* "/EnableAadAuthentication": permite que el emulador acepte tokens de Azure Active Directory personalizados como alternativa a las claves principales de Azure Cosmos. Esta característica aún está en desarrollo; actualmente no se admiten asignaciones de roles específicas y otros valores de configuración relacionados con los permisos.

### <a name="2112-07-july-2020"></a>2.11.2 (7 de julio de 2020)

- Esta versión cambia el modo en que se recopilan los seguimientos de ETL necesarios al solucionar problemas del emulador de Cosmos. Las herramientas del entorno de ejecución de rendimiento de Windows (WPR) son ahora las predeterminadas para capturar seguimientos basados en ETL; está en desuso la antigua captura basada en LOGMAN. Este cambio es necesario en parte porque las actualizaciones de seguridad más recientes de Windows afectaron de manera inesperada al funcionamiento de LOGMAN cuando se ejecuta mediante el emulador de Cosmos.

### <a name="2111-10-june-2020"></a>2.11.1 (10 de junio de 2020)

- En esta versión se corrigen errores relacionados con el Explorador de datos del emulador. En ciertos casos, cuando se usa el Explorador de datos del emulador mediante un explorador web, no se puede conectar al punto de conexión del emulador de Cosmos y todas las acciones relacionadas, como la creación de una base de datos o un contenedor, generarán un error. El segundo problema corregido está relacionado con la creación de un elemento a partir de un archivo JSON mediante la acción de carga del Explorador de datos.

### <a name="2110"></a>2.11.0

- Esta versión introduce la compatibilidad con el rendimiento aprovisionado de escalado automático. Estas características nuevas incluyen la posibilidad de establecer un nivel de rendimiento aprovisionado máximo personalizado en unidades de solicitud (RU/s), habilitar el escalado automático en los contenedores y bases de datos existentes, así como la compatibilidad con la programación mediante los SDK de Azure Cosmos DB.
- Se corrige un problema al consultar un gran número de documentos (más de 1 GB) por el que el emulador generaba el código de estado de error interno 500.

### <a name="292"></a>2.9.2

- En esta versión se corrige un error al habilitar la compatibilidad con la versión 3.2 del punto de conexión de MongoDb. También agrega compatibilidad para generar seguimientos de ETL para solucionar problemas mediante el uso de WPR en lugar de LOGMAN.

### <a name="291"></a>2.9.1

- Esta versión corrige algunos problemas en la compatibilidad de la API de consultas y restaura la compatibilidad con sistemas operativos anteriores, como Windows Server 2012.

### <a name="290"></a>2.9.0

- En esta versión se agrega la opción de establecer la coherencia con un prefijo y aumentar los límites máximos de usuarios y permisos.

### <a name="272"></a>2.7.2

- Se agrega compatibilidad para la versión 3.6 del servidor MongoDB con el emulador de Cosmos. Para iniciar un punto de conexión de MongoDB que tenga como destino la versión 3.6 del servicio, inicie el emulador desde una línea de comandos de administrador con la opción "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- En esta versión se corrige una regresión, que impedía a los usuarios ejecutar consultas en la cuenta de la API de SQL desde el emulador al usar clientes basados en .NET Core o x86 .NET.

### <a name="246"></a>2.4.6

- Esta versión proporciona paridad con las características del servicio Azure Cosmos a partir de julio de 2019, con las excepciones indicadas en [Uso del emulador de Azure Cosmos para desarrollo y pruebas locales](local-emulator.md). También corrige varios errores relacionados con el cierre del emulador cuando se invoca mediante la línea de comandos y la dirección IP interna reemplaza los clientes del SDK mediante la conectividad en modo directo.

### <a name="243"></a>2.4.3

- Deshabilitación del inicio del servicio de MongoDB de forma predeterminada. Solo el punto de conexión SQL está habilitado de forma predeterminada. El usuario debe iniciar el punto de conexión manualmente mediante la opción de línea de comandos "/EnableMongoDbEndpoint" del emulador. Ahora es similar a todos los demás puntos de conexión de servicio, como Gremlin, Cassandra y Table.
- Corrección de un error en el emulador al empezar con “/AllowNetworkAccess”, donde los puntos de conexión Gremlin, Cassandra y Table no controlaban correctamente las solicitudes de clientes externos.
- Incorporación de puertos de conexión directa a la configuración de reglas de firewall.

### <a name="240"></a>2.4.0

- Corrección de un problema con el emulador, que no se puede iniciar mientras aplicaciones de supervisión de red, como Pulse Client, están presentes en el equipo host.
