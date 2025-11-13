---
lab:
  title: '08: Creación de un área de trabajo de Log Analytics y una regla de recopilación de datos (DCR)'
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# Laboratorio 08: Creación de un área de trabajo de Log Analytics y una regla de recopilación de datos (DCR)

# Manual de laboratorio para alumnos

## Escenario del laboratorio

Como ingeniero de seguridad de Azure para una empresa de tecnología financiera, tienes la tarea de mejorar la supervisión y la visibilidad de la seguridad en todas las máquinas virtuales (VM) de Azure que se usan para procesar transacciones financieras y administrar datos confidenciales de los clientes. El equipo de seguridad requiere registros detallados y métricas de rendimiento de estas máquinas virtuales para detectar posibles amenazas y optimizar el rendimiento del sistema. El director de seguridad de la información (CISO) te ha pedido que implementes una solución que recopile eventos de seguridad, registros del sistema y contadores de rendimiento. Se te ha asignado para configurar el agente de Azure Monitor (AMA) junto con las reglas de recopilación de datos (DCR) para centralizar la recopilación de registros y la supervisión del rendimiento.



> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Implementación de una máquina virtual de Azure
- Ejercicio 2: Creación de un área de trabajo de Log Analytics
- Ejercicio 3: Creación de una cuenta de almacenamiento de Azure
- Ejercicio 4: Creación de una regla de recopilación de datos.
  
## Instrucciones

### Ejercicio 1: Implementación de una máquina virtual de Azure

### Tiempo del ejercicio: 10 minutos

En este ejercicio completará las tareas siguientes: 

- Tarea 1: Implementación de una máquina virtual de Azure. 

#### Tarea 1: Implementar una máquina virtual de Azure

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

3. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

4. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para crear el grupo de recursos que se usará en este laboratorio:
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Nota**: este grupo de recursos se usará para los laboratorios 8, 9 y 10.

5. En la sesión de PowerShell del panel de Cloud Shell, ejecuta lo siguiente para habilitar el cifrado en el host (EAH)
   
   ```powershell
    Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace Microsoft.Compute 
    ```

5. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para crear una máquina virtual de Azure. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
    ```
    
6.  Cuando se le pidan las credenciales:

    |Configuración|Value|
    |---|---|
    |Usuario |**localadmin**|
    |Contraseña|**Use su contraseña personal creada en Laboratorio 02 > Ejercicio 2 > Tarea 1 > Paso 3.**|

    >**Nota**: Espere a que la implementación se complete. 

7. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para confirmar que se creó la máquina virtual denominada **myVM** y que su valor de **provisioningState** es **Correcto**.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. Cierre el panel de Cloud Shell. 

### Ejercicio 2: Creación de un área de trabajo de Log Analytics

### Tiempo del ejercicio: 10 minutos

En este ejercicio completará las tareas siguientes: 

- Tarea 1: Creación de un área de trabajo de Log Analytics.

#### Tarea 1: Creación de un área de trabajo de Log Analytics

En esta tarea, creará un área de trabajo de Log Analytics. 

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Áreas de trabajo de Log Analytics** y presione la tecla **Entrar**.

2. En la página **Áreas de trabajo de Log Analytics**, haga clic en  **+ Crear**.

3. En la pestaña **Aspectos básicos** de la hoja **Crear un área de trabajo de Log Analytics**, especifique las siguientes opciones (deje las demás con los valores predeterminados):

    |Configuración|Valor|
    |---|---|
    |Suscripción|Nombre de la suscripción a Azure que usas en este laboratorio|
    |Resource group|**AZ500LAB131415**|
    |Name|Cualquier nombre válido y único globalmente|
    |Region|**Este de EE. UU.**|

4. Seleccione **Revisar + crear**.

5. En la pestaña **Revisar y crear** del panel **Crear área de trabajo de Log Analytics**, seleccione **Crear**.

### Ejercicio 3: Creación de una cuenta de almacenamiento de Azure

### Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Creación de una cuenta de almacenamiento de Azure.

#### Tarea 1: Creación de una cuenta de almacenamiento de Azure

En esta tarea, creará una cuenta de almacenamiento.

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Cuentas de almacenamiento** y presione la tecla **Entrar**.

2. En la hoja **Cuentas de almacenamiento** de Azure Portal, haga clic en el botón **+ Crear** para crear una nueva cuenta de almacenamiento.

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/73eb9241-d642-455a-a1ff-b504670395c0)

3. En la pestaña **Aspectos básicos** de la hoja **Crear cuenta de almacenamiento**, configure las siguientes opciones (deje las demás con los valores predeterminados):

    |Configuración|Valor|
    |---|---|
    |Suscripción|Nombre de la suscripción a Azure que usas en este laboratorio|
    |Resource group|**AZ500LAB131415**|
    |Nombre de la cuenta de almacenamiento|Cualquier nombre globalmente único con una longitud de 3 a 24 caracteres, que consta de letras y dígitos|
    |Location|**(EE. UU.) EastUS**|
    |Rendimiento|**Estándar (cuenta general-purpose v2)**|
    |Redundancia|**Almacenamiento con redundancia local (LRS)**|

4. En la pestaña **Aspectos básicos** del panel **Crear cuenta de almacenamiento**, haga clic en **Revisar**, espere a que se complete el proceso de validación y, luego, haga clic en **Crear**.

     ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d443821c-2ddf-4794-87fa-bfc092980eba)

    >**Nota**: Espere a que se cree la cuenta de almacenamiento. Este proceso tardará alrededor de 2 minutos.

### Ejercicio 3: Creación de una regla de recopilación de datos

### Tiempo estimado: 15 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Creación de una regla de recopilación de datos.

#### Tarea 1: Creación de una regla de recopilación de datos.

En esta tarea, creará una regla de recopilación de datos.

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Supervisión** y presione la tecla **Entrar**.

2. En la hoja **Supervisar configuración**, haga clic en **Reglas de recopilación de datos.**

  ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d43e8f94-efb2-4255-9320-210c976fd45e)


3. Haz clic en el botón **+ Crear** para crear la nueva regla de recopilación de datos

4. En la pestaña **Aspectos básicos** de la hoja **Crear una regla de recopilación de datos**, especifique la configuración siguiente:
  
    |Configuración|Valor|
    |---|---|
    |**Detalles de la regla**|
    |Nombre de regla|**DCR1**|
    |Subscription|Nombre de la suscripción a Azure que usas en este laboratorio|
    |Grupo de recursos|**AZ500LAB131415**|
    |Region|**Este de EE. UU.**|
    |Tipo de plataforma|**Windows**|
    |Punto de conexión de recopilación de datos|*Déjelo en blanco*|

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/9b58c4ce-b7a8-4acf-8289-d95b270a6083)


4. Haga clic en el botón denominado **Siguiente: Recursos >** para continuar.
   
6. En la pestaña Recursos, selecciona **+ Agregar recursos,** y marca **Habilitar puntos de conexión de recopilación de datos.** En la plantilla Seleccionar un ámbito, selecciona **AZ500LAB131415** y haz clic en **Aplicar.**

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d4191115-11bc-43ec-9bee-e84b9b95a821)

10. Haga clic en el botón denominado **Siguiente: Recopilar y entregar >** para continuar.

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8294d300-f910-4757-ad52-43c7594ac822)

11. Haga clic en **+ Agregar origen de datos** y, a continuación, en la página **Agregar origen de datos**, cambie el menú desplegable **Tipo de origen de datos** para mostrar **Contadores de rendimiento.** Deje la siguiente configuración predeterminada:

    |Configuración|Valor|
    |---|---|
    |**Contador de rendimiento**|**Frecuencia de muestreo (segundos)**|
    |CPU|60|
    |Memoria|60|
    |Disco|60|
    |Red|60|

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/a24e44ad-1d10-4533-80e2-bae1b3f6564d)

11. Haga clic en el botón denominado **Siguiente: Destino >** para continuar.
  
12. Haz clic en **+ Agregar destino**, cambia el menú desplegable **Tipo de destino** para mostrar los **Registros de Azure Monitor.** En la ventana **Suscripción**, asegúrese de que se muestra la *Suscripción* y, a continuación, cambie el menú desplegable **Cuenta o espacio de nombres** para reflejar el área de trabajo de Log Analytics creada anteriormente.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/481843f5-94c4-4a8f-bf51-a10d49130bf8)

11. En la parte inferior de la página, seleccione **Agregar origen de datos**.
    
    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/964091e7-bbbc-4ca8-8383-bb2871a1e7f0)

13. Haga clic en **Revisar y crear.**

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/50dd8407-a106-4540-9e14-ae40a3c04830)

14. Haga clic en **Crear**.

> Resultados: ha implementado una máquina virtual de Azure, un área de trabajo de Log Analytics, una cuenta de almacenamiento de Azure y una regla de recopilación de datos para recopilar eventos y contadores de rendimiento de máquinas virtuales con el agente de Azure Monitor.

>**Nota**: no quites los recursos de este laboratorio, ya que son necesarios para el laboratorio de Microsoft Defender for Cloud, "Habilitar acceso Just-In-Time en máquinas virtuales" y el laboratorio de Microsoft Sentinel.
 
