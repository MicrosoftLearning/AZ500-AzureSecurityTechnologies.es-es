---
lab:
  title: '05: Protección de Azure SQL Database'
  module: Module 02 - Plan and implement security for Azure SQL Database and Azure SQL Managed Instance
---

# Laboratorio 05: Protección de Azure SQL Database
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que revise las características de seguridad de una base de datos de Azure SQL. En concreto, le interesa lo siguiente:

- Protección contra ataques como la inyección SQL y la filtración de datos. 
- Capacidad de detectar y clasificar la información de la base de datos en categorías como Confidencial. 
- Capacidad de auditar el servidor de la base de datos y las consultas de la base de datos y registrar eventos. 

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Implementación de características de seguridad en SQL Database

## Diagrama de protección de Azure SQL Database

![imagen](https://user-images.githubusercontent.com/91347931/157533836-7250fa58-a109-4882-a55b-d3fa3baf34ab.png)

## Instrucciones

## Archivos de laboratorio:

- **\\Allfiles\\Labs\\11\\azuredeploy.json**

### Ejercicio 1: Implementación de características de seguridad en SQL Database

### Tiempo estimado: 30 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Implementación de Azure SQL Database
- Tarea 2: Configuración de la protección de datos avanzada
- Tarea 3: Configuración de la clasificación de datos
- Tarea 4: Configuración de la auditoría

#### Tarea 1: Implementación de Azure SQL Database

En esta tarea, usará una plantilla para implementar la infraestructura de laboratorio. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Implementar una plantilla personalizada** y presione la tecla **Entrar**.

3. En el panel **Implementación personalizada**, haga clic en la opción **Cree su propia plantilla en el editor**.

4. En el panel **Editar plantilla**, haga clic en **Cargar archivo**, busque el archivo **\\Allfiles\\Labs\\11\\azuredeploy.json** y haga clic en **Abrir**.

    >**Nota**: Revise el contenido de la plantilla y tenga en cuenta que implementa una base de datos de Azure SQL.

5. En el panel **Editar plantilla**, haga clic en **Guardar**.

6. En el panel **Implementación personalizada**, asegúrese de que las siguientes opciones están configuradas (deje las demás con los valores predeterminados):

   |Configuración|Valor|
   |---|---|
   |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
   |Resource group|haga clic en **Crear nuevo** y escriba el nombre **AZ500LAB11**|
   |Location|**(EE. UU.) Este de EE. UU.**|

7. Haga clic en **Revisar + crear** y, después, en **Crear**.

    >**Nota**: Espere a que la implementación se complete. 

#### Tarea 2: Configuración de la protección de datos avanzada

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de recursos** y presione la tecla **Entrar**.

2. En el panel **Grupos de recursos**, en la lista de grupos de recursos, haga clic en la entrada **AZ500LAB11**.

3. En el panel **AZ500LAB11**, haga clic en la entrada que representa el SQL Server recién creado.

4. En el panel Servidor SQL, en la sección **Seguridad**, haga clic en **Microsoft Defender for Cloud** y seleccione **Habilitar Microsoft Defender for SQL**.

      >**Nota**: Espere a que la notificación indique que Microsoft Defender for SQL se ha habilitado correctamente.

5. En el panel SQL Server, en la sección **Seguridad** de la página **Microsoft Defender for Cloud**, en el parámetro **Microsoft Defender for SQL: habilitado en el nivel de suscripción (configurar)** , haga clic en **(configurar)** .  

      >**Nota**: Actualice el explorador si no se muestra **Configurar**.
    
6. En el panel **Configuración del servidor**, revise la información sobre los precios y el período de prueba, **CONFIGURACIÓN DE LA EVALUACIÓN DE VULNERABILIDADES** y **CONFIGURACIÓN DE ADVANCED THREAT PROTECTION**.

7. Vuelva al panel **Microsoft Defender for Cloud** y revise **Recomendaciones** y **Alertas de seguridad**.

      >**Nota**: Las recomendaciones pueden tardar entre 10 y 15 minutos en aparecer en el panel **Microsoft Defender for Cloud**. En lugar de esperar, continúe con la siguiente tarea, pero considere la posibilidad de volver a este panel una vez completadas todas las tareas restantes.


#### Tarea 3: Configuración de la clasificación de datos

En esta tarea, detectará y clasificará información en la base de datos SQL para el cumplimiento de la protección de datos y el RGPD.

1. En el panel Servidor SQL, en la sección **Configuración**, haga clic en **Bases de datos SQL**.

2. En la lista de bases de datos, seleccione la entrada **AZ500LabDb**. 

3. En el panel de la base de datos SQL **az500LabDb**, en la sección **Seguridad**, haga clic en **Clasificación y detección de datos**.

4. En el panel **Clasificación y detección de datos**, haga clic en la pestaña **Clasificación**.

    >**Nota**: El motor de clasificación examina la base de datos en busca de columnas que contengan datos potencialmente confidenciales y proporciona una lista de clasificaciones de columna recomendadas.

5. Haga clic en el mensaje de texto **Hemos encontrado 15 columnas con recomendaciones de clasificación** que se muestra en la barra azul en la parte superior del panel.

6. Revise las columnas enumeradas y la etiqueta de confidencialidad recomendada. 

7. Active la casilla **Seleccionar todo** y, a continuación, haga clic en **Aceptar las recomendaciones seleccionadas**.

    >**Nota**: Como alternativa, podría seleccionar solo determinadas columnas y descartar las demás. 

    >**Nota**: Tiene la opción de cambiar el tipo de información y la etiqueta de confidencialidad. 

8. Cuando haya terminado, haga clic en **Guardar**. 

    >**Nota**: Esto completará la clasificación y etiquetará de forma persistente las columnas de la base de datos con los nuevos metadatos de clasificación. 

9. De nuevo en el panel **Clasificación y detección de datos**, en la pestaña **Información general**, fíjese en que se ha actualizado con la información de clasificación más reciente. 

#### Tarea 4: Configuración de la auditoría 

En esta tarea, primero configurará la auditoría de nivel de servidor y, a continuación, configurará la auditoría de nivel de base de datos. 

1. En Azure Portal, vuelva al panel SQL Server.

2. En el panel SQL Server, en la sección **Seguridad**, haga clic en **Auditoría**.

    >**Nota**: Se trata de la auditoría de nivel de servidor. La configuración de auditoría predeterminada incluye todas las consultas y procedimientos almacenados ejecutados en la base de datos, así como inicios de sesión correctos y con errores.

3. Establezca la opción **Habilitar auditoría de Azure SQL** en **Activado** para habilitar la auditoría. 

4. Active la casilla **Almacenamiento** y se mostrarán los cuadros de entrada de **Suscripción** y **Cuenta de almacenamiento**.

5. Elija la **suscripción** en la lista desplegable.

6. Haga clic en **Cuenta de almacenamiento** y elija **Crear nueva**.

7. En el panel **Crear cuenta de almacenamiento**, en el cuadro **Nombre**, escriba un nombre único global que conste de entre 3 y 24 letras minúsculas y dígitos y haga clic en **Aceptar**. 

    >**Nota**: Es posible que tenga que actualizar el explorador para que la cuenta de almacenamiento esté disponible.

8. De nuevo en el panel **Auditoría**, en **Propiedades avanzadas**, establezca **Retención (días)** en **5**. 

9. En el panel Auditoría, haga clic en **Guardar** para guardar la configuración de auditoría. 

    >**Nota**: Si recibe un mensaje de error relacionado con una ruta de acceso del contenedor de almacenamiento no válida, es posible que la cuenta de almacenamiento no se haya aprovisionado todavía. Espere unos minutos, haga clic en **Cuenta de almacenamiento**, en el panel **Elegir cuenta de almacenamiento** seleccione la cuenta de almacenamiento recién creada y, de nuevo en el panel Auditoría, haga clic en **Guardar**.

10. En el panel del servidor, en la sección **Configuración**, haga clic en **Bases de datos SQL**.

11. En la lista de bases de datos, seleccione la entrada **AZ500LabDb**. 

12. En el panel de la base de datos SQL **az500LabDb**, en la sección **Seguridad**, haga clic en **Auditoría**. 

    >**Nota**: Se trata de la auditoría de nivel de base de datos. La auditoría de nivel de servidor ya está habilitada. 
  
    >**Nota**: Las auditorías se pueden escribir en una cuenta de almacenamiento de Azure, en un área de trabajo de Log Analytics o en el centro de eventos. Puede configurar cualquier combinación de estas opciones.

    >**Nota**: Si se habilita la auditoría basada en almacenamiento en el servidor, siempre se aplicará a la base de datos, independientemente de la configuración de la base de datos.
13. En la página Información general de la base de datos SQL de Azure Portal, seleccione **Editor de consultas (versión preliminar)** en el menú de la izquierda. Intente iniciar sesión, es posible que se produzca un error en la contraseña, la regla de firewall de la dirección IP, todo se audita. Pruebe también el inicio de sesión correcto, ejecute la consulta y podría encontrar más detalles en los registros de auditoría

14. Vuelva a DB, Auditoría y Haga clic en **Ver registros de auditoría**.

15. En el panel **Registros de auditoría**, tenga en cuenta que puede cambiar entre auditoría de servidor y auditoría de base de datos. 

> Resultados: ha creado un servidor y una base de datos SQL, ha configurado la clasificación de datos y la auditoría. 


**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados. 

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, haga clic en **PowerShell** y en **Crear almacenamiento**.

2. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

3. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB11" -Force -AsJob
    ```
4. Cierre el panel de **Cloud Shell**. 
