---
lab:
  title: 13 - Azure Monitor
  module: Module 04 - Manage security operations
ms.openlocfilehash: d7418287b895ccb5af66f01b499181b321e2bc36
ms.sourcegitcommit: 3c178de473f4f986a3a7ea1d03c9f5ce699a05a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2022
ms.locfileid: "147871977"
---
# <a name="lab-13-azure-monitor"></a>Laboratorio 13: Azure Monitor
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto de supervisión del rendimiento de la máquina virtual. En concreto, quiere:

- Configurar una máquina virtual para que se puedan recopilar datos de telemetría y registros.
- Mostrar la telemetría y los registros que se pueden recopilar.
- Mostrar cómo se pueden utilizar y consultar los datos. 

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Recopilar datos de una máquina virtual de Azure con Azure Monitor

## <a name="azure-monitor"></a>Azure Monitor

![imagen](https://user-images.githubusercontent.com/91347931/157536648-0a286514-a7e2-4058-9dea-e42da21eef76.png)

## <a name="instructions"></a>Instrucciones

### <a name="exercise-1-collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Ejercicio 1: Recopilar datos de una máquina virtual de Azure con Azure Monitor

### <a name="exercise-timing-20-minutes"></a>Tiempo del ejercicio: 20 minutos

En este ejercicio completará las tareas siguientes: 

- Tarea 1: Implementar una máquina virtual de Azure 
- Tarea 2: Crear un área de trabajo de Log Analytics
- Tarea 3: Habilitar la extensión de máquina virtual de Log Analytics
- Tarea 4: Recopilar datos de rendimiento y eventos de máquina virtual
- Tarea 5: Ver y consultar los datos recopilados 

#### <a name="task-1-deploy-an-azure-virtual-machine"></a>Tarea 1: Implementar una máquina virtual de Azure

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. Haga clic en el primer icono de la esquina superior derecha para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

3. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

4. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para crear el grupo de recursos que se usará en este laboratorio:
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Nota**: Este grupo de recursos se usará para los laboratorios 13, 14 y 15. 

5. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para crear una máquina virtual de Azure. 

    >**Atención**: El comando New-AzVm no funciona en la versión 4.24 de la CLI de Azure y Microsoft está investigando actualmente para su resolución.  El trabajo de este laboratorio es instalar la versión 4.23.0 de Az.Compute, que no se ve afectadas por este problema, y revertir a ella.
   
    >**Instrucciones**: Reversión a la versión 4.23.0 de Az.Compute 
  
   #### <a name="step-1-download-the-working-version-of-the-module-4230-into-your-cloud-shell-session"></a>Paso 1: Descargar la versión de trabajo del módulo (4.23.0) en la sesión de Cloud Shell 
   **Tipo**: Install-Module -Name Az.Compute -Force -RequiredVersion 4.23.0

   #### <a name="step-2-start-a-new-powershell-session-that-will-allow-the-azcompute-assembly-version-to-be-loaded"></a>Paso 2: Iniciar una nueva sesión de PowerShell que permitirá cargar la versión del ensamblado Az.Compute 
   **Tipo**: pwsh

   #### <a name="step-3-verify-that-version-4230-is-loaded"></a>Paso 3: Comprobar que está cargada la versión 4.23.0
   **Tipo**: Get-Module -Name Az.Compute
   
    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -OpenPorts 80,3389
    ```

6.  Cuando se le pidan las credenciales:

    |Configuración|Value|
    |---|---|
    |Usuario |**localadmin**|
    |Contraseña|**Use su contraseña personal creada en: Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|

    >**Nota**: Espere a que la implementación se complete. 

7. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para confirmar que se creó la máquina virtual denominada **myVM** y que su valor de **provisioningState** es **Correcto**.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. Cierre el panel de Cloud Shell. 

#### <a name="task-2-create-a-log-analytics-workspace"></a>Tarea 2: Crear un área de trabajo de Log Analytics

En esta tarea, creará un área de trabajo de Log Analytics. 

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Áreas de trabajo de Log Analytics** y presione la tecla **Entrar**.

2. En la página **Áreas de trabajo de Log Analytics**, haga clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** de la hoja **Crear un área de trabajo de Log Analytics**, especifique las siguientes opciones (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Subscription|nombre de la suscripción de Azure que usa en este laboratorio|
    |Resource group|**AZ500LAB131415**|
    |Name|Cualquier nombre válido y único globalmente|
    |Region|**(EE. UU.) Este de EE. UU.**|

4. Seleccione **Revisar + crear**.

5. En la pestaña **Revisar y crear** del panel **Crear área de trabajo de Log Analytics**, seleccione **Crear**.

#### <a name="task-3-enable-the-log-analytics-virtual-machine-extension"></a>Tarea 3: Habilitar la extensión de máquina virtual de Log Analytics

En esta tarea, habilitará la extensión de máquina virtual de Log Analytics. Esta extensión instala el agente de Log Analytics en máquinas virtuales Windows y Linux. Este agente recopila datos de la máquina virtual y los transfiere al área de trabajo de Log Analytics que designe. Una vez instalado el agente, se actualizará automáticamente para garantizar que siempre tenga las características y correcciones más recientes. 

1. En Azure Portal, vuelva a la hoja **Áreas de trabajo de Log Analytics** y, en la lista de áreas de trabajo, haga clic en la entrada que representa el área de trabajo que creó en la tarea anterior.

2. En el panel Área de trabajo de Log Analytics, en la página **Información general** y la sección **Conectar un origen de datos**, haga clic en la entrada **Máquinas virtuales de Azure**.

    >**Nota**: Para que el agente se instale correctamente, la máquina virtual debe estar en ejecución.

3. En la lista de máquinas virtuales, busque la entrada que representa la VM de Azure **myVM** que implementó en la primera tarea de este ejercicio y observe que aparece como **No conectada**.

4. Haga clic en la entrada **myVM** y, a continuación, en la hoja **myVM**, haga clic en **Conectar**. 

5. Espere a que la máquina virtual se conecte al área de trabajo de Log Analytics.

    >**Nota**: Esta operación puede tardar unos minutos. El **Estado** que se muestra en la hoja **myVM** cambiará de **Conectando** a **Esta área de trabajo**. 

#### <a name="task-4-collect-virtual-machine-event-and-performance-data"></a>Tarea 4: Recopilar datos de rendimiento y eventos de máquina virtual

En esta tarea, configurará la recopilación del registro del sistema Windows y varios contadores de rendimiento comunes. También revisará otros orígenes que están disponibles.

1. En Azure Portal, vuelva al área de trabajo de Log Analytics que creó anteriormente en este ejercicio.

2. En la hoja Área de trabajo de Log Analytics, en la sección **Configuración**, haga clic en **Administración de agentes heredados**.

3. En la hoja **Configuración de agentes**, revise las opciones configurables, incluidas Registros de eventos de Windows, Contadores de rendimiento de Windows, Contadores de rendimiento de Linux, Registros de IIS y Syslog. 

4. Asegúrese de que la opción **Registros de eventos de Windows** está seleccionada, haga clic en **+ Add windows event log** (+ Agregar registro de eventos de Windows). En la lista de tipos de registro de eventos, seleccione **Sistema**.

    >**Nota**: Esta es la forma de agregar registros de eventos al área de trabajo. Otras opciones son, por ejemplo, **Eventos de hardware** o **Servicio de administración de claves**.  

5. Desactive la casilla **Información** y deje activadas las casillas **Error** y **Advertencia**.

6. Haga clic en **Contadores de rendimiento de Windows**, haga clic en **+ Add performance counter** (+ Agregar contador de rendimiento), revise la lista de contadores de rendimiento disponibles y agregue los siguientes:

    - Memoria (\*)\Mbytes de memoria disponibles
    - Proceso(\*)\\% de tiempo de procesador
    - Seguimiento de eventos para Windows\Uso de memoria total --- Bloque no paginado
    - Seguimiento de eventos para Windows\Uso de memoria total --- Bloque paginado

    >**Nota**: Los contadores se agregan y configuran con un intervalo de ejemplo de colección de 60 segundos.
  
7. En la hoja **Agents configuration** (Configuración de agentes), haga clic en **Aplicar**.

#### <a name="task-5-view-and-query-collected-data"></a>Tarea 5: Ver y consultar los datos recopilados

En esta tarea, ejecutará una búsqueda de registros en la recopilación de datos. 

1. En Azure Portal, vuelva al área de trabajo de Log Analytics que creó anteriormente en este ejercicio.

2. En la hoja Área de trabajo de Log Analytics, en la sección **General**, haga clic en **Registros**.

3. Si es necesario, cierre la ventana **Bienvenido a Log Analytics**. 

4. En el panel **Consultas**, en la columna **Todas las consultas**, desplácese hacia abajo hasta la parte inferior de la lista de tipos de recursos y haga clic en **Máquinas virtuales**.
    
5. Revise la lista de consultas predefinidas, seleccione **Memory and CPU usage** (Uso de memoria y CPU) y haga clic en el botón **Ejecutar** correspondiente.

    >**Nota**: Puede empezar con la consulta **Memoria disponible de la máquina virtual**. Si no obtiene ningún resultado, compruebe que el ámbito está establecido en máquina virtual.

6. La consulta se abrirá automáticamente en una nueva pestaña de consulta. 

    >**Nota**: Log Analytics usa el lenguaje de consulta Kusto. Puede personalizar las consultas existentes o crear las suyas propias. 

    >**Nota**: Los resultados de la consulta seleccionada se muestran automáticamente debajo del panel de consulta. Para volver a ejecutar la consulta, haga clic en **Ejecutar**.

    >**Nota**: Dado que esta máquina virtual se acaba de crear, es posible que aún no haya ningún dato. 

    >**Nota**: Tiene la opción de mostrar datos en formatos diferentes. También tiene la opción de crear una regla de alertas basada en los resultados de la consulta.

    >**Nota**: Puede generar alguna carga adicional en la VM de Azure que implementó anteriormente en este laboratorio mediante los pasos siguientes:

    1. Vaya a la hoja VM de Azure.
    2. En el panel VM de Azure, en la sección **Operaciones**, seleccione **Ejecutar comando**; en el panel **RunPowerShellScript**, escriba el siguiente script y haga clic en **Ejecutar**:
    3. 
       ```cmd
       cmd
       :loop
       dir c:\ /s > SWAP
       goto loop
       ```
       
    4. Vuelva a la hoja Log Analytics y ejecute de nuevo la consulta. Es posible que tenga que esperar unos minutos a que se recopilen los datos y volver a ejecutar la consulta.

> Resultados: ha usado un área de trabajo de Log Analytics para configurar orígenes de datos y registros de consultas. 

**Limpieza de recursos**

>**Nota**: No quite los recursos de este laboratorio, ya que son necesarios para los laboratorios de Azure Security Center y Azure Sentinel.
 
