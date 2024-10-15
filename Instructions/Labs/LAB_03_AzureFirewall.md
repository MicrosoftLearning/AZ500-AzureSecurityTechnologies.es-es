---
lab:
  title: "03 - Azure\_Firewall"
  module: Module 02 - Plan and implement security for public access to Azure resources
---

# Laboratorio 03: Azure Firewall
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que instale Azure Firewall. Esto ayudará a su organización a controlar el acceso de red entrante y saliente, que es una parte importante de un plan de seguridad de red general. En concreto, le gustaría crear y probar los siguientes componentes de infraestructura:

- Una red virtual con una subred de carga de trabajo y una subred de host de salto.
- Cada máquina virtual es una subred. 
- Una ruta personalizada que garantiza que todo el tráfico de carga de trabajo saliente de la subred de la carga de trabajo debe usar el firewall.
- Reglas de aplicación de firewall que solo permiten el tráfico saliente a www.bing.com. 
- Reglas de red de firewall que permiten búsquedas de servidores DNS externos.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Implementar y probar una instancia de Azure Firewall

## Diagrama de Azure Firewall

![imagen](https://user-images.githubusercontent.com/91347931/157529954-a1bc434b-2eca-41c1-b875-1f0c977d5e20.png)

## Instrucciones

## Archivos de laboratorio:

- **\\Allfiles\\Labs\\08\\template.json**

### Ejercicio 1: Implementar y probar una instancia de Azure Firewall

### Tiempo estimado: 40 minutos

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

En este ejercicio completará las tareas siguientes:

- Tarea 1: Usar una plantilla para implementar el entorno de laboratorio. 
- Tarea 2: Implementar una instancia de Azure Firewall.
- Tarea 3: Crear una ruta predeterminada.
- Tarea 4: Configurar una regla de aplicación.
- Tarea 5: Configurar una regla de red. 
- Tarea 6: Configurar servidores DNS.
- Tarea 7: Probar el firewall. 

#### Tarea 1: Usar una plantilla para implementar el entorno de laboratorio. 

En esta tarea, revisará e implementará el entorno de laboratorio. 

En esta tarea, creará una máquina virtual mediante una plantilla de ARM. Esta máquina virtual se usará en el último ejercicio de este laboratorio. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Implementar una plantilla personalizada** y presione la tecla **Entrar**.

3. En la hoja **Implementación personalizada**, haga clic en la opción **Cree su propia plantilla en el editor**.

4. En la hoja **Editar plantilla**, haga clic en **Cargar archivo**, busque el archivo **\\Allfiles\\Labs\\08\\template.json** y haga clic en **Abrir**.

    >**Nota**: Revise el contenido de la plantilla y observe que implementa una VM de Azure que hospeda Windows Server 2016 Datacenter.

5. En el panel **Editar plantilla**, haga clic en **Guardar**.

6. En el panel **Implementación personalizada**, asegúrese de que las siguientes opciones están configuradas (deje las demás con los valores predeterminados):

   |Configuración|Valor|
   |---|---|
   |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
   |Resource group|Haga clic en **Crear nuevo** y escriba el nombre **AZ500LAB08**.|
   |Location|**(EE. UU.) Este de EE. UU.**|
   |adminPassword|Una contraseña segura de su elección para las máquinas virtuales. Recuerde la contraseña. La necesitará más adelante para conectarse a las máquinas virtuales.|

    >**Nota**: Para identificar las regiones de Azure donde puede aprovisionar VM de Azure, consulte [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)

7. Haga clic en **Revisar y crear** y después en **Crear**.

    >**Nota**: Espere a que la implementación se complete. Este proceso tardará alrededor de 2 minutos. 

#### Tarea 2: Implementar Azure Firewall

En esta tarea, implementará Azure Firewall en la red virtual. 

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** de la parte superior de la página, escriba **Firewalls** y presione la tecla **Entrar**.

2. En la hoja **Firewalls**, haga clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** del panel **Crear un firewall**, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Resource group|**AZ500LAB08**|
   |Nombre|**Test-FW01**|
   |Region|**(EE. UU.) Este de EE. UU.**|
   |SKU del firewall|**Estándar**|
   |Administración del firewall|**Use reglas de firewall (clásicas) para administrar este firewall**.|
   |Elegir una red virtual|Haga clic en la opción **Usar existente** y, en la lista desplegable, seleccione **Test-FW-VN**.|
   |Dirección IP pública|Haga clic en **Agregar nuevo**, escriba el nombre **TEST-FW-PIP** y haga clic en **Aceptar**.|

4. Haga clic en **Revisar y crear** y, a continuación, en **Crear**. 

    >**Nota**: Espere a que la implementación se complete. Este proceso tardará alrededor de 5 minutos. 

5. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de recursos** y presione la tecla **Entrar**.

6. En el panel **Grupos de recursos**, en la lista de grupos de recursos, haga clic en la entrada **AZ500LAB08**.

    >**Nota**: En la hoja del grupo de recursos **AZ500LAB08**, revise la lista de recursos. Se pueden ordenar por **Tipo**.

7. En la lista de recursos, haga clic en la entrada que representa el firewall **Test-FW01**.

8. En la hoja **Test-FW01**, identifique la dirección **IP privada** que se asignó al firewall. 

    >**Nota**: Necesitará esta información en la siguiente tarea.


#### Tarea 3: Crear una ruta predeterminada

En esta tarea, creará una ruta predeterminada para la subred **Workload-SN**. Esta ruta configurará el tráfico saliente a través del firewall.

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** de la parte superior de la página, escriba **Tablas de rutas** y presione la tecla **Entrar**.

2. En la hoja **Tablas de rutas**, haga clic en **+ Crear**.

3. En la hoja **Crear tabla de rutas**,especifique la siguiente configuración:

   |Configuración|Value|
   |---|---|
   |Resource group|**AZ500LAB08**|
   |Region| **Este de EE. UU.**|
   |NOMBRE|**Firewall-route**|

4. Haga clic en **Revisar y crear** y, a continuación, haga clic en **Crear** y espere a que se complete el aprovisionamiento. 

5. En la hoja **Tablas de rutas**, haga clic en **Actualizar** y, en la lista de tablas de rutas, haga clic en la entrada **Firewall-route**.

6. En la hoja **Firewall-route**, en la sección **Configuración**, haga clic en **Subredes** y, a continuación, en la hoja **Firewall-route \| Subredes**, haga clic en **+ Asociar**.

7. En la hoja **Asociar subred**, especifique la siguiente configuración:

   |Configuración|Value|
   |---|---|
   |Virtual network|**Test-FW-VN**|
   |Subnet|**Workload-SN**|

    >**Nota**: Asegúrese de que la subred **Workload-SN** está seleccionada para esta ruta; de lo contrario, el firewall no funcionará correctamente.

8. Haga clic en **Aceptar** para asociar el firewall a la subred de la red virtual. 

9. De nuevo en la hoja **Firewall-route**, en la sección **Configuración**, haga clic en **Rutas** y luego en **+ Agregar**. 

10. En la hoja **Agregar ruta**, especifique la siguiente configuración:  

   |Configuración|Value|
   |---|---|
   |Nombre de ruta|**FW-DG**|
   |Destino del prefijo de dirección|**Dirección IP**|
   |Intervalos de direcciones IP de destino y CIDR|**0.0.0.0/0**
   |Tipo de próximo salto|**Aplicación virtual**|
   |Siguiente dirección de salto|Dirección IP privada del firewall que identificó en la tarea anterior|

    >**Nota**: Azure Firewall es en realidad un servicio administrado, pero la aplicación virtual funciona en esta situación.
    
11.  Haga clic en **Agregar** para agregar la ruta. 


#### Tarea 4: Configurar una regla de aplicación

En esta tarea, creará una regla de aplicación que permita el acceso saliente a `www.bing.com`.

1. En Azure Portal, vuelva al firewall **Test-FW01**.

2. En la hoja **Test-FW01**, en la sección **Configuración**, haga clic en **Reglas (clásicas)** .

3. En la hoja **Test-FW01 \| Reglas (clásico)** , haga clic en la pestaña **Colección de reglas de aplicación** y, a continuación, haga clic en **+ Agregar una colección de reglas de aplicación**.

4. En el panel **Agregar una colección de reglas de aplicación**, especifique la siguiente configuración (deje todas las demás opciones con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Nombre|**App-Coll01**|
   |Prioridad|**200**|
   |Acción|**Permitir**|

5. En la hoja **Agregar una colección de reglas de aplicación**, cree una nueva entrada en la sección **FQDN de destino** con la siguiente configuración (deje todas las demás opciones con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |name|**AllowGH**|
   |Tipo de origen|**Dirección IP**|
   |Source|**10.0.2.0/24**|
   |Protocolo y puerto|**http:80, https:443**|
   |FQDN de destino|**www.bing.com**|

6. Haga clic en **Agregar** para agregar la regla de aplicación basada en los FQDN de destino.

    >**Nota**: Azure Firewall incluye una colección de reglas integradas para los FQDN de infraestructura que están permitidos de forma predeterminada. Estos FQDN son específicos para la plataforma y no se pueden usar para otros fines. 

#### Tarea 5: Configurar una regla de red

En esta tarea, agregará una regla de red que permita el acceso saliente a dos direcciones IP en el puerto 53 (DNS).

1. En Azure Portal, vuelva a la hoja **Test-FW01 \| Reglas (clásicas)** .

2. En la hoja **Test-FW01 \| Reglas (clásicas)** , haga clic en la pestaña **Recopilación de reglas de red** y, a continuación, haga clic en **+ Agregar una colección de reglas de red**.

3. En el panel **Agregar una colección de reglas de red**, especifique la siguiente configuración (deje las demás con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Nombre|**Net-Coll01**|
   |Prioridad|**200**|
   |Acción|**Permitir**|

4. En la hoja **Agregar una colección de reglas de red**, cree una nueva entrada en la sección **Direcciones IP** con la siguiente configuración (deje las demás opciones con sus valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Nombre|**AllowDNS**|
   |Protocolo|**UDP**|
   |Tipo de origen|**Dirección IP**|
   |Direcciones de origen|**10.0.2.0/24**|
   |Tipo de destino|**Dirección IP**|
   |Dirección de destino|**209.244.0.3,209.244.0.4**|
   |Puertos de destino|**53**|

5. Haga clic en **Agregar** para agregar la regla de red.

    >**Nota**: Las direcciones de destino usadas en este caso son servidores DNS públicos conocidos. 

#### Tarea 6: Configurar los servidores DNS de la máquina virtual

En esta tarea, configurará las direcciones DNS principal y secundaria para la máquina virtual. Este no es un requisito del firewall. 

1. En Azure Portal, vuelva al grupo de recursos **AZ500LAB08**.

2. En la hoja **AZ500LAB08**, en la lista de recursos, haga clic en la máquina virtual **Srv-Work**.

3. En la hoja **Srv-Work**, haz clic en **Redes**.

4. En la hoja **Srv-Work \| Redes** haga clic en el vínculo situado junto a la entrada **Interfaz de red**.

5. En la hoja Interfaz de red, en la sección **Configuración**, haga clic en **Servidores DNS**, seleccione la opción **Personalizado**, agregue los dos servidores DNS a los que se hace referencia en la regla de red: **209.244.0.3** y **209.244.0.4**, y haga clic en **Guardar** para guardar el cambio.

6. Vuelva a la página de la máquina virtual **Srv-Work**.

    >**Nota**: Espere a que se complete la actualización.

    >**Nota**: La actualización de los servidores DNS para una interfaz de red reiniciará automáticamente la máquina virtual a la que está conectada y, si procede, el resto de máquinas virtuales del mismo conjunto de disponibilidad.

#### Tarea 7: Probar el firewall

En esta tarea, probará el firewall para confirmar que funciona según lo previsto.

1. En Azure Portal, vuelva al grupo de recursos **AZ500LAB08**.

2. En la hoja **AZ500LAB08**, en la lista de recursos, haga clic en la máquina virtual **Srv-Jump**.

3. En la hoja **Srv-Jump**, haga clic en **Conectar** y, en el menú desplegable, haga clic en **RDP**. 

4. Haga clic en **Descargar archivo RDP** y úselo para conectarse a la VM de Azure **Srv-Jump** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**localadmin**|
   |Contraseña|La contraseña segura que eligió durante la implementación de la plantilla personalizada en la tarea 1 paso 6.|

    >**Nota**: Los pasos siguientes se realizan en la sesión de Escritorio remoto para la VM de Azure **Srv-Jump**. 

    >**Nota**: Se conectará a la máquina virtual **Srv-Work**. Esto se hace para que podamos probar la capacidad de acceder al sitio web bing.com.  

5. En la sesión de Escritorio remoto para **Srv-Jump**, haga clic con el botón derecho en **Iniciar**; en el menú contextual, haga clic en **Ejecutar** y, en el cuadro de diálogo **Ejecutar**, ejecute lo siguiente para conectarse a **Srv-Work**. 

    ```
    mstsc /v:Srv-Work
    ```

6. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**localadmin**|
   |Contraseña|La contraseña segura que eligió durante la implementación de la plantilla personalizada en la tarea 1 paso 6.|

    >**Nota**: Espere a que se establezca la sesión de Escritorio remoto y se cargue la interfaz del Administrador del servidor.

7. En la sesión de Escritorio remoto sesión para **Srv-Work**, en **Administrador del servidor**, haga clic en **Servidor local** y, a continuación, en **Configuración de seguridad mejorada de IE**.

8. En el cuadro de diálogo **Configuración de seguridad mejorada de Internet Explorer**, establezca ambas opciones en **Desactivado** y haga clic en **Aceptar**.

9. En la sesión de Escritorio remoto para **Srv-Work**, inicie Internet Explorer y vaya a **`https://www.bing.com`** . 

    >**Nota**: El sitio web debería mostrarse correctamente. El firewall le permite el acceso.

10. Vaya a **`http://www.microsoft.com/`**.

    >**Nota**: En la página del explorador, debería recibir un mensaje con texto similar al siguiente: `HTTP request from 10.0.2.4:xxxxx to microsoft.com:80. Action: Deny. No rule matched. Proceeding with default action.` Se espera porque el firewall bloquea el acceso a este sitio web. 

11. Finalice ambas sesiones de Escritorio remoto.

> Resultado: ha configurado y probado correctamente Azure Firewall.

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados. 

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, haga clic en **PowerShell** y en **Crear almacenamiento**.

2. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

3. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB08" -Force -AsJob
    ```
4. Cierre el panel de **Cloud Shell**. 
