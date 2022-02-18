---
lab:
  title: " 12: Puntos de conexión de servicio y protección del almacenamiento"
  module: Module 03 - Secure data and applications
ms.openlocfilehash: fddd3eaa1b6c161e172daa2ab94c539edaae4465
ms.sourcegitcommit: 4a94ae2382fc99dda007add73148dd4108227ab1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/27/2022
ms.locfileid: "137818193"
---
# <a name="lab-12-service-endpoints-and-securing-storage"></a>Laboratorio 12: Puntos de conexión de servicio y protección del almacenamiento
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto para demostrar la protección de recursos compartidos de archivos de Azure. En concreto, quiere:
    
- Crear un punto de conexión de almacenamiento para que el tráfico destinado a Azure Storage permanezca siempre dentro de la red troncal de Azure.
- Configurar el punto de conexión de almacenamiento para que solo los recursos de una subred específica puedan acceder al almacenamiento.
- Confirmar que los recursos fuera de la subred específica no pueden acceder al almacenamiento. 

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Puntos de conexión de servicio y almacenamiento de seguridad

### <a name="exercise-1-service-endpoints-and-security-storage"></a>Ejercicio 1: Puntos de conexión de servicio y almacenamiento de seguridad

### <a name="estimated-timing-45-minutes"></a>Tiempo estimado: 45 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Creación de una red virtual
- Tarea 2: Adición de una subred a la red virtual y configuración de un punto de conexión de almacenamiento
- Tarea 3: Configuración de un grupo de seguridad de red para restringir el acceso a la subred
- Tarea 4: Creación de una cuenta de almacenamiento con un recurso compartido de archivos
- Tarea 5: Implementación de máquinas virtuales en las subredes designadas
- Tarea 6: Prueba de la conexión del almacenamiento desde la subred privada para confirmar que se permite el acceso
- Tarea 7: Prueba de la conexión del almacenamiento desde la subred pública para confirmar que se denega el acceso 

#### <a name="task-1-create-a-virtual-network"></a>Tarea 1: Creación de una red virtual

En esta tarea, creará una red virtual.

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Redes virtuales** y presione la tecla **Entrar**.

3. En el panel **Redes virtuales**, haga clic en **+ Crear**.

4. En la pestaña **Aspectos básicos** del panel **Crear red virtual**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados) y haga clic en **Siguiente: Direcciones IP**:

    |Configuración|Value|
    |---|---|
    |Subscription|Nombre de la suscripción de Azure que está usando en este laboratorio|
    |Resource group|haga clic en **Crear nuevo** y escriba el nombre **AZ500LAB12**|
    |Name|**myVirtualNetwork**|
    |Region|**(EE. UU.) Este de EE. UU.**|

5. En la pestaña **Direcciones IP** del panel **Crear red virtual**, establezca el **espacio de direcciones IPv4** en **10.0.0.0/16**, en la columna **Nombre de subred**, haga clic en **predeterminado** y, en el panel **Editar subred**, especifique la siguiente configuración y haga clic en **Guardar**:

    |Configuración|Value|
    |---|---|
    |Nombre de subred|**Public**|
    |Intervalo de direcciones de subred|**10.0.0.0/24**|

6. De vuelta en la pestaña **Direcciones IP** del panel **Crear red virtual**, haga clic en **Revisar y crear**.

7. En la pestaña **Revisar y crear** del panel **Crear red virtual**, haga clic en **Crear**.

#### <a name="task-2-add-a-subnet-to-the-virtual-network-and-configure-a-storage-endpoint"></a>Tarea 2: Adición de una subred a la red virtual y configuración de un punto de conexión de almacenamiento

En esta tarea, creará otra subred y habilitará un punto de conexión de servicio en esa subred. Los punto de conexión de servicio están habilitados por servicio, por subred. 

1. En Azure Portal, vuelva al panel **Redes virtuales**.

2. En el panel **Redes virtuales**, haga clic en la entrada **myVirtualNetwork**.

3. En el panel **myVirtualNetwork**, en la sección **Configuración**, haga clic en **Subredes**.

4. En el panel **myVirtualNetwork \| Subredes**, haga clic en **+ Subred**. 

5. En el panel **Agregar subred**, configure las opciones siguientes (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Nombre de subred|**Privado**|
    |Intervalo de direcciones de subred|**10.0.1.0/24**|
    |Puntos de conexión del servicio|**Microsoft.Storage**|

6. En el panel **Agregar subred**, haga clic en **Guardar** para agregar la nueva subred.

    >**Nota**: La red virtual ahora tiene dos subredes: pública y privada. 
    
#### <a name="task-3-configure-a-network-security-group-to-restrict-access-to-the-subnet"></a>Tarea 3: Configuración de un grupo de seguridad de red para restringir el acceso a la subred

En esta tarea, creará un grupo de seguridad de red con dos reglas de seguridad de salida (almacenamiento e Internet) y una regla de seguridad de entrada (RDP). También asociará el grupo de seguridad de red a la subred Privada. Esto restringirá el tráfico saliente desde máquinas virtuales de Azure conectadas a esa subred.

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de seguridad de red** y presione la tecla **Entrar**.

2. En el panel **Grupos de seguridad de red**, haga clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** del panel **Crear grupo de seguridad de red**, especifique la siguiente configuración: 

    |Configuración|Value|
    |---|---|
    |Subscription|Nombre de la suscripción de Azure que está usando en este laboratorio|
    |Resource group|**AZ500LAB12**|
    |Name|**myNsgPrivate**|
    |Region|**Este de EE. UU.**|

4. Haga clic en **Revisar y crear** y, a continuación, en **Crear**.

    >**Nota**: En los pasos siguientes, creará una regla de seguridad de salida que permita la comunicación con el servicio de Azure Storage. 

5. En Azure Portal, vuelva al panel **Grupos de seguridad de red** y haga clic en la entrada **myNsgPrivate**.

6. En el panel **myNsgPrivate**, en la sección **Configuración**, haga clic en **Reglas de seguridad de salida**.

7. En el panel **myNsgPrivate \| Reglas de seguridad de salida**, haga clic en **+ Agregar**.

8. En el panel **Agregar regla de seguridad de salida**, especifique la siguiente configuración para permitir explícitamente el tráfico saliente a Azure Storage (deje todos los demás valores con su configuración predeterminada): 

    |Configuración|Value|
    |---|---|
    |Source|**VirtualNetwork**|
    |Source port ranges|**\***|
    |Destination|en la lista desplegable, seleccione **Etiqueta de servicio** y, a continuación, haga clic en **Almacenamiento**|
    |Intervalos de puertos de destino|**\***|
    |Protocolo|**Cualquiera**|
    |Acción|**Permitir**|
    |Priority|**1000**|
    |Name|**Allow-Storage-All (Permitir-almacenar-todo)**|

9. En el panel **Agregar regla de seguridad de salida**, haga clic en **Agregar** para crear la nueva regla de salida. 

10. En el panel **myNsgPrivate**, en la sección **Configuración**, haga clic en **Reglas de seguridad de salida** y, a continuación, haga clic en **+ Agregar**.

11. En el panel **Agregar regla de seguridad de salida**, especifique la siguiente configuración para denegar explícitamente el tráfico saliente a Internet (deje todos los demás valores con su configuración predeterminada): 

    |Configuración|Value|
    |---|---|
    |Source|**VirtualNetwork**|
    |Source port ranges|**\***|
    |Destination|en la lista desplegable, seleccione **Etiqueta de servicio** y, a continuación, haga clic en **Internet**|
    |Intervalos de puertos de destino|**\***|
    |Protocolo|**Cualquiera**|
    |Acción|**Deny**|
    |Priority|**1100**|
    |Name|**Deny-Internet-All**|

    >**Nota**: Esta regla invalida una regla predeterminada en todos los grupos de seguridad de red que permite la comunicación saliente de Internet. 

    >**Nota**: En los siguientes pasos, creará una regla de seguridad de entrada que permita que el tráfico de Protocolo de escritorio remoto (RDP) a la subred. La regla invalidará cualquier regla de seguridad predeterminada que deniegue todo el tráfico de entrada procedente de Internet. Las conexiones entre Escritorio remoto y la subred están permitidas, por lo que dicha conectividad podrá probarse en un paso posterior.

12. En el panel **myNsgPrivate**, en la sección **Configuración**, haga clic en **Reglas de seguridad de entrada** y, a continuación, en **+ Agregar**.

13. En el panel **Agregar regla de seguridad de entrada**, configure las opciones siguientes (deje todas las demás con los valores predeterminados): 

    |Configuración|Value|
    |---|---|
    |Source|**Cualquiera**|
    |Source port ranges|**\***|
    |Destination|**VirtualNetwork**|
    |Intervalos de puertos de destino|**3389**|
    |Protocolo|**TCP**|
    |Acción|**Permitir**|
    |Priority|**1200**|                                                    
    |Name|**Allow-RDP-All (Permitir-RDP-Todo)**|

14. En el panel **Agregar regla de seguridad de entrada**, haga clic en **Agregar** para crear la nueva regla de entrada. 

    >**Nota**: Ahora asociará el grupo de seguridad de red a la subred Privada.

15. En el panel **Subredes**, seleccione **+ Asociar**, especifique la siguiente configuración en la sección **Asociar subred** y, a continuación, haga clic en **Aceptar**:

    |Configuración|Value|
    |---|---|
    |Virtual network|**myVirtualNetwork**|
    |Subnet|**Privado**|

#### <a name="task-4-create-a-storage-account-with-a-file-share"></a>Tarea 4: Creación de una cuenta de almacenamiento con un recurso compartido de archivos

En esta tarea, creará una cuenta de almacenamiento con un recurso compartido de archivos y obtendrá la clave de la cuenta de almacenamiento.  

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Cuentas de almacenamiento** y presione la tecla **Entrar**.

2. En el panel **Cuentas de almacenamiento**, haga clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** del panel **Crear cuenta de almacenamiento**, configure las siguientes opciones (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Subscription|Nombre de la suscripción de Azure que está usando en este laboratorio|
    |Resource group|**AZ500LAB12**|
    |Nombre de la cuenta de almacenamiento|Cualquier nombre globalmente único con una longitud de 3 a 24 caracteres, que consta de letras y dígitos|
    |Location|**(EE. UU.) EastUS**|
    |Rendimiento|**Estándar (cuenta general-purpose v2)**|
    |Redundancia|**Almacenamiento con redundancia local (LRS)**|

4. En la pestaña **Aspectos básicos** del panel **Crear cuenta de almacenamiento**, haga clic en **Revisar y crear**, espere a que se complete el proceso de validación y, luego, haga clic en **Crear**.

    >**Nota**: Espere a que se cree la cuenta de almacenamiento. Este proceso tardará alrededor de 2 minutos.

5. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de recursos** y presione la tecla **Entrar**.

6. En el panel **Grupos de recursos**, en la lista de grupos de recursos, haga clic en la entrada **AZ500LAB12**.

7. En el panel del grupo de recursos **AZ500LAB12**, en la lista de recursos, haga clic en la entrada que representa la cuenta de almacenamiento recién creada.

8. En el panel **Información general** de la cuenta de almacenamiento, haga clic en **Recursos compartidos de archivos** en la pestaña **Almacenamiento de datos** y, a continuación, haga clic en **+ Recurso compartido de archivos**.

9. En el panel **Nuevo recurso compartido de archivos**, configure las opciones siguientes:

    |Configuración|Value|
    |---|---|
    |Nombre|**my-file-share**|

10. En el panel **Nuevo recurso compartido de archivos**, haga clic en **Crear**.

    >**Nota**: Ahora, recupere y registre el script de PowerShell que crea una asignación de unidad al recurso compartido de archivos de Azure. 

11. En el panel de la cuenta de almacenamiento, en la lista de recursos compartidos de archivos, haga clic en **my-file-share**.

12. En el panel **my-file-share**, haga clic en **Conectar**.

13. En el panel **Conectar** en la pestaña **Windows**, copie el script de PowerShell que crea una asignación de unidad Z al recurso compartido de archivos. 

    >**Nota**: Registre este script. Lo necesitará más adelante en este laboratorio para asignar el recurso compartido de archivos de la máquina virtual de Azure en la subred **Privada**.

14. Vuelva al panel de la cuenta de almacenamiento y, a continuación, en la sección **Seguridad y redes**, haga clic en **Redes**.

15. En el panel **Firewalls y redes virtuales**, seleccione la opción **Redes seleccionadas** y haga clic en el vínculo **+ Agregar red virtual existente**. 

16. En el panel **Agregar redes**, configure las opciones siguientes:

    |Configuración|Value|
    |---|---|
    |Subscription|nombre de la suscripción de Azure que usa en este laboratorio|
    |Redes virtuales|**myVirtualNetwork**|
    |Subredes|**Privado**|

17. En el panel **Agregar redes**, haga clic en **Agregar**. 

18. De nuevo en el panel de la cuenta de almacenamiento, haga clic en **Guardar**.

    >**Nota**: En este punto del laboratorio ha configurado una red virtual, un grupo de seguridad de red y una cuenta de almacenamiento con un recurso compartido de archivos. 

#### <a name="task-5-deploy-virtual-machines-into-the-designated-subnets"></a>Tarea 5: Implementación de máquinas virtuales en las subredes designadas

En esta tarea, creará dos máquinas virtuales: una en la subred Privada y la otra en la subred Pública. 

>**Nota**: La primera máquina virtual se conectará a la subred Privada.

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Máquinas virtuales** y presione la tecla **Entrar**.

2. En el panel **Máquinas virtuales**, haga clic en **+ Crear** y, en la lista desplegable, haga clic en **+ Máquina virtual**.

3. En la pestaña **Aspectos básicos** de la hoja **Crear una máquina virtual**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
    |Resource group|**AZ500LAB12**|
    |Nombre de la máquina virtual|**myVmPrivate**|
    |Region|**(EE. UU.) Este de EE. UU.**|
    |Imagen|**Windows Server 2022 Datacenter - Gen 2**|
    |Nombre de usuario|**localadmin**|
    |Contraseña|**Vuelva a usar la contraseña que creó en el laboratorio del módulo 6 (ejercicio 1, tarea 2).**|
    |Puertos de entrada públicos|**None**|
    |Ya tiene una licencia de Windows Server.|**No**|

    >**Nota**: Para los puertos de entrada públicos, nos basaremos en el grupo de seguridad de red creado previamente. 

4. Haga clic en **Siguiente: Discos >** y, en la pestaña **Discos** del panel **Crear una máquina virtual**, establezca el **tipo de disco del sistema operativo** en **HDD estándar** y haga clic en **Siguiente: Redes >** .

5. Haga clic en **Siguiente: Redes >** , en la pestaña **Redes** del panel **Crear una máquina virtual**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Virtual network|**myVirtualNetwork**|
    |Subnet|**Privado (10.0.1.0/24)**|
    |Dirección IP pública|**(new)myVmPrivate-ip**|
    |Grupo de seguridad de red de NIC|**None**|

6. Haga clic en **Siguiente: Administración >** , en la pestaña **Administración** del panel **Crear una máquina virtual**, acepte la configuración predeterminada y haga clic en **Revisar y crear**.

7. En el panel **Revisar y crear**, asegúrese de que la validación se ha realizado correctamente y haga clic en **Crear**.

    >**Nota**: La segunda máquina virtual se conectará a la subred Pública.

8. En el panel **Máquinas virtuales**, haga clic en **+ Agregar** y, en la lista desplegable, haga clic en **+ Máquina virtual**.

9. En la pestaña **Aspectos básicos** del panel **Crear una máquina virtual**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
    |Resource group|**AZ500LAB12**|
    |Nombre de la máquina virtual|**myVmPublic**|
    |Region|**(EE. UU.) Este de EE. UU.**|
    |Imagen|**Windows Server 2022 Datacenter - Gen 2**|
    |Nombre de usuario|**localadmin**|
    |Contraseña|**Vuelva a usar la contraseña que creó en el laboratorio del módulo 6 (ejercicio 1, tarea 2).**|
    |Puertos de entrada públicos|**None**|
    |Ya tiene una licencia de Windows Server.|**No**|

    >**Nota**: Para los puertos de entrada públicos, nos basaremos en el grupo de seguridad de red creado previamente. 

10. Haga clic en **Siguiente: Discos >** y, en la pestaña **Discos** del panel **Crear una máquina virtual**, establezca el **tipo de disco del sistema operativo** en **HDD estándar** y haga clic en **Siguiente: Redes >** .

11. Haga clic en **Siguiente: Redes >** , en la pestaña **Redes** del panel **Crear una máquina virtual**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Virtual network|**myVirtualNetwork**|
    |Subnet|**Público (10.0.0.0/24)**|
    |Dirección IP pública|**(new)myVmPublic-ip**|
    |Grupo de seguridad de red de NIC|**None**|

12. Haga clic en **Siguiente: Administración >** , en la pestaña **Administración** del panel **Crear una máquina virtual**, acepte la configuración predeterminada y haga clic en **Revisar y crear**.

13. En el panel **Revisar y crear**, asegúrese de que la validación se ha realizado correctamente y haga clic en **Crear**.

    >**Nota**: Puede continuar con la siguiente tarea una vez completada la implementación de la máquina virtual de Azure **myVMPrivate**.

#### <a name="task-6-test-the-storage-connection-from-the-private-subnet-to-confirm-that-access-is-allowed"></a>Tarea 6: Prueba de la conexión del almacenamiento desde la subred privada para confirmar que se permite el acceso

En esta tarea, se conectará a la máquina virtual myVMPrivate a través de Escritorio remoto y asignará una unidad al recurso compartido de archivos. 

1. Vuelva al panel **Máquinas virtuales**. 

2. En el panel **Máquinas virtuales**, haga clic en la entrada **myVMPrivate**.

3. En el panel **myVMPrivate**, haga clic en **Conectar** y, en el menú desplegable, haga clic en **RDP**. 

4. Haga clic en **Descargar archivo RDP** y úselo para conectarse a la máquina virtual de Azure **myVMPrivate** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

    |Configuración|Valor|
    |---|---|
    |Nombre de usuario|**localadmin**|
    |Contraseña|**Vuelva a usar la contraseña que creó en el laboratorio del módulo 6 (ejercicio 1, tarea 2).**|

    >**Nota**: Espere a que se abra la sesión de Escritorio remoto y a que se cargue el Administrador del servidor.

    >**Nota**: Ahora asignará la unidad Z a un recurso compartido de archivos de Azure dentro de la sesión de Escritorio remoto a un equipo con Windows Server 2022.

5. En la sesión de Escritorio remoto a **myVMPrivate**, haga clic en **Iniciar** y, a continuación, haga clic en **Windows PowerShell ISE**.

6. En la ventana **Windows PowerShell ISE**, abra el panel **Script** y pegue y ejecute el script de PowerShell que registró anteriormente en este laboratorio. El script tiene el siguiente formato:

    ```powershell
    $connectTestResult = Test-NetConnection -ComputerName <storage_account_name>.file.core.windows.net -Port 445
    if ($connectTestResult.TcpTestSucceeded) {
       # Save the password so the drive will persist on reboot
       cmd.exe /C "cmdkey /add:`"<storage_account_name>.file.core.windows.net`" /user:`"localhost\<storage_account_name>`"  /pass:`"<storage_account_key>`""
       # Mount the drive
       New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage_account_name>.file.core.windows.net\my-file-share" -Persist
    } else {
       Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
    }
    ```
    >**Nota**: El marcador de posición `<storage-account-name>` representa el nombre de la cuenta de almacenamiento que hospeda el recurso compartido de archivos y `<storage_account_name>` uno su clave principal

7. Inicie el Explorador de archivos y compruebe que la asignación de la unidad Z: se ha creado correctamente.

8. A continuación, en el panel de la consola de **Windows PowerShell ISE**, ejecute lo siguiente para comprobar que la máquina virtual no tiene conectividad saliente a Internet:

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**Nota**: La prueba devolverá un error, dado que el grupo de seguridad de red asociado a la subred Private no permite el acceso de salida a Internet.

9. Finalice la sesión de Escritorio remoto a la máquina virtual de Azure **myVMPrivate**.

    >**Nota**: En este momento, ha confirmado que la máquina virtual de la subred Privada puede acceder a la cuenta de almacenamiento. 

####  <a name="task-7-test-the-storage-connection-from-the-public-subnet-to-confirm-that-access-is-denied"></a>Tarea 7: Prueba de la conexión del almacenamiento desde la subred pública para confirmar que se denega el acceso 

1. Vuelva al panel **Máquinas virtuales**. 

2. En el panel **Máquinas virtuales**, haga clic en la entrada **myVMPublic**.

3. En el panel **myVMPublic**, haga clic en **Conectar** y, en el menú desplegable, haga clic en **RDP**. 

4. Haga clic en **Descargar archivo RDP** y úselo para conectarse a la máquina virtual de Azure **myVMPublic** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

    |Configuración|Valor|
    |---|---|
    |Nombre de usuario|**localadmin**|
    |Contraseña|**Vuelva a usar la contraseña que creó en el laboratorio del módulo 6 (ejercicio 1, tarea 2).**|

    >**Nota**: Espere a que se abra la sesión de Escritorio remoto y a que se cargue el Administrador del servidor.

    >**Nota**: Ahora asignará la unidad Z a un recurso compartido de archivos de Azure dentro de la sesión de Escritorio remoto a un equipo con Windows Server 2022.

5. En la sesión de Escritorio remoto a **myVMPublic**, haga clic en **Iniciar** y, a continuación, haga clic en **Windows PowerShell ISE**.

6. En la ventana de **Windows PowerShell ISE**, abra el panel **Script** y pegue y ejecute el mismo script de PowerShell que ejecutó en la sesión de Escritorio remoto en la máquina virtual de Azure **myVMPrivate**.

    >**Nota**: Esta vez, recibirá el error **New-PSDrive : Acceso denegado**. 

    >**Nota**: El acceso se deniega porque la máquina virtual *myVmPublic* está implementada en la subred Pública. La subred Pública no tiene un punto de conexión de servicio habilitado para Azure Storage. La cuenta de almacenamiento solo permite el acceso a la red desde la subred Privada.

7. A continuación, en el panel de la consola de **Windows PowerShell ISE**, ejecute lo siguiente para comprobar que la máquina virtual tenga conectividad saliente a Internet: 

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**Nota**: La prueba se realizará correctamente porque no hay ningún grupo de seguridad de red asociado a la subred Pública.

8. Finalice la sesión de Escritorio remoto a la máquina virtual de Azure **myVMPublic**.

    >**Nota**: En este momento, ha confirmado que la máquina virtual de la subred Pública no puede acceder a la cuenta de almacenamiento, pero tiene acceso a Internet.


**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados. 

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

2. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

3. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB12" -Force -AsJob
    ```

4.  Cierre el panel de **Cloud Shell**. 
 
