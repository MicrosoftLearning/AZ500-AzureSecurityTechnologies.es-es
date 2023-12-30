---
lab:
  title: 06 - Implementación de la sincronización de directorios
  module: Module 01 - Manage Identity and Access
---

# Laboratorio 06: Implementación de la sincronización de directorios
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se te ha pedido que crees una prueba de concepto que muestre cómo integrar el entorno local de Microsoft Entra Domain Services con un inquilino de Microsoft Entra. En concreto, quiere:

- Implementar un bosque de dominio único de Microsoft Entra Domain Services mediante la implementación de una de máquina virtual de Azure que hospeda un controlador de dominio de Microsoft Entra Domain Services.
- Crear y configurar un inquilino de Microsoft Entra
- Sincronizar el bosque de Microsoft Entra Domain Services con el inquilino de Microsoft Entra.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Implementación de una máquina virtual de Azure que hospede un controlador de dominio de Microsoft Entra ID
- Ejercicio 2: Creación y configuración de un inquilino de Microsoft Entra ID
- Ejercicio 3: Sincronización del bosque de Microsoft Entra ID con un inquilino de Microsoft Entra ID

## Implementación de la sincronización de directorios

![imagen](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/5d9cc4c7-7dcd-4d88-920d-9c97d5a482a2)

## Instructions

### Ejercicio 1: Implementación de una máquina virtual de Azure que hospede un controlador de dominio de Microsoft Entra ID

### Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Identificar un nombre DNS disponible para una implementación de VM de Azure
- Tarea 2: Usar una plantilla de ARM para implementar una máquina virtual de Azure que hospede un controlador de dominio de Microsoft Entra ID

#### Tarea 1: Identificar un nombre DNS disponible para una implementación de VM de Azure

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

    >**Nota**: Para identificar las regiones de Azure donde puede aprovisionar VM de Azure, consulte [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)

5. Compruebe que el comando devolvió **True**. Si no es así, vuelva a ejecutar el mismo comando con un valor diferente de `<custom-label>` hasta que el comando devuelva **True**.

6. Registre el valor de `<custom-label>` que devolvió el resultado correcto. Lo necesitará en la próxima tarea.

7. Cierre Cloud Shell.

#### Tarea 2: Usar una plantilla de ARM para implementar una máquina virtual de Azure que hospede un controlador de dominio de Microsoft Entra ID

En esta tarea, implementarás una máquin virtual de Azure que hospedará un controlador de dominio de Microsoft Entra ID

1. Abra otra pestaña del explorador en la misma ventana del explorador y vaya a **https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain**.

2. En la página **Crear una VM de Azure con un nuevo bosque de AD**, haga clic en **Implementar en Azure**. Se redirigirá automáticamente el explorador a la hoja **Create an Azure VM with a new AD Forest** (Crear una VM de Azure con un nuevo bosque de AD) en Azure Portal. 

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

> Resultado: después de completar este ejercicio, habrás iniciado la implementación de una máquina virtual de Azure que hospedará un controlador de dominio de Microsoft Entra ID mediante una plantilla de Resource Manager de Azure.


### Ejercicio 2: Creación y configuración de un inquilino de Microsoft Entra ID 

### Tiempo estimado: 20 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crea un inquilino de Microsoft Entra.
- Tarea 2: Agrega un nombre DNS personalizado al nuevo inquilino de Microsoft Entra
- Tarea 3: Crea un usuario de Microsoft Entra ID con el rol de administrador global

#### Tarea 1: Crea un inquilino de Microsoft Entra.

En esta tarea, crearás un nuevo inquilino de Microsoft Entra para usarlo en este laboratorio. 

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos**, que encontrará en la parte superior de la página, escribe **Microsoft Entra ID** y presiona la tecla **ENTRAR**.

2. En la hoja que muestra **Información general** de tu inquilino de Microsoft Entra actual, haz clic en **Administrar inquilinos** y después, en la siguiente pantalla, haz clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** de la hoja **Creación de un inquilino**, asegúrate de que la opción **Microsoft Entra ID** está seleccionada y haz clic en **Siguiente: Configuración >**.

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

#### Tarea 2: Agregar un nombre DNS personalizado al nuevo inquilino de Azure AD

En esta tarea, agregará el nombre DNS personalizado al nuevo inquilino de Azure AD. 

1. En Azure Portal, en la barra de herramientas, haga clic en el icono **Directorio + suscripción**, situado a la derecha del icono de Cloud Shell. 

2. En el panel **Directorio + suscripción**, seleccione la línea del inquilino recién creado **AdatumSync** y haga clic en el botón **Guardar**.

    >**Nota**: Es posible que tenga que actualizar la ventana del explorador si la entrada **AdatumSync** no aparece en la lista de filtros **Directorio + suscripción**.

3. En la hoja **AdatumSync \| Microsoft Entra ID**, en la sección **Administrar**, haz clic en **Nombres de dominio personalizados**.

4. En la hoja **AdatumSync \| Nombres de dominio personalizados**, haga clic en **+ Agregar dominio personalizado**.

5. En la hoja **Nombre de dominio personalizado**, en el cuadro de texto **Nombre de dominio personalizado**, escriba **adatum.com** y haga clic en **Agregar dominio**.

6. En la hoja **adatum.com**, revisa la información necesaria para realizar la verficación del nombre de dominio de Microsoft Entra y selecciona **Eliminar** dos veces. 

    >**Nota**: No podrá completar el proceso de validación porque no posee el nombre de dominio DNS **adatum.com**. Esto no te impedirá sincronizar el dominio **adatum.com** de Microsoft Entra Domain Services con el inquilino de Microsoft Entra. Para ello, usarás el nombre DNS inicial del inquilino de Microsoft Entra (el nombre que termina con el sufijo **onmicrosoft.com**), que identificaste en la tarea anterior. Pero, ten en cuenta que, como resultado, el nombre de dominio DNS del dominio de Microsoft Entra Domain Services y el nombre DNS del inquilino de Microsoft Entra serán diferentes. Esto significa que los usuarios de Adatum tendrán que usar nombres diferentes al iniciar sesión en el dominio de Microsoft Entra Domain Services y al iniciar sesión en el inquilino de Microsoft Entra.

#### Tarea 3: Crea un usuario de Microsoft Entra ID con el rol de administrador global

En esta tarea, agregarás un nuevo usuario de Microsoft Entra ID y lo asignarás al rol de administrador global. 

1. En la hoja **AdatumSync** del inquilino de Microsoft Entra, en la sección **Administrar**, haz clic en **Usuarios**.

2. En el panel **Usuarios | Todos los usuarios**, haga clic en **+ Nuevo usuario** y, a continuación, en **Crear nuevo usuario**.

3. En la hoja **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada, especifique la siguiente configuración en la pestaña Aspectos básicos (deje la otra configuración con sus valores predeterminados) y haga clic en **Siguiente: Propiedades >** :

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**syncadmin**|
   |Name|**syncadmin**|
   |Contraseña|Asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada y haga clic en **Mostrar contraseña**.|

    >**Nota**: Registre el nombre de usuario completo. Para copiar su valor, haga clic en el botón **Copiar en el Portapapeles** del lado derecho de la lista desplegable que muestra el nombre de dominio y péguelo en un documento del Bloc de notas. La necesitará más adelante en este laboratorio.

    >**Nota**: Para registrar la contraseña del usuario, haga clic en el botón **Copiar en el Portapapeles** situado en el lado derecho del cuadro de texto Contraseña y péguelo en un documento del Bloc de notas. La necesitará más adelante en este laboratorio.

4. En la pestaña **Propiedades**, desplácese hasta la parte inferior y especifique la Ubicación de uso: **Estados Unidos** (deje todos los demás con sus valores predeterminados) y haga clic en **Siguiente: Asignaciones >** .

5. En la pestaña **Asignaciones**, haga clic en **+ Agregar rol**, busque y seleccione **Administrador global** y, a continuación, haga clic en **Seleccionar**. Haga clic en **Revisar y crear** y, a continuación, en **Crear**.
   
    >**Nota**: se requiere un usuario de Azure AD con el rol de administrador global para implementar Microsoft Entra Connect.

6. Abra una ventana del explorador de InPrivate.

7. Vaya a Azure Portal en **`https://portal.azure.com/`** e inicie sesión con la cuenta de usuario **syncadmin**. Cuando se le solicite, cambie la contraseña que registró anteriormente en esta tarea a su propia contraseña que cumpla los requisitos de complejidad y regístrela para futuras referencias. Se le pedirá esta contraseña en tareas posteriores.

    >**Nota**: Para iniciar sesión, deberás proporcionar el nombre completo de la cuenta de usuario **syncadmin**, incluido el nombre de dominio DNS del inquilino de Microsoft Entra que registraste anteriormente en esta tarea. Este nombre de usuario tiene el formato syncadmin@`<your_tenant_name>`.onmicrosoft.com, donde `<your_tenant_name>` es el marcador de posición que representa el nombre único del inquilino de Microsoft Entra. 

8. Cierre la sesión de **syncadmin** y cierre la ventana del explorador InPrivate.

> **Resultado**: después de completar este ejercicio, habrás creado un inquilino de Microsoft Entra, habrás visto cómo agregar un nombre DNS personalizado al nuevo inquilino de  Microsoft Entra y habrás creado un usuario de  Microsoft Entra con el rol de administrador global.


### Ejercicio 3: Sincronización del bosque de Microsoft Entra ID con un inquilino de Microsoft Entra ID

### Tiempo estimado: 20 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Preparación de Microsoft Entra Domain Services para la sincronización de directorios
- Tarea 2: instalación de Microsoft Entra Connect
- Tarea 3: Comprobar la sincronización de directorios

#### Tarea 1: Preparación de Microsoft Entra Domain Services para la sincronización de directorios

En esta tarea, te conectarás a la máquina virtual de Azure que ejecuta el controlador de dominio de Microsoft Entra Domain Services y crearás una cuenta de sincronización de directorios. 

   > Antes de iniciar esta tarea, asegúrese de que se ha completado la implementación de plantillas que inició en el primer ejercicio de este laboratorio.

1. En Azure Portal, establezce el filtro **Directorio + suscripción** en el inquilino deMicrosoft Entra asociado a la suscripción de Azure en la que implementaste la máquina virtual de Azure en el primer ejercicio de este laboratorio.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Máquinas virtuales** y presione la tecla **Entrar**.

3. En la hoja **Máquinas virtuales**, haga clic en la entrada **adVM**. 

4. En la hoja **adVM**, haga clic en **Conectar** y, en el menú desplegable, haga clic en **RDP**. 

5. En el desplegable **Dirección IP,** seleccione **Dirección IP pública del equilibrador de carga** y, a continuación, haga clic en **Descargar archivo RDP** y úselo para conectarse a la VM de Azure **adVM** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**Estudiante**|
   |Contraseña|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|

    >**Nota**: Espere a que se carguen la sesión de Escritorio remoto y **Administrador del servidor**.  

    >**Nota**: Los pasos siguientes se realizan en la sesión de Escritorio remoto para la VM de Azure **adVM**.

    >**Nota**: Si la **dirección IP pública del equilibrador de carga** no está disponible en la lista desplegable **Dirección IP** de la hoja RDP, en Azure Portal, busque **Direcciones IP públicas**, seleccione **adPublicIP** y anote su dirección IP. Haga clic en el botón Inicio, escriba **MSTSC** y presione **Entrar** para iniciar el cliente de Escritorio remoto. Escriba la dirección IP pública del equilibrador de carga en el cuadro de texto **Equipo:** y haga clic en **Conectar**.

6. En **Administrador del servidor**, haz clic en **Herramientas** y, en el menú desplegable, haz clic en **Centro de administración de Microsoft Entra ID **.

7. En el **Centro de administración de Microsoft Entra ID**, haz clic en **adatum (local)**; en el panel **Tareas**, bajo el nombre de dominio **adatum (local)** , haz clic en **Nuevo** y, en el menú en cascada, haz clic en **Unidad organizativa**.

8. En la ventana **Crear unidad organizativa**, en el cuadro de texto **Nombre**, escriba **ToSync** y haga clic en **Aceptar**.

9. Haz doble clic en la unidad organizativa **ToSync** recién creada para que su contenido aparezca en el panel de detalles de la consola del Centro de administración de Microsoft Entra ID. 

10. En el panel **Tareas**, en la sección **ToSync**, haga clic en **Nuevo** y, en el menú en cascada, haga clic en **Usuario**.

11. En la ventana **Crear usuario**, cree una cuenta de usuario con la siguiente configuración (deje las demás opciones con los valores existentes) y haga clic en **Aceptar**:
    
    |Configuración|Value|
    |---|---|
    |Nombre completo|**aduser1**|
    |Inicio de sesión UPN de usuario|**aduser1**|
    |Inicio de sesión SamAccountName de usuario|**aduser1**|
    |Contraseña y Confirmar contraseña|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|
    |Otras opciones de contraseña|**La contraseña nunca expira**|


#### Tarea 2: instalación de Microsoft Entra Connect

En esta tarea, instalarás Microsoft Entra en la máquina virtual. 

1. En la sesión de Escritorio remoto para **adVM**, use Microsoft Edge para ir a Azure Portal ( **https://portal.azure.com** ) e iniciar sesión con la cuenta de usuario **syncadmin** que creó en el ejercicio anterior. Cuando se le solicite, especifique el nombre principal de usuario completo y la contraseña que registró en el ejercicio anterior.

2. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos**, que encontrará en la parte superior de la página, escribe **Microsoft Entra ID** y presiona la tecla **ENTRAR**.

3. En Azure Portal, en la hoja **AdatumSync\| Información general**, en el panel de navegación izquierdo, en **Administrar**, haz clic en **Microsoft Entra Connect**.

4. En la hoja **Microsoft Entra Connect \| Introducción**, haz clic en **Conectar sincronización** en el panel de navegación izquierdo y, a continuación, haz clic en el vínculo **Descargar Microsoft Entra Connect**. Se le redirigirá a la página de descarga de **Microsoft Entra Connect**.

5. En la página de descarga de **Microsoft Entra Connect**, haz clic en **Descargar**.

6. Cuando se te solicite, haz clic en **Ejecutar** para iniciar el asistente de **Microsoft Entra Connect**.

7. En la página **Bienvenido a Microsoft Entra Connect** del asistente de **Microsoft Entra Connect**, haz clic en la casilla **I agree to the license terms and privacy notice** (Acepto los términos de licencia y el aviso de privacidad) y luego en **Continuar**.

8. En la página **Configuración rápida** del asistente de **Microsoft Entra Connect**, haz clic en la opción **Personalizar**.

9. En la página **Instalar componentes necesarios**, deje desactivadas todas las opciones de configuración opcionales y haga clic en **Instalar**.

10. En la página **Inicio de sesión de usuario**, asegúrese de que solo esté habilitada la opción **Sincronización de hash de contraseñas** y haga clic en **Siguiente**.

11. En la página **Conectar con Microsoft Entra ID**, autentícate con las credenciales de la cuenta de usuario **syncadmin** que creaste en el ejercicio anterior y haz clic en **Siguiente**. 

12. En la página **Conectar su directorios**, haga clic en el botón **Agregar directorio** situado a la derecha de la entrada del bosque **adatum.com**.

13. En la ventana **Cuenta del bosque de AD**, asegúrate de que está seleccionada la opción **Crear una cuenta de Microsoft Entra ID**, especifica las credenciales siguientes y haz clic en **Aceptar**:

    |Configuración|Valor|
    |---|---|
    |Nombre de usuario|**ADATUM\\Student**|
    |Contraseña|**Use su contraseña personal creada en Laboratorio 06 > Ejercicio 1 > Tarea 2.**|

14. De nuevo en la página **Conectar sus directorios**, asegúrese de que la entrada **adatum.com** aparece como un directorio configurado y haga clic en **Siguiente**.

15. En la página **Configuración de inicio de sesión de Microsoft Entra ID**, observa la advertencia que indica **Los usuarios no podrán iniciar sesión en Microsoft Entra ID con credenciales del entorno local si el sufijo de UPN no coincide con ningún dominio verificado**, activa la casilla **Continuar sin relacionar todos los sufijos UPN con los dominios verificados** y haz clic en **Siguiente**.

    >**Nota**: Tal como se explicó anteriormente, esto es lo esperado, ya que no se pudo comprobar el dominio DNS de Microsoft Entra ID personalizado **adatum.com**.

16. En la página **Filtrado de dominios y unidades organizativas**, haga clic en la opción **Sincronizar los dominios y las unidades organizativas seleccionados** y desactive la casilla situada junto al nombre de dominio **adatum.com**. Haga clic para expandir **adatum.com**, seleccione solo la casilla situada junto a la unidad organizativa **ToSync** y, a continuación, haga clic en **Siguiente**.

17. En la página **Identificación de forma exclusiva de usuarios**, acepte la configuración predeterminada y haga clic en **Siguiente**.

18. En la página **Filtrar usuarios y dispositivos,** acepte la configuración predeterminada y haga clic en **Siguiente**.

19. En la página **Características opcionales**, acepte la configuración predeterminada y haga clic en **Siguiente**.

20. En la página **Listo para configurar**, asegúrese de que la casilla **Start the synchronization process when configuration completes** (Iniciar el proceso de sincronización cuando se complete la configuración) esté activada y haga clic en **Instalar**.

    >**Nota**: La instalación debería tardar aproximadamente 2 minutos.

21. Revisa la información de la página **Configuración completada** y haz clic en **Salir** para cerrar la ventana **Microsoft Entra Connect**.


#### Tarea 3: Comprobar la sincronización de directorios

En esta tarea, comprobará que la sincronización de directorios funciona. 

1. En la sesión de Escritorio remoto para **adVM**, en la ventana de Microsoft Edge que muestra Azure Portal, ve a la hoja **Usuarios - Todos los usuarios (versión preliminar)** del inquilino de Microsoft Entra ID del laboratorio de Adatum.

2. En la hoja **Usuarios \| Todos los usuarios**, observe que la lista de objetos de usuario incluye la cuenta **aduser1**. 

   >**Nota**: Es posible que tenga que esperar unos minutos y seleccionar **Actualizar** para que aparezca la cuenta de usuario **aduser1**.

3. Haga clic en la cuenta **aduser1** y seleccione la pestaña **Propiedades**. Desplácese hacia abajo hasta la sección **Local**, tenga en cuenta que el atributo **Habilitado para sincronización local** está establecido en **Sí**.

4. En la hoja **aduser1**, en la sección **Información del trabajo**, observe que el atributo **Departamento** no está establecido.

5. En la sesión de Escritorio remoto para **adVM**, cambia a **Centro de administración de Microsoft Entra ID**, selecciona la entrada **aduser1** en la lista de objetos de la unidad organizativa **ToSync** y, en el panel **Tareas**, en la sección **aduser1**, selecciona **Propiedades**.

6. En la ventana **aduser1**, en la sección **Organización**, en el cuadro de texto **Departamento**, escriba **Ventas** y seleccione **Aceptar**.

7. En la sesión de Escritorio remoto para **adVM**, inicie **Windows PowerShell**.

8. Desde la consola **Administrador: Windows PowerShell**, ejecuta lo siguiente para iniciar la sincronización diferencial de Microsoft Entra Connect:

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. Cambie a la ventana de Microsoft Edge que muestra el panel **aduser1**, actualice la página y observe que la propiedad Departamento está establecida en Ventas.

    >**Nota**: Es posible que tenga que esperar hasta tres minutos y volver a actualizar la página si el atributo **Departamento** sigue sin establecerse.

> **Resultado**: después de completar este ejercicio, habrás preparado Microsoft Entra Domain Services para la sincronización de directorios, instalado Microsoft Entra Connect y verificado la sincronización de directorios.


**Limpieza de recursos**

>**Nota**: Para empezar, deshabilita la sincronización de Microsoft Entra ID.

1. En la sesión de Escritorio remoto para **adVM**, inicie Windows PowerShell como administrador.

2. Desde la consola de Windows PowerShell, instale el módulo de PowerShell MsOnline ejecutando el comando siguiente (cuando se le solicite, en el cuadro de diálogo Se necesita el proveedor de NuGet para continuar, escriba **Sí** y presione Entrar):

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. Desde la consola de Windows PowerShell, conéctate al inquilino AdatumSync de Microsoft Entra ejecutando el comando siguiente (cuando se te solicite, inicia sesión con las credenciales de **syncadmin**):

    ```powershell
    Connect-MsolService
    ```

4. Desde la consola de Windows PowerShell, deshabilita la sincronización de Microsoft Entra Connect ejecutando el siguiente comando:

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

7. En Azure Portal, establece el filtro **Directorio + suscripción** en el inquilino de Microsoft Entra asociado a la suscripción de Azure en la que implementaste la máquina virtual de Azure **adVM**.

8. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. 

9. En el menú desplegable de la esquina superior izquierda del panel de Cloud Shell, seleccione **PowerShell** y, cuando se le solicite, haga clic en **Confirmar**. 

10. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. Cierre el panel de **Cloud Shell**.

    >**Nota**: Por último, quita el inquilino de Microsoft Entra.
    
    >**Nota 2**: La eliminación de un inquilino está pensada para ser un proceso muy difícil para que nunca se pueda realizar de forma accidental o malintencionada.  Esto significa que quitar el inquilino como parte de este laboratorio a menudo no funciona.  Aunque aquí se indican los pasos necesarios para eliminar el inquilino, no es necesario que lo haga en este laboratorio. Si alguna vez tiene la necesidad de quitar un inquilino realmente, encontrará artículos en DOCS.Microsoft.com que le ayudarán a hacerlo.

12. De nuevo en Azure Portal, usa el filtro **Directorio + suscripción** para cambiar el inquilino **AdatumSync** de Microsoft Entra.

13. En Azure Portal, vaya al panel **Usuarios - Todos los usuarios** y haga clic en la entrada que representa la cuenta de usuario **syncadmin**. A continuación, en el panel **syncadmin - Perfil**, haga clic en **Eliminar** y, cuando se le pida confirmación, haga clic en **Sí**.

14. Repita la misma secuencia de pasos para eliminar la cuenta de usuario **aduser1** y la **cuenta de servicio de sincronización de directorios local**.

15. Ve a la hoja **AdatumSync - Información general** del inquilino de Microsoft Entra, haz clic en **Administrar inquilinos** y selecciona la casilla del directorio **AdatumSync**. Haz clic en **Eliminar** en la hoja **Eliminar inquilino "AdatumSync"** y en el vínculo **Obtener permiso para eliminar los recursos de Azure**, en la hoja **Propiedades** de Microsoft Entra, establece **Administración de acceso para recursos de Azure** en **Sí** y haz clic en **Guardar**.

    >**Nota**: Durante la eliminación, si recibe alguna señal de advertencia como **Eliminar todos los usuarios**, continúe con la eliminación de los usuarios que ha creado. Si la señal de advertencia indica **Eliminar aplicaciones de LinkedIn**, haga clic en el mensaje de advertencia y confirme la eliminación de la aplicación de LinkedIn. Para pasar la eliminación del inquilino, todas las advertencias deben quedar solucionadas.

16. Cierre la sesión de Azure Portal y vuelva a iniciarla. 

17. Vuelva al panel **Eliminar directorio "AdatumSync"** y haga clic en **Eliminar**.

> Para obtener información adicional sobre esta tarea, consulte [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
