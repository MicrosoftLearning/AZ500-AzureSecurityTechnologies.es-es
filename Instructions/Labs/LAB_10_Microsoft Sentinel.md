---
lab:
  title: '10: Microsoft Sentinel'
  module: Module 05 - Configure and manage security monitoring and automation solutions
---

# Laboratorio 10: Microsoft Sentinel
# Manual de laboratorio para alumnos

## Escenario del laboratorio

**Nota:****Microsoft Sentinel** ahora se llama **Microsoft Sentinel**. 

Se le ha pedido que cree una prueba de concepto de detección y respuesta de amenazas basadas en Microsoft Sentinel. En concreto, quiere:

- Empiece por recopilar datos de la actividad de Azure y Microsoft Defender for Cloud.
- Agregar alertas integradas y personalizadas 
- Revisar cómo se pueden usar cuadernos de estrategias para automatizar una respuesta a un incidente.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Implementación de Microsoft Sentinel

## Diagrama de Microsoft Sentinel

![imagen](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/509aa70d-de11-4470-a289-877fbfecbc00)

## Instrucciones

## Archivos de laboratorio:

- **\\Allfiles\\Labs\\15\\changeincidentseverity.json**

### Ejercicio 1: Implementación de Microsoft Sentinel

### Tiempo estimado: 30 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Incorporación de Microsoft Sentinel
- Tarea 2: Conexión de actividad de Azure a Sentinel
- Tarea 3: Creación de una regla que use el conector de datos de actividad de Azure. 
- Tarea 4: Creación de un cuaderno de estrategias
- Tarea 5: Creación de una alerta personalizada y configuración del cuaderno de estrategias como una respuesta automatizada.
- Tarea 6: Invocación de un incidente y revisión de las acciones asociadas.

#### Tarea 1: Incorporación de Microsoft Sentinel

En esta tarea, incorporará Microsoft Sentinel y conectará el área de trabajo de Log Analytics. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos**, que encontrará en la parte superior de la página, escriba **Microsoft Sentinel** y presione la tecla **Entrar**.

    >**Nota**: Si este es su primer intento de accionar Microsoft Sentinel en el panel de control de Azure, complete los siguientes pasos: En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos**, que encontrará en la parte superior de la página, escriba **Microsoft Sentinel** y presione la tecla **Entrar**. Seleccione **Microsoft Sentinel** en la vista **Servicios**.
  
3. En el panel **Microsoft Sentinel**, haga clic en **+ Crear**.

4. En el panel **Agregar Microsoft Sentinel a un área de trabajo**, seleccione el área de trabajo de Log Analytics que creó en el laboratorio de Azure Monitor y haga clic en **Agregar**.

    >**Nota**: Microsoft Sentinel tiene unos requisitos muy específicos para las áreas de trabajo. Por ejemplo, no se pueden usar áreas de trabajo creadas por Microsoft Defender for Cloud. Obtén más información en [Inicio rápido: Incorporación de Microsoft Sentinel](https://docs.microsoft.com/en-us/microsoft/sentinel/quickstart-onboard)
    
#### Tarea 2: Configuración de Microsoft Sentinel para usar el conector de datos de actividad de Azure. 

En esta tarea, configurará Sentinel para usar el conector de datos de actividad de Azure.  

1. En Azure Portal, en la hoja **Microsoft Sentinel \| Información general**, en la sección **Administración de contenido**, haga clic en **Centro de contenido**.

2. En la hoja **Microsoft Sentinel \| Centro de contenido**, revise la lista de contenido disponible.

3. Escriba **Azure** en la barra de búsqueda y seleccione la entrada que representa la **Actividad de Azure**. Revise su descripción en el extremo derecho y, después, haga clic en **Instalar**.

4. Espere a que aparezca la notificación **Instalación correcta**. En el panel de navegación izquierdo, en la sección **Configuración**, haga clic en **Conectores de datos**.

5. En la hoja **Microsoft Sentinel \| Conectores de datos**, haga clic en **Actualizar** y revise la lista de conectores disponibles. Seleccione la entrada que representa el conector **Actividad de Azure** (oculte la barra de menú de la izquierda con \<< si es necesario), revise su descripción y estado en el extremo derecho. Después, haga clic en **Abrir página de conector**.

6. En el panel **Actividad de Azure**, debe seleccionar la pestaña **Instrucciones**, fíjese en los **Requisitos previos** y desplácese hacia abajo hasta la **Configuración**. Tome nota de la información que describe la actualización del conector. La suscripción Pase para Azure nunca usó el método de conexión heredado, por lo que puede omitir el paso 1 (el botón **Desconectar todo** estará atenuado) y continuar con el paso 2.

7. En el paso 2 **Conexión de las suscripciones a través de la nueva canalización de configuración de diagnóstico**, revise las instrucciones de "Inicie el asistente para asignaciones de Azure Policy y siga estos pasos" y, a continuación, haga clic en **Inicie el asistente para asignaciones de Azure Policy\>** .

8. En la pestaña **Configurar registros de actividad de Azure para transmitirlos al área de trabajo especificada de Log Analytics** (Asignar página de Policy) **Aspectos básicos**, haga clic en el botón de puntos suspensivos (...) **Ámbito**. En la página **Ámbito**, elija la suscripción Pase para Azure en la lista desplegable de suscripciones y haga clic en el botón **Seleccionar** situado en la parte inferior de la página.

    >**Nota**: *No* elija ningún grupo de recursos

9. Haga clic dos veces en el botón **Siguiente** situado en la parte inferior de la pestaña **Aspectos básicos** para continuar con la pestaña **Parámetros**. En la pestaña **Parámetros**, haga clic en el botón de puntos suspensivos (...) **Área de trabajo principal de Log Analytics**. En la página **Área de trabajo principal de Log Analytics**, asegúrese de que la suscripción Paso para Azure está seleccionada y use la lista desplegable **Áreas de trabajo** para seleccionar el área de trabajo de Log Analytics que usa para Sentinel. Cuando haya terminado, haga clic en el botón **Seleccionar** situado en la parte inferior de la página.

10. Haga clic en el botón **Siguiente** situado en la parte inferior de la pestaña **Parámetros** para continuar con la pestaña **Corrección**. En la pestaña **Corrección**, active la casilla **Creación de una tarea de corrección**. Esto habilitará la opción "Configurar registros de actividad de Azure para transmitir a un área de trabajo de Log Analytics especificada" en la lista desplegable **Directiva para corregir**. En la lista desplegable **Ubicación de identidad asignada por el sistema**, seleccione la región (Este de EE. UU., por ejemplo) que seleccionó anteriormente para el área de trabajo de Log Analytics.

11. Haga clic en el botón **Siguiente** situado en la parte inferior de la pestaña **Corrección** para continuar con la pestaña **Mensaje de no cumplimiento**. Escriba un mensaje de no cumplimiento si lo desea (es opcional) y haga clic en el botón **Revisar y crear** situado en la parte inferior de la pestaña **Mensaje de no cumplimiento**.

12. Haga clic en el botón **Crear**. Debe observar tres mensajes de estado correctos: **Creación correcta de la asignación de directiva, Creación correcta de las asignaciones de roles y Creación correcta de la tarea de corrección**.

    >**Nota**: Puede comprobar el icono de la campana de notificaciones para comprobar que las tres tareas son correctas.

13. Compruebe que el panel **Actividad de Azure** muestra el gráfico **Datos recibidos** (es posible que tenga que actualizar la página del explorador).  

    >**Nota**: El estado puede tardar más de 15 minutos en mostrarse como "Conectado" y el gráfico también en mostrar Datos recibidos.

#### Tarea 3: Creación de una regla que use el conector de datos de actividad de Azure. 

En esta tarea, revisará y creará una regla que usa el conector de datos de actividad de Azure. 

1. En el panel **Microsoft Sentinel \| Configuración**, haga clic en **Análisis**. 

2. En el panel **Microsoft Sentinel \| Análisis**, haga clic en la pestaña **Plantillas de regla**. 

    >**Nota**: Revise los tipos de reglas que puede crear. Cada regla está asociada a un origen de datos específico.

3. En la lista de plantillas de regla, escriba **Sospechoso** en el formulario de la barra de búsqueda y haga clic en la entrada **Suspicious number of resource creation or deployment (Número sospechoso de creación o implementación de recursos)** asociada al origen de datos **Actividad de Azure.** A continuación, en el panel que muestra las propiedades de la plantilla de regla, haga clic en **Crear regla** (desplácese a la derecha de la página si es necesario).

    >**Nota**: Esta regla es de gravedad media. 

4. En la pestaña **General** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, acepte la configuración predeterminada y haga clic en **Siguiente: Establecer lógica de regla >** .

5. En la pestaña **Establecer lógica de regla** de la hoja **Asistente para reglas de análisis - Crear una nueva programada**, acepte la configuración predeterminada y haga clic en **Siguiente: Configuración del incidente (Versión preliminar) >** .

6. En la pestaña **Configuración del incidente** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, acepte la configuración predeterminada y haga clic en **Siguiente: Respuesta automatizada >** . 

    >**Nota**: Aquí es donde puede agregar un cuaderno de estrategias, implementado como una aplicación lógica, a una regla para automatizar la corrección de un problema.

7. En la pestaña **Respuesta automatizada** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, acepte la configuración predeterminada y haga clic en **Siguiente: Revisar y crear >** . 

8. En la pestaña **Revisar y crear** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, haga clic en **Guardar**.

    >**Nota**: Ahora tiene una regla activa.

#### Tarea 4: Creación de un cuaderno de estrategias

En esta tarea, creará un cuaderno de estrategias. Un cuaderno de estrategias de seguridad es una colección de tareas que se pueden Microsoft Sentinel puede invocar en respuesta a una alerta. 

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Implementar una plantilla personalizada** y presione la tecla **Entrar**.

2. En el panel **Implementación personalizada**, haga clic en la opción **Cree su propia plantilla en el editor**.

3. En el panel **Editar plantilla**, haga clic en **Cargar archivo**, busque el archivo **\\Allfiles\\Labs\\15\\changeincidentseverity.json** y haga clic en **Abrir**.

    >**Nota**: Puede encontrar cuadernos de estrategias de muestra en [https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks).

4. En el panel **Editar plantilla**, haga clic en **Guardar**.

5. En el panel **Implementación personalizada**, asegúrese de que las siguientes opciones están configuradas (deje las demás con los valores predeterminados):

    |Configuración|Valor|
    |---|---|
    |Suscripción|nombre de la suscripción de Azure que usa en este laboratorio|
    |Resource group|**AZ500LAB131415**|
    |Location|**(EE. UU.) Este de EE. UU.**|
    |Nombre del cuaderno de estrategias|**Change-Incident-Severity**|
    |Nombre de usuario|dirección de correo electrónico|

6. Haga clic en **Revisar y crear** y, a continuación, en **Crear**.

    >**Nota**: Espere a que la implementación se complete.

7. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Grupos de recursos** y presione la tecla **Entrar**.

8. En el panel **Grupos de recursos**, en la lista de grupos de recursos, haga clic en la entrada **AZ500LAB131415**.

9. En el panel del grupo de recursos **AZ500LAB131415**, en la lista de recursos, haga clic en la entrada que representa la aplicación lógica **Change-Incident-Severity** recién creada.

10. En el panel **Change-Incident-Severity**, haga clic en **Editar**.

    >**Nota**: En la hoja **Diseñador de Logic Apps**, cada una de las cuatro conexiones muestra una advertencia. Esto significa que cada una debe revisarse y configurarse.

11. En la hoja **Diseñador de Logic Apps**, haga clic en el primer paso de **s**.

12. Haga clic en **Agregar nuevo**, asegúrese de que la entrada de la lista desplegable **Inquilino** contiene el nombre de inquilino de Azure AD y haga clic en **Iniciar sesión**.

13. Cuando se le solicite, inicie sesión con una cuenta de usuario que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

14. Haga clic en el segundo paso de **s** y, en la lista de s, seleccione la segunda entrada, que representa la conexión que ha creado en el paso anterior.

15. Repita el paso anterior para los dos pasos de **s** restantes.

    >**Nota**: Asegúrese de que no se muestran advertencias en ninguno de los pasos.

16. En el panel **Diseñador de Logic Apps**, haga clic en **Guardar** para guardar los cambios.

#### Tarea 5: Creación de una alerta personalizada y configuración de un cuaderno de estrategias como una respuesta automatizada

1. En Azure Portal, vuelva al panel **Microsoft Sentinel \| Información general**.

2. En el panel **Microsoft Sentinel \| Información general**, vaya a la sección **Configuración** y haga clic en **Análisis**.

3. En el panel **Microsoft Sentinel \| Análisis**, haga clic en **+ Crear** y, en el menú desplegable, haga clic en **Regla de consulta programada**. 

4. En la pestaña **General** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, configure las siguientes opciones (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |---|---|
    |Nombre|**Demostración del cuaderno de estrategias**|
    |Tácticas|**Acceso inicial**|

5. Haga clic en **Siguiente: Establecer la lógica de la regla >** .

6. En la pestaña **Establecer la lógica de la regla** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, en el cuadro de texto **Consulta de regla**, pegue la siguiente consulta de regla. 

    ```
    AzureActivity
     | where ResourceProviderValue =~ "Microsoft.Security" 
     | where OperationNameValue =~ "Microsoft.Security/locations/jitNetworkAccessPolicies/delete" 
    ```

    >**Nota**: Esta regla identifica la eliminación de directivas de acceso a máquinas virtuales Just-In-Time.

    >**Nota**: Tenga en cuenta que si recibe un error de análisis, IntelliSense podría haber agregado valores a la consulta. Asegúrese de que la consulta coincide; de lo contrario, pegue la consulta en el Bloc de notas y, a continuación, del Bloc de notas a la consulta de regla. 


7. En la pestaña **Establecer la lógica de la regla** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, en la sección **Programación de consultas**, establezca **Ejecutar consulta cada** en **5 minutos**.

8. En la pestaña **Establecer lógica de regla** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, acepte los valores predeterminados de la configuración restante y haga clic en **Siguiente: Configuración del incidente >** .

9. En la pestaña **Configuración del incidente** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, acepte la configuración predeterminada y haga clic en **Siguiente: Respuesta automatizada >** . 

10. En la pestaña **Respuesta automatizada** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada** en **Reglas de automatización**, haga clic en **+ Agregar nueva**.

11. En la ventana **Crear nueva regla de automatización**, escriba **Run Change-Severity Playbook** para el **Nombre de regla de automatización**; en el campo **Desencadenador**, haga clic en el menú desplegable y seleccione **Cuando se crea la alerta**.

12. En la ventana **Crear nueva regla de automatización**, en **Acciones**, lea la nota y, después, haga clic en **Administrar permisos del cuaderno de estrategias**. En la ventana **Administrar permisos**, active la casilla situada junto al **Grupo de recursos creado anteriormente AZ500LAB1314151** y, después, haga clic en **Aplicar**.

13.  En la ventana **Crear nueva regla de automatización**, en **Acciones**, haga clic en el segundo menú desplegable y seleccione la aplicación lógica **Change-Incident-Severity**. En la ventana **Crear nueva regla de automatización**, haga clic en **Aplicar**.

14. En la pestaña **Respuesta automatizada** de la hoja **Asistente para reglas de análisis - Crear una nueva regla programada**, haga clic en **Siguiente: Revisar y crear >** y haga clic en **Guardar**

    >**Nota**: Ahora tiene una nueva regla activa denominada **Demostración del cuaderno de estrategias**. Si se produce un evento identificado por la lógica de la regla, se producirá una alerta de gravedad media, lo que generará un incidente correspondiente.

#### Tarea 6: Invocación de un incidente y revisión de las acciones asociadas.

1. En Azure Portal, vaya al panel **Microsoft Defender for Cloud \| Información general**.

    >**Nota**: Compruebe la puntuación de seguridad. Por ahora debería haberse actualizado. 

2. En la hoja **Microsoft Defender for Cloud \| Información general**, haga clic en **Protección de cargas de trabajo** en **Seguridad en la nube** en el panel de navegación izquierdo.

3. En la hoja **Microsoft Defender for Cloud \| Protecciones de cargas de trabajo**, haga clic en la sección **Acceso a máquinas virtuales Just-In-Time** en **Protección avanzada**.

4. En la hoja **Acceso de máquina virtual Just-in-time**, en el lado derecho de la fila que hace referencia a la máquina virtual **myVM**, haga clic en el botón de **puntos suspensivos (...)**, haga clic en **Quitar** y luego haga clic en **Sí**.

    >**Nota**: Si la máquina virtual no aparece en la lista **Máquinas virtuales Just-In-Time**, vaya al panel **Máquina virtual** y haga clic en **Configuración**. Haga clic en la opción **Habilitar las máquinas virtuales Just-In-Time** de **Acceso de máquina virtual Just-In-Time**. Repita el paso anterior para volver a **Microsoft Defender for Cloud** y actualice la página. Se mostrará la máquina virtual.

5. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Registro de actividad** y presione la tecla **Entrar**.

6. Vaya al panel **Registro de actividad** y anote una entrada **Eliminar directivas de acceso a la red JIT**. 

    >**Nota**: Los datos pueden tardar unos minutos en aparecer. **Actualice** la página si no aparece.

7. En Azure Portal, vuelva al panel **Microsoft Sentinel \| Información general**.

8. En la hoja **Microsoft Sentinel \| Información general**, revise el panel y compruebe que se muestra un incidente correspondiente a la eliminación de la directiva de acceso a las máquinas virtuales Just-In-Time.

    >**Nota**: Las alertas pueden tardar hasta 5 minutos en aparecer en el panel **Microsoft Sentinel\| Información general**. Si no ve una alerta en ese momento, ejecute la regla de consulta a la que se hace referencia en la tarea anterior para comprobar que la actividad de eliminación de la directiva de acceso Just-In-Time se ha propagado al área de trabajo de Log Analytics asociada a la instancia de Microsoft Sentinel. Si no fuera el caso, vuelva a crear la directiva de acceso a las máquinas virtuales Just-In-Time y elimínela de nuevo.

9. En la página **Microsoft Sentinel \| Información general**, vaya a la sección **Administración de amenazas** y haga clic en **Incidentes**.

10. Compruebe que el panel muestra un incidente con un nivel de gravedad medio o alto.

    >**Nota**: El incidente puede tardar hasta 5 minutos en aparecer en el panel **Microsoft Sentinel \| Incidentes**. 

    >**Nota**: Revise el panel **Microsoft Sentinel \| Cuadernos de estrategias**. Allí encontrará el recuento de ejecuciones correctas y con errores.

    >**Nota**: Tiene la opción de asignar un nivel de gravedad y un estado diferentes a un incidente.

> Ha creado un área de trabajo de Microsoft Sentinel, la ha conectado a los registros de actividad de Azure, ha creado un cuaderno de estrategias y alertas personalizadas que se desencadenan como respuesta a la eliminación de directivas de acceso a máquinas virtuales Just-In-Time y ha comprobado que la configuración es válida.

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados. 

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, haga clic en **PowerShell** y en **Crear almacenamiento**.

2. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

3. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB131415" -Force -AsJob
    ```
4. Cierre el panel de **Cloud Shell**.
