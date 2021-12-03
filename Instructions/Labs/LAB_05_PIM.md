---
lab:
  title: 05 - Azure AD Privileged Identity Management
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: 9e0b57125345a57e9f050667c7df18c6b8585ef9
ms.sourcegitcommit: 2eb153f2856445e5afaa218a012cb92e3d48f24b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625712"
---
# <a name="lab-05-azure-ad-privileged-identity-management"></a>Laboratorio 05: Azure AD Privileged Identity Management
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto que use Azure Privileged Identity Management (PIM) para habilitar la administración Just-In-Time y controlar el número de usuarios que pueden realizar operaciones con privilegios. Los requisitos específicos son los siguientes:

- Crear una asignación permanente del usuario aaduser2 de Azure AD al rol Administrador de seguridad. 
- Configurar el usuario aaduser2 de Azure AD para que sea apto para los roles Administrador de facturación y Lector global.
- Configurar la activación del rol Lector global para que requiera una aprobación del usuario aaduser3 de Azure AD.
- Configurar una revisión de acceso del rol Lector global y revisar las funcionalidades de auditoría.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

> Antes de continuar, asegúrese de que ha completado el laboratorio 04: MFA, acceso condicional y AAD Identity Protection. Necesitará el inquilino de Azure AD, AdatumLab500-04, y las cuentas de usuario aaduser1, aaduser2 y aaduser3.

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Configurar usuarios y roles de PIM.
- Ejercicio 2: Activar los roles de PIM con y sin aprobación.
- Ejercicio 3: Crear una revisión de acceso y revisar las características de auditoría de PIM.

### <a name="exercise-1---configure-pim-users-and-roles"></a>Ejercicio 1: Configurar usuarios y roles de PIM

#### <a name="estimated-timing-15-minutes"></a>Tiempo estimado: 15 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Hacer que un usuario sea apto para un rol.
- Tarea 2: Configurar un rol para que requiera aprobación para la activación y adición de un miembro apto.
- Tarea 3: Proporcionar a un usuario la asignación permanente a un rol. 

#### <a name="task-1-make-a-user-eligible-for-a-role"></a>Tarea 1: Hacer que un usuario sea apto para un rol

En esta tarea, hará que un usuario sea apto para un rol de directorio de Azure AD.

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota:** Asegúrese de que ha iniciado sesión en el inquilino **AdatumLab500-04** de Azure AD. Puede usar el filtro **Directorio + suscripción** para cambiar entre inquilinos de Azure AD. Asegúrese de que ha iniciado sesión como usuario con el rol Administrador global.
    
    >**Nota**: Si todavía no ve la entrada AdatumLab500-04, haga clic en el vínculo Cambiar directorio, seleccione la línea AdatumLab500-04 y haga clic en el botón Cambiar.

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** de la parte superior de la página de Azure Portal, escriba **Azure AD Privileged Identity Management** y presione la tecla **Entrar**.

1. En la hoja **Azure AD Privileged Identity Management**, en la sección **Administrar**, haga clic **Roles de Azure AD**.

1. En la hoja **AdatumLab500-04 \| Inicio rápido**, en la sección **Administrar**, haga clic en **Roles**.

1. En la hoja **AdatumLab500-04 \| Roles**, haga clic en **+ Agregar asignaciones**.

1. En la hoja **Agregar asignaciones**, en la lista desplegable **Seleccionar rol**, seleccione **Administrador de facturación**.

1. Haga clic en el vínculo **No se ha seleccionado ningún miembro**, en la hoja **Seleccionar un miembro**, haga clic en **aaduser2** y, a continuación, en **Seleccionar**.

1. De nuevo en la hoja **Agregar asignaciones**, haga clic en **Siguiente**. 

1. Asegúrese de que **Tipo de asignación** está establecido en **Apto** y haga clic en **Asignar**.
 
1. De nuevo en la hoja **AdatumLab500-04 \| Roles**, en la sección **Administrar**, haga clic en **Asignaciones**.

1. De nuevo en la hoja **AdatumLab500-04 \| Asignaciones**, observe las pestañas **Eligible assignments** (Asignaciones aptas), **Asignaciones activas** y **Expired assignments** (Asignaciones expiradas).

1. Compruebe en la pestaña **Eligible assignments** (Asignaciones aptas) que **aaduser2** se muestra como un **Administrador de facturación**. 

    >**Nota**: Durante el inicio de sesión, el usuario aaduser2 podrá usar el rol Administrador de facturación. 

#### <a name="task-2-configure-a-role-to-require-approval-to-activate-and-add-an-eligible-member"></a>Tarea 2: Configurar un rol para que requiera aprobación para la activación y adición de un miembro apto

1. En Azure Portal, vuelva a la hoja **Azure AD Privileged Identity Management** y haga clic **Roles de Azure AD**.

1. En la hoja **AdatumLab500-04 \| Inicio rápido**, en la sección **Administrar**, haga clic en **Roles**.

1. En la hoja **AdatumLab500-04 \| Roles**, haga clic en la entrada del rol **Lector global**. 

1. En la hoja **Lector global \| Asignaciones**, haga clic en el icono **Configuración** en la barra de herramientas de la hoja y revise los valores de configuración del rol, incluidos los requisitos de Azure Multi-Factor Authentication.

1. Haga clic en **Editar**.

1. En la pestaña **Activación**, active la casilla **Se requiere aprobación para activar.**

1. Haga clic en **Seleccionar aprobadores** y, en la hoja **Seleccionar un miembro**, haga clic en **aaduser3** y, a continuación, en **Seleccionar**.

1. Haga clic en **Siguiente: asignación**.

1. Desactive la casilla **Permitir asignación elegible permanente** y deje todas las demás opciones con sus valores predeterminados.

1. Haga clic en **Siguiente: notificación**.

1. En la hoja **Editar configuración de rol - Lector global**, revise la configuración y haga clic en **Actualizar**.

    >**Nota**: Cualquier persona que intente usar el rol Lector global ahora necesitará la aprobación del usuario aaduser3. 

1. En la hoja **Lector global \| Asignaciones**, haga clic en **+ Agregar asignaciones**.

1. En la hoja **Agregar asignaciones**, haga clic en **No se ha seleccionado ningún miembro** y, en la hoja **Seleccionar un miembro**, haga clic en **aaduser2** y, a continuación, en **Seleccionar**.

1. Haga clic en **Siguiente**. 

1. Asegúrese de que el valor de **Tipo de asignación** sea **Apto** y revise la configuración de duración apta.

1. Haga clic en **Asignar**.

    >**Nota**: El usuario aaduser2 es apto para el rol Lector global. 
 
#### <a name="task-3-give-a-user-permanent-assignment-to-a-role"></a>Tarea 3: Proporcionar a un usuario la asignación permanente a un rol.

1. En Azure Portal, vuelva a la hoja **Azure AD Privileged Identity Management** y haga clic **Roles de Azure AD**.

1. En la hoja **AdatumLab500-04 \| Inicio rápido**, en la sección **Administrar**, haga clic en **Roles**.

1. En la hoja **AdatumLab500-04 \| Roles**, haga clic en **+ Agregar asignaciones**.

1. En la hoja **Agregar asignaciones**, en la lista desplegable **Seleccionar rol**, seleccione **Administrador de seguridad**.

1. En la hoja **Agregar asignaciones**, haga clic en **No se ha seleccionado ningún miembro** y, en la hoja **Seleccionar un miembro**, haga clic en **aaduser2** y, a continuación, en **Seleccionar**.

1. Haga clic en **Siguiente**. 

1. Revise la configuración de **Tipo de asignación** y haga clic en **Asignar**.

    >**Nota**: El usuario aaduser2 ahora es apto de forma permanente para el rol Administrador de seguridad.
    
### <a name="exercise-2---activate-pim-roles-with-and-without-approval"></a>Ejercicio 2: Activar los roles de PIM con y sin aprobación

#### <a name="estimated-timing-15-minutes"></a>Tiempo estimado: 15 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Activar un rol que no requiere aprobación. 
- Tarea 2: Activar un rol que requiere aprobación. 

#### <a name="task-1-activate-a-role-that-does-not-require-approval"></a>Tarea 1: Activar un rol que no requiere aprobación.

En esta tarea, activará un rol que no requiere aprobación.

1. Abra una ventana del explorador de InPrivate.

1. En la ventana del explorador InPrivate, vaya a Azure Portal e inicie sesión con la cuenta de usuario **aaduser2**.

    >**Nota**: Para iniciar sesión, deberá proporcionar un nombre completo de la cuenta de usuario **aaduser2**, incluido el nombre de dominio DNS del inquilino de Azure AD que registró anteriormente en este laboratorio. Este nombre de usuario tiene el formato aaduser2@`<your_tenant_name>`.onmicrosoft.com, donde `<your_tenant_name>` es el marcador de posición que representa el nombre único del inquilino de Azure AD. 

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** de la parte superior de la página de Azure Portal, escriba **Azure AD Privileged Identity Management** y presione la tecla **Entrar**.

1. En la hoja **Azure AD Privileged Identity Management**, en la sección **Tareas**, haga clic en **Mis roles**.

1. Debería ver tres **roles aptos** para **aaduser2**: **Lector global**, **Administrador de seguridad** y **Administrador de facturación**. 

1. En la fila que muestra la entrada de rol **Administrador de facturación**, haga clic en **Activar**.

1. Si es necesario, haga clic en la advertencia **Comprobación adicional necesaria. Haga clic para continuar** y siga las instrucciones para comprobar su identidad.

1. En la hoja **Activar - Administrador de facturación**, en el cuadro de texto **Motivo**, escriba un texto que proporcione una justificación para la activación y, a continuación, haga clic en **Activar**.

    >**Nota**: Al activar un rol en PIM, la activación puede tardar hasta 10 minutos en surtir efecto. 
    
    >**Nota**: Cuando la asignación de roles esté activa, el explorador se actualizará (si surge algún problema, simplemente cierre la sesión y vuelva a iniciar sesión en Azure Portal mediante la cuenta de usuario **aaduser2**).

1. Vuelva a la hoja **Azure AD Privileged Identity Management** y, en la sección **Tareas**, haga clic en **Mis roles**.

1. En la hoja **Mis roles \| Roles de Azure AD**, cambie a la pestaña **Asignaciones activas**. Observe que el estado del rol **Administrador de facturación** es **Activado**.

    >**Nota**: Cuando se activa un rol, si se alcanza el límite de tiempo de **Hora final** (duración elegible), este se desactiva automáticamente.

    >**Nota**: Si completa las tareas de administrador con antelación, puede desactivar un rol manualmente.

1.  En la lista **Asignaciones activas**, en la fila que representa el rol Administrador de facturación, haga clic en el vínculo **Desactivar**.

1.  En la hoja **Desactivar - Administrador de facturación**, haga clic en **Desactivar** de nuevo para confirmar la operación.


#### <a name="task-2-activate-a-role-that-requires-approval"></a>Tarea 2: Activar un rol que requiere aprobación. 

En esta tarea, activará un rol que requiere aprobación.

1. En la ventana del explorador InPrivate, en Azure Portal, con la sesión iniciada como **aaduser2**, vuelva a la hoja **Privileged Identity Management \| Inicio rápido**. 

1. En la hoja **Privileged Identity Management \| Inicio rápido**, en la sección **Administrar**, haga clic en **Mis roles**.

1. En la hoja **Mis roles \| Roles de Azure AD**, diríjase a la lista **Eligible assignments** (Asignaciones aptas) y, en la fila que muestra el rol **Lector global**, haga clic en **Activar**. 

1. En la hoja **Activar - Lector global**, en el cuadro de texto **Motivo**, escriba un texto que proporcione una justificación para la activación y, a continuación, haga clic en **Activar**.

1. Haga clic el icono **Notificaciones** de la barra de herramientas de Azure Portal y observe la notificación que indica que la solicitud está pendiente de aprobación.

    >**Nota**: Como Administrador de roles con privilegios puede revisar y cancelar solicitudes en cualquier momento. 

1. En la hoja **Mis roles \| Azure AD**, busque el rol **Administrador de seguridad** y haga clic en **Activar**. 

1. Haga clic en la advertencia **Se requiere una verificación adicional. Haga clic para continuar.** 

1. Siga las instrucciones para comprobar su identidad.

    >**Nota**: Solo tiene que autenticarse una vez por sesión. 

1. Cuando vuelva a la interfaz de Azure Portal, en la hoja **Activar - Administrador de seguridad**, en el cuadro de texto **Motivo**, escriba un texto que proporcione una justificación para la activación y, a continuación, haga clic en **Activar**.

    >**Nota**: El proceso de aprobación automática debería completarse.

1. De nuevo en la hoja **Mis roles Azure AD \| Roles de Azure AD**, haga clic en la pestaña **Asignaciones activas** y observe que la lista de **asignaciones activas** incluye el rol **Administrador de seguridad**, pero no el de **Lector global**.

    >**Nota**: Ahora aprobará el rol Lector global.

1. Cierre la sesión de **aaduser2** de Azure Portal.

1. Inicie sesión en Azure Portal como **aaduser3**.

    >**Nota**: Si tiene problemas con la autenticación mediante cualquiera de las cuentas de usuario, puede iniciar sesión en el inquilino de Azure AD con su cuenta de usuario para restablecer sus contraseñas o volver a configurar sus opciones de inicio de sesión.

1. En Azure Portal, desplácese a **Azure AD Privileged Identity Management**.

1. En la hoja **Privileged Identity Management \| Inicio rápido**, en la sección **Tareas**, haga clic en **Aprobar solicitudes**.

1. En la hoja **Aprobar solicitudes \| Roles de Azure AD**, en la sección **Solicitudes de activación de roles**, active la casilla de la entrada que representa la solicitud de activación de rol para el rol **Lector global** con **aaduser2**.

1. Haga clic en **Approve** (Aprobar). En la hoja **Aprobar solicitud**. en el cuadro de texto **Justificación**, escriba un motivo para la activación, anote las horas de inicio y finalización y, a continuación, haga clic en **Confirmar**. 

    >**Nota**: También tiene la opción de denegar solicitudes.

1. Cierre la sesión de **aaduser3** de Azure Portal.

1. Inicie sesión en Azure Portal como **aaduser2**.

1. En Azure Portal, desplácese a **Azure AD Privileged Identity Management**.

1. En la hoja **Privileged Identity Management \| Inicio rápido**, en la sección **Tareas**, haga clic en **Mis roles**.

1. En la hoja **Mis roles \| Roles de Azure AD**, haga clic en la pestaña **Asignaciones activas** y compruebe que el rol Lector global ahora está activo.

    >**Nota**: Es posible que tenga que actualizar la página para ver la lista actualizada de asignaciones activas.

1. Cierre la sesión y cierre la ventana del explorador InPrivate.

> Resultado: ha practicado la activación de roles de PIM con y sin aprobación. 

### <a name="exercise-3---create-an-access-review-and-review-pim-auditing-features"></a>Ejercicio 3: Crear una revisión de acceso y revisar las características de auditoría de PIM

#### <a name="estimated-timing-10-minutes"></a>Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Configurar alertas de seguridad para roles de directorio de Azure AD en PIM
- Tarea 2: Revisar las alertas de PIM, la información de resumen y la información de auditoría detallada

#### <a name="task-1-configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Tarea 1: Configurar alertas de seguridad para roles de directorio de Azure AD en PIM

En esta tarea, reducirá los riesgos asociados a las asignaciones de roles obsoletas. Para ello, creará una revisión de acceso de PIM para asegurarse de que los roles asignados siguen siendo válidos. En concreto, revisará el rol Lector global. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** con su cuenta.

    >**Nota:** Asegúrese de que ha iniciado sesión en el inquilino **AdatumLab500-04** de Azure AD. Puede usar el filtro **Directorio + suscripción** para cambiar entre inquilinos de Azure AD. Asegúrese de que ha iniciado sesión como usuario con el rol Administrador global.
    
    >**Nota**: Si todavía no ve la entrada AdatumLab500-04, haga clic en el vínculo Cambiar directorio, seleccione la línea AdatumLab500-04 y haga clic en el botón Cambiar.

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** de la parte superior de la página de Azure Portal, escriba **Azure AD Privileged Identity Management** y presione la tecla **Entrar**.

1. Desplácese a la hoja **Azure AD Privileged Identity Management**. 

1. En la hoja **Privileged Identity Management \| Inicio rápido**, en la sección **Tareas**, haga clic **Roles de Azure AD**.

1. En la hoja **AdatumLab500-04 \| Inicio rápido**, en la sección **Administrar**, haga clic en **Revisiones de acceso**.

1. En la hoja **AdatumLab500-04 \| Revisiones de acceso**, haga clic en **Nuevo**:

1. En la hoja **Crear una revisión de acceso**, especifique las opciones de configuración siguientes (deje las demás con los valores predeterminados): 

   |Configuración|Value|
   |---|---|
   |Nombre de la revisión|**Revisión del lector global**|
   |Fecha de inicio|fecha de hoy| 
   |Frecuencia|**Una vez**|
   |Fecha de finalización|fin del mes actual|
   |Pertenencia a rol de revisión|**Lector global**|
   |Revisores|**Usuarios seleccionados**|
   |Selección de los revisores|su cuenta|

1. En la hoja **Crear una revisión de acceso**, haga clic en **Iniciar**:
 
    >**Nota**: La revisión puede tardar aproximadamente un minuto en implementarse y aparecer en la hoja **AdatumLab500-04 \| Revisiones de acceso**. Es posible que tenga que actualizar la página web. El estado de la revisión será **Activo**. 

1. En la hoja **AdatumLab500-04 \| Revisiones de acceso**, en el encabezado **Global Reader Review** (Revisión del lector global), haga clic en la entrada **Lector global**. 

1. En la hoja **Global Reader Review** (Revisión del lector global), examine la página **Información general** y observe que los gráficos **Progreso** muestran un único usuario en la categoría **Sin revisar**. 

1. En la hoja **Global Reader Review** (Revisión del lector global), en la sección **Administrar**, haga clic en **Resultados**. Observe que aaduser2 aparece como un usuario con acceso a este rol.

1. Haga clic en **Ver** en la línea **aaduser2** para ver un registro de auditoría detallado con entradas que representan las actividades de PIM que implican a ese usuario.

1. Vuelva a la hoja **AdatumLab500-04 \| Revisiones de acceso**.

1. En la hoja **AdatumLab500-04 \| Revisiones de acceso**, en la sección **Tareas**, haga clic en **Revisar acceso** y, a continuación, haga clic en la entrada **Global Reader Review** (Revisión del lector global). 

1. En la hoja **Global Reader Review** (Revisión del lector global), haga clic en la entrada **aaduser2**. 

1. En el cuadro de texto **Motivo**, escriba una justificación para la aprobación y, a continuación, haga clic en **Aprobar** para mantener la pertenencia al rol actual o en **Denegar** para revocarla. 

1. Vuelva a la hoja **Azure AD Privileged Identity Management** y, en la sección **Administrar**, haga clic en **Roles de Azure AD**.

1. En la hoja **AdatumLab500-04 \| Inicio rápido**, en la sección **Administrar**, haga clic en **Revisiones de acceso**.

1. Seleccione la entrada que representa la revisión del **Lector global**. Observe que el gráfico **Progreso** se ha actualizado para mostrar la revisión. 

#### <a name="task-2-review-pim-alerts-summary-information-and-detailed-audit-information"></a>Tarea 2: Revisar las alertas de PIM, la información de resumen y la información de auditoría detallada. 

En esta tarea, revisará las alertas de PIM, la información de resumen y la información de auditoría detallada. 

1. Vuelva a la hoja **Azure AD Privileged Identity Management** y, en la sección **Administrar**, haga clic en **Roles de Azure AD**.

1. En la hoja **AdatumLab500-04 \| Inicio rápido** , en la sección **Administrar**, haga clic en **Alertas** y, a continuación, haga clic **Configuración**.

1. En la hoja **Configuración de alertas**, revise las alertas preconfiguradas y los niveles de riesgo. Haga clic en cualquiera de ellos para obtener información más detallada. 

1. Vuelva a la hoja **AdatumLab500-04 \| Inicio rápido** y haga clic en **Información general**. 

1. En la hoja **AdatumLab500-04 \| Información general**, revise la información de resumen sobre las activaciones de roles, las actividades de PIM, las alertas y las asignaciones de roles.

1. En la hoja **AdatumLab500-04 \| Información general**, en la sección **Actividad**, haga clic en **Auditoría de recursos**. 

    >**Nota**: El historial de auditoría está disponible para todas las activaciones y asignaciones de roles con privilegios de los últimos 30 días.

1. Tenga en cuenta que puede recuperar información detallada, como **Hora**, **Solicitante**, **Acción**, **Nombre del recurso**, **Ámbito**, **Destino principal** y **Asunto**. 

> Resultado: ha configurado una revisión de acceso y ha revisado la información de auditoría. 

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados. 

1. En Azure Portal, establezca el filtro **Directorio + suscripción** en el inquilino de Azure AD asociado a la suscripción de Azure en la que implementó la VM de Azure **az500-04-vm1**.

>**Nota**: Si no ve la entrada del inquilino principal de Azure AD, haga clic en el vínculo Cambiar directorio, seleccione la línea del inquilino principal y haga clic en el botón Cambiar.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, haga clic en **PowerShell** y en **Crear almacenamiento**.

1. Asegúrese de que **PowerShell** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

1. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar el grupo de recursos que creó en el laboratorio anterior:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB04" -Force -AsJob
    ```

1.  Cierre el panel de **Cloud Shell**. 

1. De nuevo en Azure Portal, use el filtro **Directorio + suscripción** para cambiar al inquilino **AdatumLab500-04** de Azure Active Directory.

1. Vaya a la hoja **Azure Active Directory Premium P2 - Usuarios con licencia**, seleccione las cuentas de usuario a las que asignó licencias, haga clic en **Quitar licencia** y, cuando se le pida confirmación, seleccione **Sí**.

1. En Azure Portal, vaya a la hoja **Usuarios - Todos los usuarios**, haga clic en la entrada que representa la cuenta de usuario **aaduser1**. A continuación, en la hoja **aaduser1: Perfil**, haga clic en **Eliminar** y, cuando se le pida confirmación, haga clic en **Sí**.

1. Repita la misma secuencia de pasos para eliminar las cuentas de usuario restantes que creó.

1. Vaya a la hoja **AdatumLab500-04 - Información general** del inquilino de Azure AD, haga clic en **Manage tenant** (Administrar inquilino) y, a continuación, en la siguiente pantalla, haga clic en **Eliminar inquilino**, en la hoja **Eliminar directorio "AdatumLab500-04"** , en el vínculo **Obtener permiso para eliminar los recursos de Azure**, en la hoja **Propiedades** de Azure Active Directory, establezca **Administración del acceso para los recursos de Azure** en **Sí** y haga clic en **Guardar**.

1. Cierre la sesión de Azure Portal y vuelva a iniciarla. 

1. Vuelva a la hoja **Delete directory 'AdatumLab500-04'** (Eliminar directorio 'AdatumLab500-04') y haga clic en **Eliminar**.

> Para obtener información adicional sobre esta tarea, consulte [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
