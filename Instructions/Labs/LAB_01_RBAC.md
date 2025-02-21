---
lab:
  title: '01: Control de acceso basado en roles'
  module: Module 01 - Manage Identity and Access
---

# Laboratorio 01: Control de acceso basado en roles
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto que muestre cómo se crean los usuarios y grupos de Azure. Además, cómo se usa el control de acceso basado en roles para asignar roles a grupos. En concreto, debe:

- Cree un grupo de administradores sénior que contenga la cuenta de usuario de Joseph Price como miembro.
- Cree un grupo de administradores junior que contenga la cuenta de usuario de Isabel García como miembro.
- Cree un grupo de consola de servicio que contenga la cuenta de usuario de Dylan Williams como miembro.
- Asigne el rol de colaborador de la máquina virtual al grupo Consola de servicio. 

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Creación del grupo Administradores sénior con la cuenta de usuario de Joseph Price como miembro (Azure Portal). 
- Ejercicio 2: Creación del grupo Administradores junior con la cuenta de usuario de Isabel García como miembro (PowerShell).
- Ejercicio 3: Creación del grupo Consola de servicio con el usuario Dylan Williams como miembro (CLI de Azure). 
- Ejercicio 4: Asignación del rol de colaborador de la máquina virtual al grupo Consola de servicio.

## Diagrama de arquitectura de control de acceso basado en rol

![imagen](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/506cde9c-5242-4438-a793-f88a5434a2b2)

## Instrucciones

### Ejercicio 1: Creación del grupo Administradores sénior con la cuenta de usuario de Joseph Price como miembro. 

#### Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Uso de Azure Portal para crear una cuenta de usuario para Joseph Price.
- Tarea 2: Uso de Azure Portal para crear un grupo de administradores sénior y agregar la cuenta de usuario de Joseph Price al grupo.

#### Tarea 1: Uso de Azure Portal para crear una cuenta de usuario para Joseph Price 

En esta tarea, creará una cuenta de usuario para Joseph Price. 

1. Inicie una sesión del explorador e inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicia sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usas para este laboratorio y el rol Administrador global en el inquilino de Microsoft Entra asociado a esa suscripción.

2. En el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de Azure Portal, escriba **Microsoft Entra ID** y presione la tecla **Entrar**.

3. En el panel **Introducción** del inquilino de Microsoft Entra ID, en la sección **Administrar**, seleccione **Usuarios** y, a continuación, seleccione **+ Nuevo usuario**.

4. En el panel **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada y especifique la siguiente configuración:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**Joseph**|
   |Name|**Joseph Price**|

5. Haga clic en el icono de copia situado junto a **Nombre de usuario** para copiar el usuario completo.

6. Asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada, active la casilla **Mostrar contraseña** para identificar la contraseña generada automáticamente. Tendría que proporcionar esta contraseña, junto con el nombre de usuario, a Joseph. 

7. Haga clic en **Crear**.

8. Actualiza la hoja **Usuarios \| Todos los usuarios** para verificar que el nuevo usuario se crease en el inquilino de Microsoft Entra.

#### Tarea 2: Uso de Azure Portal para crear un grupo de administradores sénior y agregar la cuenta de usuario de Joseph Price al grupo.

En esta tarea, creará el grupo *Administradores sénior*, agregará la cuenta de usuario de Joseph Price al grupo y lo configurará como propietario del grupo.

1. En Azure Portal, vuelva al panel que muestra el inquilino de Microsoft Entra ID. 

2. En la sección **Administrar**, haga clic en **Grupos** y, a continuación, seleccione **+ Nuevo grupo**.
 
3. En el panel **Nuevo grupo**, configure las opciones siguientes (deje las demás con los valores predeterminados):

   |Configuración|Valor|
   |---|---|
   |Tipo de grupo|**Seguridad**|
   |Nombre del grupo|**Administradores sénior**|
   |Tipo de pertenencia|**Asignado**|
    
4. Haz clic en el vínculo **No se ha seleccionado ningún propietario**, en la hoja **Agregar propietarios** selecciona **Joseph Price** y haz clic en **Seleccionar**.

5. Haz clic en el vínculo **No se ha seleccionado ningún miembro**, en la hoja **Agregar miembros** selecciona **Joseph Price** y haz clic en **Seleccionar**.

6. De nuevo en la hoja **Nuevo grupo**, haz clic en **Crear**.

> Resultado: has usado Azure Portal para crear un usuario y un grupo, y has asignado el usuario al grupo. 

### Ejercicio 2: Creación de un grupo de administradores junior que contenga la cuenta de usuario de Isabel García como miembro.

#### Tiempo estimado: 10 minutos

En este ejercicio completarás las tareas siguientes:

- Tarea 1: Uso de PowerShell para crear una cuenta de usuario para Isabel García.
- Tarea 2: Uso de PowerShell para crear el grupo Administradores junior y agregar la cuenta de usuario de Isabel García al grupo. 

#### Tarea 1: Uso de PowerShell para crear una cuenta de usuario para Isabel García.

En esta tarea, crearás una cuenta de usuario para Isabel García mediante PowerShell.

1. **Abre Cloud Shell** haciendo clic en el **icono Cloud Shell** de la esquina superior derecha de Azure Portal.

2. **Si se te solicita, configura Cloud Shell mediante la creación de una cuenta de almacenamiento**. Esto solo es necesario **la primera vez** que inicias Cloud Shell.

3. **Asegúrate de que PowerShell esté seleccionado** en el menú desplegable superior izquierdo del panel de Cloud Shell.

   >**Nota**: para pegar el texto copiado en Cloud Shell, haz clic con el botón derecho en la ventana del panel y selecciona **Pegar**. Como alternativa, puedes usar la combinación de teclas **Mayús + Insertar**.

4. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para crear un objeto de perfil de contraseña:

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

5. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para establecer el valor de la contraseña en el objeto de perfil:
    ```powershell
    $passwordProfile.Password = "Pa55w.rd1234"
    ```

6. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para conectarte a Microsoft Entra ID:

    ```powershell
    Connect-AzureAD
    ```
      
7. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para identificar el nombre de tu inquilino de Microsoft Entra: 

    ```powershell
    $domainName = ((Get-AzureAdTenantDetail).VerifiedDomains)[0].Name
    ```

8. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para crear una cuenta de usuario para Isabel García: 

    ```powershell
    New-AzureADUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -AccountEnabled $true -MailNickName 'Isabel'
    ```

9. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para enumerar los usuarios de Microsoft Entra ID (las cuentas de Joseph e Isabel deberían aparecer en la lista): 

    ```powershell
    Get-AzureADUser -All $true | Where-Object {$_.UserPrincipalName -like "*43846135@LOD*"} 
    ```

#### Tarea 2: Uso de PowerShell para crear el grupo Administradores junior y agregar la cuenta de usuario de Isabel García al grupo.

En esta tarea, crearás el grupo Administradores junior y agregar la cuenta de usuario de Isabel García al grupo mediante PowerShell.

1. En la misma sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para **crear un nuevo grupo de seguridad** denominado Administradores junior:
   
   ```powershell
   New-AzureADGroup -DisplayName 'Junior Admins43846135' -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins
   ```
   
2. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para **enumerar los grupos** del inquilino de Microsoft Entra (la lista debe incluir los grupos Administradores sénior y Administradores junior)
   
   ```powershell
   Get-AzureADGroup
   ```

3. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para **obtener una referencia** a la cuenta de usuario de Isabel García:

   ```powershell
   $user = Get-AzureADUser -Filter "UserPrincipalName eq 'Isabel-43846135@LODSPRODMCA.onmicrosoft.com'"
   ```

4. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para agregar la cuenta de usuario de Isabel al grupo júnior Admins43846135:
   ```powershell
   Add-AzADGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins43846135"
   ```

5. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para comprobar que el grupo júnior Admins43846135 contiene la cuenta de usuario de Isabel:
   
   ```powershell
    Get-AzADGroupMember -GroupDisplayName "Junior Admins43846135"
    ```
   
> Resultado: has usado PowerShell para crear un usuario y una cuenta de grupo, y has agregado la cuenta de usuario a la cuenta de grupo. 

### Ejercicio 3: Creación de un grupo de consola de servicio que contenga la cuenta de usuario de Dylan Williams como miembro.

#### Tiempo estimado: 10 minutos

En este ejercicio completarás las tareas siguientes:

- Tarea 1: Uso del CLI de Azure para crear una cuenta de usuario para Dylan Williams.
- Tarea 2: Uso del CLI de Azure para crear el grupo Consola de servicio y agregar la cuenta de usuario de Dylan al grupo. 

#### Tarea 1: Uso del CLI de Azure para crear una cuenta de usuario para Dylan Williams.

En esta tarea, crearás una cuenta de usuario para Dylan Williams.

1. En el menú desplegable de la esquina superior izquierda del panel de Cloud Shell, selecciona **Bash** y, cuando se te solicite, haz clic en **Confirmar**. 

2. En la sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para identificar el nombre de tu inquilino de Microsoft Entra:

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

3. En la sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para crear un usuario, Dylan Williams. Usa *yourdomain*.
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
4. En la sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para enumerar las cuentas de usuario de Microsoft Entra ID (la lista debe incluir las cuentas de usuario de Joseph, Isabel y Dylan)
    
    ```cli
    az ad user list --output table
    ```

#### Tarea 2: Uso del CLI de Azure para crear el grupo Consola de servicio y agregar la cuenta de usuario de Dylan al grupo. 

En esta tarea, crearás el grupo Consola de servicio y asignarás a Dylan al grupo. 

1. En la misma sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para crear un nuevo grupo de seguridad denominado Consola de servicio.

    ```cli
    az ad group create --display-name "Service Desk" --mail-nickname "ServiceDesk"
    ```
 
2. En la sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para enumerar los grupos de Microsoft EntraID (la lista debe incluir los grupos Consola de servicio, Administradores sénior y Administradores júnior):

    ```cli
    az ad group list -o table
    ```

3. En la sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para obtener una referencia a la cuenta de usuario de Dylan Williams: 

    ```cli
    USER=$(az ad user list --filter "displayname eq 'Dylan Williams'")
    ```

4. En la sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para obtener la propiedad objectId de la cuenta de usuario de Dylan Williams: 

    ```cli
    OBJECTID=$(echo $USER | jq '.[].id' | tr -d '"')
    ```

5. En la sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para agregar la cuenta de usuario de Dylan al grupo Consola de servicio: 

    ```cli
    az ad group member add --group "Service Desk" --member-id $OBJECTID
    ```

6. En la sesión de Bash del panel de Cloud Shell, ejecuta lo siguiente para enumerar los miembros del grupo Consola de servicio y comprueba que incluye la cuenta de usuario de Dylan:

    ```cli
    az ad group member list --group "Service Desk"
    ```

7. Cierra el panel de Cloud Shell.

> Resultado: con el CLI de Azure, has creado un usuario y una cuenta de grupo, y has agregado la cuenta de usuario al grupo. 


### Ejercicio 4: Asignación del rol de colaborador de la máquina virtual al grupo Consola de servicio.

#### Tiempo estimado: 10 minutos

En este ejercicio completarás las tareas siguientes:

- Tarea 1: Creación de un grupo de recursos. 
- Tarea 2: Asignación de permisos de colaborador de la máquina virtual de la consola de servicio al grupo de recursos.  

#### Tarea 1: Creación de un grupo de recursos

1. En Azure Portal, usa el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escribe **Grupos de recursos** y presiona la tecla **Entrar**.

2. En la hoja **Grupos de recursos**, haz clic en **+ Crear** y especifica la siguiente configuración:

   |Configuración|Value|
   |---|---|
   |Nombre de suscripción|el nombre de su suscripción de Azure|
   |Nombre de grupo de recursos|**AZ500Lab01**|
   |Ubicación|**Este de EE. UU.**|

3. Selecciona **Revisar y crear** y, a continuación, **Crear**.

   >**Nota**: espera a que se implemente el grupo de recursos. Usa el icono **notificación** (parte superior derecha) para realizar un seguimiento del progreso del estado de la implementación.

4. De nuevo, en la hoja **Grupos de recursos**, actualiza la página y comprueba que el nuevo grupo de recursos aparece en la lista de grupos de recursos.


#### Tarea 2: Asignación a la Consola de servicio de los permisos de colaborador de máquina virtual. 

1. En la hoja **Grupos de recursos**, haz clic en la entrada del grupo de recursos **AZ500LAB01**.

2. En la hoja **AZ500Lab01**, haz clic en **Control de acceso (IAM)** en la parte central.

3. En la hoja **AZ500Lab01 \| Control de acceso (IAM)** , **+ Agregar** y, en el menú desplegable, haz clic en **Agregar asignación de roles**.

4. En la hoja **Agregar asignación de roles**, completa cada una de las siguientes opciones de configuración antes de hacer clic en Siguiente:

   **Nota:** después de completar todos los pasos, haz clic en **Siguiente**.

   |Configuración|Value|
   |---|---|
   |Rol en la pestaña de búsqueda|**Colaborador de la máquina virtual**|
   |Asignar acceso a (en el panel Miembros)|**Usuario, grupo o entidad de servicio**|
   |Seleccionar (+ Seleccionar miembros)|**Departamento de servicios**|

6. Haz clic en **Revisar y asignar** dos veces para crear la asignación de roles.

7. En la hoja **Control de acceso (IAM)**, selecciona **Asignaciones de roles**.

8. En la hoja **AZ500Lab01 \| Control de acceso (IAM)**, en la pestaña **Comprobar acceso**, en el cuadro de texto **Buscar por nombre o dirección de correo electrónico**, escribe **Dylan Williams**.

9. En la lista de resultados de la búsqueda, selecciona la cuenta de usuario de Dylan Williams y, en la hoja **Asignaciones de Dylan Williams: AZ500Lab01**, mira la asignación recién creada.

10. Cierra la hoja **Asignaciones de Dylan Williams: AZ500Lab01**.

11. Repita los dos últimos pasos para comprobar el acceso de **Joseph Price**. 

> Resultado: ha asignado y comprobado los permisos de RBAC. 

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. 

2. En el menú desplegable de la esquina superior izquierda del panel de Cloud Shell, seleccione **PowerShell** y, cuando se le solicite, haga clic en **Confirmar**. 

3. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```
    Remove-AzResourceGroup -Name "AZ500LAB01" -Force -AsJob
    ```

4.  Cierre el panel de **Cloud Shell**. 
