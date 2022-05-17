---
lab:
  title: 07 - Grupos de seguridad de red y grupos de seguridad de aplicaciones
  module: Module 02 - Implement Platform Protection
ms.openlocfilehash: d7cfed1e861215cf32c3b51c4a07aa6886575000
ms.sourcegitcommit: 2f08105eaaf0413d3ec3c12a3b078678151fd211
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2022
ms.locfileid: "141368716"
---
# <a name="lab-07-network-security-groups-and-application-security-groups"></a>Laboratorio 07: Grupos de seguridad de red y grupos de seguridad de aplicaciones
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que implemente la infraestructura de red virtual de su organización y realizar pruebas para asegurarse de que funciona correctamente. En concreto:

- La organización tiene dos grupos de servidores: servidores web y servidores de administración.
- Cada grupo de servidores debe estar en su propio grupo de seguridad de aplicaciones. 
- Debería poder usar RDP en los servidores de administración, pero no en los servidores web.
- Los servidores web deben mostrar la página web de IIS cuando se accede a esta desde Internet. 
- Las reglas de grupos de seguridad de red deben usarse para controlar el acceso a la red. 

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Crear la infraestructura de red virtual
- Ejercicio 2: Implementar máquinas virtuales y probar filtros de red

## <a name="network-and-application-security-groups-diagram"></a>Diagrama de grupos de seguridad de red y de aplicación

![imagen](https://user-images.githubusercontent.com/91347931/157526438-6da4f68b-db88-4931-a041-8474e66d3fe5.png)

## <a name="instructions"></a>Instrucciones

### <a name="exercise-1-create-the-virtual-networking-infrastructure"></a>Ejercicio 1: Crear la infraestructura de red virtual

### <a name="estimated-timing-20-minutes"></a>Tiempo estimado: 20 minutos

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crear una red virtual con una subred.
- Tarea 2: Crear dos grupos de seguridad de aplicaciones.
- Tarea 3: Crear un grupo de seguridad de red y asociarlo a la subred de red virtual.
- Tarea 4: Crear reglas de seguridad de NSG de entrada para todo el tráfico a los servidores web y RDP para los servidores de administración.

#### <a name="task-1--create-a-virtual-network"></a>Tarea 1: Crear una red virtual

En esta tarea, creará una red virtual para usarla con los grupos de seguridad de red y de aplicaciones. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Redes virtuales** y presione la tecla **Entrar**.

3. En el panel **Redes virtuales**, haga clic en **+ Crear**.

4. En la pestaña **Aspectos básicos** del panel **Crear red virtual**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados) y haga clic en **Siguiente: Direcciones IP**:

    |Configuración|Value|
    |---|---|
    |Subscription|Nombre de la suscripción de Azure que está usando en este laboratorio|
    |Resource group|Haga clic en **Crear nuevo** y escriba el nombre **AZ500LAB07**.|
    |Name|**myVirtualNetwork**|
    |Region|**Este de EE. UU.**|

5. En la pestaña **Direcciones IP** de la hoja **Crear red virtual**, establezca el **espacio de direcciones IPv4** en **10.0.0.0/16** y, si es necesario, en la columna **Nombre de subred**, haga clic en **predeterminado** y, en la hoja **Editar subred**, especifique la siguiente configuración y haga clic en **Guardar**:

    |Configuración|Value|
    |---|---|
    |Nombre de subred|**default**|
    |Intervalo de direcciones de subred|**10.0.0.0/24**|

6. De vuelta en la pestaña **Direcciones IP** del panel **Crear red virtual**, haga clic en **Revisar y crear**.

7. En la pestaña **Revisar y crear** del panel **Crear red virtual**, haga clic en **Crear**.

#### <a name="task-2--create-application-security-groups"></a>Tarea 2: Crear grupos de seguridad de aplicaciones

En esta tarea, creará un grupo de seguridad de aplicaciones.

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** de la parte superior de la página, escriba **Grupos de seguridad de aplicaciones** y presione la tecla **Entrar**.

2. En la hoja **Grupos de seguridad de aplicaciones**, haga clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** de la hoja **Crear un grupo de seguridad de aplicación**, especifique la siguiente configuración: 

    |Configuración|Value|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |Name|**myAsgWebServers**|
    |Region|**Este de EE. UU.**|

    >**Nota**: Este grupo será para los servidores web.

4. Haga clic en **Revisar y crear** y, a continuación, en **Crear**.

5. Vuelva a la hoja **Grupos de seguridad de aplicaciones** y haga clic en **+ Crear**.

6. En la pestaña **Aspectos básicos** de la hoja **Crear un grupo de seguridad de aplicación**, especifique la siguiente configuración: 

    |Configuración|Value|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |Name|**myAsgMgmtServers**|
    |Region|**Este de EE. UU.**|

    >**Nota**: Este grupo será para los servidores de administración.

7. Haga clic en **Revisar y crear** y, a continuación, en **Crear**.

#### <a name="task-3--create-a-network-security-group-and-associate-the-nsg-to-the-subnet"></a>Tarea 3: Crear un grupo de seguridad de red y asociarlo a la subred

En esta tarea, creará un grupo de seguridad de red. 

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de seguridad de red** y presione la tecla **Entrar**.

2. En el panel **Grupos de seguridad de red**, haga clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** del panel **Crear grupo de seguridad de red**, especifique la siguiente configuración: 

    |Configuración|Value|
    |---|---|
    |Subscription|Nombre de la suscripción de Azure que está usando en este laboratorio|
    |Resource group|**AZ500LAB07**|
    |Name|**myNsg**|
    |Region|**Este de EE. UU.**|

4. Haga clic en **Revisar y crear** y, a continuación, en **Crear**.

5. En Azure Portal, vuelva a la hoja **Grupos de seguridad de red** y haga clic en la entrada **myNsg**.

6. En la hoja **myNsg**, en la sección **Configuración**, haga clic en **Subredes** y, a continuación, haga clic en **+ Asociar**. 

7. En la hoja **Asociar subred**, especifique la siguiente configuración y haga clic en **Aceptar**:

    |Configuración|Value|
    |---|---|
    |Virtual network|**myVirtualNetwork**|
    |Subnet|**default**|

#### <a name="task-4-create-inbound-nsg-security-rules-to-all-traffic-to-web-servers-and-rdp-to-the-management-servers"></a>Tarea 4: Crear reglas de seguridad de NSG de entrada para todo el tráfico a los servidores web y RDP para los servidores de administración. 

1. En la hoja **myNsg**, en la sección **Configuración**, haga clic en **Reglas de seguridad de entrada**.

2. Revise las reglas de seguridad de entrada predeterminadas y luego haga clic en **+ Agregar**.

3. En la hoja **Agregar regla de seguridad de entrada**, especifique la siguiente configuración para permitir los puertos TCP 80 y 443 al grupo de seguridad de aplicaciones **myAsgWebServers** (deje todas las demás opciones con sus valores predeterminados): 

    |Configuración|Value|
    |---|---|
    |Destination|En la lista desplegable, seleccione **Grupo de seguridad de aplicaciones** y, a continuación, haga clic **en myAsgWebServers**.|
    |Intervalos de puertos de destino|**80, 443**|
    |Protocolo|**TCP**|
    |Priority|**100**|                                                    
    |Name|**Allow-Web-All**|

4. En la hoja **Agregar regla de seguridad de entrada**, haga clic en **Agregar** para crear la nueva regla de entrada. 

5. En la hoja **myNsg**, en la sección **Configuración**, haga clic en **Reglas de seguridad de entrada** y, a continuación, haga clic en **+ Agregar**.

6. En la hoja **Agregar regla de seguridad de entrada**, especifique la siguiente configuración para permitir el puerto RDP (TCP 3389) al grupo de seguridad de aplicaciones **myAsgMgmtServers** (deje todas las demás opciones con sus valores predeterminados): 

    |Configuración|Value|
    |---|---|
    |Destination|En la lista desplegable, seleccione **Grupo de seguridad de aplicaciones** y, a continuación, haga clic en **myAsgMgmtServers**.|
    |Intervalos de puertos de destino|**3389**|
    |Protocolo|**TCP**|
    |Priority|**110**|                                                    
    |Name|**Allow-RDP-All (Permitir-RDP-Todo)**|

7. En la hoja **Agregar regla de seguridad de entrada**, haga clic en **Agregar** para crear la nueva regla de entrada. 

> Resultado: ha implementado una red virtual, seguridad de red con reglas de seguridad de entrada y dos grupos de seguridad de aplicaciones. 

### <a name="exercise-2-deploy-virtual-machines-and-test-network-filters"></a>Ejercicio 2: Implementar máquinas virtuales y probar filtros de red

### <a name="estimated-timing-25-minutes"></a>Tiempo estimado: 25 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crear una máquina virtual para usarla como servidor web.
- Tarea 2: Crear una máquina virtual para usarla como servidor de administración. 
- Tarea 3: Asociar cada interfaz de red de máquinas virtuales a su grupo de seguridad de aplicaciones.
- Tarea 4: Probar el filtrado del tráfico de red.

#### <a name="task-1-create-a-virtual-machine-to-use-as-a-web-server"></a>Tarea 1: Crear una máquina virtual para usarla como servidor web.

En esta tarea, creará una máquina virtual para usarla como servidor web.

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Máquinas virtuales** y presione la tecla **Entrar**.

2. En el panel **Máquinas virtuales**, haga clic en **+ Crear** y, en la lista desplegable, haga clic en **+ Máquina virtual de Azure**.

3. En la pestaña **Aspectos básicos** de la hoja **Crear una máquina virtual**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
   |Resource group|**AZ500LAB07**|
   |Nombre de la máquina virtual|**myVmWeb**|
   |Region|**(EE. UU.) Este de EE. UU.**|
   |Imagen|**Windows Server 2022 Datacenter: Azure Edition - Gen. 2**|
   |Size|**Estándar D2s v3**|
   |Nombre de usuario|**Estudiante**|
   |Contraseña|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|
   |Confirmar contraseña|**Vuelva a escribir la contraseña**.|
   |Puertos de entrada públicos|**None**|
   |¿Quiere usar una licencia de Windows Server existente? |**No**|

    >**Nota**: Para los puertos de entrada públicos, nos basaremos en el grupo de seguridad de red creado previamente. 

4. Haga clic en **Siguiente: Discos >** y, en la pestaña **Discos** del panel **Crear una máquina virtual**, establezca el **tipo de disco del sistema operativo** en **HDD estándar** y haga clic en **Siguiente: Redes >** .

5. En la pestaña **Redes** de la hoja **Crear una máquina virtual**, seleccione la red creada anteriormente, **myVirtualNetwork**.

6. En **Grupo de seguridad de red de NIC**, seleccione **Ninguno**.

7. Haga clic en **Siguiente: Administración >** y, en la pestaña **Administración** de la hoja **Crear una máquina virtual**, compruebe la configuración siguiente:

   |Configuración|Value|
   |---|---|
   |Diagnósticos de arranque|**Enabled with managed storage account (recommended)** (Habilitado con la cuenta de almacenamiento administrada [recomendado])|

8. Haga clic en **Revisar y crear** y, en la hoja **Revisar y crear**, compruebe que la validación se ha realizado correctamente y haga clic en **Crear**.

#### <a name="task-2-create-a-virtual-machine-to-use-as-a-management-server"></a>Tarea 2: Crear una máquina virtual para usarla como servidor de administración. 

En esta tarea, creará una máquina virtual para usarla como servidor de administración.

1. En Azure Portal, vuelva a la hoja **Máquinas virtuales**, haga clic en **+ Crear** y, en la lista desplegable, haga clic en **+ Máquina virtual de Azure**.

2. En la pestaña **Aspectos básicos** de la hoja **Crear una máquina virtual**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
   |Resource group|**AZ500LAB07**|
   |Nombre de la máquina virtual|**myVMMgmt**|
   |Region|(EE. UU.) Este de EE. UU.|
   |Imagen|**Windows Server 2022 Datacenter: Azure Edition - Gen. 2**|
   |Size|**Estándar D2s v3**|
   |Nombre de usuario|**Estudiante**|
   |Contraseña|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|
   |Puertos de entrada públicos|**None**|
   |Ya tiene una licencia de Windows Server.|**No**|

    >**Nota**: Para los puertos de entrada públicos, nos basaremos en el grupo de seguridad de red creado previamente. 

3. Haga clic en **Siguiente: Discos >** y, en la pestaña **Discos** del panel **Crear una máquina virtual**, establezca el **tipo de disco del sistema operativo** en **HDD estándar** y haga clic en **Siguiente: Redes >** .

4. En la pestaña **Redes** de la hoja **Crear una máquina virtual**, seleccione la red creada anteriormente, **myVirtualNetwork**.

5. En **Grupo de seguridad de red de NIC**, seleccione **Ninguno**.

6. Haga clic en **Siguiente: Administración >** y, en la pestaña **Administración** de la hoja **Crear una máquina virtual**, especifique las siguientes opciones de configuración:

   |Configuración|Value|
   |---|---|
   |Diagnósticos de arranque|**Enabled with managed storage account (recommended)** (Habilitado con la cuenta de almacenamiento administrada [recomendado])|

7. Haga clic en **Revisar y crear** y, en la hoja **Revisar y crear**, compruebe que la validación se ha realizado correctamente y haga clic en **Crear**.

    >**Nota**: Espere a que se aprovisionen ambas máquinas virtuales antes de continuar. 

#### <a name="task-3-associate-each-virtual-machines-network-interface-to-its-application-security-group"></a>Tarea 3: Asocie cada interfaz de red de máquinas virtuales a su grupo de seguridad de aplicaciones.

En esta tarea, asociará cada interfaz de red de máquinas virtuales con el grupo de seguridad de aplicaciones correspondiente. La interfaz de máquina virtual myVMWeb se asociará al grupo ASG myAsgWebServers. La interfaz de máquina virtual myVMMgmt se asociará al grupo ASG myAsgMgmtServers. 

1. En Azure Portal, vuelva a la hoja **Máquinas virtuales** y compruebe que ambas máquinas virtuales aparecen con el estado **En ejecución**.

2. En la lista de máquinas virtuales, haga clic en la entrada **myVMWeb**.

3. En la hoja **myVMWeb**, en la sección **Configuración**, haga clic en **Redes** y, a continuación, en la hoja **MyVMWeb \| Redes**, haga clic en la pestaña **Grupos de seguridad de aplicaciones**.

4. Haga clic en **Configurar los grupos de seguridad de aplicaciones** y, en la lista desplegable **Grupo de seguridad de aplicaciones**, seleccione **myAsgWebServers** y haga clic en **Guardar**.

5. Vuelva a la hoja **Máquinas virtuales** y, en la lista de máquinas virtuales, haga clic en la entrada **myVMMgmt**.

6. En la hoja **myVMMgmt**, en la sección **Configuración**, haga clic en **Redes** y, a continuación, en la hoja **myVMMgmt \| Redes**, haga clic en la pestaña **Grupos de seguridad de aplicaciones**.

7. Haga clic en **Configurar los grupos de seguridad de aplicaciones** y, en la lista desplegable **Grupo de seguridad de aplicaciones**, seleccione **myAsgMgmtServers** y haga clic en **Guardar**.

#### <a name="task-4-test-the-network-traffic-filtering"></a>Tarea 4: Probar el filtrado del tráfico de red

En esta tarea, probará los filtros de tráfico de red. Debería poder usar RDP en la máquina virtual myVMMgmnt. Debería poder conectarse desde Internet a la máquina virtual myVMWeb y ver la página web predeterminada de IIS.  

1. Vuelva a la hoja de la máquina virtual **myVMMgmt**.

2. En la hoja **myVMMgmt**, haga clic en **Conectar** y, en el menú desplegable, haga clic en **RDP**. 

3. Haga clic en **Descargar archivo RDP** y úselo para conectarse a la VM de Azure **myVMMgmt** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**Estudiante**|
   |Contraseña|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|

    >**Nota**: Compruebe que la conexión de Escritorio remoto se ha realizado correctamente. En este momento ha confirmado que puede conectarse a través de Escritorio remoto a myVMMgmt.

4. En Azure Portal, vaya a la hoja de la máquina virtual **myVMWeb**.

5. En la hoja **myVMWeb**, en la sección **Operaciones**, haga clic en **Ejecutar comando** y, a continuación, en **RunPowerShellScript**.

6. En el panel **Ejecutar script de comando**, ejecute lo siguiente para instalar el rol de servidor web en **myVmWeb**:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    >**Nota**: Espere a que termine la instalación. Esto puede tardar un par de minutos. En ese momento, puede comprobar que se puede acceder a myVMWeb a través de HTTP/HTTPS.

7. En Azure Portal, vuelva a la hoja **myVMWeb**.

8. En la hoja **myVMWeb**, identifique la **Dirección IP pública** de la VM de Azure myVmWeb.

9. Abra otra pestaña del explorador y vaya a la dirección IP que identificó en el paso anterior.

    >**Nota**: La página del explorador debe mostrar la página principal predeterminada de IIS porque se permite la entrada en el puerto 80 desde Internet en función de la configuración del grupo de seguridad de aplicaciones **myAsgWebServers**. La interfaz de red de la VM de Azure myVMWeb está asociada a ese grupo de seguridad de aplicaciones. 

> Resultado: ha validado que la configuración de NSG y ASG funciona y que el tráfico se administra correctamente. 

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados. 

1. Haga clic en el primer icono de la esquina superior derecha para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

2. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

3. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

4.  Cierre el panel de **Cloud Shell**. 
