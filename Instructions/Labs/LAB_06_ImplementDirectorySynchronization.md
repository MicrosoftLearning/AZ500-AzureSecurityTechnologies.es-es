---
lab:
  title: 06 - Implementación de la sincronización de directorios
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: 9403e136799cd27b91f27c5d8d268ab0aec3f7c5
ms.sourcegitcommit: 79ca7b110859fe71a3849a28fdc781cad95d1567
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2022
ms.locfileid: "146381353"
---
# <a name="lab-06-implement-directory-synchronization"></a>Laboratorio 06: Implementación de la sincronización de directorios
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto que demuestre cómo integrar un entorno local de Active Directory Domain Services (AD DS) con un inquilino de Azure Active Directory (Azure AD). En concreto, quiere:

- Implementar un bosque de AD DS de dominio único mediante la implementación de una VM de Azure que hospeda un controlador de dominio de AD DS.
- Crear y configurar un inquilino de Azure AD.
- Sincronizar el bosque de AD DS con el inquilino de Azure AD.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Implementar una VM de Azure que hospeda un controlador de dominio de Active Directory
- Ejercicio 2: Crear y configurar un inquilino de Azure Active Directory
- Ejercicio 3: Sincronizar un bosque de Active Directory con un inquilino de Azure Active Directory

## <a name="implement-directory-synchronization"></a>Implementación de la sincronización de directorios

![imagen](https://user-images.githubusercontent.com/91347931/157525374-8f740f14-c2db-47b3-98f8-7feb9bc122b5.png)

## <a name="instructions"></a>Instrucciones

### <a name="exercise-1-deploy-an-azure-vm-hosting-an-active-directory-domain-controller"></a>Ejercicio 1: Implementar una VM de Azure que hospeda un controlador de dominio de Active Directory

### <a name="estimated-timing-10-minutes"></a>Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Identificar un nombre DNS disponible para una implementación de VM de Azure
- Tarea 2: Usar una plantilla de ARM para implementar una VM de Azure que hospeda un controlador de dominio de Active Directory

#### <a name="task-1-identify-an-available-dns-name-for-an-azure-vm-deployment"></a>Tarea 1: Identificar un nombre DNS disponible para una implementación de VM de Azure

En esta tarea, identificará un nombre DNS para la implementación de la VM de Azure. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, haga clic en **PowerShell** y en **Crear almacenamiento**.

3. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

4. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para identificar un nombre DNS disponible que puede usar para una implementación de VM de Azure en la siguiente tarea de este ejercicio:

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**Nota**: Reemplace el marcador de posición `<custom-label>` por un nombre DNS válido que es probable que sea único globalmente. Reemplace el marcador de posición `<location>` por el nombre de la región en la que desea implementar la VM de Azure que hospedará el controlador de dominio de Active Directory que usará en este laboratorio.

    >**Nota**: Para identificar las regiones de Azure donde puede aprovisionar VM de Azure, consulte [ **https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

5. Compruebe que el comando devolvió **True**. Si no es así, vuelva a ejecutar el mismo comando con un valor diferente de `<custom-label>` hasta que el comando devuelva **True**.

6. Registre el valor de `<custom-label>` que devolvió el resultado correcto. Lo necesitará en la próxima tarea.

7. Cierre Cloud Shell.

#### <a name="task-2-use-an-arm-template-to-deploy-an-azure-vm-hosting-an-active-directory-domain-controller"></a>Tarea 2: Usar una plantilla de ARM para implementar una VM de Azure que hospeda un controlador de dominio de Active Directory

En esta tarea, implementará una VM de Azure que hospedará un controlador de dominio de Active Directory.

1. Abra otra pestaña del explorador en la misma ventana del explorador y vaya a **https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain**.

2. En la página **Create a new Windows VM and create a new AD Forest, Domain and DC** (Crear una VM Windows y un bosque, un dominio y un controlador de dominio de AD), haga clic en **Implementar en Azure**. Se redirigirá automáticamente el explorador a la hoja **Create an Azure VM with a new AD Forest** (Crear una VM de Azure con un nuevo bosque de AD) en Azure Portal.

3. En la hoja **Create an Azure VM with a new AD Forest** (Crear una VM de Azure con un nuevo bosque de AD), haga clic en **Editar parámetros**.

4. En el panel **Editar parámetros**, haga clic en **Cargar archivo**. En el cuadro de diálogo **Abrir**, vaya a la carpeta **\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json**, haga clic en **Abrir** y, a continuación, haga clic en **Guardar**.

5. En la hoja **Create an Azure VM with a new AD Forest** (Crear una VM de Azure con un nuevo bosque de AD), especifique la configuración siguiente (deje las demás opciones con los valores existentes):

   |Configuración|Value|
   |---|---|
   |Subscription|nombre de la suscripción de Azure|
   |Resource group|Haga clic en **Crear nuevo** y escriba el nombre **AZ500LAB06**.|
   |Region|Región de Azure que identificó en la tarea anterior|
   |Nombre de usuario administrador|**Estudiante**|
   |Contraseña de administrador|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|
   |Nombre de dominio|**adatum.com**|
   |Prefijo de DNS|Nombre de host DNS que identificó en la tarea anterior|
   |Tamaño de VM|**Standard_D2s_v3**|

6. En la hoja **Create an Azure VM with a new AD Forest** (Crear una VM de Azure con un nuevo bosque de AD), haga clic en **Revisar y crear** y luego en **Crear**.

    >**Nota**: No espere a que se complete la implementación y avance al siguiente ejercicio. La implementación puede tardar unos 15 minutos. Usará la máquina virtual implementada en esta tarea en el tercer ejercicio de este laboratorio.

> Resultado: después de completar este ejercicio, ha iniciado la implementación de una VM de Azure que hospedará un controlador de dominio de Active Directory mediante una plantilla de Azure Resource Manager.


### <a name="exercise-2-create-and-configure-an-azure-active-directory-tenant"></a>Ejercicio 2: Crear y configurar un inquilino de Azure Active Directory 

### <a name="estimated-timing-20-minutes"></a>Tiempo estimado: 20 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crear un inquilino de Azure Active Directory (AD)
- Tarea 2: Agregar un nombre DNS personalizado al nuevo inquilino de Azure AD
- Tarea 3: Crear un usuario de Azure AD con el rol Administrador global

#### <a name="task-1-create-an-azure-active-directory-ad-tenant"></a>Tarea 1: Crear un inquilino de Azure Active Directory (AD)

En esta tarea, creará un nuevo inquilino de Azure AD para usarlo en este laboratorio. 

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Azure Active Directory** y presione la tecla **Entrar**.

2. En el panel **Información general** del inquilino de Azure AD actual, haga clic en **Administrar inquilinos** y, a continuación, en la siguiente pantalla, haga clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** de la hoja **Creación de un inquilino**, asegúrese de que la opción **Azure Active Directory** está seleccionada y haga clic en **Siguiente: Configuración >** .

4. En la pestaña **Configuración** de la hoja **Creación de un directorio**, especifique la siguiente configuración:

   |Configuración|Valor|
   |---|---|
   |Nombre de la organización|**AdatumSync**|
   |Nombre de dominio inicial|Nombre único que consta de una combinación de letras y dígitos|
   |País o región|**Estados Unidos**|

    >**Nota**: Registre el nombre de dominio inicial. Lo necesitará más adelante en este laboratorio.

    >**Nota**: La marca de verificación verde del cuadro de texto **Nombre de dominio inicial** indicará si el nombre de dominio que ha escrito es válido y único. (Registre el nombre de dominio inicial para su uso posterior).

5. Haga clic en **Revisar y crear** y, a continuación, en **Crear**.

    >**Nota**: Espere a que se cree el inquilino. Utilice el icono **Notificación** para supervisar el estado de la implementación. 

#### <a name="task-2-add-a-custom-dns-name-to-the-new-azure-ad-tenant"></a>Tarea 2: Agregar un nombre DNS personalizado al nuevo inquilino de Azure AD

En esta tarea, agregará el nombre DNS personalizado al nuevo inquilino de Azure AD. 

1. En Azure Portal, en la barra de herramientas, haga clic en el icono **Directorio + suscripción**, situado a la derecha del icono de Cloud Shell. 

2. En el panel **Directorio + suscripción**, seleccione la línea del inquilino recién creado **AdatumSync** y haga clic en el botón **Guardar**.

    >**Nota**: Es posible que tenga que actualizar la ventana del explorador si la entrada **AdatumSync** no aparece en la lista de filtros **Directorio + suscripción**.

3. En la hoja **AdatumSync \| Azure Active Directory**, en la sección **Administrar**, haga clic en **Nombres de dominio personalizados**.

4. En la hoja **AdatumSync \| Nombres de dominio personalizados**, haga clic en **+ Agregar dominio personalizado**.

5. En la hoja **Nombre de dominio personalizado**, en el cuadro de texto **Nombre de dominio personalizado**, escriba **adatum.com** y haga clic en **Agregar dominio**.

6. En el panel **adatum.com**, revise la información necesaria para comprobar el nombre de dominio de Azure AD y seleccione **Eliminar** dos veces. 

    >**Nota**: No podrá completar el proceso de validación porque no posee el nombre de dominio DNS **adatum.com**. Esto no le impedirá sincronizar el dominio de AD DS **adatum.com** con el inquilino de Azure AD. Para ello, usará el nombre DNS inicial del inquilino de Azure AD (el nombre que termina con el sufijo **onmicrosoft.com),** que identificó en la tarea anterior. Sin embargo, tenga en cuenta que, como resultado, el nombre de dominio DNS del dominio de AD DS y el nombre DNS del inquilino de Azure AD serán diferentes. Esto significa que los usuarios de Adatum tendrán que usar nombres diferentes al iniciar sesión en el dominio de AD DS y al iniciar sesión en el inquilino de Azure AD.

#### <a name="task-3-create-an-azure-ad-user-with-the-global-administrator-role"></a>Tarea 3: Crear un usuario de Azure AD con el rol Administrador global

En esta tarea, agregará un nuevo usuario de Azure AD y lo asignará al rol Administrador global. 

1. En la hoja **AdatumSync** del inquilino de Azure AD, en la sección **Administrar**, haga clic en **Usuarios**.

2. En la hoja **Usuarios \| Todos los usuarios**, haga clic en **+ Nuevo usuario**. 

3. En la hoja **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada, especifique la siguiente configuración (deje la otra configuración con sus valores predeterminados) y haga clic en **Crear**:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**syncadmin**|
   |Name|**syncadmin**|
   |Contraseña|Asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada y haga clic en **Mostrar contraseña**.|
   |Grupos|**0 grupos seleccionados**|
   |Roles|Haga clic en **Usuario** y, a continuación, en **Administrador global** y en **Seleccionar**.|
   |Ubicación de uso|**Estados Unidos**|  

    >**Nota**: Registre el nombre de usuario completo. Para copiar su valor, haga clic en el botón **Copiar en el Portapapeles** del lado derecho de la lista desplegable que muestra el nombre de dominio. 

    >**Nota**: Registre la contraseña del usuario. La necesitará más adelante en este laboratorio. 

    >**Nota**: Se requiere un usuario de Azure AD con el rol Administrador global para implementar Azure AD Connect.

4. Abra una ventana del explorador de InPrivate.

5. Vaya a Azure Portal e inicie sesión con la cuenta de usuario **syncadmin**. Cuando se le solicite, cambie la contraseña que registró anteriormente en esta tarea por **Pa55w.rd1234**.

    >**Nota**: Para iniciar sesión, deberá proporcionar el nombre completo de la cuenta de usuario **syncadmin**, incluido el nombre de dominio DNS del inquilino de Azure AD que registró anteriormente en esta tarea. Este nombre de usuario tiene el formato syncadmin@`<your_tenant_name>`.onmicrosoft.com, donde `<your_tenant_name>` es el marcador de posición que representa el nombre único del inquilino de Azure AD. 

6. Cierre la sesión de **syncadmin** y cierre la ventana del explorador InPrivate.

> **Resultado**: después de completar este ejercicio, ha creado un inquilino de Azure AD, agregado un nombre DNS personalizado al nuevo inquilino de Azure AD y creado un usuario de Azure AD con el rol Administrador global.


### <a name="exercise-3-synchronize-active-directory-forest-with-an-azure-active-directory-tenant"></a>Ejercicio 3: Sincronizar un bosque de Active Directory con un inquilino de Azure Active Directory

### <a name="estimated-timing-20-minutes"></a>Tiempo estimado: 20 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Preparar AD DS para la sincronización de directorios
- Parte 2: Instalar Azure AD Connect
- Tarea 3: Comprobar la sincronización de directorios

#### <a name="task-1-prepare-ad-ds-for-directory-synchronization"></a>Tarea 1: Preparar AD DS para la sincronización de directorios

En esta tarea, se conectará a la VM de Azure que ejecuta el controlador de dominio de AD DS y creará una cuenta de sincronización de directorios. 

   > Antes de iniciar esta tarea, asegúrese de que se ha completado la implementación de plantillas que inició en el primer ejercicio de este laboratorio.

1. En Azure Portal, establezca el filtro **Directorio + suscripción** en el inquilino de Azure AD asociado a la suscripción de Azure en la que implementó la VM de Azure en el primer ejercicio de este laboratorio.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Máquinas virtuales** y presione la tecla **Entrar**.

3. En la hoja **Máquinas virtuales**, haga clic en la entrada **adVM**. 

4. En la hoja **adVM**, haga clic en **Conectar** y, en el menú desplegable, haga clic en **RDP**. 

5. En el parámetro **Dirección IP,** seleccione **Dirección IP pública del equilibrador de carga** y, a continuación, haga clic en **Descargar archivo RDP** y úselo para conectarse a la VM de Azure **adVM** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**Estudiante**|
   |Contraseña|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|

    >**Nota**: Espere a que se carguen la sesión de Escritorio remoto y **Administrador del servidor**.  

    >**Nota**: Los pasos siguientes se realizan en la sesión de Escritorio remoto para la VM de Azure **adVM**. 

6. En **Administrador del servidor**, haga clic en **Servidor local** y, a continuación, haga clic en **Configuración de seguridad mejorada de IE**.

7. En el cuadro de diálogo **Configuración de seguridad mejorada de Internet Explorer**, establezca ambas opciones en **Desactivado** y haga clic en **Aceptar**.

8. Inicie Internet Explorer, vaya a **https://www.microsoft.com/en-us/edge/business/download**, descargue los archivos binarios de instalación de Microsoft Edge, ejecute la instalación y configure el explorador web con la configuración predeterminada.

9. En **Administrador del servidor**, haga clic en **Herramientas** y, en el menú desplegable, haga clic en **Centro de administración de Active Directory**.

10. En **Centro de administración de Active Directory**, haga clic en **adatum (local)** ; en el panel **Tareas**, bajo el nombre de dominio **adatum (local)** , haga clic en **Nuevo** y, en el menú en cascada, haga clic en **Unidad organizativa**.

11. En la ventana **Crear unidad organizativa**, en el cuadro de texto **Nombre**, escriba **ToSync** y haga clic en **Aceptar**.

12. Haga doble clic en la unidad organizativa **ToSync** recién creada para que su contenido aparezca en el panel de detalles de la consola del Centro de administración de Active Directory. 

13. En el panel **Tareas**, en la sección **ToSync**, haga clic en **Nuevo** y, en el menú en cascada, haga clic en **Usuario**.

14. En la ventana **Crear usuario**, cree una cuenta de usuario con la siguiente configuración (deje las demás opciones con los valores existentes) y haga clic en **Aceptar**:

   |Configuración|Value|
   |---|---|
   |Nombre completo|**aduser1**|
   |Inicio de sesión UPN de usuario|**aduser1**|
   |Inicio de sesión SamAccountName de usuario|**aduser1**|
   |Contraseña y Confirmar contraseña|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|
   |Otras opciones de contraseña|**La contraseña nunca expira**|

#### <a name="task-2-install-azure-ad-connect"></a>Parte 2: Instalar Azure AD Connect

En esta tarea, instalará AD Conectar en la máquina virtual. 

1. En la sesión de Escritorio remoto para **adVM**, use Microsoft Edge para ir a Azure Portal ( **https://portal.azure.com** ) e iniciar sesión con la cuenta de usuario **syncadmin** que creó en el ejercicio anterior. Cuando se le solicite, especifique el nombre de usuario completo que registró y la contraseña **Pa55w.rd1234**.

2. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Azure Active Directory** y presione la tecla **Entrar**.

3. En Azure Portal, en la hoja **AdatumSync \| Información general**, haga clic en **Azure AD Connect**.

4. En la hoja **AdatumSync \| Azure AD Connect**, haga clic en el vínculo **Descargar Azure AD Connect**. Se le redirigirá a la página de descarga de **Microsoft Azure Active Directory Connect**.

5. En la página de descarga de **Microsoft Azure Active Directory Connect**, haga clic en **Descargar**.

6. Cuando se le solicite, haga clic en **Ejecutar** para iniciar el asistente de **Microsoft Azure Active Directory Connect**.

7. En la página **Bienvenido a Azure AD Connect** del asistente de **Microsoft Azure Active Directory Connect**, haga clic en la casilla **I agree to the license terms and privacy notice** (Acepto los términos de licencia y el aviso de privacidad) y luego en **Continuar**.

8. En la página **Configuración rápida** del asistente de **Microsoft Azure Active Directory Connect**, haga clic en la opción **Personalizar**.

9. En la página **Instalar componentes necesarios**, deje desactivadas todas las opciones de configuración opcionales y haga clic en **Instalar**.

10. En la página **Inicio de sesión de usuario**, asegúrese de que solo esté habilitada la opción **Sincronización de hash de contraseñas** y haga clic en **Siguiente**.

11. En la página **Conectar con Azure AD**, autentíquese con las credenciales de la cuenta de usuario **syncadmin** que creó en el ejercicio anterior y haga clic en **Siguiente**. 

12. En la página **Conectar su directorios**, haga clic en el botón **Agregar directorio** situado a la derecha de la entrada del bosque **adatum.com**.

13. En la ventana **Cuenta del bosque de AD**, asegúrese de que está seleccionada la opción **Crear una cuenta de AD**, especifique las credenciales siguientes y haga clic en **Aceptar**:

   |Configuración|Value|
   |---|---|
   |Nombre de usuario|**ADATUM\\Student**|
   |Contraseña|**Use su contraseña personal creada en Laboratorio 06 > Ejercicio 1 > Tarea 2.**|

14. De nuevo en la página **Conectar sus directorios**, asegúrese de que la entrada **adatum.com** aparece como un directorio configurado y haga clic en **Siguiente**.

15. En la página **Configuración de inicio de sesión de Azure AD**, observe la advertencia que indica **que los usuarios no podrán iniciar sesión en Azure AD con credenciales locales si el sufijo UPN no coincide con un nombre de dominio comprobado**, active la casilla **Continue without matching all UPN suffixes to verified domain** (Continuar sin hacer coincidir todos los sufijos UPN con el dominio comprobado) y haga clic en **Siguiente**.

    >**Nota**: Como se explicó anteriormente, esto es lo esperado, ya que no se pudo comprobar el dominio DNS de Azure AD personalizado **adatum.com**.

16. En la página **Filtrado de dominios y unidades organizativas**, haga clic en la opción **Sincronizar dominios y unidades organizativas seleccionados**. Se comprobará el nombre de dominio **adatum.com**. Expanda **adatum.com** para ver **ToSync**. Desmarque todas las casillas, haga clic solo en la casilla situada junto a la unidad organizativa **ToSync** y haga clic en **Siguiente**.

17. En la página **Identificación de forma exclusiva de usuarios**, acepte la configuración predeterminada y haga clic en **Siguiente**.

18. En la página **Filtrar usuarios y dispositivos,** acepte la configuración predeterminada y haga clic en **Siguiente**.

19. En la página **Características opcionales**, acepte la configuración predeterminada y haga clic en **Siguiente**.

20. En la página **Listo para configurar**, asegúrese de que la casilla **Start the synchronization process when configuration completes** (Iniciar el proceso de sincronización cuando se complete la configuración) esté activada y haga clic en **Instalar**.

    >**Nota**: La instalación debería tardar aproximadamente 2 minutos.

21. Revise la información de la página **Configuración completada** y haga clic en **Salir** para cerrar la ventana **Microsoft Azure Active Directory Connect**.


#### <a name="task-3-verify-directory-synchronization"></a>Tarea 3: Comprobar la sincronización de directorios

En esta tarea, comprobará que la sincronización de directorios funciona. 

1. En la sesión de Escritorio remoto para **adVM**, en la ventana de Microsoft Edge que muestra Azure Portal, vaya al panel **Usuarios - Todos los usuarios (versión preliminar)** del inquilino de Azure AD del laboratorio de Adatum.

2. En la hoja **Usuarios \| Todos los usuarios**, observe que la lista de objetos de usuario incluye la cuenta **aduser1**. 

>**Nota**: Es posible que tenga que esperar unos minutos y seleccionar **Actualizar** para que aparezca la cuenta de usuario **aduser1**.

3. Seleccione la cuenta **aduser1** y, en la sección **Perfil > Identidad**, observe que el atributo **Origen** está establecido en **Windows Server AD**.

4. En la hoja **aduser1 \| Perfil**, en la sección **Información del trabajo**, observe que el atributo **Departamento** no está establecido.

5. En la sesión de Escritorio remoto para **adVM**, cambie a **Centro de administración de Active Directory**, seleccione la entrada **aduser1** en la lista de objetos de la unidad organizativa **ToSync** y, en el panel **Tareas**, en la sección **aduser1**, seleccione **Propiedades**.

6. En la ventana **aduser1**, en la sección **Organización**, en el cuadro de texto **Departamento**, escriba **Ventas** y seleccione **Aceptar**.

7. En la sesión de Escritorio remoto para **adVM**, inicie **Windows PowerShell**.

8. Desde la consola **Administrador: Windows PowerShell**, ejecute lo siguiente para iniciar la sincronización diferencial de Azure AD Connect:

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. Cambie a la ventana de Microsoft Edge que muestra la hoja **aduser1 \| Perfil**, actualice la página y observe que la propiedad **Departamento** está establecida en **Ventas**.

    >**Nota**: Es posible que tenga que esperar otro minuto y volver a actualizar la página si el atributo **Departamento** sigue sin establecerse.

> **Resultado**: después de completar este ejercicio, ha preparado AD DS para la sincronización de directorios, instado Azure AD Connect y comprobado la sincronización de directorios.


**Limpieza de recursos**

>**Nota**: Para empezar, deshabilite la sincronización de Azure AD.

1. En la sesión de Escritorio remoto para **adVM**, inicie Windows PowerShell como administrador.

2. Desde la consola de Windows PowerShell, instale el módulo de PowerShell MsOnline ejecutando el comando siguiente (cuando se le solicite, en el cuadro de diálogo Se necesita el proveedor de NuGet para continuar, escriba **Sí** y presione Entrar):

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. Desde la consola de Windows PowerShell, conéctese al inquilino AdatumSync de Azure AD ejecutando el comando siguiente (cuando se le solicite, inicie sesión con las credenciales de **syncadmin**):

    ```powershell
    Connect-MsolService
    ```

4. Desde la consola de Windows PowerShell, deshabilite la sincronización de Azure AD Connect ejecutando el siguiente comando:

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

5. Desde la consola de Windows PowerShell, ejecute lo siguiente para comprobar que la operación se ha realizado correctamente:

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```
    >**Nota**: El resultado debería ser `False`. Si ese no es el caso, espere un minuto y vuelva a ejecutar el comando.

    >**Nota**: A continuación, quite los recursos de Azure.
6. Cierre la sesión de Escritorio remoto.

7. En Azure Portal, establezca el filtro **Directorio + suscripción** en el inquilino de Azure AD asociado a la suscripción de Azure en la que implementó la máquina virtual de Azure **adVM**.

8. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. 

9. En el menú desplegable de la esquina superior izquierda del panel de Cloud Shell, seleccione **PowerShell** y, cuando se le solicite, haga clic en **Confirmar**. 

10. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. Cierre el panel de **Cloud Shell**.

    >**Nota**: Por último, quite el inquilino de Azure AD.
    
    >**Nota 2**: La eliminación de un inquilino está pensada para ser un proceso muy difícil para que nunca se pueda realizar de forma accidental o malintencionada.  Esto significa que quitar el inquilino como parte de este laboratorio a menudo no funciona.  Aunque aquí se indican los pasos necesarios para eliminar el inquilino, no es necesario que lo haga en este laboratorio. Si alguna vez tiene la necesidad de quitar un inquilino realmente, encontrará artículos en DOCS.Microsoft.com que le ayudarán a hacerlo.

12. De nuevo en Azure Portal, use el filtro **Directorio + suscripción** para cambiar el inquilino **AdatumSync** de Azure Active Directory.

13. En Azure Portal, vaya al panel **Usuarios - Todos los usuarios** y haga clic en la entrada que representa la cuenta de usuario **syncadmin**. A continuación, en el panel **syncadmin - Perfil**, haga clic en **Eliminar** y, cuando se le pida confirmación, haga clic en **Sí**.

14. Repita la misma secuencia de pasos para eliminar la cuenta de usuario **aduser1** y la **cuenta de servicio de sincronización de directorios local**.

15. Vaya al panel **AdatumSync - Información general** del inquilino de Azure AD, haga clic en **Administrar inquilinos** y seleccione la casilla del directorio **AdatumSync**; haga clic en **Eliminar** en el panel **Eliminar inquilino "AdatumSync"** y en el vínculo **Obtener permiso para eliminar todos los recursos de Azure**. En el panel **Propiedades** de Azure Active Directory, establezca **Administración de acceso para recursos de Azure** en **Sí** y haga clic en **Guardar**.

    >**Nota**: Durante la eliminación, si recibe alguna señal de advertencia como **Eliminar todos los usuarios**, continúe con la eliminación de los usuarios que ha creado. Si la señal de advertencia indica **Eliminar aplicaciones de LinkedIn**, haga clic en el mensaje de advertencia y confirme la eliminación de la aplicación de LinkedIn. Para pasar la eliminación del inquilino, todas las advertencias deben quedar solucionadas.

16. Cierre la sesión de Azure Portal y vuelva a iniciarla. 

17. Vuelva al panel **Eliminar directorio "AdatumSync"** y haga clic en **Eliminar**.

> Para obtener información adicional sobre esta tarea, consulte [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
