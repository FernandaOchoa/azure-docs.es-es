---
title: Administración de instantáneas mediante Azure NetApp Files | Microsoft Docs
description: Describe cómo crear y administrar instantáneas mediante Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/04/2020
ms.author: b-juche
ms.openlocfilehash: e9f2a1f9125d25caa9506e954cab3b94dfcb5c24
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932284"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Administración de instantáneas mediante Azure NetApp Files

Azure NetApp Files admite la creación de instantáneas a petición y el uso de directivas de instantáneas para programar la creación automática de instantáneas.  También puede restaurar una instantánea en un nuevo volumen o restaurar un único archivo mediante un cliente.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Creación de una instantánea a petición para un volumen

Puede crear instantáneas de volumen a petición. 

1.  Vaya al volumen para el que desee crear una instantánea. Haga clic en **Instantáneas**.

    ![Desplazamiento hasta las instantáneas](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Haga clic en **+ Add snapshot** (+Agregar instantánea) para crear una instantánea a petición para un volumen.

    ![Agregar instantánea](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  En la ventana Nueva instantánea, proporcione un nombre para la nueva instantánea que va a crear.   

    ![Nueva instantánea](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Haga clic en **OK**. 

## <a name="manage-snapshot-policies"></a>Administración de directivas de instantánea

Puede programar la realización automática de instantáneas de volumen mediante el uso de directivas de instantánea. También puede modificar una directiva de instantánea según sea necesario o eliminar una directiva de instantánea que ya no necesite.  

### <a name="register-the-feature"></a>Registrar la característica

La característica **directiva de instantánea** está actualmente en la versión preliminar. Si usa esta característica por primera vez, debe registrarla primero. 

1. Registre la característica: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Compruebe el estado del registro de la característica: 

    > [!NOTE]
    > **RegistrationState** puede estar en el estado `Registering` hasta 60 minutos antes de cambiar a `Registered`. Espere hasta que el estado sea **Registrado** antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
También puede usar los comandos de la [CLI de Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` y `az feature show` para registrar la característica y mostrar el estado del registro. 

### <a name="create-a-snapshot-policy"></a>Creación de una directiva de instantánea 

Una directiva de instantánea le permite especificar la frecuencia de creación de instantáneas en ciclos de horas, diarios, semanales o mensuales. También debe especificar el número máximo de instantáneas que se conservarán para el volumen.  

1.  En la vista Cuenta de NetApp, haga clic en **Directiva de instantánea**.

    ![Navegación por la directiva de instantánea](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  En la ventana Directiva de instantánea, establezca el estado de la directiva en **Habilitado**. 

3.  Haga clic en la pestaña **Por hora**, **Cada día**, **Semanalmente** o **Mensualmente** para crear directivas de instantánea por hora, diarias, semanales o mensuales. Seleccione el **número de instantáneas que desea mantener**.  

    Consulte [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md) acerca del número máximo de instantáneas permitido para un volumen. 

    En el ejemplo siguiente se muestra la configuración de la directiva de instantánea por hora. 

    ![Directiva de instantánea por hora](../media/azure-netapp-files/snapshot-policy-hourly.png)

    En el ejemplo siguiente se muestra la configuración de la directiva de instantánea diaria.

    ![Directiva de instantánea diaria](../media/azure-netapp-files/snapshot-policy-daily.png)

    En el ejemplo siguiente se muestra la configuración de la directiva de instantánea semanal.

    ![Directiva de instantánea semanal](../media/azure-netapp-files/snapshot-policy-weekly.png)

    En el ejemplo siguiente se muestra la configuración de la directiva de instantánea mensual.

    ![Directiva de instantánea mensual](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Haga clic en **Save**(Guardar).  

Si tiene que crear directivas de instantánea adicionales, repita el paso 3.
Las directivas que ha creado aparecen en la página Directiva de instantánea.

Si desea que un volumen use la directiva de instantánea, debe [aplicar la directiva al volumen](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Aplicación de una directiva de instantánea a un volumen

Si desea que un volumen use una directiva de instantánea que haya creado, debe aplicarla al volumen. 

1.  Vaya a la página **Volúmenes**, haga clic con el botón derecho en el volumen al que desee aplicar una directiva de instantánea y seleccione **Editar**.

    ![Menú contextual de volúmenes](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  En la ventana de edición, en **Directiva de instantánea**, seleccione la directiva que se vaya a usar para el volumen.  Haga clic en **Aceptar** para aplicar la directiva.  

    ![Edición de directiva de instantánea](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Modificación de una directiva de instantánea 

Puede modificar una directiva de instantánea existente para cambiar su estado, la frecuencia (cada hora, diariamente, semanalmente o mensualmente) o el número de instantáneas que se deben conservar.  
 
1.  En la vista Cuenta de NetApp, haga clic en **Directiva de instantánea**.

2.  Haga clic con el botón derecho en la directiva de instantánea que desee modificar y, a continuación, seleccione **Editar**.

    ![Menú contextual de directiva de instantánea](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Realice los cambios en la ventana Directiva de instantánea que aparezca y, a continuación, haga clic en **Guardar**. 

### <a name="delete-a-snapshot-policy"></a>Eliminación de una directiva de instantánea 

Puede eliminar una directiva de instantánea que ya no desee conservar.   

1.  En la vista Cuenta de NetApp, haga clic en **Directiva de instantánea**.

2.  Haga clic con el botón derecho en la directiva de instantánea que desee modificar y, a continuación, seleccione **Eliminar**.

    ![Menú contextual de directiva de instantánea](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Haga clic en **Sí** para confirmar que desea eliminar la directiva de instantánea.   

    ![Confirmación de la eliminación de una directiva de instantánea](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restauración de una instantánea a un nuevo volumen

Actualmente, puede restaurar una instantánea solo a un nuevo volumen. 
1. Seleccione **Instantáneas** en la hoja Volumen para mostrar la lista de instantáneas. 
2. Haga clic con el botón derecho en la instantánea que desee restaurar y seleccione **Restaurar al nuevo volumen** en la opción de menú.  

    ![Restauración de la instantánea al nuevo volumen](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. En la ventana Crear un volumen, especifique la información del nuevo volumen:  
    * **Nombre**   
        Especifique el nombre para el volumen que va a crear.  
        
        El nombre debe ser único dentro de un grupo de recursos. Debe tener tres caracteres de longitud, como mínimo.  Puede usar cualquier carácter alfanumérico.

    * **Cuota**  
        Especifique la cantidad de almacenamiento lógico que desee asignar al volumen.  

    ![Restauración en el nuevo volumen](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Haga clic en **Revisar y crear**.  Haga clic en **Crear**.   
    El nuevo volumen utiliza el mismo protocolo que la instantánea.   
    El nuevo volumen al que se restaura la instantánea aparece en la hoja Volúmenes.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Restauración de un archivo desde una instantánea mediante un cliente

Si no desea [restaurar la instantánea completa en un volumen](#restore-a-snapshot-to-a-new-volume), tiene la opción de restaurar un archivo desde una instantánea mediante un cliente que tenga el volumen montado.  

El volumen montado contiene un directorio de instantáneas denominado `.snapshot` (en clientes NFS) o `~snapshot` (en clientes SMB) que es accesible para el cliente. El directorio de instantáneas contiene subdirectorios correspondientes a las instantáneas del volumen. Cada subdirectorio contiene los archivos de la instantánea. Si elimina o sobrescribe accidentalmente un archivo, puede restaurarlo en el directorio principal de lectura y escritura copiando el archivo de un subdirectorio de instantánea en el directorio de lectura y escritura. 

Si ha activado la casilla Ocultar la ruta de acceso de la instantánea al crear el volumen, se ocultará el directorio de instantáneas. Puede ver el estado de Ocultar la ruta de acceso de la instantánea del volumen seleccionando este último. Para editar la opción Ocultar la ruta de acceso de la instantánea, haga clic en **Editar** en la página del volumen.  

![Edición de opciones de instantánea de volumen](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Restauración de un archivo mediante un cliente NFS de Linux 

1. Utilice el comando de Linux `ls` para mostrar el archivo que desea restaurar desde el directorio `.snapshot`. 

    Por ejemplo:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Utilice el comando `cp` para copiar el archivo en el directorio principal.  

    Por ejemplo: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Restauración un archivo mediante un cliente Windows 

1. Si el directorio `~snapshot` del volumen está oculto, [muestre los elementos ocultos](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) en el directorio principal para ver `~snapshot`.

    ![Mostrar elementos ocultos](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Vaya al subdirectorio de `~snapshot` para buscar el archivo que desea restaurar.  Haga clic con el botón derecho en el archivo. Seleccione **Copiar**.  

    ![Copia de archivo para restaurar](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Vuelva al directorio principal. Haga clic con el botón derecho en el directorio principal y seleccione `Paste` para pegar el archivo en el directorio.

    ![Pegado de archivo para restaurar](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. También puede hacer clic con el botón derecho en el directorio principal, seleccionar **Propiedades**, hacer clic en la pestaña **Versiones anteriores** para ver la lista de instantáneas y seleccionar **Restaurar** para restaurar un archivo.  

    ![Versiones anteriores de Propiedades](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas de directivas de instantáneas](troubleshoot-snapshot-policies.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vídeo Instantáneas de Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)