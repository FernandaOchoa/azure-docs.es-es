---
title: Descripción de los componentes de los modelos de IoT Plug and Play | Microsoft Docs
description: Conozca la diferencia entre los modelos de DTDL de IoT Plug and Play que usan componentes y los modelos que no los usan.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e7b24dd9cdbd11b56545f85ac233665f8fa4adfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91574286"
---
# <a name="iot-plug-and-play-components-in-models"></a>Componentes de los modelos de IoT Plug and Play

En las convenciones de IoT Plug and Play, un dispositivo es un dispositivo IoT Plug and Play si presenta su identificador de modelo del lenguaje de definición de Digital Twins (DTDL) cuando se conecta a un centro de IoT.

En el fragmento de código siguiente se muestran algunos identificadores de modelo:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Sin componentes

Un modelo sencillo no utiliza componentes insertados o en cascada. Incluye información de encabezado y una sección de contenido para definir la telemetría, las propiedades y los comandos.

En el ejemplo siguiente se muestra parte de un modelo sencillo que no utiliza componentes:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Aunque el modelo no define explícitamente un componente, se comporta como si hubiera un único _componente predeterminado_ con todas las definiciones de telemetría, propiedades y comandos.

En la captura de pantalla siguiente se muestra cómo se visualiza el modelo en la herramienta Azure IoT Explorer:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Componente predeterminado en Azure IoT Explorer":::

El identificador de modelo se almacena en una propiedad de dispositivo gemelo, como muestra la captura de pantalla siguiente:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Componente predeterminado en Azure IoT Explorer":::

Un modelo de DTDL sin componentes es una simplificación útil de un dispositivo o de un módulo de IoT Edge con un único conjunto de telemetría, propiedades y comandos. Un modelo de este tipo facilita la migración de los dispositivos o los módulos existentes a dispositivos o módulos IoT Plug and Play; se crea un modelo de DTDL que describe el dispositivo o el módulo reales sin necesidad de definir ningún componente.

> [!TIP]
> Un módulo puede ser un dispositivo [módulo](../iot-hub/iot-hub-devguide-module-twins.md o un [módulo de IoT Edge](../iot-edge/about-iot-edge.md).

## <a name="multiple-components"></a>Varios componentes

Los componentes permiten compilar una interfaz del modelo como un ensamblado de otras interfaces.

Por ejemplo, la interfaz [Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) se define como un modelo. Puede incorporar esta interfaz como uno o varios componentes cuando defina el [modelo de controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). En el ejemplo siguiente, estos componentes se denominan `thermostat1` y `thermostat2`.

En el caso de un modelo de DTDL con varios componentes, hay dos o más secciones de componentes. Cada sección tiene `@type` establecido en `Component` y hace referencia explícita a un esquema, como se muestra en el siguiente fragmento de código:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
...
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Este modelo tiene tres componentes definidos en la sección de contenido: dos componentes `Thermostat` y un componente `DeviceInformation`. También hay un componente predeterminado.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido sobre el modelado de dispositivos, estos son algunos recursos adicionales:

- [Instalación y uso de las herramientas de creación de DTDL](howto-use-dtdl-authoring-tools.md)
- [Lenguaje de definición de Digital Twins v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repositorios de modelos](./concepts-model-repository.md)