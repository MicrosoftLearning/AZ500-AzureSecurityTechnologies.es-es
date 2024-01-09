---
lab:
  title: '10: Key Vault (implementación de datos seguros mediante la configuración de Always Encrypted)'
  module: Module 03 - Secure Data and Applications
---

# Laboratorio 10: Key Vault (implementación de datos seguros mediante la configuración de Always Encrypted)
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que cree una aplicación de prueba de concepto que use la compatibilidad de Azure SQL Database con la funcionalidad Always Encrypted. Todos los secretos y claves usados en este escenario deben almacenarse en Key Vault. La aplicación debe registrarse en Microsoft Entra ID para mejorar su posición de seguridad. Para lograr estos objetivos, la prueba de concepto debe incluir:

- Creación de un almacén Azure Key Vault y almacenamiento de claves y secretos en el almacén.
- Creación de una base de datos SQL Database y cifrado del contenido de las columnas de las tablas de la base de datos mediante Always Encrypted.

>**Nota**: Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

Para mantener el centro de atención en los aspectos de seguridad de Azure relacionados con la creación de esta prueba de concepto, comenzará desde una implementación automatizada de plantillas de ARM, configurará una máquina virtual con Visual Studio 2019 y SQL Server Management Studio 19.

## Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Implementación de la infraestructura base desde una plantilla de ARM
- Ejercicio 2: Configuración del recurso de Key Vault con una clave y un secreto
- Ejercicio 3: Configuración de una base de datos de Azure SQL y una aplicación controlada por datos
- Ejercicio 4: Demostración del uso de Azure Key Vault para cifrar la base de datos de Azure SQL

## Diagrama de Key Vault

![imagen](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/38c4ba6d-2fc7-45e5-b9a2-d5dbb4fbbcbc)

## Instrucciones

## Archivos de laboratorio:

- **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json**

- **\\Allfiles\\Labs\\10\\program.cs**

### Estimación del tiempo total de laboratorio: 60 minutos

### Ejercicio 1: Implementación de la infraestructura base desde una plantilla de ARM

En este ejercicio completará las tareas siguientes:

- Tarea 1: Implementación de una máquina virtual de Azure y una base de datos de Azure SQL

#### Tarea 1: Implementación de una máquina virtual de Azure y una base de datos de Azure SQL

En esta tarea, implementará una máquina virtual de Azure, que instalará automáticamente Visual Studio 2019 y SQL Server Management Studio 19 como parte de la implementación. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Implementar una plantilla personalizada** y presione la tecla **Entrar**.

3. En el panel **Implementación personalizada**, haga clic en la opción **Cree su propia plantilla en el editor**.

4. En el panel **Editar plantilla**, haga clic en **Cargar archivo**, busque el archivo **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json** y haga clic en **Abrir**.

5. En el panel **Editar plantilla**, haga clic en **Guardar**.

6. En el panel **Implementación personalizada**, en **Ámbito de la implementación**, asegúrese de que las siguientes opciones están configuradas (deje las demás con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
   |Resource group|Haga clic en **Crear nuevo** y escriba el nombre **AZ500LAB10**|
   |Location|**Este de EE. UU.**|
   |Nombre de usuario administrador|**Estudiante**|
   |Contraseña de administrador|**Usa tu contraseña personal creada en: Laboratorio 02 > Ejercicio 1 > Tarea 1 > Paso 9.**|
   
    >**Nota**: Aunque puede cambiar las credenciales administrativas usadas para iniciar sesión en la máquina virtual, no es obligatorio.

    >**Nota**: Para identificar las regiones de Azure donde puede aprovisionar máquinas virtuales de Azure, consulte [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)

7. Haga clic en el botón **Revisar y crear** y confirme la implementación haciendo clic en el botón **Crear**. 

    >**Nota**: Esto inicia la implementación de la máquina virtual de Azure y Azure SQL Database necesarias para este laboratorio. 

    >**Nota**: No espere a que se complete la implementación de la plantilla de ARM; continúe con el ejercicio siguiente. La implementación puede tardar entre **20 y 25 minutos**. 

### Ejercicio 2: Configuración del recurso de Key Vault con una clave y un secreto

>**Nota**: Para todos los recursos de este laboratorio, se usa la región **Este (EE. UU.)** . Compruebe con el instructor que esta es la región que se va a usar para la clase. 

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crear y configurar un almacén de claves
- Tarea 2: Agregar una clave al almacén de claves
- Tarea 3: Agregar un secreto al almacén de claves

#### Tarea 1: Crear y configurar un almacén de claves

En esta tarea, creará un recurso de Azure Key Vault. También configurará los permisos para Azure Key Vault.

1. Haga clic en el primer icono (junto a la barra de búsqueda) de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

2. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

3. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear un Azure Key Vault en el grupo de recursos **AZ500LAB10**. (Si eligió otro nombre para el grupo de recursos de este laboratorio en la tarea 1, use ese nombre también para esta tarea). Los nombres del Key Vault deben ser únicos. Recuerde el nombre que ha elegido. Lo necesitará a lo largo de este laboratorio.  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location
    ```

    >**Nota**: La salida del último comando mostrará el nombre del almacén y el URI del almacén. El URI del almacén tiene el formato `https://<vault_name>.vault.azure.net/`

4. Cierre el panel de Cloud Shell. 

5. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de recursos** y presione la tecla **Entrar**.

6. En el panel **Grupos de recursos**, en la lista del grupos de recursos, haga clic en la entrada **AZ500LAB10** (o el otro nombre que eligió anteriormente para el grupo de recursos).

7. En el panel Grupo de recursos, haga clic en la entrada que representa el Key Vault recién creado. 

8. En la hoja Key Vault, en la sección **Información general**, haga clic en **Directivas de acceso** y, a continuación, haga clic en **+ Crear**.

9. En el panel **Crear una directiva de acceso**, configure las opciones siguientes (deje todas las demás con los valores predeterminados): 

    |Configuración|Value|
    |----|----|
    |Configurar a partir de una plantilla (opcional)|**Administración de claves, secretos y certificados**|
    |Permisos de claves|haga clic en **Seleccionar todo**, lo que da como resultado un total de **9 permisos seleccionados**|
    |Permisos de clave y operaciones criptográficas|haga clic en **Firmar**, lo que da como resultado un total de **1 permiso seleccionado**|
    |Permisos de secretos|haga clic en **Seleccionar todo**, lo que da como resultado un total de **7 permisos seleccionados**|
    |Permisos de certificación|haga clic en **Seleccionar todo**, lo que da como resultado un total de **15 permisos seleccionados**|
    |Selección de la entidad de seguridad|haga clic en **Ninguno seleccionado** en la hoja **Entidad de seguridad**, seleccione la cuenta de usuario y haga clic en **Siguiente**.|
    |Aplicación (opcional)|Haga clic en **Siguiente**|
    |Revisar y crear|haga clic en **Crear**.|
    
    >**Nota**: La operación anterior Revisar y crear vuelve a la página Directivas de acceso que enumera Aplicación, Correo electrónico, Permisos de clave, Permisos de secreto y Permisos de certificado.
      
#### Tarea 2: Adición de una clave a Key Vault

En esta tarea, agregará una clave al Key Vault y verá información sobre la clave. 

1. En Azure Portal, abra una sesión de PowerShell en el panel de Cloud Shell.

2. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

3. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para agregar una clave protegida por software al Key Vault: 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```

    >**Nota**: El nombre de la clave es **MyLabKey**

4. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para comprobar que se creó la clave:

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

5. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para mostrar el identificador de la clave:

    ```powershell
    $key.key.kid
    ```

6. Minimice el panel de Cloud Shell. 

7. De nuevo en Azure Portal, en la hoja de Key Vault, en la sección **Objetos**, haga clic en **Claves**.

8. En la lista de claves, haga clic en la entrada **MyLabKey** y, a continuación, en el panel **MyLabKey**, haga clic en la entrada que representa la versión actual de la clave.

    >**Nota**: Examine la información sobre la clave que ha creado.

    >**Nota**: Puede hacer referencia a cualquier clave mediante el identificador de la clave. Para obtener la versión más reciente, haga referencia a `https://<key_vault_name>.vault.azure.net/keys/MyLabKey` u obtenga la versión específica con `https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`


#### Tarea 3: Adición de un secreto a Key Vault

1. Vuelva al panel de Cloud Shell.

2. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear una variable con un valor de cadena segura:

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

3.  En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para agregar el secreto al almacén:

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**Nota**: El nombre del secreto es SQLPassword. 

4.  En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para comprobar que se creó el secreto.

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

5. Minimice el panel de Cloud Shell. 

6. En Azure Portal, vaya la hoja de Key Vault y, en la sección **Objetos**, haga clic en **Secretos**.

7. En la lista de secretos, haga clic en la entrada **SQLPassword** y, a continuación, en el panel **SQLPassword**, haga clic en la entrada que representa la versión actual del secreto.

    >**Nota**: Examine la información sobre el secreto que ha creado.

    >**Nota**: Para obtener la versión más reciente de un secreto, haga referencia a `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>` u obtenga una versión específica haciendo referencia a `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>`


### Ejercicio 3: Configuración de una base de datos de Azure SQL y una aplicación controlada por datos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Habilitación de una aplicación cliente para obtener acceso al servicio de Azure SQL Database.
- Tarea 2: Creación de una directiva que permita a la aplicación acceder al Key Vault.
- Tarea 3: Recuperación de la cadena de conexión ADO.NET de Azure SQL Database 
- Tarea 4: Inicio de sesión en la máquina virtual de Azure Visual Studio 2019 y SQL Management Studio 19
- Tarea 5: Creación de una tabla en SQL Database y selección de columnas de datos para el cifrado


#### Tarea 1: Habilitación de una aplicación cliente para obtener acceso al servicio de Azure SQL Database. 

En esta tarea, habilitará una aplicación cliente para obtener acceso al servicio de Azure SQL Database. Para ello, debe configurar la autenticación necesaria y adquirir el identificador y el secreto de aplicación necesarios para autenticar la aplicación.

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Registros de la aplicación** y presione la tecla **Entrar**.

2. Abra el panel **Registros de la aplicación** y haga clic en **+ Nuevo registro**. 

3. En el panel **Registrar una aplicación**, configure las opciones siguientes (deje todas las demás con los valores predeterminados):

    |Configuración|Value|
    |----|----|
    |Nombre|**sqlApp**|
    |URI de redireccionamiento (opcional)|**Web** y **https://sqlapp**|

4. En el panel **Registrar una aplicación**, haga clic en **Registrar**. 

    >**Nota**: Una vez completado el registro, el explorador le redirigirá automáticamente al panel **sqlApp**. 

5. En el panel **sqlApp**, identifique el valor de **Id. de aplicación (cliente)** . 

    >**Nota**: Registre este valor. Lo necesitará en la próxima tarea.

6. En el panel **sqlApp**, en la sección **Administrar**, haga clic en **Certificados y secretos**.

7. En el panel **sqlApp | Certificados y secretos**, en la sección **Secretos de cliente**, haga clic en **+ Nuevo secreto de cliente**

8. En el panel **Agregar un secreto de cliente**, especifique la siguiente configuración:

    |Configuración|valor|
    |----|----|
    |Descripción|**Key1**|
    |Expira|**12 meses**|
    
9. Haga clic en **Agregar** para actualizar las credenciales de la aplicación.

10. En el panel **sqlApp | Certificados y secretos**, identifique el valor de **Key1**.

    >**Nota**: Registre este valor. Lo necesitará en la próxima tarea. 

    >**Nota**: Copie el valor *antes* de salir del panel. Una vez hecho esto, ya no es posible recuperar su valor de texto sin formato.


#### Tarea 2: Creación de una directiva que permita a la aplicación acceder al Key Vault.

En esta tarea, concederá a la aplicación recién registrada permisos para acceder a los secretos almacenados en Key Vault.

1. En Azure Portal, abra una sesión de PowerShell en el panel de Cloud Shell.

2. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

3. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear una variable que almacene el **id. de aplicación (cliente)** que registró en la tarea anterior (reemplace el marcador de posición `<Azure_AD_Application_ID>` por el valor del **id. de aplicación (cliente)** ):
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
4. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para crear una variable que almacene el nombre de Key Vault.
    ```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName

    $kvName
    ```

5. En la sesión de PowerShell del panel de Cloud Shell, ejecute lo siguiente para conceder permisos en Key Vault a la aplicación que registró en la tarea anterior:

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

6. Cierre el panel de Cloud Shell. 


#### Tarea 3: Recuperación de la cadena de conexión ADO.NET de Azure SQL Database 

La implementación de la plantilla de ARM del ejercicio 1 aprovisionó una instancia de Azure SQL Server y una base de datos de Azure SQL llamada **medicina**. Actualizará el recurso de base de datos vacío con una nueva estructura de tabla y seleccionará columnas de datos para el cifrado

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Bases de datos SQL** y presione la tecla **Entrar**.

2. En la lista de bases de datos SQL, haga clic en la entrada **medicina(<randomsqlservername>)** .

    >**Nota**: Si no se encuentra la base de datos, probablemente significa que la implementación que inició en el ejercicio 1 aún no se ha completado. Para validarlo, puede ir al grupo de recursos de Azure "AZ500LAB10" (o el nombre que eligió) y seleccionar **Implementaciones** en el panel Configuración.  

3. En el panel base de datos SQL, en la sección **Configuración**, haga clic en **Cadenas de conexión**. 

    >**Nota**: La interfaz incluye cadenas de conexión para ADO.NET, JDBC, ODBC, PHP y Go. 
   
4. Registre la cadena de conexión **ADO.NET (autenticación de SQL**). Lo necesitará más adelante.

    >**Nota**: Al usar la cadena de conexión, asegúrese de reemplazar el marcador de posición `{your_password}` por la contraseña que configuró con la implementación en el ejercicio 1.

#### Tarea 4: Inicio de sesión en la máquina virtual de Azure Visual Studio 2019 y SQL Management Studio 19

En esta tarea, iniciará sesión en la máquina virtual de Azure cuya implementación inició en el ejercicio 1. Esta máquina virtual de Azure hospeda Visual Studio 2019 y SQL Server Management Studio 19.

    >**Note**: Before you proceed with this task, ensure that the deployment you initiated in the first exercise has completed successfully. You can validate this by navigating to the blade of the Azure resource group "Az500Lab10" (or other name you chose) and selecting **Deployments** from the Settings pane.  

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **máquinas virtuales** y presione la tecla **Entrar**.

2. En la lista de máquinas virtuales que se muestra, seleccione la entrada **az500-10-vm1**. En el panel **az500-10-vm1**, en el panel **Essentials**, tome nota de la **dirección IP pública**. Utilizará esta dirección más adelante. 

#### Tarea 5: Creación de una tabla en SQL Database y selección de columnas de datos para el cifrado

En esta tarea, se conectará a la base de datos SQL con SQL Server Management Studio y creará una tabla. A continuación, cifrará dos columnas de datos mediante una clave autogenerada de Azure Key Vault. 

1. En Azure Portal, vaya al panel de la base de datos SQL **medical**, en la sección **Essentials**, identifique el **nombre del servidor** (cópielo en el Portapapeles) y, a continuación, en la barra de herramientas, haga clic en **Establecer el firewall del servidor**.  

    >**Nota**: Registre el nombre del servidor. Necesitará el nombre del servidor más adelante en esta tarea.

2. En el panel **Configuración del firewall**, desplácese hacia abajo hasta Nombre de regla, haga clic en **+Agregar regla de firewall** y especifique la siguiente configuración: 

    |Configuración|Value|
    |---|---|
    |Nombre de la regla|**Permitir máquina virtual mgmt**|
    |Dirección IP inicial|la dirección IP pública de az500-10-vm1|
    |Dirección IP final|la dirección IP pública de az500-10-vm1|

3. Haga clic en **Guardar** para guardar el cambio y cerrar el panel de confirmación. 

    >**Nota**: Esto modifica la configuración del firewall del servidor, lo que permite conexiones a la base de datos de medicina desde la dirección IP pública de la máquina virtual de Azure que implementó en este laboratorio.

4. Vuelva al panel **az500-10-vm1**, haga clic en **Introducción** y,a continuación, haga clic en **Conectar** y, en el menú desplegable, haga clic en **RDP**. 

5. Haga clic en **Descargar archivo RDP** y úselo para conectarse a la máquina virtual de Azure **az500-10-vm1** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

    |Configuración|Valor|
    |---|---|
    |Nombre de usuario|**Estudiante**|
    |Contraseña|**Usa tu contraseña personal creada en: Laboratorio 02 > Ejercicio 1 > Tarea 1 > Paso 9.**|

    >**Nota**: Espere a que se carguen la sesión de Escritorio remoto y **Administrador del servidor**. Cierre el Administrador del servidor. 

    >**Nota**: Los pasos restantes de este laboratorio se realizan dentro de la sesión de Escritorio remoto a la máquina virtual de Azure **az500-10-vm1**. 

6. Haga clic en **Inicio**, en el menú **Inicio**, expanda la carpeta **Microsoft SQL Server Tools 19** y haga clic en el elemento de menú **Microsoft SQL Server Management Studio**.

7. En el cuadro de diálogo **Conectar con el servidor**, aplique la configuración siguiente: 

    |Configuración|Value|
    |---|---|
    |Tipo de servidor|**Motor de base de datos**|
    |Nombre del servidor|nombre de servidor que identificó anteriormente en esta tarea|
    |Autenticación|**SQL Server Authentication**|
    |Iniciar sesión|**Estudiante**|
    |Contraseña|**Usa tu contraseña personal creada en: Laboratorio 02 > Ejercicio 1 > Tarea 1 > Paso 9.**|

8. En el cuadro de diálogo **Conectar con el servidor**, haga clic en **Conectar**.

9. En la consola de **SQL Server Management Studio**, en el panel **Explorador de objetos**, expanda la carpeta **Bases de datos**.

10. En el panel **Explorador de objetos**, haga clic con el botón derecho en la base de datos **medicina** y haga clic en **Nueva consulta**.

11. Pegue el código siguiente en la ventana de consulta y haga clic en **Ejecutar**. Esto creará una tabla de **Pacientes**.

     ```sql
     CREATE TABLE [dbo].[Patients](
        [PatientId] [int] IDENTITY(1,1),
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NULL,
        [LastName] [nvarchar](50) NULL,
        [MiddleName] [nvarchar](50) NULL,
        [StreetAddress] [nvarchar](50) NULL,
        [City] [nvarchar](50) NULL,
        [ZipCode] [char](5) NULL,
        [State] [char](2) NULL,
        [BirthDate] [date] NOT NULL 
     PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
     ```
12. Una vez creada correctamente la tabla, en el panel **Explorador de objetos**, expanda el nodo de la base de datos **medicina**, el nodo de **tablas** y haga clic con el botón derecho en el nodo **dbo.Patients** y haga clic en **Cifrar columnas**. 

    >**Nota**: Esto iniciará el asistente para **Always Encrypted**.

13. En la página **Introducción**, haga clic en **Siguiente**.

14. En la página **Selección de columna**, seleccione las columnas **SSN** y **Fecha de nacimiento**, establezca el **Tipo de cifrado** de la columna **SSN** en **Determinista** y el de la columna **Fecha de nacimiento** en **Aleatorio** y haga clic en **Siguiente**.

    >**Nota**: Al realizar el cifrado, si se genera cualquier error como **Excepción generada por el destino de la invocación** relacionado con **Rotary(Microsoft.SQLServer.Management.ServiceManagement)** , compruebe que los valores de **Permisos clave** de **Operaciones de directiva de rotación** estén **desmarcados**. Si no está en Azure Portal, vaya a **Key Vault** >> **Directivas de acceso** >> **Permisos clave** y desmarque todos los valores que figuren en **Operaciones de directiva de rotación** >> En **Operaciones de clave con privilegios** >> Desmarque **Publicar**.

15. En la página **Configuración de clave maestra**, seleccione **Azure Key Vault**, haga clic en **Iniciar sesión**, cuando se le solicite, autentíquese con la misma cuenta de usuario que usó para aprovisionar la instancia de Azure Key Vault anteriormente en este laboratorio, asegúrese de que Key Vault aparezca en la lista desplegable **Seleccionar un Azure Key Vault** y haga clic en **Siguiente**.

16. En la página **Ejecutar configuración**, haga clic en **Siguiente**.
    
17. En la página **Resumen**, haga clic en **Finalizar** para continuar con el cifrado. Cuando se le solicite, vuelva a iniciar sesión con la misma cuenta de usuario que usó para aprovisionar la instancia de Azure Key Vault anteriormente en este laboratorio.

18. Una vez completado el proceso de cifrado, en la página **Resultados**, haga clic en **Cerrar**.

19. En la consola de **SQL Server Management Studio**, en el panel **Explorador de objetos**, en el nodo **medicina**, expanda los subnodos **Seguridad** y **Claves de Always Encrypted**. 

    >**Nota**: El subnodo **Claves de Always Encrypted** contiene las subcarpetas **Claves maestras de columna** y **Claves de cifrado de columna**.


### Ejercicio 4: Demostración del uso de Azure Key Vault para cifrar la base de datos de Azure SQL

En este ejercicio completará las tareas siguientes:

- Tarea 1: Ejecución de una aplicación controlada por datos para demostrar el uso de Azure Key Vault para cifrar la base de datos de Azure SQL

#### Tarea 1: Ejecución de una aplicación controlada por datos para demostrar el uso de Azure Key Vault para cifrar la base de datos de Azure SQL

Creará una aplicación de consola mediante Visual Studio para cargar datos en las columnas cifradas y, a continuación, acceder a los datos de forma segura mediante una cadena de conexión que accede a la clave en el Key Vault.

1. Desde la sesión RDP a **az500-10-vm1**, inicie **Visual Studio 2019** desde el **menú Inicio**.

2. Cambie a la ventana que muestra el mensaje de bienvenida de Visual Studio 2019, haga clic en el botón **Iniciar sesión** y, cuando se le solicite, proporcione las credenciales que usó para autenticarse en la suscripción de Azure que usa en este laboratorio.

3. En la página **Comenzar**, haga clic en **Crear un proyecto nuevo**. 

4. En la lista de plantillas de proyecto, busque **Aplicación de consola (.NET Framework)** , en la lista de resultados, haga clic en **Aplicación de consola (.NET Framework)** para **C#** y haga clic en **Siguiente**.

5. En la página **Configurar el nuevo proyecto**, especifique las siguientes opciones (deje las demás opciones con sus valores predeterminados) y, a continuación, haga clic en **Crear**:

    |Configuración|Valor|
    |---|---|
    |Nombre de proyecto|**OpsEncrypt**|
    |Nombre de la solución|**OpsEncrypt**|
    |marco|**.NET Framework 4.7.2**|

6. En la consola de Visual Studio, haga clic en el menú **Herramientas**, en el menú desplegable, haga clic en **Administrador de paquetes NuGet** y, en el menú en cascada, haga clic en **Consola del administrador de paquetes**.

7. En el panel **Consola del administrador de paquetes**, ejecute lo siguiente para instalar el primer paquete **NuGet** necesario:

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

8. En el panel **Consola del administrador de paquetes**, ejecute lo siguiente para instalar el segundo paquete **NuGet** necesario:

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```
    
9. Minimice la sesión de RDP en la máquina virtual de Azure y, a continuación, vaya a **\\Todos los archivos\\Laboratorios\\10\\program.cs**, ábralo en el Bloc de notas y copie su contenido en el Portapapeles.

10. Vuelva a la sesión de RDP y, en la consola de Visual Studio, en la ventana **Explorador de soluciones**, haga clic en **Program.cs** y reemplace su contenido por el código que copió en el Portapapeles.

11. En la ventana de Visual Studio, en el panel **Program.cs** en la línea 15, reemplace el marcador de posición `<connection string noted earlier>` por la cadena de conexión **ADO.NET** de la base de datos de Azure SQL que registró anteriormente en el laboratorio. En la cadena de conexión, reemplace el marcador de posición `{your_password}` por la contraseña que especificó en la implementación en el ejercicio 1. Si guardó la cadena en el equipo del laboratorio, es posible que tenga que salir de la sesión de RDP para copiar la cadena de ADO y, a continuación, volver a la máquina virtual de Azure para pegarla.

12. En la ventana de Visual Studio, en el panel **Program.cs** en la línea 16, reemplace el marcador de posición `<client id noted earlier>` por el valor del **id. de aplicación (cliente)** de la aplicación registrada que registró anteriormente en el laboratorio. 

13. En la ventana de Visual Studio, en el panel **Program.cs** en la línea 17, reemplace el marcador de posición `<key value noted earlier>` por el valor de **Key1** de la aplicación registrada que registró anteriormente en el laboratorio. 

14. En la consola de Visual Studio, haga clic en el botón **Iniciar** para iniciar la compilación de la aplicación de consola e iniciarla.

15. La aplicación iniciará una ventana del símbolo del sistema. Cuando se le solicite una contraseña, escriba la contraseña que especificó en la implementación del ejercicio 1 para conectarse a Azure SQL Database. 

16. Deje la aplicación de consola en ejecución y cambie a la consola de **SQL Management Studio**. 

17. En el panel **Explorador de objetos**, haga clic con el botón derecho en la **base de datos medica** y, abriendo el menú con el botón derecho, haga clic en **Nueva consulta**.

18. En la ventana de consulta, ejecute la consulta siguiente para comprobar que los datos cargados en la base de datos desde la aplicación de consola están cifrados.

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

19. Vuelva a la aplicación de consola, donde se le pedirá que escriba un SSN válido. Esto consultará los datos en la columna cifrada. En el símbolo del sistema, escriba lo siguiente y presione la tecla Entrar:

    ```cmd
    999-99-0003
    ```

    >**Nota**: Compruebe que los datos devueltos por la consulta no están cifrados.

20. Para finalizar la aplicación de consola, presione la tecla Entrar

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. 

2. En el menú desplegable superior izquierdo del panel de Cloud Shell, si es necesario, seleccione **PowerShell** y, cuando se le solicite, haga clic en **Confirmar**.

3. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar los grupos de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

4.  Cierre el panel de **Cloud Shell**. 
