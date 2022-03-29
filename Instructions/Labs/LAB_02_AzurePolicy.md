---
lab:
  title: '02: Azure Policy'
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: a28872fa69650b18226d3da63cac7d5a0632fb43
ms.sourcegitcommit: a8470295248a6363987bd5ea47154fe39f8535c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2022
ms.locfileid: "139703573"
---
# <a name="lab-02-azure-policy"></a>Laboratorio 02: Azure Policy
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto que muestre cómo se puede usar Azure Policy. En concreto, debe:

- Crear una directiva de ubicaciones permitidas que garantice que los recursos solo se creen en una región específica.
- Probarla para asegurarse de que los recursos solo se crean en la ubicación permitida

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará las tareas siguientes:

- Ejercicio 1: Implementación de Azure Policy. 

## <a name="azure-policy-diagram"></a>Diagrama de Azure Policy

![imagen](https://user-images.githubusercontent.com/91347931/157511920-19c1f06c-86bd-440d-80ac-d96aa27aefff.png)

## <a name="instructions"></a>Instrucciones

### <a name="exercise-1-implement-azure-policy"></a>Ejercicio 1: Implementación de Azure Policy

#### <a name="estimated-timing-20-minutes"></a>Tiempo estimado: 20 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Creación de un grupo de recursos de Azure. 
- Tarea 2: Creación de una asignación de directiva de ubicaciones permitidas.
- Tarea 3: Comprobación de que la asignación de directiva de ubicaciones permitidas funciona. 

#### <a name="task-1-create-a-resource-group-for-the-lab"></a>Tarea 1: Creación de un grupo de recursos para el laboratorio. 

En esta tarea, creará un grupo de recursos para el laboratorio. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

1. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear un grupo de recursos (compruebe con el instructor el valor del parámetro de ubicación):

    ```powershell
    New-AzResourceGroup -Name AZ500LAB02 -Location 'East US'
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para enumerar los grupos de recursos para comprobar que se ha creado el nuevo grupo de recursos:

    ```powershell
    Get-AzResourceGroup | format-table
    ```

1. Cierre **Cloud Shell**.

#### <a name="task-2-create-an-allowed-locations-policy-assignment"></a>Tarea 2: Creación de una asignación de directiva de ubicaciones permitidas.

En esta tarea, creará una asignación de directiva de ubicaciones permitidas y especificará qué regiones de Azure puede usar la directiva. 

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Directiva** y presione la tecla **Entrar**.

1. En el panel **Directiva**, seleccione **Definiciones** en la sección **Creación**.

1. Tómese un minuto para examinar las definiciones integradas. Use la lista desplegable **Categoría** para filtrar la lista de directivas.

1. En el cuadro de texto **Buscar**, escriba **Ubicaciones permitidas**. 

   >**Nota**: La directiva **Ubicaciones permitidas** permite restringir la ubicación de los recursos, no los grupos de recursos. Para restringir las ubicaciones de los grupos de recursos, puede usar la directiva **Ubicaciones permitidas para grupos de recursos**.

1. Haga clic en la definición de la directiva **Ubicaciones permitidas** para mostrar sus detalles. 

   >**Nota**: Esta definición de directiva toma una matriz de ubicaciones como parámetros. Una regla de directiva es una instrucción "if-then". La cláusula "if" comprueba si la ubicación del recurso está incluida en la lista de parámetros y, si no es así, la cláusula "then" deniega la creación de los recursos o, en el caso de los recursos existentes, los marca como no compatibles.

1. En el panel **Ubicaciones permitidas**, haga clic en **Asignar**.

1. En la pestaña **Aspectos básicos** del panel **Ubicaciones permitidas**, haga clic en el botón Puntos suspensivos (...) situado junto al cuadro de texto **Ámbito** y, en el panel **Ámbito**, especifique la siguiente configuración:

   |Configuración|Value|
   |---|---|
   |Subscription|nombre de la suscripción de Azure|
   |Resource group|**AZ500LAB02**|

1. Haga clic en **Seleccionar**.

1. En el panel **Ubicaciones permitidas** de la pestaña **Aspectos básicos**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Nombre de asignación|**Permitir Sur de Reino Unido para AZ500LAB02**|
   |Descripción|**Permitir la creación de recursos en Sur de Reino Unido solo para AZ500LAB02**|
   |Aplicación de directivas|**Enabled**|

1. Haga clic en **Siguiente**.

1. En la pestaña **Parámetros** del panel **Ubicaciones permitidas**, en la lista desplegable **Ubicaciones permitidas**, seleccione **Sur de Reino Unido** como la única ubicación permitida. 

   >**Nota**: Puede seleccionar más de una ubicación. Si la directiva requiere un conjunto diferente de parámetros, esta pestaña proporcionaría esas selecciones. 

1. Haga clic en **Revisar y crear** y, a continuación, en **Crear** para crear la asignación de directiva. 

   >**Nota**: Verá una notificación de que la asignación se ha realizado correctamente y que la asignación puede tardar unos 30 minutos en completarse.

   >**Nota**: La razón por la que la asignación de la directiva de Azure puede tardar hasta 30 minutos en tener efecto es que tiene que replicarse globalmente. Normalmente, esto solo tarda unos minutos.  Si se produce un error en la siguiente tarea, espere unos minutos e intente sus pasos de nuevo.

#### <a name="task-3-test-the-allowed-locations-policy-assignment"></a>Tarea 3: Prueba de la asignación de directiva de ubicaciones permitidas

En esta tarea, probará la asignación de directiva de ubicaciones permitidas. 

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Redes virtuales** y presione la tecla **Entrar**.

1. En el panel **Redes virtuales**, haga clic en **+ Crear**.

   >**Nota**: En primer lugar, intentará crear una red virtual en el Este de EE. UU. Puesto que no se trata de una ubicación permitida, la solicitud debería bloquearse. 

1. En la pestaña **Aspectos básicos** del panel **Crear red virtual**, especifique las opciones de configuración siguientes (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Resource group|**AZ500LAB02**|
    |Name|**myVnet**|
    |Region|**(EE. UU.) Este de EE. UU.**|

1. Haga clic en **Revisar y crear**. 

1. En la pestaña **Revisar y crear** del panel **Crear red virtual**, fíjese en el mensaje **Error de validación**. 

    > **Nota**: Si no aparece la advertencia **Error de validación**, haga clic en **Anterior** y espere unos minutos más.

1. En la pestaña **Aspectos básicos**, haga clic en el vínculo del mensaje de error para abrir el panel **Asignación de directivas**. Verá la asignación de directivas que restringe la ubicación.

1. Cierre el panel **Asignación de directivas**. En el panel **Crear red virtual** haga clic en la pestaña **Aspectos básicos** y, en la lista desplegable **Región**, seleccione **(Europa) Sur de Reino Unido**.

1. Haga clic en **Revisar y crear**,compruebe que se ha superado la validación, haga clic en **Crear** y compruebe que la red virtual se creó correctamente. 

> Resultados del ejercicio: en este ejercicio, ha aprendido a aplicar una directiva de Azure seleccionando definiciones de directivas integradas y a asignarla a un grupo de recursos.

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Cuando se le solicite, haga clic en **Volver a conectar**.

1. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB02" -Force -AsJob
    ```

1.  Cierre el panel de **Cloud Shell**. 
