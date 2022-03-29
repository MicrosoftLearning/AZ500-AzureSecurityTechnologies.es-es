---
lab:
  title: 03 - Bloqueos de Resource Manager
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: 54375454646bdcf0586b249f65349691c3a3b9c3
ms.sourcegitcommit: a8470295248a6363987bd5ea47154fe39f8535c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2022
ms.locfileid: "139703537"
---
# <a name="lab-03-resource-manager-locks"></a>Laboratorio 03: Bloqueos de Resource Manager
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio 

Se le ha pedido que cree una prueba de concepto que muestre cómo se pueden usar los bloqueos de recursos para evitar la eliminación o cambios accidentales. En concreto, debe:

- Crear un bloqueo de solo lectura.

- Crear un bloqueo de eliminación.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 
 
## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Bloqueos de Resource Manager

## <a name="resource-manager-locks-diagram"></a>Diagrama de bloqueos de Resource Manager

![imagen](https://user-images.githubusercontent.com/91347931/157514986-1bf6a9ea-4c7f-4487-bcd7-542648f8dc95.png)

## <a name="instructions"></a>Instrucciones

### <a name="exercise-1-resource-manager-locks"></a>Ejercicio 1: Bloqueos de Resource Manager

#### <a name="estimated-timing-20-minutes"></a>Tiempo estimado: 20 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crear un grupo de recursos con una cuenta de almacenamiento
- Tarea 2: Agregar un bloqueo de solo lectura en la cuenta de almacenamiento. 
- Tarea 3: Probar el bloqueo de solo lectura. 
- Tarea 4: Quitar el bloqueo de solo lectura y crear un bloqueo de eliminación.
- Tarea 5: Probar el bloqueo de eliminación.

#### <a name="task-1-create-a-resource-group-with-a-storage-account"></a>Tarea 1: Crear un grupo de recursos con una cuenta de almacenamiento

En esta tarea, creará un grupo de recursos y una cuenta de almacenamiento para el laboratorio. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

1. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear un grupo de recursos (compruebe con el instructor el valor del parámetro de ubicación):

    ```powershell
    New-AzResourceGroup -Name AZ500LAB03 -Location 'EastUS'
    ```

1. En la sesión de PowerShell del panel Cloud Shell, ejecute lo siguiente para crear una cuenta de almacenamiento en el grupo de recursos recién creado:
    
    ```powershell
    New-AzStorageAccount -ResourceGroupName AZ500LAB03 -Name (Get-Random -Maximum 999999999999999) -Location  EastUS -SkuName Standard_LRS -Kind StorageV2 
    ```

   >**Nota**: Espere a que se cree la cuenta de almacenamiento. Esto puede tardar un par de minutos. 

1. Cierre el panel de Cloud Shell.

#### <a name="task-2-add-a-readonly-lock-on-the-storage-account"></a>Tarea 2: Agregar un bloqueo de solo lectura en la cuenta de almacenamiento. 

En esta tarea, agregará un bloqueo de solo lectura a la cuenta de almacenamiento. Este protegerá el recurso frente a eliminaciones o modificaciones accidentales. 

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de recursos** y presione la tecla **Entrar**.

1. En la hoja **Grupos de recursos**, seleccione la entrada del grupo de recursos **AZ500LAB03**.

1. En la hoja del grupo de recursos **AZ500LAB03**, en la lista de recursos, seleccione la nueva cuenta de almacenamiento. 

1. En la sección **Configuración**, haga clic en el icono "Bloqueos".

1. Haga clic en **+ Agregar** y especifique la siguiente configuración:

   |Configuración|Value|
   |---|---|
   |Nombre del bloqueo|**Bloqueo de solo lectura**|
   |Tipo de bloqueo|**Solo lectura**|

1. Haga clic en **OK**. 

   >**Nota**: La cuenta de almacenamiento ahora está protegida frente a eliminaciones y modificaciones accidentales.

#### <a name="task-3-test-the-readonly-lock"></a>Tarea 3: Probar el bloqueo de solo lectura 

1. En la sección **Configuración** de la hoja de la cuenta de almacenamiento, haga clic en **Configuración**.

1. Establezca la opción **Se requiere transferencia segura** en **Deshabilitado** y, a continuación, haga clic en **Guardar**.

1. Debería poder detectar una notificación que indica **No se pudo actualizar la cuenta de almacenamiento**.

1. Haga clic en el icono **Notificaciones** de la barra de herramientas de la parte superior de Azure Portal y revise la notificación, que será similar al texto siguiente: 

    > **"No se pudo actualizar la cuenta de almacenamiento 'xxxxxxxx'. Error: El ámbito 'xxxxxxxx' no puede realizar la operación de escritura porque los ámbitos siguientes están bloqueados: '/subscriptions/xxxxx-xxx-xxxx-xxxx-xxxxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'. Quite el bloqueo e inténtelo de nuevo".**

1. Vuelva a la hoja **Configuración** de la cuenta de almacenamiento y haga clic en **Descartar**. 

1. En la hoja de la cuenta de almacenamiento, seleccione **Información general** y, en la hoja **Información general**, haga clic en **Eliminar**.

1. En la hoja **Eliminar cuenta de almacenamiento**, escriba el nombre de la cuenta de almacenamiento para confirmar que tiene previsto continuar y, a continuación, haga clic en **Eliminar**.

1. Revise la notificación recién generada, que será similar al texto siguiente: 

    > **"No se pudo eliminar la cuenta de almacenamiento 'xxxxxxx'. Error: El ámbito 'xxxxxxx' no puede realizar la operación de eliminación porque los ámbitos sigiuientes están bloqueados: '/subscriptions/xxxx-xxxx-xxxx-xxxx-xxxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'. Quite el bloqueo e inténtelo de nuevo".**

   >**Nota**: Ahora ha comprobado que un bloqueo de solo lectura detendrá la eliminación y modificación accidentales de un recurso.

#### <a name="task-4-remove-the-readonly-lock-and-create-a-delete-lock"></a>Tarea 4: Quitar el bloqueo de solo lectura y crear un bloqueo de eliminación.

En esta tarea, quitará el bloqueo de solo lectura de la cuenta de almacenamiento y creará un bloqueo de eliminación. 

1. En Azure Portal, vuelva a la hoja que muestra las propiedades de la cuenta de almacenamiento recién creada.

1. En la sección **Configuración**, haga clic en **Bloqueos**.  

1. En la hoja **Bloqueos**, haga clic en el icono **Eliminar** situado en el extremo derecho de la entrada **Bloqueo de solo lectura**.

1. Haga clic en **+ Agregar** y especifique la siguiente configuración:

   |Configuración|Value|
   |---|---|
   |Nombre del bloqueo|**Bloqueo de eliminación**|
   |Tipo de bloqueo|**Eliminar**|

1. Haga clic en **Aceptar**. 

#### <a name="task-5-test-the-delete-lock"></a>Tarea 5: Probar el bloqueo de eliminación.

En esta tarea, probará el bloqueo de eliminación. Debería poder modificar la cuenta de almacenamiento, pero no eliminarla. 

1. En la sección **Configuración** de la hoja de la cuenta de almacenamiento, haga clic en **Configuración**.

1. Establezca la opción **Se requiere transferencia segura** en **Deshabilitado** y, a continuación, haga clic en **Guardar**.

   >**Nota**: Esta vez, el cambio debería ser correcto.

1. En la hoja de la cuenta de almacenamiento, seleccione **Información general** y, en la hoja **Información general**, haga clic en **Eliminar**.

1. En la hoja **Eliminar cuenta de almacenamiento**, escriba el nombre de la cuenta de almacenamiento para confirmar que tiene previsto continuar y, a continuación, haga clic en **Eliminar**.

1. Revise la notificación similar al texto siguiente: 

    > **"No se puede eliminar 'xxxxxx' porque este recurso o su elemento primario tiene un bloqueo de eliminación. Se deben quitar los bloqueos para poder eliminar este recurso".**

   >**Nota**: Ahora ha comprobado que un bloqueo de **eliminación** permitirá cambios de configuración, pero detendrá la eliminación accidental.

   >**Nota**: Mediante el uso de bloqueos de recursos, puede implementar una línea de defensa adicional contra cambios accidentales o malintencionados o la eliminación de los recursos más importantes. Cualquier usuario con el rol **Propietario** puede quitar bloqueos de recursos, pero para ello es necesario un esfuerzo consciente. Los bloqueos complementan el control de acceso basado en roles: 

> Resultados: en este ejercicio, ha aprendido a usar bloqueos de Resource Manager para proteger los recursos frente a modificaciones y eliminaciones accidentales.

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Cuando se le solicite, haga clic en **Volver a conectar**.

1. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el bloqueo de eliminación:

    ```powershell
    $storageaccountname = (Get-AzStorageAccount -ResourceGroupName AZ500LAB03).StorageAccountName

    $lockName = (Get-AzResourceLock -ResourceGroupName AZ500LAB03 -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).Name

    Remove-AzResourceLock -LockName $lockName -ResourceName $storageAccountName  -ResourceGroupName AZ500LAB03 -ResourceType Microsoft.Storage/storageAccounts -Force
    ```

1.  En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos:

    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB03" -Force -AsJob
    ```

1.  Cierre el panel de **Cloud Shell**. 
