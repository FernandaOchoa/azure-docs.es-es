---
title: 'Autenticación de dispositivos de bajada: Azure IoT Edge | Microsoft Docs'
description: Procedimientos para autenticar dispositivos de bajada o de hoja en IoT Hub, y enrutar su conexión a través de dispositivos de puerta de enlace de Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 73584353d0d003588ef7de6131d3c3c4bbfcff59
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046730"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticación de un dispositivo de bajada en Azure IoT Hub

En un escenario de puerta de enlace transparente, los dispositivos de bajada (también denominados dispositivos de hoja o secundarios) necesitan identidades en IoT Hub como cualquier otro dispositivo. Este artículo le guía a través de las opciones para autenticar un dispositivo de bajada en IoT Hub y, después, se muestra cómo declarar la conexión de puerta de enlace.

Hay tres pasos generales para configurar una conexión de puerta de enlace transparente correcta. En este artículo se describe el segundo paso:

1. Configure el dispositivo de puerta de enlace como servidor para que los dispositivos de bajada puedan conectarse a él de forma segura. Configure la puerta de enlace para recibir mensajes de los dispositivos de bajada y enrutarlos al destino adecuado. Para más información, consulte [Configuración de un dispositivo IoT Edge para que actúe como puerta de enlace transparente](how-to-create-transparent-gateway.md).
2. **Cree una identidad de dispositivo para el dispositivo de bajada para que pueda autenticarse en IoT Hub. Configure el dispositivo de bajada para enviar mensajes a través del dispositivo de puerta de enlace.**
3. Conecte el dispositivo de bajada al dispositivo de puerta de enlace y empiece a enviar mensajes. Para más información, consulte [Conexión de un dispositivo de bajada a una puerta de enlace Azure IoT Edge](how-to-connect-downstream-device.md).

Los dispositivos de bajada se pueden autenticar con IoT Hub mediante uno de estos tres métodos: claves simétricas (en ocasiones denominadas claves de acceso compartido), certificados X.509 autofirmados o certificados X.509 firmados por una entidad de certificación (CA). Los pasos de autenticación son similares a los que se usan para configurar cualquier dispositivo que no es IoT Edge con IoT Hub, con pequeñas diferencias para declarar la relación de la puerta de enlace.

En los pasos de este artículo se muestra el aprovisionamiento manual de dispositivos. No se admite el aprovisionamiento automático de dispositivos de bajada con Azure IoT Hub Device Provisioning Service.

## <a name="prerequisites"></a>Requisitos previos

Siga los pasos de [Configuración de un dispositivo IoT Edge para que actúe como puerta de enlace transparente](how-to-create-transparent-gateway.md).

Si usa la autenticación X.509, se generarán certificados para el dispositivo de bajada. Tenga disponible el mismo certificado de entidad de certificación raíz y el script de generación de certificados que usó para el artículo de puerta de enlace transparente para usarlo de nuevo.

Este artículo hace referencia al *nombre de host de la puerta de enlace* en varios puntos. Este nombre se declara en el parámetro **hostname** del archivo config.yaml del dispositivo de puerta de enlace IoT Edge. Se hace referencia a él en la cadena de conexión del dispositivo de bajada. El nombre de host de la puerta de enlace debe poderse resolverse en una dirección IP, ya sea mediante DNS o una entrada del archivo de hosts en el dispositivo de bajada.

## <a name="register-device-with-iot-hub"></a>Registro del dispositivo con IoT Hub

Elija cómo desea que el dispositivo de bajada se autentique con IoT Hub:

* [Autenticación de clave simétrica](#symmetric-key-authentication): IoT Hub crea una clave que se coloca en el dispositivo de bajada. Cuando el dispositivo se autentica, IoT Hub comprueba que las dos claves coinciden. No es necesario crear certificados adicionales para usar la autenticación de clave simétrica.
* [Autenticación con X.509 autofirmado](#x509-self-signed-authentication): a veces denominada autenticación mediante huella digital, porque comparte la huella digital del certificado X.509 del dispositivo con IoT Hub.
* [Autenticación X.509 firmado por una entidad de certificación](#x509-ca-signed-authentication): cargue el certificado de la entidad de certificación raíz en IoT Hub. Cuando los dispositivos presentan su certificado X.509 para la autenticación, IoT Hub comprueba que pertenece a una cadena de confianza firmada por el mismo certificado de entidad de certificación raíz.

Después de registrar el dispositivo con uno de estos tres métodos, continúe con la siguiente sección para [recuperar y modificar la cadena de conexión](#retrieve-and-modify-connection-string) para el dispositivo de bajada.

### <a name="symmetric-key-authentication"></a>Autenticación de clave simétrica

La autenticación de clave simétrica (o de clave de acceso compartido) es la manera más sencilla de autenticarse con IoT Hub. Con la autenticación de clave simétrica, se asocia una clave de base64 con el identificador de dispositivo IoT en la instancia de IoT Hub. Esa clave se incluye en las aplicaciones de IoT para que el dispositivo pueda presentarla cuando se conecta a IoT Hub.

Agregue un nuevo dispositivo IoT en IoT Hub, mediante Azure Portal, la CLI de Azure o la extensión IoT para Visual Studio Code. Recuerde que los dispositivos de bajada se deben identificar en IoT Hub como dispositivos IoT normales, no como dispositivos IoT Edge.

Al crear la identidad del dispositivo, proporcione la información siguiente:

* Cree un identificador para el dispositivo.

* Seleccione **Clave simétrica** como el tipo de autenticación.

* Seleccione **Establecer un dispositivo primario** y elija el dispositivo de puerta de enlace de IoT Edge a través del que se va a conectar este dispositivo de bajada. Este paso habilita [funcionalidades sin conexión](offline-capabilities.md) para el dispositivo de bajada. Siempre puede cambiar el dispositivo primario más adelante.

   ![Creación de la identidad del dispositivo con la autenticación de clave simétrica en el portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

También puede usar la [extensión IoT para la CLI de Azure](https://github.com/Azure/azure-iot-cli-extension) con el fin de completar la misma operación. En el ejemplo siguiente se crea un dispositivo IoT con la autenticación de clave simétrica y se asigna un dispositivo primario:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Para obtener más información sobre los comandos de la CLI de Azure para la creación de dispositivos y la administración de elementos primarios y secundarios, vea el contenido de referencia para los comandos [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity).

A continuación, [recupere y modifique la cadena de conexión](#retrieve-and-modify-connection-string) para que el dispositivo sepa conectarse a través de su puerta de enlace.

### <a name="x509-self-signed-authentication"></a>Autenticación con X.509 autofirmado

Para la autenticación con X.509 autofirmado, en ocasiones denominada autenticación de huella digital, tendrá que crear certificados para el dispositivo de bajada. Estos certificados contienen una huella digital que se comparte con IoT Hub para la autenticación.

1. Con el certificado de la entidad de certificación, cree dos certificados de dispositivo (principal y secundario) para el dispositivo de bajada.

   Si no tiene una entidad de certificación para crear certificados X. 509, puede usar los scripts de certificado de demostración de IoT Edge para [crear certificados de dispositivo de bajada](how-to-create-test-certificates.md#create-downstream-device-certificates). Siga los pasos para crear certificados autofirmados. Use el mismo certificado de entidad de certificación raíz que generó los certificados para el dispositivo de puerta de enlace.

   Si crea sus propios certificados, asegúrese de que el nombre de sujeto del certificado del dispositivo se establece en el identificador de dispositivo que usa al registrar el dispositivo de IoT en Azure IoT Hub. Esta opción de configuración es necesaria para la autenticación.

2. Recupere la huella digital SHA1 (denominada huella digital en la interfaz de IoT Hub) de cada certificado, que es una cadena de 40 caracteres hexadecimales. Use el comando de openssl siguiente para ver el certificado y localizar la huella digital:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Ejecute este comando dos veces, una vez para el certificado principal y otra para el certificado secundario. Proporcione huellas digitales para ambos certificados cuando registre un nuevo dispositivo IoT mediante certificados X. 509 autofirmados.

3. Vaya a la instancia de IoT Hub en Azure Portal y cree una identidad del dispositivo IoT con los valores siguientes:

   * Proporcione el **identificador de dispositivo** que coincida con el nombre de sujeto de los certificados de dispositivo.
   * Seleccione **X.509 autofirmado** como el tipo de autenticación.
   * Pegue las cadenas hexadecimales que ha copiado de los certificados principal y secundario del dispositivo.
   * Seleccione **Establecer un dispositivo primario** y elija el dispositivo de puerta de enlace de IoT Edge a través del que se va a conectar este dispositivo de bajada. Para la autenticación X.509 de un dispositivo de bajada se requiere un dispositivo primario.

   ![Creación de la identidad del dispositivo con la autenticación autofirmada de X.509 en el portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copie los certificados de dispositivo principal y secundario y sus claves en cualquier ubicación del dispositivo de bajada. Mueva también una copia del certificado de la entidad de certificación raíz compartido que generaron el certificado de dispositivo de puerta de enlace y los certificados de dispositivo de bajada.

   Hará referencia a estos archivos de certificado en todas las aplicaciones del dispositivo de bajada que se conecten a IoT Hub. Puede usar un servicio como [Azure Key Vault](../key-vault/index.yml) o una función como [Protocolo de copia segura](https://www.ssh.com/ssh/scp/) para mover los archivos de certificado.

5. En función de su lenguaje preferido, revise los ejemplos de cómo se puede hacer referencia a los certificados X. 509 en las aplicaciones de IoT:

   * C#: [Configuración de la seguridad de X.509 en Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

También puede usar la [extensión IoT para la CLI de Azure](https://github.com/Azure/azure-iot-cli-extension) para completar la misma operación de creación de dispositivos. En el ejemplo siguiente se crea un dispositivo IoT con autenticación X.509 autofirmado y se asigna un dispositivo primario:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Para obtener más información sobre los comandos de la CLI de Azure para la creación de dispositivos, la generación de certificados y la administración de elementos primarios y secundarios, vea el contenido de referencia para los comandos [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity).

A continuación, [recupere y modifique la cadena de conexión](#retrieve-and-modify-connection-string) para que el dispositivo sepa conectarse a través de su puerta de enlace.

### <a name="x509-ca-signed-authentication"></a>Autenticación X.509 firmado por una entidad de certificación

Para la autenticación de certificados X.509 firmados por entidad de certificación (CA), necesita un certificado de entidad de certificación raíz registrado en IoT Hub que use para firmar los certificados para el dispositivo de bajada. Cualquier dispositivo con un certificado emitido por el certificado de entidad de certificación raíz o cualquiera de sus certificados intermedios tendrá permiso para autenticarse.

Esta sección se basa en las instrucciones detalladas en el artículo de IoT Hub [Configuración de la seguridad de X.509 en Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md).

1. Con el certificado de la entidad de certificación, cree dos certificados de dispositivo (principal y secundario) para el dispositivo de bajada.

   Si no tiene una entidad de certificación para crear certificados X.509, puede usar los scripts de certificado de demostración de IoT Edge para [crear certificados de dispositivo de bajada](how-to-create-test-certificates.md#create-downstream-device-certificates). Siga los pasos para crear certificados firmados por una entidad de certificación. Use el mismo certificado de entidad de certificación raíz que generó los certificados para el dispositivo de puerta de enlace.

2. Siga las instrucciones de la sección [Registro de certificados de entidad de certificación X.509 en una instancia de IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) de *Configuración de la seguridad de X.509 en Azure IoT Hub*. En esa sección, realice los pasos siguientes:

   1. Cargue un certificado de entidad de certificación raíz. Si va a usar los certificados de demostración, la entidad de certificación raíz será **\<path>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Compruebe que es el propietario de ese certificado de entidad de certificación raíz.

3. Siga las instrucciones de la sección [Creación de un dispositivo X.509 para una instancia de IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) de *Configuración de la seguridad de X.509 en Azure IoT Hub*. En esa sección, realice los pasos siguientes:

   1. Agregue un nuevo dispositivo. Proporcione un nombre en minúsculas para **Id. de dispositivo** y elija el tipo de autenticación **X.509 firmado por CA**.

   2. Configure un dispositivo primario. Para los dispositivos de bajada, seleccione **Establecer un dispositivo primario** y elija el dispositivo de puerta de enlace de IoT Edge que proporcionará la conexión a IoT Hub.

4. Cree una cadena de certificados para el dispositivo de bajada. Use el mismo certificado de entidad de certificación raíz que ha cargado en IoT Hub para crear esta cadena. Use el mismo identificador de dispositivo en minúsculas que ha asignado a la identidad del dispositivo en el portal.

5. Copie el certificado de dispositivo y las claves en cualquier ubicación del dispositivo de bajada. Mueva también una copia del certificado de la entidad de certificación raíz compartido que generaron el certificado de dispositivo de puerta de enlace y los certificados de dispositivo de bajada.

   Hará referencia a estos archivos en todas las aplicaciones del dispositivo de bajada que se conecten a IoT Hub. Puede usar un servicio como [Azure Key Vault](../key-vault/index.yml) o una función como [Protocolo de copia segura](https://www.ssh.com/ssh/scp/) para mover los archivos de certificado.

6. En función de su lenguaje preferido, revise los ejemplos de cómo se puede hacer referencia a los certificados X. 509 en las aplicaciones de IoT:

   * C#: [Configuración de la seguridad de X.509 en Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

También puede usar la [extensión IoT para la CLI de Azure](https://github.com/Azure/azure-iot-cli-extension) para completar la misma operación de creación de dispositivos. En el ejemplo siguiente se crea un dispositivo IoT con autenticación X.509 firmado por CA y se asigna un dispositivo primario:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Para más información, consulte el contenido de referencia de la CLI de Azure de los comandos [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity).

A continuación, [recupere y modifique la cadena de conexión](#retrieve-and-modify-connection-string) para que el dispositivo sepa conectarse a través de su puerta de enlace.

## <a name="retrieve-and-modify-connection-string"></a>Recuperación y modificación de la cadena de conexión

Después de crear una identidad de dispositivo IoT en el portal, puede recuperar sus claves principales o secundarias. Una de estas claves debe incluirse en la cadena de conexión que usan las aplicaciones para comunicarse con IoT Hub. Para la autenticación de clave simétrica, IoT Hub proporciona la cadena de conexión completamente formada en los detalles del dispositivo para su comodidad. Tendrá que agregar información adicional sobre el dispositivo de puerta de enlace a la cadena de conexión.

Las cadenas de conexión de los dispositivos de bajada necesitan los componentes siguientes:

* La instancia de IoT Hub a la que se conecta el dispositivo: `Hostname={iothub name}.azure-devices.net`
* El id. de dispositivo que se ha registrado con el centro: `DeviceID={device ID}`
* El método de autenticación, si es una clave simétrica o certificados X.509.
  * Si el uso de la autenticación de clave simétrica proporciona la clave principal o secundaria: `SharedAccessKey={key}`
  * Si el uso de la autenticación de certificado X.509 proporciona una marca: `x509=true`
* El dispositivo de puerta de enlace a través del que se conecta el dispositivo. Proporcione el valor **hostname** (nombre de host) del archivo config.yaml del dispositivo de puerta de enlace de IoT Edge: `GatewayHostName={gateway hostname}`

Con todos los elementos, una cadena de conexión completa tiene este aspecto:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

O:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Gracias a la relación de tipo primario-secundario, puede simplificar la cadena de conexión mediante una llamada a la puerta de enlace directamente como host de la conexión. Por ejemplo:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Usará esta cadena de conexión modificada en el siguiente artículo de la serie de puerta de enlace transparente.

## <a name="next-steps"></a>Pasos siguientes

En este punto, tiene un dispositivo IoT Edge registrado en su centro de IoT y configurado como puerta de enlace transparente. También tiene un dispositivo de bajada registrado en su centro de IoT que apunta a su dispositivo de puerta de enlace.

En los pasos de este artículo se configura el dispositivo de bajada para autenticarse en IoT Hub. A continuación, tendrá que configurar los dispositivos de bajada para que confíen en el dispositivo de puerta de enlace y se conecten a él de forma segura. Continúe con el siguiente artículo de la serie sobre la puerta de enlace transparente, [Conexión de un dispositivo de bajada a una puerta de enlace Azure IoT Edge](how-to-connect-downstream-device.md).