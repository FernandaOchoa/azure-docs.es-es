---
ms.openlocfilehash: 46e8609be2ec57600e666ef9dab33eae2900e1ce
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691125"
---
1. En Visual Studio Code, vaya a *src/edge*. Vará el archivo *.env* y algunos archivos de plantilla de implementación.

    La plantilla de implementación hace referencia al manifiesto de implementación del dispositivo perimetral, en el que las variables se utilizan para algunas propiedades. El archivo *.env* contiene los valores de esas variables.
1. Vaya a la carpeta *src/cloud-to-device-console-app*. Aquí verá el archivo *appsettings.json* y algunos otros archivos:

    * ***operations.json***: una lista de las operaciones que desea que ejecute el programa.
    * **main.py**: código del programa de ejemplo. Este código:
    
      * Carga la configuración de la aplicación.
      * Invoca los métodos directos que expone el módulo Live Video Analytics en IoT Edge. Puede usar el módulo para analizar secuencias de vídeo en directo mediante la invocación de sus [métodos directos](../../../direct-methods.md).
      * Se pone en pausa para que pueda examinar la salida del programa en la ventana **TERMINAL** y los eventos generados por el módulo en la ventana de **SALIDA**.
      * Invoca los métodos directos para limpiar los recursos.   

