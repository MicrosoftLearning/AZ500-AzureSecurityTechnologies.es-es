---
lab:
  title: '01: Control de acceso basado en roles'
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: e5ba41dc944bb38479479a321221ed1d4e034971
ms.sourcegitcommit: 2eb153f2856445e5afaa218a012cb92e3d48f24b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625716"
---
# <a name="lab-01-role-based-access-control"></a>Laboratorio 01: Control de acceso basado en roles
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto que muestre cómo se crean los usuarios y grupos de Azure. Además, cómo se usa el control de acceso basado en roles para asignar roles a grupos. En concreto, debe:

- Cree un grupo de administradores sénior que contenga la cuenta de usuario de Joseph Price como miembro.
- Cree un grupo de administradores junior que contenga la cuenta de usuario de Isabel García como miembro.
- Cree un grupo de consola de servicio que contenga la cuenta de usuario de Dylan Williams como miembro.
- Asigne el rol de colaborador de la máquina virtual al grupo Consola de servicio. 

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes

- Ejercicio 1: Creación del grupo Administradores sénior con la cuenta de usuario de Joseph Price como miembro (Azure Portal). 
- Ejercicio 2: Creación del grupo Administradores junior con la cuenta de usuario de Isabel García como miembro (PowerShell).
- Ejercicio 3: Creación del grupo Consola de servicio con el usuario Dylan Williams como miembro (CLI de Azure). 
- Ejercicio 4: Asignación del rol de colaborador de la máquina virtual al grupo Consola de servicio.

### <a name="exercise-1-create-the-senior-admins-group-with-the-user-account-joseph-price-as-its-member"></a>Ejercicio 1: Creación del grupo Administradores sénior con la cuenta de usuario de Joseph Price como miembro. 

#### <a name="estimated-timing-10-minutes"></a>Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Uso de Azure Portal para crear una cuenta de usuario para Joseph Price.
- Tarea 2: Uso de Azure Portal para crear un grupo de administradores sénior y agregar la cuenta de usuario de Joseph Price al grupo.

#### <a name="task-1-use-the-azure-portal-to-create-a-user-account-for-joseph-price"></a>Tarea 1: Uso de Azure Portal para crear una cuenta de usuario para Joseph Price 

En esta tarea, creará una cuenta de usuario para Joseph Price. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio y el rol Administrador global en el inquilino de Azure AD asociado a esa suscripción.

1. En el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Azure Active Directory** y presione la tecla **Entrar**.

1. En el panel **Introducción** del inquilino de Azure Active Directory, en la sección **Administrar**, seleccione **Usuarios** y, a continuación, seleccione **+ Nuevo usuario**.

1. En el panel **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada y especifique la siguiente configuración:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**Joseph**|
   |Name|**Joseph Price**|

1. Haga clic en el icono de copia situado junto a **Nombre de usuario** para copiar el usuario completo.

1. Asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada, active la casilla **Mostrar contraseña** para identificar la contraseña generada automáticamente. Tendría que proporcionar esta contraseña, junto con el nombre de usuario, a Joseph. 

1. Haga clic en **Crear**.

1. Actualice el panel **Usuarios \| Todos los usuarios** para comprobar que el nuevo usuario se creó en el inquilino de Azure AD.

#### <a name="task2-use-the-azure-portal-to-create-a-senior-admins-group-and-add-the-user-account-of-joseph-price-to-the-group"></a>Tarea 2: Uso de Azure Portal para crear un grupo de administradores sénior y agregar la cuenta de usuario de Joseph Price al grupo.

En esta tarea, creará el grupo *Administradores sénior*, agregará la cuenta de usuario de Joseph Price al grupo y lo configurará como propietario del grupo.

1. En Azure Portal, vuelva al panel que muestra el inquilino de Azure Active Directory. 

1. En la sección **Administrar**, haga clic en **Grupos** y, a continuación, seleccione **+ Nuevo grupo**.
 
1. En el panel **Nuevo grupo**, configure las opciones siguientes (deje las demás con los valores predeterminados):

   |Configuración|Valor|
   |---|---|
   |Tipo de grupo|**Seguridad**|
   |Nombre del grupo|**Administradores sénior**|
   |Tipo de pertenencia|**Asignado**|
    
1. Haga clic en el vínculo **No se ha seleccionado ningún propietario**, en el panel **Agregar propietarios** seleccione **Joseph Price** y haga clic en **Seleccionar**.

1. Haga clic en el vínculo **No se ha seleccionado ningún miembro**, en el panel **Agregar miembros** seleccione **Joseph Price** y haga clic en **Seleccionar**.

1. De nuevo en el panel **Nuevo grupo**, haga clic en **Crear**.

> Resultado: ha usado Azure Portal para crear un usuario y un grupo, y ha asignado el usuario al grupo. 

### <a name="exercise-2-create-a-junior-admins-group-containing-the-user-account-of-isabel-garcia-as-its-member"></a>Ejercicio 2: Creación de un grupo de administradores junior que contenga la cuenta de usuario de Isabel García como miembro.

#### <a name="estimated-timing-10-minutes"></a>Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Uso de PowerShell para crear una cuenta de usuario para Isabel García.
- Tarea 2: Uso de PowerShell para crear el grupo Administradores junior y agregar la cuenta de usuario de Isabel García al grupo. 

#### <a name="task-1-use-powershell-to-create-a-user-account-for-isabel-garcia"></a>Tarea 1: Uso de PowerShell para crear una cuenta de usuario para Isabel García.

En esta tarea, creará una cuenta de usuario para Isabel García mediante PowerShell.

1. Haga clic en el primer icono de la esquina superior derecha para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

1. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

   >**Nota**: Para pegar el texto copiado en Cloud Shell, haga clic con el botón derecho en la ventana del panel y seleccione **Pegar**. Como alternativa, puede usar la combinación de teclas **Mayús + Insertar**.

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear un objeto de perfil de contraseña:

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para establecer el valor de la contraseña en el objeto de perfil:
    ```powershell
    $passwordProfile.Password = 'Pa55w.rd1234'
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para conectarse a Azure Active Directory:

    ```powershell
    Connect-AzureAD
    ```
      
1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para identificar el nombre del inquilino de Azure AD: 

    ```powershell
    $domainName = ((Get-AzureAdTenantDetail).VerifiedDomains)[0].Name
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear una cuenta de usuario para Isabel García: 

    ```powershell
    New-AzureADUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -AccountEnabled $true -MailNickName 'Isabel'
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para enumerar los usuarios de Azure AD (las cuentas de Joseph e Isabel deberían aparecer en la lista): 

    ```powershell
    Get-AzureADUser 
    ```

#### <a name="task2-use-powershell-to-create-the-junior-admins-group-and-add-the-user-account-of-isabel-garcia-to-the-group"></a>Tarea 2: Uso de PowerShell para crear el grupo Administradores junior y agregar la cuenta de usuario de Isabel García al grupo.

En esta tarea, creará el grupo Administradores junior y agregar la cuenta de usuario de Isabel García al grupo mediante PowerShell.

1. En la misma sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear un nuevo grupo de seguridad denominado Administradores junior:
    
    ```powershell
    New-AzureADGroup -DisplayName 'Junior Admins' -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para enumerar los grupos del inquilino de Azure AD (la lista debe incluir los grupos Administradores sénior y Administradores junior):

    ```powershell
    Get-AzureADGroup
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para obtener una referencia a la cuenta de usuario de Isabel García:

    ```powershell
    $user = Get-AzureADUser -Filter "MailNickName eq 'Isabel'"
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para agregar la cuenta de usuario de Isabel García al grupo Administradores junior:
    
    ```powershell
    Add-AzADGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins" 
    ```

1. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para comprobar que el grupo Administradores junior contiene la cuenta de usuario de Isabel:

    ```powershell
    Get-AzADGroupMember -GroupDisplayName "Junior Admins"
    ```

> Resultado: ha usado PowerShell para crear un usuario y una cuenta de grupo, y ha agregado la cuenta de usuario a la cuenta de grupo. 


### <a name="exercise-3-create-a-service-desk-group-containing-the-user-account-of-dylan-williams-as-its-member"></a>Ejercicio 3: Creación de un grupo de consola de servicio que contenga la cuenta de usuario de Dylan Williams como miembro.

#### <a name="estimated-timing-10-minutes"></a>Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Uso del CLI de Azure para crear una cuenta de usuario para Dylan Williams.
- Tarea 2: Uso del CLI de Azure para crear el grupo Consola de servicio y agregar la cuenta de usuario de Dylan al grupo. 

#### <a name="task-1-use-azure-cli-to-create-a-user-account-for-dylan-williams"></a>Tarea 1: Uso del CLI de Azure para crear una cuenta de usuario para Dylan Williams.

En esta tarea, creará una cuenta de usuario para Dylan Williams.

1. En el menú desplegable de la esquina superior izquierda del panel de Cloud Shell, seleccione **Bash** y, cuando se le solicite, haga clic en **Confirmar**. 

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para identificar el nombre del inquilino de Azure AD:

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para crear un usuario, Dylan Williams. Use *yourdomain*.
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para enumerar las cuentas de usuario de Azure AD (la lista debe incluir las cuentas de usuario de Joseph, Isabel y Dylan)
    
    ```cli
    az ad user list --output table
    ```

#### <a name="task-2-use-azure-cli-to-create-the-service-desk-group-and-add-the-user-account-of-dylan-to-the-group"></a>Tarea 2: Uso del CLI de Azure para crear el grupo Consola de servicio y agregar la cuenta de usuario de Dylan al grupo. 

En esta tarea, creará el grupo Consola de servicio y asignará a Dylan al grupo. 

1. En la misma sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para crear un nuevo grupo de seguridad denominado Consola de servicio.

    ```cli
    az ad group create --display-name "Service Desk" --mail-nickname "ServiceDesk"
    ```
 
1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para enumerar los grupos de Azure AD (la lista debe incluir los grupos Consola de servicio, Administradores sénior y Administradores junior):

    ```cli
    az ad group list -o table
    ```

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para obtener una referencia a la cuenta de usuario de Dylan Williams: 

    ```cli
    USER=$(az ad user list --filter "displayname eq 'Dylan Williams'")
    ```

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para obtener la propiedad objectId de la cuenta de usuario de Dylan Williams: 

    ```cli
    OBJECTID=$(echo $USER | jq '.[].objectId' | tr -d '"')
    ```

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para agregar la cuenta de usuario de Dylan al grupo Consola de servicio: 

    ```cli
    az ad group member add --group "Service Desk" --member-id $OBJECTID
    ```

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para enumerar los miembros del grupo Consola de servicio y compruebe que incluye la cuenta de usuario de Dylan:

    ```cli
    az ad group member list --group "Service Desk"
    ```

1. Cierre el panel de Cloud Shell.

> Resultado: con el CLI de Azure, ha creado un usuario y una cuenta de grupo, y ha agregado la cuenta de usuario al grupo. 


### <a name="exercise-4-assign-the-virtual-machine-contributor-role-to-the-service-desk-group"></a>Ejercicio 4: Asignación del rol de colaborador de la máquina virtual al grupo Consola de servicio.

#### <a name="estimated-timing-10-minutes"></a>Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Creación de un grupo de recursos. 
- Tarea 2: Asignación de permisos de colaborador de la máquina virtual de la consola de servicio al grupo de recursos.  

#### <a name="task-1-create-a-resource-group"></a>Tarea 1: Creación de un grupo de recursos

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de recursos** y presione la tecla **Entrar**.

1. En el panel **Grupos de recursos**, haga clic en **+ Crear** y especifique la siguiente configuración:

   |Configuración|Value|
   |---|---|
   |Nombre de suscripción|el nombre de su suscripción de Azure|
   |Definición de un nombre de grupo de recursos|**AZ500Lab01**|
   |Location|**Este de EE. UU.**|

1. Seleccione **Revisar y crear** y, a continuación, **Crear**.

   >**Nota**: Espere a que se implemente el grupo de recursos. Use el icono **notificación** (parte superior derecha) para realizar un seguimiento del progreso del estado de la implementación.

1. De nuevo en el panel **Grupos de recursos**, actualice la página y compruebe que el nuevo grupo de recursos aparece en la lista de grupos de recursos.


#### <a name="task-2-assign-the-service-desk-virtual-machine-contributor-permissions"></a>Tarea 2: Asignación a la Consola de servicio de los permisos de colaborador de máquina virtual. 

1. En el panel **Grupos de recursos**, haga clic en la entrada del grupo de recursos **AZ500LAB01**.

1. En el panel **AZ500Lab01**, haga clic en **Control de acceso (IAM)** .

1. En el panel **AZ500Lab01 \| Control de acceso (IAM)** , **+ Agregar** y, en el menú desplegable, haga clic en **Agregar asignación de roles**.

1. En el panel **Agregar asignación de roles**, especifique la configuración siguiente:

   |Configuración|Valor|
   |---|---|
   |Role|**Colaborador de la máquina virtual**|
   |Asignar acceso a|**Usuario, grupo o entidad de servicio**|
   |Seleccionar|**Departamento de servicios**|

1. Seleccione **Guardar** para crear la asignación de roles.

1. En el panel **Control de acceso (IAM)** , seleccione **Asignaciones de roles**.

1. En el panel **AZ500Lab01 \| Control de acceso (IAM)** , en la pestaña **Comprobar acceso**, en el cuadro de texto **Buscar por nombre o dirección de correo electrónico**, escriba **Dylan Williams**.

1. En la lista de resultados de la búsqueda, seleccione la cuenta de usuario de Dylan Williams y, en el panel **Asignaciones de Dylan Williams: AZ500Lab01**, vea la asignación recién creada.

1. Cierre el panel **Asignaciones de Dylan Williams: AZ500Lab01**.

1. Repita los dos últimos pasos para comprobar el acceso de **Joseph Price**. 

> Resultado: ha asignado y comprobado los permisos de RBAC. 

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. 

1. En el menú desplegable de la esquina superior izquierda del panel de Cloud Shell, seleccione **PowerShell** y, cuando se le solicite, haga clic en **Confirmar**. 

1. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```
    Remove-AzResourceGroup -Name "AZ500LAB01" -Force -AsJob
    ```

1.  Cierre el panel de **Cloud Shell**. 
