---
lab:
  title: 13 - Implementación de una máquina virtual de Azure y el área de trabajo de Log Analytics
  module: Module 04 - Manage security operations
---

# Laboratorio 13: Implementación de una máquina virtual de Azure y el área de trabajo de Log Analytics

# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que cree una máquina virtual de Azure y un área de trabajo de Log Analytics.

- Implementar una máquina virtual de Azure.
- Crear un área de trabajo de Log Analytics.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Creación de una máquina virtual de Azure
  
## Instrucciones

### Ejercicio 1: Implementación de una máquina virtual de Azure

### Tiempo del ejercicio: 10 minutos

En este ejercicio completará las tareas siguientes: 

- Tarea 1: Implementar una máquina virtual de Azure 

#### Tarea 1: Implementar una máquina virtual de Azure

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, seleccione **PowerShell** y **Crear almacenamiento**.

3. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

4. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para crear el grupo de recursos que se usará en este laboratorio:
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Nota**: Este grupo de recursos se usará para los laboratorios 13, 14 y 15. 

5. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para crear una máquina virtual de Azure. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
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

#### Tarea 2: Crear un área de trabajo de Log Analytics

En esta tarea, creará un área de trabajo de Log Analytics. 

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Áreas de trabajo de Log Analytics** y presione la tecla **Entrar**.

2. En la página **Áreas de trabajo de Log Analytics**, haga clic en  **+ Crear**.

3. En la pestaña **Aspectos básicos** de la hoja **Crear un área de trabajo de Log Analytics**, especifique las siguientes opciones (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Subscription|nombre de la suscripción de Azure que usa en este laboratorio|
    |Resource group|**AZ500LAB131415**|
    |Name|Cualquier nombre válido y único globalmente|
    |Region|**Este de EE. UU.**|

4. Seleccione **Revisar + crear**.

5. En la pestaña **Revisar y crear** del panel **Crear área de trabajo de Log Analytics**, seleccione **Crear**.

> Resultados: Implementó una máquina virtual de Azure y creó un área de trabajo de Log Analytics.

>**Nota**: No quite los recursos de este laboratorio, ya que son necesarios para los laboratorios de Microsoft Defender for Cloud y Microsoft Sentinel.
 
