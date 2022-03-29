---
lab:
  title: 04 - MFA, acceso condicional y AAD Identity Protection
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: c4b21d80083316c681f5916c5fe75973220695d1
ms.sourcegitcommit: a8470295248a6363987bd5ea47154fe39f8535c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2022
ms.locfileid: "139703525"
---
# <a name="lab-04-mfa-conditional-access-and-aad-identity-protection"></a>Laboratorio 04: MFA, acceso condicional y AAD Identity Protection
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto de características que mejoren la autenticación Azure Active Directory (Azure AD). En concreto, quiere evaluar lo siguiente:

- Azure AD Multi-Factor Authentication
- Acceso condicional de Azure AD
- Azure AD Identity Protection

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Implementar VM de Azure mediante una plantilla de Azure Resource Manager
- Ejercicio 2: Implementar Azure MFA
- Ejercicio 3: Implementar directivas de acceso condicional de Azure AD 
- Ejercicio 4: Implementar Azure AD Identity Protection

## <a name="mfa---conditional-access---identity-protection-diagram"></a>MFA - Acceso condicional - Identity Protection

![imagen](https://user-images.githubusercontent.com/91347931/157518628-8b4a9efe-0086-4ec0-825e-3d062748fa63.png)

## <a name="instructions"></a>Instrucciones

## <a name="lab-files"></a>Archivos de laboratorio:

- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json**
- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json** 

### <a name="exercise-1-deploy-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Ejercicio 1: Implementar VM de Azure mediante una plantilla de Azure Resource Manager

### <a name="estimated-timing-10-minutes"></a>Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Implementar una VM de Azure mediante una plantilla de Azure Resource Manager

#### <a name="task-1-deploy-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Tarea 1: Implementar una VM de Azure mediante una plantilla de Azure Resource Manager

En esta tarea, creará una máquina virtual mediante una plantilla de ARM. Esta máquina virtual se usará en el último ejercicio de este laboratorio. 

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio y el rol Administrador global en el inquilino de Azure AD asociado a esa suscripción.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** y, en la parte superior de la página, escriba **Implementar una plantilla personalizada**.

    >**Nota**: También puede seleccionar **Template Deployment (implementar mediante plantillas personalizadas)** en la lista **Marketplace**.

3. En la hoja **Implementación personalizada**, haga clic en la opción **Cree su propia plantilla en el editor**.

4. En la hoja **Editar plantilla**, haga clic en **Cargar archivo**, busque el archivo **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json** y haga clic en **Abrir**.

    >**Nota**: Revise el contenido de la plantilla y observe que implementa una VM de Azure que hospeda Windows Server 2019 Datacenter.

5. En la hoja **Editar plantilla**, haga clic en **Guardar**.

6. De nuevo en la hoja **Implementación personalizada**, haga clic en **Editar parámetros**.

7. En la hoja **Editar parámetros**, haga clic en **Cargar archivo**, busque el archivo **\\ Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json** y haga clic en **Abrir**.

    >**Nota**: Revise el contenido del archivo de parámetros y observe los valores de adminUsername y adminPassword.

8. En la hoja **Editar parámetros**, haga clic en **Guardar**.

9. En el panel **Implementación personalizada**, asegúrese de que las siguientes opciones están configuradas (deje las demás con los valores predeterminados):

   |Configuración|Value|
   |---|---|
   |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
   |Resource group|Haga clic en **Crear nuevo** y escriba el nombre **AZ500LAB04**.|
   |Location|**(EE. UU.) Este de EE. UU.**|
   |Tamaño de máquina virtual:|**Standard_D2s_v3**|
   |Nombre de la máquina virtual|**az500-04-vm1**|
   |Nombre de usuario administrador|**Estudiante**|
   |Contraseña de administrador|**Pa55w.rd1234**|
   |El nombre de la red virtual|**az500-04-vnet1**|

    >**Nota**: Para identificar las regiones de Azure donde puede aprovisionar VM de Azure, consulte [ **https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

10. Haga clic en **Revisar y crear** y después en **Crear**.

    >**Nota**: No espere a que se completen la implementación, sino que avance al siguiente ejercicio. Usará la máquina virtual incluida en esta implementación en el último ejercicio de este laboratorio.

> Resultado: ha iniciado una implementación de plantilla de una VM de Azure **az500-04-vm1** que usará en el último ejercicio de este laboratorio.


### <a name="exercise-2-implement-azure-mfa"></a>Ejercicio 2: Implementar Azure MFA

### <a name="estimated-timing-30-minutes"></a>Tiempo estimado: 30 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crear un nuevo inquilino de Azure AD.
- Tarea 2: Activar la prueba de Azure AD Premium P2.
- Tarea 3: Crear usuarios y grupos de Azure AD.
- Tarea 4: Asignar licencias de Azure AD Premium P2 a usuarios de Azure AD.
- Tarea 5: Configurar Azure MFA.
- Tarea 6: Validar la configuración de MFA.

#### <a name="task-1-create-a-new-azure-ad-tenant"></a>Tarea 1: Crear un nuevo inquilino de Azure AD

En esta tarea, creará un nuevo inquilino de Azure AD. 

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Azure Active Directory** y presione la tecla **Entrar**.

2. En el panel **Información general** del inquilino de Azure AD actual, haga clic en **Administrar inquilinos** y, a continuación, en la siguiente pantalla, haga clic en **+ Crear**.

3. En la pestaña **Aspectos básicos** de la hoja **Creación de un inquilino**, asegúrese de que la opción **Azure Active Directory** está seleccionada y haga clic en **Siguiente: Configuración >** .

4. En la pestaña **Configuración** de la hoja **Creación de un inquilino**, especifique las siguientes opciones:

   |Configuración|Valor|
   |---|---|
   |Nombre de la organización|**AdatumLab500-04**|
   |Nombre de dominio inicial|Nombre único que consta de una combinación de letras y dígitos|
   |País o región|**Estados Unidos**|

    >**Nota**: Registre el nombre de dominio inicial. Lo necesitará más adelante en este laboratorio.

5. Haga clic en **Revisar + crear** y, después, en **Crear**.
6. Agregue el código captcha en la hoja **Help us prove you're not a robot** (Ayúdenos a demostrar que no es un robot) y, a continuación, haga clic en el botón **Enviar**. 

    >**Nota**: Espere a que se cree el inquilino. Utilice el icono **Notificación** para supervisar el estado de la implementación. 


#### <a name="task-2-activate-azure-ad-premium-p2-trial"></a>Tarea 2: Activar la prueba de Azure AD Premium P2

En esta tarea, se registrará para la prueba gratuita de Azure AD Premium P2. 

1. En Azure Portal, en la barra de herramientas, haga clic en el icono **Directorio + suscripción**, situado a la derecha del icono de Cloud Shell. 

2. En el panel **Directorio + suscripción**, haga clic en el inquilino recién creado **AdatumLab500-04** y en el botón **Cambiar** para establecerlo como el directorio actual.

    >**Nota**: Es posible que tenga que actualizar la ventana del explorador si la entrada **AdatumLab500-04** no aparece en la lista de filtros **Directorio + suscripción**.

3. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Azure Active Directory** y presione la tecla **Entrar**. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Licencias**.

4. En la hoja **Licencias \| Información general**, en la sección **Administrar**, haga clic en **Todos los productos** y, a continuación, en **+ Probar o comprar**.

5. En la hoja **Activar**, en la sección Azure AD Premium P2, haga clic en **Evaluación gratuita** y, a continuación, en **Activar**.


#### <a name="task-3-create-azure-ad-users-and-groups"></a>Tarea 3: Crear usuarios y grupos de Azure AD.

En esta tarea, creará tres usuarios: aaduser1 (administrador global), aaduser2 (usuario) y aaduser3 (usuario). Necesitará el nombre principal y la contraseña de cada usuario para tareas posteriores. 

1. Vuelva a la hoja **AdatumLab500-04**  Azure Active Directory y, en la sección **Administrar**, haga clic en **Usuarios**.

2. En el panel **Usuarios\| Todos los usuarios (versión preliminar)** , haga clic en **+ Nuevo usuario**. 

3. En la hoja **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada y, a continuación, especifique la siguiente configuración (deje la otra configuración con sus valores predeterminados) y haga clic en **Crear**:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**aaduser1**|
   |Name|**aaduser1**|
   |Contraseña|Asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada y haga clic en **Mostrar contraseña**.|
   |Grupos|**0 grupos seleccionados**|
   |Roles|Haga clic en **Usuario** y, a continuación, en **Administrador global** y en **Seleccionar**.|
   |Ubicación de uso|**Estados Unidos**|  

    >**Nota**: Registre el nombre de usuario completo. Para copiar su valor, haga clic en el botón **Copiar en el Portapapeles** del lado derecho de la lista desplegable que muestra el nombre de dominio. 

    >**Nota**: Registre la contraseña del usuario. La necesitará más adelante en este laboratorio. 

4. De nuevo en el panel **Usuarios \| Todos los usuarios (versión preliminar)** , haga clic en **+ Nuevo usuario**. 

5. En la hoja **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada y, a continuación, especifique la siguiente configuración (deje la otra configuración con sus valores predeterminados):

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**aaduser2**|
   |Name|**aaduser2**|
   |Contraseña|Asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada y haga clic en **Mostrar contraseña**.|
   |Grupos|**0 grupos seleccionados**|
   |Roles|**User**|
   |Ubicación de uso|**Estados Unidos**|  

    >**Nota**: Registre el nombre de usuario completo y la contraseña.

6. De nuevo en el panel **Usuarios \| Todos los usuarios (versión preliminar)** , haga clic en **+ Nuevo usuario**. 

7. Haga clic en **Nuevo usuario**, complete las nuevas opciones de configuración de usuario y, a continuación, haga clic en **Crear**.

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**aaduser3**|
   |Name|**aaduser3**|
   |Contraseña|Asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada y haga clic en **Mostrar contraseña**.|
   |Grupos|**0 grupos seleccionados**|
   |Roles|**User**|
   |Ubicación de uso|**Estados Unidos**|  

    >**Nota**: Registre el nombre de usuario completo y la contraseña.

    >**Nota**: En este momento, debería tener tres nuevos usuarios en la página **Usuarios**. 
    
#### <a name="task-4-assign-azure-ad-premium-p2-licenses-to-azure-ad-users"></a>Tarea 4: Asignar licencias de Azure AD Premium P2 a usuarios de Azure AD

En esta tarea, asignará cada usuario a la licencia de Azure Active Directory Premium P2.

1. En el panel **Usuarios \| Todos los usuarios (versión preliminar)** , haga clic en la entrada que represente su cuenta de usuario. 

2. En la hoja que muestra las propiedades de la cuenta de usuario, haga clic en **Editar**.  Compruebe que Ubicación de uso esté establecido en **Estados Unidos** si no establece la ubicación de uso y haga clic en **Guardar**.

3. Vuelva a la hoja **AdatumLab500-04**  Azure Active Directory y, en la sección **Administrar**, haga clic en **Licencias**.

4. En la hoja **Licencias \| Información general**, haga clic en **Todos los productos**, active la casilla **Azure Active Directory Premium P2** y haga clic en **+ Asignar**.

5. En la hoja **Asignar licencias** haga clic en **+ Agregar usuarios y grupos**.

6. En la hoja **Usuarios**, seleccione **aaduser1**, **aaduser2**, **aaduser3** y su cuenta de usuario, y haga clic en **Seleccionar**.

7. De nuevo en hoja **Asignar licencias**, haga clic en **Opciones de asignación**, asegúrese de que todas las opciones están habilitadas, haga clic en **Review + assign** (Revisar y asignar) y luego en **Asignar**.

8. Cierre la sesión de Azure Portal y vuelva a iniciarla con la misma cuenta. Este paso es necesario para que la asignación de licencias surta efecto.

    >**Nota**: En este momento, ha asignado licencias de Azure Active Directory Premium P2 a todas las cuentas de usuario que va a usar en este laboratorio. Asegúrese de cerrar la sesión y, a continuación, de volver a iniciarla. 

#### <a name="task-5-configure-azure-mfa-settings"></a>Tarea 5: Configurar Azure MFA.

En esta tarea, configurará MFA y habilitará MFA para aaduser1. 

1. En Azure Portal, vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory.

    >**Nota**: Asegúrese de que usa el inquilino AdatumLab500-04 de Azure AD.

2. En la hoja **AdatumLab500-04** del inquilino de Azure Active Directory, en la sección **Administrar**, haga clic en **Seguridad**.

3. En la hoja **Seguridad \| Introducción**, en la sección **Administrar**, haga clic en **MFA**.

4. En la hoja **Multi-Factor Authentication \| Introducción**, haga clic en el vínculo **Configuración adicional de MFA basado en la nube**. 

    >**Nota**: Se abrirá una nueva pestaña del explorador, que muestra la página **Multi-Factor Authentication**.

5. En la página **Multi-Factor Authentication**, haga clic en la **Configuración del servicio**. Revise las **opciones de comprobación**. Observe que las opciones **Mensaje de texto a teléfono**, **Notificación a través de aplicación móvil** y **de hardware** están habilitadas. Haga clic en **Guardar** y, a continuación, en **Cerrar**.

6. Cambie a la pestaña **usuarios**, haga clic en la entrada **aaduser1**, en el vínculo **Habilitar** y, cuando se le solicite, en **Habilitar Multi-Factor Auth**.

7. Observe que el valor de la columna **Estado de Multi-Factor Auth** para **aaduser1** ahora es **Habilitado**.

8. Haga clic en **aaduser1** y observe que, en este momento, también tiene la opción **Aplicar**. 

    >**Nota**: El cambio del estado de usuario de Habilitado a Aplicado solo afecta a las aplicaciones integradas de Azure AD heredadas que no admiten Azure MFA y, una vez que el estado cambia a Aplicado, requieren el uso de contraseñas de aplicación.

9. Con la entrada **aaduser1** seleccionada, haga clic en **Administrar configuración de usuario** y revise las opciones disponibles: 

   - Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto.

   - Eliminar todas las contraseñas de aplicaciones existentes generadas por los usuarios seleccionados.

   - Restaurar autenticación multifactor en todos los dispositivos recordados.

10. Haga clic en **Cancelar** y vuelva a la pestaña del explorador que muestra la hoja **Multi-Factor Authentication \| Introducción** en Azure Portal.

11. En la sección **Configuración**, haga clic en **Alerta de fraude**.

12. En la hoja **Multi-Factor Authentication \| Alerta de fraude**, configure las siguientes opciones:

   |Configuración|Value|
   |---|---|
   |Permitir a los usuarios enviar alertas de fraude|**Activado**|
   |Bloquear automáticamente a los usuarios que notifican fraudes|**Activado**|
   |Código para notificar fraudes durante el saludo inicial|**0**|

13. Haga clic en **Guardar**

    >**Nota**: En este momento, ha habilitado MFA para aaduser1 y ha configurado las alertas de fraude. 

14. Vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory y, en la sección **Administrar**, haga clic en **Propiedades**; a continuación, haga clic en el vínculo **Administrar valores predeterminados de seguridad** en la parte inferior de la hoja y, en la hoja **Habilitar los valores predeterminados de seguridad**, haga clic en **No**. Seleccione **Mi organización usa el acceso condicional** como motivo y, a continuación, haga clic en **Guardar**.

    >**Nota:** Asegúrese de que ha iniciado sesión en el inquilino **AdatumLab500-04** de Azure AD. Puede usar el filtro **Directorio + suscripción** para cambiar entre inquilinos de Azure AD. Asegúrese de que ha iniciado sesión como usuario con el rol Administrador global en el inquilino de Azure AD.

#### <a name="task-6-validate-mfa-configuration"></a>Tarea 6: Validar la configuración de MFA.

En esta tarea, validará la configuración de MFA. Para ello, probará el inicio de sesión de la cuenta de usuario aaduser1. 

1. Abra una ventana del explorador de InPrivate.

2. Vaya a Azure Portal e inicie sesión con la cuenta de usuario **aaduser1**. 

    >**Nota**: Para iniciar sesión, deberá proporcionar el nombre completo de la cuenta de usuario **aaduser1**, incluido el nombre de dominio DNS del inquilino de Azure AD que registró anteriormente en este laboratorio. Este nombre de usuario tiene el formato aaduser1@`<your_tenant_name>`.onmicrosoft.com, donde `<your_tenant_name>` es el marcador de posición que representa el nombre único del inquilino de Azure AD. 

3. Cuando se le solicite, en el cuadro de diálogo **Se necesita más información**, haga clic en **Siguiente**.

    >**Nota**: La sesión del explorador se redirigirá a la página **Comprobación de seguridad adicional**.

4. En la página **Garantizar la seguridad de la cuenta**, seleccione el vínculo **Quiero configurar otro método** y, en la lista desplegable **¿Qué método quiere usar?** , seleccione **Teléfono** y **Confirmar**.

5. En la página **Garantizar la seguridad de la cuenta**, seleccione su país o región, escriba el número de teléfono móvil en el área **Indicar número de teléfono**, asegúrese de que la opción **Enviarme un código por mensaje de texto** está seleccionada y haga clic en **Siguiente**.
 
6. En la página **Garantizar la seguridad de la cuenta**, escriba el código que recibió en el mensaje de texto del teléfono móvil y haga clic en **Siguiente**.

7. En la página **Garantizar la seguridad de la cuenta**, asegúrese de que la comprobación se ha realizado correctamente y haga clic en **Siguiente**.

8. En la página **Garantizar la seguridad de la cuenta**, haga clic en **I want to use a different method** (Quiero utilizar otro método), seleccione **Correo electrónico** en la lista desplegable, haga clic en **Confirmar**, proporcione la dirección de correo electrónico que quiere usar y haga clic en **Siguiente**. Una vez que reciba el correo electrónico correspondiente, identifique el código en el cuerpo del correo electrónico, proporciónelo y, a continuación, haga clic en **Listo**.

9. Cuando se le pida, cambie la contraseña. Asegúrese de registrar la contraseña nueva.

10. Compruebe que ha iniciado sesión correctamente en Azure Portal.

11. Cierre la sesión de **aaduser1** y cierre la ventana del explorador InPrivate.

> Resultado: ha creado un nuevo inquilino de AD, configurado usuarios de AD, configurado MFA y probado la experiencia de MFA para un usuario. 


### <a name="exercise-3-implement-azure-ad-conditional-access-policies"></a>Ejercicio 3: Implementar directivas de acceso condicional de Azure AD 

### <a name="estimated-timing-15-minutes"></a>Tiempo estimado: 15 minutos

En este ejercicio completará las tareas siguientes: 

- Tarea 1: Configurar un directiva de acceso condicional.
- Tarea 2: Probar la directiva de acceso condicional.

#### <a name="task-1---configure-a-conditional-access-policy"></a>Tarea 1: Configurar una directiva de acceso condicional 

En esta tarea, revisará la configuración de la directiva de acceso condicional y creará una directiva que requiera MFA al iniciar sesión en el portal. 

1. En Azure Portal, vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory.

2. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Seguridad**.

3. En la hoja **Seguridad \| Introducción**, en la sección **Proteger**, haga clic en **Acceso condicional**.

4. En el panel **Acceso condicional \| Directivas**, haga clic en **+ Nueva directiva** y seleccione **Crear nueva directiva** en la lista desplegable. 

5. En la hoja **Nuevo**, configure las siguientes opciones:

   - En el cuadro de texto **Nombre**, escriba **AZ500Policy1**.
    
   - Haga clic en **Usuarios o identidades de carga de trabajo seleccionadas**. En el lado derecho, bajo ¿A qué se aplica esta directiva? >> Usuarios y grupos >> Incluir >> Habilitar **Seleccionar usuarios y grupos** >>, marque la casilla **Usuarios y grupos**. En el panel **Seleccionar**, haga clic en **aaduser2** y **Seleccionar**.
    
   - Haga clic en **Aplicaciones o acciones en la nube** y en **Seleccionar aplicaciones** y, en la hoja **Seleccionar**, haga clic en **Microsoft Azure Management** (Administración de Microsoft Azure) y, a continuación, en **Seleccionar**. 

    >**Nota**: Revise la advertencia que indica que esta directiva afecta al acceso a Azure Portal.
    
   - Haga clic en **Condiciones** y en **Riesgo de inicio de sesión**. En la hoja **Riesgo de inicio de sesión**, revise los niveles de riesgo, pero no realice ningún cambio. Cuando termine, cierre la hoja **Riesgo de inicio de sesión**.
    
   - Haga clic en **Plataformas de dispositivo**, revise las plataformas de dispositivo que se pueden incluir y haga clic en **Listo**.
    
   - Haga clic en **Ubicaciones** y revise las opciones de ubicación sin realizar ningún cambio.
    
   - Haga clic en **Conceder** en la sección **Controles de acceso** y, en la hoja **Conceder**, active la casilla **Requerir autenticación multifactor** y haga clic en **Seleccionar**.
    
   - Establezca **Habilitar directiva** en **Activado**.

6. En la hoja **Nuevo**, haga clic en **Crear**. 

    >**Nota**: En este momento, tiene una directiva de acceso condicional que requiere que MFA inicie sesión en Azure Portal. 

#### <a name="task-2---test-the-conditional-access-policy"></a>Tarea 2: Probar la directiva de acceso condicional

En esta tarea, iniciará sesión en Azure Portal como **aaduser2** y comprobará que se requiere MFA. También eliminará la directiva antes de continuar con el ejercicio siguiente. 

1. Abra una ventana InPrivate de Microsoft Edge.

2. En la nueva ventana del explorador, vaya a Azure Portal e inicie sesión con la cuenta de usuario **aaduser2**.

3. Cuando se le solicite, en el cuadro de diálogo **Se necesita más información**, haga clic en **Siguiente**.

    >**Nota**: La sesión del explorador se redirigirá a la página **Garantizar la seguridad de la cuenta**.
    
4. En la página **Garantizar la seguridad de la cuenta**, seleccione el vínculo **Quiero configurar otro método** y, en la lista desplegable **¿Qué método quiere usar?** , seleccione **Teléfono** y **Confirmar**.

5. En la página **Garantizar la seguridad de la cuenta**, seleccione su país o región, escriba el número de teléfono móvil en el área **Indicar número de teléfono**, asegúrese de que la opción **Enviarme un código por mensaje de texto** está seleccionada y haga clic en **Siguiente**.

6. En la página **Garantizar la seguridad de la cuenta**, escriba el código que recibió en el mensaje de texto del teléfono móvil y haga clic en **Siguiente**.

7. En la página **Garantizar la seguridad de la cuenta**, asegúrese de que la comprobación se ha realizado correctamente y haga clic en **Siguiente**.

8. En la página **Garantizar la seguridad de la cuenta**, haga clic en **Listo**.

9. Cuando se le pida, cambie la contraseña. Asegúrese de registrar la contraseña nueva.

10. Compruebe que ha iniciado sesión correctamente en Azure Portal.

11. Cierre la sesión de **aaduser2** y cierre la ventana del explorador InPrivate.

    >**Nota**: Ahora ha comprobado que la directiva de acceso condicional recién creada aplica MFA cuando aaduser2 inicia sesión en Azure Portal.

12. De nuevo en la ventana del explorador que muestra Azure Portal, vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory.

13. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Seguridad**.

14. En la hoja **Seguridad \| Introducción**, en la sección **Proteger**, haga clic en **Acceso condicional**.

15. En la hoja **Acceso condicional \| Directivas**, haga clic en los puntos suspensivos junto a **AZ500Policy1**, en **Eliminar** y, cuando se le pida confirmación, haga clic en **Sí**.

    >**Nota**. Resultado: En este ejercicio ha implementado una directiva de acceso condicional para requerir MFA cuando un usuario inicia sesión en Azure Portal. 

>Resultado: ha configurado y probado el acceso condicional de Azure AD.

### <a name="exercise-4-implement-azure-ad-identity-protection"></a>Ejercicio 4: Implementar Azure AD Identity Protection

### <a name="estimated-timing-30-minutes"></a>Tiempo estimado: 30 minutos

En este ejercicio completará las tareas siguientes: 

- Tarea 1: Ver las opciones de Azure AD Identity Protection en Azure Portal
- Tarea 2: Configurar una directiva de riesgo de usuario
- Tarea 3: Configurar una directiva de riesgo de inicio de sesión
- Tarea 4: Simular eventos de riesgo en las directivas de Azure AD Identity Protection 
- Tarea 5: Revisar informes de Azure AD Identity Protection

#### <a name="task-1-enable-azure-ad-identity-protection"></a>Tarea 1: Habilitar Azure AD Identity Protection

En esta tarea, verá las opciones de Azure AD Identity Protection en Azure Portal. 

1. Si es necesario, inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota:** Asegúrese de que ha iniciado sesión en el inquilino **AdatumLab500-04** de Azure AD. Puede usar el filtro **Directorio + suscripción** para cambiar entre inquilinos de Azure AD. Asegúrese de que ha iniciado sesión como usuario con el rol Administrador global en el inquilino de Azure AD.

2. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Seguridad**.

3. En la hoja **Seguridad \| Introducción**, en la sección **Proteger**, haga clic en **Identity Protection**.

4. En la hoja **Identity Protection \| Información general**, revise las opciones **Proteger**, **Informar** y **Notificar**. 

#### <a name="task-2-configure-a-user-risk-policy"></a>Tarea 2: Configurar una directiva de riesgo de usuario

En esta tarea, creará una directiva de riesgo de usuario. 

1. En la hoja **Identity Protection \| Información general**, en la sección **Proteger**, haga clic en **directiva de riesgo de usuario**.

2. Configure la **Directiva de corrección de riesgos de usuario** con las siguientes opciones: 

   - Haga clic en **Usuarios**; en la pestaña **Incluir** de la hoja **Usuarios**, asegúrese de que está seleccionada la opción **Todos los usuarios**.

   - En la hoja **Usuarios**, cambie a la pestaña **Excluir**, haga clic en **Seleccionar usuarios excluidos**, seleccione su cuenta de usuario y, a continuación, haga clic en **Seleccionar**. 

   - Haga clic en **Riesgo de usuario**; en la hoja **Riesgo de usuario**, seleccione **Bajo y superior** y, a continuación, haga clic en **Listo**. 

   - Haga clic en **Acceso**; en la hoja **Acceso**, asegúrese de que las casillas **Permitir acceso** y **Requerir cambio de contraseña** están activadas y haga clic en **Listo**.

   - Establezca **Aplicar directiva** en **Activado** y haga clic en **Guardar**.

#### <a name="task-3-configure-sign-in-risk-policy"></a>Tarea 3: Configurar directiva de riesgo de inicio de sesión

En esta tarea, configurará una directiva de riesgo de inicio de sesión. 

1. En la hoja **Identity Protection \| Directiva de riesgo de usuario**, en la sección **Proteger**, haga clic en **Directiva de riesgo de inicio de sesión**.

2. Configure la **Directiva de corrección de riesgos de inicio de sesión** con las siguientes opciones: 

   - Haga clic en **Usuarios**; en la pestaña **Incluir** de la hoja **Usuarios**, asegúrese de que está seleccionada la opción **Todos los usuarios**.

   - Haga clic en **Riesgo de inicio de sesión**; en la hoja **Riesgo de inicio de sesión**, seleccione **Medio y superior** y, a continuación, haga clic en **Listo**. 

   - Haga clic en **Acceso**; en la hoja **Acceso**, asegúrese de que la opción **Permitir acceso** y la casilla **Requerir autenticación multifactor** están activadas y haga clic en **Listo**.

   - Establezca **Aplicar directiva** en **Activado** y haga clic en **Guardar**.

#### <a name="task-4-simulate-risk-events-against-the-azure-ad-identity-protection-policies"></a>Tarea 4: Simular eventos de riesgo en las directivas de Azure AD Identity Protection 

> Antes de iniciar esta tarea, asegúrese de que se ha completado la implementación de plantillas que inició en el ejercicio 1. La implementación incluye una VM de Azure denominada **az500-04-vm1**. 

1. En Azure Portal, establezca el filtro **Directorio + suscripción** en el inquilino de Azure AD asociado a la suscripción de Azure en la que implementó la VM de Azure **az500-04-vm1**.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Máquinas virtuales** y presione la tecla **Entrar**.

3. En la hoja **Máquinas virtuales**, haga clic en la entrada **az500-04-vm1**. 

4. En la hoja **az500-04-vm1**, haga clic en **Conectar** y, en el menú desplegable, haga clic en **RDP**. 

5. Haga clic en **Descargar archivo RDP** y úselo para conectarse a la VM de Azure **az500-04-vm1** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

   |Configuración|Valor|
   |---|---|
   |Nombre de usuario|**Estudiante**|
   |Contraseña|**Pa55w.rd1234**|

    >**Nota**: Espere a que se carguen la sesión de Escritorio remoto y **Administrador del servidor**.  

    >**Nota**: Los pasos siguientes se realizan en la sesión de Escritorio remoto para la VM de Azure **az500-04-vm1**. 

6. En **Administrador del servidor**, haga clic en **Servidor local** y, a continuación, haga clic en **Configuración de seguridad mejorada de IE**.

7. En el cuadro de diálogo **Configuración de seguridad mejorada de Internet Explorer**, establezca ambas opciones en **Desactivado** y haga clic en **Aceptar**.

8. Inicie **Internet Explorer**, haga clic en el icono de rueda de engranaje de la barra de herramientas y, en el menú desplegable, haga clic en **Seguridad** y, a continuación, en **Exploración de InPrivate**.

9. En la ventana InPrivate de Internet Explorer, vaya al proyecto del Navegador Tor en  **https://www.torproject.org/projects/torbrowser.html.en** .

10. Descargue e instale la versión para Windows del Navegador Tor con la configuración predeterminada. 

11. Una vez completada la instalación, inicie el Navegador Tor, use la opción **Conectar** de la página inicial y vaya al Panel de acceso de la aplicación en  **https://myapps.microsoft.com** .

12. Cuando se le solicite, intente iniciar sesión con la cuenta **aaduser3**. 

    >**Nota**: Aparecerá el mensaje **Inicio de sesión bloqueado**. Esto es lo esperado, ya que esta cuenta no está configurada con la autenticación multifactor, que es necesaria debido a un mayor riesgo de inicio de sesión asociado al uso del Navegador Tor.

13. Use la opción **Cerrar sesión e iniciar sesión con otra cuenta** o la flecha atrás para iniciar sesión con la cuenta **aaduser1** que creó y configuró para la autenticación multifactor en este laboratorio.

    >**Nota**: Esta vez, aparecerá el mensaje **Se ha detectado una actividad sospechosa.** De nuevo, esto es lo esperado, ya que esta cuenta está configurada con autenticación multifactor. Teniendo en cuenta el mayor riesgo de inicio de sesión asociado al uso del Navegador Tor, tendrá que usar la autenticación multifactor.

14. Use la opción **Comprobar** y especifique si desea comprobar su identidad mediante un mensaje de texto o una llamada.

15. Complete la comprobación y asegúrese de que ha iniciado sesión correctamente en el Panel de acceso de la aplicación.

16. Cierre la sesión de RDP. 

    >**Nota**: En este momento, ha probado dos inicios de sesión diferentes. A continuación, revisará los informes de Azure Identity Protection.

#### <a name="task-5-review-the-azure-ad-identity-protection-reports"></a>Tarea 5: Revisar informes de Azure AD Identity Protection

En esta tarea, revisará los informes Azure AD Identity Protection generados a partir de los inicios de sesión del Navegador Tor.

1. De nuevo en Azure Portal, use el filtro **Directorio + suscripción** para cambiar al inquilino **AdatumLab500-04** de Azure Active Directory.

2. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Seguridad**.

3. En el panel **Seguridad \| Introducción**, en la sección **Informe**, haga clic en **Usuarios de riesgo**. 

4. Revise el informe e identifique las entradas que hacen referencia a la cuenta de usuario **aaduser3**.

5. En la hoja **Seguridad \| Introducción**, en la sección **Informes**, haga clic en **Inicios de sesión de riesgo**. 

6. Revise el informe e identifique las entradas correspondientes al inicio de sesión con la cuenta de usuario **aaduser3**.

7. En **Informes**, haga clic en **Detecciones de riesgos**.

8. Revise el informe e identifique las entradas que representan el inicio de sesión desde la dirección IP anónima generada por el Navegador Tor. 

 >**Nota**: Los riesgos pueden tardar entre 10 y 15 minutos en aparecer en los informes.

> **Resultado**: ha habilitado Azure AD Identity Protection, ha configurado la directiva de riesgo de usuario y la directiva de riesgo de inicio de sesión, y ha validado la configuración de Azure AD Identity Protection mediante la simulación de eventos de riesgo.

**Limpieza de recursos**

> Es necesario quitar los recursos de protección de identidades que ya no usa. 

Siga estos pasos para deshabilitar las directivas de protección de identidades en el inquilino de Azure AD **AdatumLab500-04**.

1. En Azure Portal, vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory.

2. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Seguridad**.

3. En la hoja **Seguridad \| Introducción**, en la sección **Proteger**, haga clic en **Identity Protection**.

4. En la hoja **Identity Protection \| Información general**, haga clic en **Directiva de riesgo de usuario**.

5. En la hoja **Identity Protection \| Directiva de riesgo de usuario**, establezca **Aplicar directiva** en **Desactivado** y, a continuación, haga clic en **Guardar**.

6. En la hoja **Identity Protection \| Directiva de riesgo de usuario**, haga clic en **Directiva de riesgo de inicio de sesión**.

7. En la hoja **Identity Protection \| Directiva de riesgo de inicio de sesión**, establezca **Aplicar directiva** en **Desactivado** y, a continuación, haga clic en **Guardar**.

Siga estos pasos para detener la VM de Azure que aprovisionó anteriormente en el laboratorio.

1. En Azure Portal, establezca el filtro **Directorio + suscripción** en el inquilino de Azure AD asociado a la suscripción de Azure en la que implementó la VM de Azure **az500-04-vm1**.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Máquinas virtuales** y presione la tecla **Entrar**.

3. En la hoja **Máquinas virtuales**, haga clic en la entrada **az500-04-vm1**. 
 
4. En la hoja **az500-04-vm1**, haga clic en **Detener** y, cuando se le pida confirmación, haga clic en **Aceptar**. 

>  No quite los recursos aprovisionados en este laboratorio, ya que el laboratorio de PIM depende de ellos.
