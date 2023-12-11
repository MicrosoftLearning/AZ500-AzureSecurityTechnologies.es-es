---
lab:
  title: '04: MFA y acceso condicional'
  module: Module 01 - Manage Identity and Access
---

# Laboratorio 05: MFA y acceso condicional
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto de características que mejoren la autenticación Azure Active Directory (Azure AD). En concreto, quiere evaluar lo siguiente:

- Azure AD Multi-Factor Authentication
- Acceso condicional de Azure AD
- Directivas basadas en riesgos de acceso condicional de Azure AD

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Implementar VM de Azure mediante una plantilla de Azure Resource Manager
- Ejercicio 2: Implementar Azure MFA
- Ejercicio 3: Implementar directivas de acceso condicional de Azure AD 
- Ejercicio 4: Implementar Azure AD Identity Protection

## MFA - Acceso condicional - Identity Protection

![imagen](https://user-images.githubusercontent.com/91347931/157518628-8b4a9efe-0086-4ec0-825e-3d062748fa63.png)

## Instrucciones

## Archivos de laboratorio:

- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json**
- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json** 

### Ejercicio 1: Implementar VM de Azure mediante una plantilla de Azure Resource Manager

### Tiempo estimado: 10 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Implementar una VM de Azure mediante una plantilla de Azure Resource Manager

#### Tarea 1: Implementar una VM de Azure mediante una plantilla de Azure Resource Manager

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

   >**Nota**: Deberá crear una contraseña única que se usará para crear máquinas virtuales durante el resto del curso. La contraseña debe tener al menos 12 caracteres y cumplir los requisitos de complejidad definidos (tres de los siguientes: una minúscula, una mayúscula, un número y un carácter especial). [Requisitos de contraseña de máquinas virtuales](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm-) Anote la contraseña.

   |Configuración|Value|
   |---|---|
   |Subscription|nombre de la suscripción de Azure que usará en este laboratorio|
   |Resource group|Haga clic en **Crear nuevo** y escriba el nombre **AZ500LAB04**.|
   |Location|**Este de EE. UU.**|
   |Tamaño de máquina virtual:|**Standard_D2s_v3**|
   |Nombre de la máquina virtual|**az500-04-vm1**|
   |Nombre de usuario administrador|**Estudiante**|
   |Contraseña de administrador|**Cree una contraseña y guárdela para poder consultarla más adelante. Se le pedirá que introduzca esta contraseña para acceder al laboratorio.**|
   |El nombre de la red virtual|**az500-04-vnet1**|

   >**Nota**: Para identificar las regiones de Azure donde puede aprovisionar VM de Azure, consulte [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)

10. Haga clic en **Revisar y crear** y después en **Crear**.

    >**Nota**: No espere a que se completen la implementación, sino que avance al siguiente ejercicio. Usará la máquina virtual incluida en esta implementación en el último ejercicio de este laboratorio.

> Resultado: ha iniciado una implementación de plantilla de una VM de Azure **az500-04-vm1** que usará en el último ejercicio de este laboratorio.


### Ejercicio 2: Implementar Azure MFA

### Tiempo estimado: 30 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crear un nuevo inquilino de Azure AD.
- Tarea 2: Activar la prueba de Azure AD Premium P2.
- Tarea 3: Crear usuarios y grupos de Azure AD.
- Tarea 4: Asignar licencias de Azure AD Premium P2 a usuarios de Azure AD.
- Tarea 5: Configurar Azure MFA.
- Tarea 6: Validar la configuración de MFA.

#### Tarea 1: Crear un nuevo inquilino de Azure AD

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


#### Tarea 2: Activar la prueba de Azure AD Premium P2

En esta tarea, se registrará para la prueba gratuita de Azure AD Premium P2. 

1. En Azure Portal, en la barra de herramientas, haga clic en el icono **Directorio + suscripción**, situado a la derecha del icono de Cloud Shell. 

2. En el panel **Directorio + suscripción**, haga clic en el inquilino recién creado **AdatumLab500-04** y en el botón **Cambiar** para establecerlo como el directorio actual.

    >**Nota**: Es posible que tenga que actualizar la ventana del explorador si la entrada **AdatumLab500-04** no aparece en la lista de filtros **Directorio + suscripción**.

3. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Azure Active Directory** y presione la tecla **Entrar**. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Licencias**.

4. En la hoja **Licencias \| Información general**, en la sección **Administrar**, haga clic en **Todos los productos** y, a continuación, en **+ Probar o comprar**.

5. En la hoja **Activar**, en la sección Azure AD Premium P2, haga clic en **Evaluación gratuita** y, a continuación, en **Activar**.


#### Tarea 3: Crear usuarios y grupos de Azure AD.

En esta tarea, creará tres usuarios: aaduser1 (administrador global), aaduser2 (usuario) y aaduser3 (usuario). Necesitará el nombre principal y la contraseña de usuario de cada uno para tareas posteriores. 

1. Vuelva a la hoja **AdatumLab500-04**  Azure Active Directory y, en la sección **Administrar**, haga clic en **Usuarios**.

2. En la hoja **Usuarios \| Todos los usuarios**, haga clic en **+ Nuevo usuario** y, a continuación, en **Crear nuevo usuario**. 

3. En la hoja **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada, especifique la siguiente configuración en la pestaña Aspectos básicos (deje la otra configuración con sus valores predeterminados) y haga clic en **Siguiente: Propiedades >** :

   |Configuración|Value|
   |---|---|
   |Nombre principal de usuario|**aaduser1**|
   |Name|**aaduser1**|
   |Contraseña|asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada|
   
      >**Nota**: Registre el nombre principal de usuario completo. Para copiar su valor, haga clic en el botón **Copiar en el Portapapeles** del lado derecho de la lista desplegable que muestra el nombre de dominio. La necesitará más adelante en este laboratorio.
   
      >**Nota**: Registre la contraseña del usuario. Para copiar su valor, haga clic en el botón **Copiar en el Portapapeles** del lado derecho del cuadro de texto. La necesitará más adelante en este laboratorio. 
   
4. En la pestaña **Propiedades**, desplácese hasta la parte inferior y especifique la Ubicación de uso: **Estados Unidos** (deje todos los demás con sus valores predeterminados) y haga clic en **Siguiente: Asignaciones >** .

5. En la pestaña **Asignaciones**, haga clic en **+ Agregar rol**, busque y seleccione **Administrador global**. Haga clic en **Seleccionar**, **Revisar y crear** y, después, **Crear**.

6. De nuevo en la hoja **Usuarios \| Todos los usuarios**, haga clic en **+ Nuevo usuario**. 

7. En la hoja **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada y, después, especifique la siguiente configuración (deje la otra configuración con sus valores predeterminados) y haga clic en **Siguiente: Propiedades >** :

   |Configuración|Value|
   |---|---|
   |Nombre principal de usuario|**aaduser2**|
   |Name|**aaduser2**|
   |Contraseña|asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada| 

    >**Nota**: Registre el nombre principal de usuario completo y la contraseña.

8. En la pestaña **Propiedades**, desplácese hasta la parte inferior y especifique la Ubicación de uso: **Estados Unidos** (deje todos los demás con sus valores predeterminados) y haga clic en **Revisar y crear** y luego **Crear**.

9. De nuevo en la hoja **Usuarios \| Todos los usuarios**, haga clic en **+ Nuevo usuario**. 

10. En la hoja **Nuevo usuario**, asegúrese de que la opción **Crear usuario** está seleccionada y, después, especifique la siguiente configuración (deje la otra configuración con sus valores predeterminados) y haga clic en **Siguiente: Propiedades >** :

    |Configuración|Value|
    |---|---|
    |Nombre principal de usuario|**aaduser3**|
    |Name|**aaduser3**|
    |Contraseña|asegúrese de que la opción **Generar contraseña automáticamente** está seleccionada|

    >**Nota**: Registre el nombre principal de usuario completo y la contraseña.

11. En la pestaña **Propiedades**, desplácese hasta la parte inferior y especifique la Ubicación de uso: **Estados Unidos** (deje todos los demás con sus valores predeterminados) y haga clic en **Revisar y crear** y luego **Crear**.

    >**Nota**: En este momento, debería tener tres nuevos usuarios en la página **Usuarios**. 
    
#### Tarea 4: Asignar licencias de Azure AD Premium P2 a usuarios de Azure AD

En esta tarea, asignará cada usuario a la licencia de Azure Active Directory Premium P2.

1. En la hoja **Usuarios \| Todos los usuarios**, haga clic en la entrada que representa su cuenta de usuario. 

2. En la hoja que muestra las propiedades de la cuenta de usuario, haga clic en **Editar propiedades**.  Confirme que la ubicación de uso está establecida en **Estados Unidos**. Si no es así, establezca la ubicación de uso y haga clic en **Guardar**.

3. Vuelva a la hoja **AdatumLab500-04**  Azure Active Directory y, en la sección **Administrar**, haga clic en **Licencias**.

4. En la hoja **Licencias \| Información general**, haga clic en **Todos los productos**, active la casilla **Azure Active Directory Premium P2** y haga clic en **+ Asignar**.

5. En la hoja **Asignar licencias** haga clic en **+ Agregar usuarios y grupos**.

6. En la hoja **Usuarios**, seleccione **aaduser1**, **aaduser2**, **aaduser3** y su cuenta de usuario, y haga clic en **Seleccionar**.

7. De nuevo en hoja **Asignar licencias**, haga clic en **Opciones de asignación**, asegúrese de que todas las opciones están habilitadas, haga clic en **Revisar y asignar** y luego en **Asignar**.

8. Cierre la sesión de Azure Portal y vuelva a iniciarla con la misma cuenta. (Este paso es necesario para que la asignación de licencias surta efecto).

    >**Nota**: En este momento, ha asignado licencias de Azure Active Directory Premium P2 a todas las cuentas de usuario que va a usar en este laboratorio. Asegúrese de cerrar la sesión y, a continuación, de volver a iniciarla. 

#### Tarea 5: Configurar Azure MFA.

En esta tarea, configurará MFA y habilitará MFA para aaduser1. 

1. En Azure Portal, vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory.

    >**Nota**: Asegúrese de que usa el inquilino AdatumLab500-04 de Azure AD.

2. En la hoja **AdatumLab500-04** del inquilino de Azure Active Directory, en la sección **Administrar**, haga clic en **Seguridad**.

3. En la hoja **Seguridad \| Introducción**, en la sección **Administrar**, haga clic en **Autenticación multifactor**.

4. En la hoja **Autenticación multifactor \| Introducción**, haga clic en el vínculo **Configuración adicional de la autenticación multifactor basada en la nube**. 

    >**Nota**: Se abrirá una nueva pestaña del explorador, que muestra la página **Autenticación multifactor**.

5. En la página **Autenticación multifactor**, haga clic en la **Configuración del servicio**. Revise las **opciones de comprobación**. Observe que las opciones **Mensaje de texto a teléfono**, **Notificación a través de aplicación móvil** y **de hardware** están habilitadas. Haga clic en **Guardar** y, a continuación, en **Cerrar**.

6. Cambie a la pestaña **usuarios**, haga clic en la entrada **aaduser1** en el vínculo **Habilitar** y, cuando se le solicite, en **Habilitar autenticación multifactor**. Después, haga clic en **cerrar**.

7. Observe que el valor de la columna **Estado de Multi-Factor Auth** para **aaduser1** ahora es **Habilitado**.

8. Haga clic en **aaduser1** y observe que, en este momento, también tiene la opción **Aplicar**. 

    >**Nota**: El cambio del estado de usuario de Habilitado a Aplicado solo afecta a las aplicaciones integradas de Azure AD heredadas que no admiten Azure MFA y, una vez que el estado cambia a Aplicado, requieren el uso de contraseñas de aplicación.

9. Con la entrada **aaduser1** seleccionada, haga clic en **Administrar configuración de usuario** y revise las opciones disponibles: 

   - Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto.

   - Eliminar todas las contraseñas de aplicaciones existentes generadas por los usuarios seleccionados.

   - Restaurar autenticación multifactor en todos los dispositivos recordados.

10. Haga clic en **Cancelar** y vuelva a la pestaña del explorador que muestra la hoja **Autenticación multifactor \| Introducción** en Azure Portal.

11. En la sección **Configuración**, haga clic en **Alerta de fraude**.

12. En la hoja **Multi-Factor Authentication \| Alerta de fraude**, configure las siguientes opciones:

    |Configuración|Value|
    |---|---|
    |Permitir a los usuarios enviar alertas de fraude|**Activado**|
    |Bloquear automáticamente a los usuarios que notifican fraudes|**Activado**|
    |Código para notificar fraudes durante el saludo inicial|**0**|

13. Haga clic en **Guardar**

    >**Nota**: En este momento, ha habilitado MFA para aaduser1 y ha configurado las alertas de fraude. 

14. Vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory y, en la sección **Administrar**, haga clic en **Propiedades**; a continuación, haga clic en el vínculo **Administrar valores predeterminados de seguridad** en la parte inferior de la hoja y, en la hoja **Habilitar los valores predeterminados de seguridad**, haga clic en **Deshabilitado**. Seleccione **Mi organización que usa el acceso condicional** como *Motivo para deshabilitarlo*, haga clic en **Guardar**, lea la advertencia y, después, haga clic en **Deshabilitar**.

    >**Nota:** Asegúrese de que ha iniciado sesión en el inquilino **AdatumLab500-04** de Azure AD. Puede usar el filtro **Directorio + suscripción** para cambiar entre inquilinos de Azure AD. Asegúrese de que ha iniciado sesión como usuario con el rol Administrador global en el inquilino de Azure AD.

#### Tarea 6: Validar la configuración de MFA.

En esta tarea, validará la configuración de MFA. Para ello, probará el inicio de sesión de la cuenta de usuario aaduser1. 

1. Abra una ventana del explorador de InPrivate.

2. Vaya a Azure Portal, **`https://portal.azure.com/`** , e inicie sesión con la cuenta de usuario **aaduser1**. 

    >**Nota**: Para iniciar sesión, deberá proporcionar el nombre completo de la cuenta de usuario **aaduser1**, incluido el nombre de dominio DNS del inquilino de Azure AD que registró anteriormente en este laboratorio. Este nombre de usuario tiene el formato aaduser1@`<your_tenant_name>`.onmicrosoft.com, donde `<your_tenant_name>` es el marcador de posición que representa el nombre único del inquilino de Azure AD. 

3. Cuando se le solicite, en el cuadro de diálogo **Se necesita más información**, haga clic en **Siguiente**.

    >**Nota**: La sesión del explorador se redirigirá a la página **Comprobación de seguridad adicional**.

4. En la página **Garantizar la seguridad de la cuenta**, seleccione el vínculo **Quiero configurar otro método** y, en la lista desplegable **¿Qué método quiere usar?** , seleccione **Teléfono** y **Confirmar**.

5. En la página **Garantizar la seguridad de la cuenta**, seleccione su país o región, escriba el número de teléfono móvil en el área **Indicar número de teléfono**, asegúrese de que la opción **Enviarme un código por mensaje de texto** está seleccionada y haga clic en **Siguiente**.
 
6. En la página **Garantizar la seguridad de la cuenta**, escriba el código que recibió en el mensaje de texto del teléfono móvil y haga clic en **Siguiente**.

7. En la página **Garantizar la seguridad de la cuenta**, asegúrese de que la comprobación se ha realizado correctamente y haga clic en **Siguiente**.

8. Si se le solicita un método de autenticación adicional, haga clic en **Quiero utilizar otro método**, seleccione **Correo electrónico** en la lista desplegable, haga clic en **Confirmar**, proporcione la dirección de correo electrónico que quiere usar y haga clic en **Siguiente**. Una vez que reciba el correo electrónico correspondiente, identifique el código en el cuerpo del correo electrónico, proporciónelo y, a continuación, haga clic en **Listo**.

9. Cuando se le pida, cambie la contraseña. Asegúrese de registrar la contraseña nueva.

10. Compruebe que ha iniciado sesión correctamente en Azure Portal.

11. Cierre la sesión de **aaduser1** y cierre la ventana del explorador InPrivate.

> Resultado: ha creado un nuevo inquilino de AD, configurado usuarios de AD, configurado MFA y probado la experiencia de MFA para un usuario. 


### Ejercicio 3: Implementar directivas de acceso condicional de Azure AD 

### Tiempo estimado: 15 minutos

En este ejercicio completará las tareas siguientes: 

- Tarea 1: Configurar un directiva de acceso condicional.
- Tarea 2: Probar la directiva de acceso condicional.

#### Tarea 1: Configurar una directiva de acceso condicional 

En esta tarea, revisará la configuración de la directiva de acceso condicional y creará una directiva que requiera MFA al iniciar sesión en el portal. 

1. En Azure Portal, vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory.

2. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Seguridad**.

3. En la hoja **Seguridad \| Introducción**, en la sección **Proteger**, haga clic en **Acceso condicional**. En el panel de navegación izquierdo, haga clic en **Directivas**.

4. En la hoja **Acceso condicional \| Directivas**, haga clic en **+ Nueva directiva**. 

5. En la hoja **Nuevo**, configure las siguientes opciones:

   - En el cuadro de texto **Nombre**, escriba **AZ500Policy1**.
    
   - En **Usuarios**, haga clic en **0 usuarios y grupos seleccionados**. En el lado derecho, en Incluir, active **Seleccionar usuarios y grupos** >> seleccione la casilla **Usuarios y grupos**, en la hoja **Seleccionar usuarios y grupos**. Seleccione la casilla **aaduser2** y haga clic en **Seleccionar**.
    
   - En **Recursos de destino**, haga clic en **No hay recursos de destino seleccionados**, haga clic en **Seleccionar aplicaciones**, en Seleccionar, haga clic en **Ninguno**. En la hoja **Seleccionar**, seleccione la casilla **Administración de Microsoft Azure** y haga clic en **Seleccionar**. 

     >**Nota**: Revise la advertencia que indica que esta directiva afecta al acceso a Azure Portal.
    
   - En **Condiciones**, haga clic en **0 condiciones seleccionadas**, **en Riesgo de inicio de sesión**, haga clic en **No configurado**. En la hoja **Riesgo de inicio de sesión**, revise los niveles de riesgo, pero no realice ningún cambio. Cuando termine, cierre la hoja **Riesgo de inicio de sesión**.
    
   - En **Plataformas de dispositivos**, haga clic en **No configurado**, revise las plataformas de dispositivos que se pueden incluir, pero no realice ningún cambio y haga clic en **Listo**.
    
   - En **Ubicaciones**, haga clic en **No configurado** y revise las opciones de ubicación sin realizar ningún cambio.
    
   - En **Conceder** en la sección **Controles de acceso**, haga clic en **0 controles seleccionados**. En la hoja **Conceder**, active la casilla **Requerir autenticación multifactor** y haga clic en **Seleccionar**
    
   - Establezca **Habilitar directiva** en **Activado**.

6. En la hoja **Nuevo**, haga clic en **Crear**. 

    >**Nota**: En este momento, tiene una directiva de acceso condicional que requiere que MFA inicie sesión en Azure Portal. 

#### Tarea 2: Probar la directiva de acceso condicional

En esta tarea, iniciará sesión en Azure Portal como **aaduser2** y comprobará que se requiere MFA. También eliminará la directiva antes de continuar con el ejercicio siguiente. 

1. Abra una ventana InPrivate de Microsoft Edge.

2. En la nueva ventana del explorador, vaya a Azure Portal, **`https://portal.azure.com/`** , e inicie sesión con la cuenta de usuario **aaduser2**.

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

14. En la hoja **Seguridad \| Introducción**, en la sección **Proteger**, haga clic en **Acceso condicional**. En el panel de navegación izquierdo, haga clic en **Directivas**.

15. En la hoja **Acceso condicional \| Directivas**, haga clic en los puntos suspensivos junto a **AZ500Policy1**, en **Eliminar** y, cuando se le pida confirmación, haga clic en **Sí**.

    >**Nota**. Resultado: En este ejercicio ha implementado una directiva de acceso condicional para requerir MFA cuando un usuario inicia sesión en Azure Portal. 

>Resultado: ha configurado y probado el acceso condicional de Azure AD.

### Ejercicio 4: Implementación de directivas basadas en riesgos en el acceso condicional

### Tiempo estimado: 30 minutos

En este ejercicio completará las tareas siguientes:

- Tarea 1: Ver las opciones de Azure AD Identity Protection en Azure Portal
- Tarea 2: Configurar una directiva de riesgo de usuario
- Tarea 3: Configurar una directiva de riesgo de inicio de sesión
- Tarea 4: Simular eventos de riesgo en las directivas de Azure AD Identity Protection 
- Tarea 5: Revisar informes de Azure AD Identity Protection

#### Tarea 1: Habilitar Azure AD Identity Protection

En esta tarea, verá las opciones de Azure AD Identity Protection en Azure Portal. 

1. Si es necesario, inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota:** Asegúrese de que ha iniciado sesión en el inquilino **AdatumLab500-04** de Azure AD. Puede usar el filtro **Directorio + suscripción** para cambiar entre inquilinos de Azure AD. Asegúrese de que ha iniciado sesión como usuario con el rol Administrador global en el inquilino de Azure AD.

#### Tarea 2: Configurar una directiva de riesgo de usuario

En esta tarea, creará una directiva de riesgo de usuario. 

1. Vaya al inquilino **AdatumLab500-04** de Azure AD >**Seguridad** > **Acceso condicional** > **Directivas**.

2. Haga clic en **+ Nueva directiva**.

3. Escriba el siguiente nombre de directiva en el cuadro de texto **Nombre**: **AZ500Policy2**.

4. En **Asignaciones** > **Usuarios**, haga clic en **0 usuarios y grupos seleccionados**.

5. En **Incluir**, haga clic en **Seleccionar usuarios y grupos**, haga clic en **Usuarios y grupos**, seleccione **aaduser2** y **aaduser3** y, después, haga clic en **Seleccionar**.

6. En **Excluir**, haga clic en **Usuarios y grupos** y seleccione **aaduser1**. Después, haga clic en **Seleccionar**. 

7. En **Recursos de destino**, haga clic en **No hay recursos de destino seleccionados**, confirme que las **Aplicaciones en la nube** están seleccionadas en la lista desplegable y, en **Incluir**, seleccione **Todas las aplicaciones en la nube**.

8. En **Condiciones**, haga clic en **0 condiciones seleccionadas**, en **Riesgo de usuario**, haga clic en **No configurado**. En la hoja **Riesgo de usuario**, establezca **Configurar** en **Sí**.

9. En **Configure los niveles de riesgo de usuario necesarios para aplicar la directiva**, seleccione **Alto**.

10. Haga clic en **Done**(Listo).

11. En **Conceder** en la sección **Controles de acceso**, haga clic en **0 controles seleccionados**. En la hoja **Conceder**, confirme que la opción **Conceder acceso** está seleccionada.

12. Seleccione **Requerir autenticación multifactor** y **Requerir cambio de contraseña**.

13. Haga clic en **Seleccionar**.

14. En **Sesión**, haga clic en **0 controles seleccionados**. Seleccione **Frecuencia de inicio de sesión** y, después, seleccione **Cada vez**.

15. Haga clic en **Seleccionar**.

16. Confirme que **Habilitar directiva** está establecida en **Solo informe**.

17. Haga clic en **Crear** para habilitar la directiva.

#### Tarea 3: Configurar una directiva de riesgo de inicio de sesión

1. Vaya al inquilino **AdatumLab500-04** de Azure AD > **Seguridad** > **Acceso condicional**> **Directivas**.

2. Seleccione **+ Nueva directiva**.

3. Escriba el siguiente nombre de directiva en el cuadro de texto **Nombre**: **AZ500Policy3**.

4. En **Asignaciones** > **Usuarios**, haga clic en **0 usuarios y grupos seleccionados**.

5. En **Incluir**, haga clic en **Seleccionar usuarios y grupos**. Después, haga clic en **Usuarios y grupos**, seleccione **aaduser2** y **aaduser3** y, finalmente, haga clic en **Seleccionar**.

6. En **Excluir**, haga clic en **Usuarios y grupos** y seleccione **aaduser1**. Después, haga clic en **Seleccionar**. 

7. En **Recursos de destino**, haga clic en **No hay recursos de destino seleccionados**, confirme que las **Aplicaciones en la nube** están seleccionadas en la lista desplegable y, en **Incluir**, seleccione **Todas las aplicaciones en la nube**.

8. En **Condiciones**, haga clic en **0 condiciones seleccionadas**, **en Riesgo de inicio de sesión**, haga clic en **No configurado**. En la hoja **Riesgo de inicio de sesión**, establezca **Configurar** en **Sí**.

9. En **Seleccionar el nivel de riesgo de inicio de sesión al que se aplicará la directiva**, seleccione **Alto** y **Medio**.

10. Haga clic en **Done**(Listo).

11. En **Conceder** en la sección **Controles de acceso**, haga clic en **0 controles seleccionados**. En la hoja **Conceder**, confirme que la opción **Conceder acceso** está seleccionada.   

12. Seleccione **Requerir autenticación multifactor**.

13. Haga clic en **Seleccionar**.

14. En **Sesión**, haga clic en **0 controles seleccionados**. Seleccione **Frecuencia de inicio de sesión** y, después, seleccione **Cada vez**.

15. Haga clic en **Seleccionar**.

16. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.

17. Haga clic en **Crear** para habilitar la directiva.

#### Tarea 4: Simular eventos de riesgo en las directivas de Azure AD Identity Protection 

> Antes de iniciar esta tarea, asegúrese de que se ha completado la implementación de plantillas que inició en el ejercicio 1. La implementación incluye una VM de Azure denominada **az500-04-vm1**. 

1. En Azure Portal, establezca el filtro **Directorio + suscripción** en el inquilino de Azure AD asociado a la suscripción de Azure en la que implementó la VM de Azure **az500-04-vm1**.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Máquinas virtuales** y presione la tecla **Entrar**.

3. En la hoja **Máquinas virtuales**, haga clic en la entrada **az500-04-vm1**. 

4. En la hoja **az500-04-vm1**, haga clic en **Conectar**. Confirme que está en la pestaña **RDP**.

5. Haga clic en **Descargar archivo RDP** y úselo para conectarse a la VM de Azure **az500-04-vm1** a través de Escritorio remoto. Cuando se le pida que se autentique, proporcione las credenciales siguientes:

    |Configuración|Valor|
    |---|---|
    |Nombre de usuario|**Estudiante**|
    |Contraseña|**Use su contraseña personal creada en Laboratorio 04 > Ejercicio 1 > Tarea 1 > Paso 9.**|

    >**Nota**: Espere a que se carguen la sesión de Escritorio remoto y **Administrador del servidor**.  

    >**Nota**: Los pasos siguientes se realizan en la sesión de Escritorio remoto para la VM de Azure **az500-04-vm1**. 

6. En **Administrador del servidor**, haga clic en **Servidor local** y, a continuación, haga clic en **Configuración de seguridad mejorada de IE**.

7. En el cuadro de diálogo **Configuración de seguridad mejorada de Internet Explorer**, establezca ambas opciones en **Desactivado** y haga clic en **Aceptar**.

8. Inicie **Internet Explorer**, haga clic en el icono de rueda de engranaje de la barra de herramientas y, en el menú desplegable, haga clic en **Seguridad** y, a continuación, en **Exploración de InPrivate**.

9. En la ventana InPrivate de Internet Explorer, vaya al proyecto del Navegador Tor en  **https://www.torproject.org/projects/torbrowser.html.en** .

10. Descargue e instale la versión para Windows del explorador ToR con la configuración predeterminada. 

11. Una vez completada la instalación, inicie el explorador ToR, use la opción **Conectar** de la página inicial y vaya al Panel de acceso de la aplicación en **https://myapps.microsoft.com** .

12. Cuando se le solicite, intente iniciar sesión con la cuenta **aaduser3**. 

    >**Nota**: Aparecerá el mensaje **Inicio de sesión bloqueado**. Esto es lo esperado, ya que esta cuenta no está configurada con la autenticación multifactor, que es necesaria debido a un mayor riesgo de inicio de sesión asociado al uso del explorador ToR.

13. En el explorador ToR, seleccione la flecha hacia atrás para iniciar sesión con la cuenta **aaduser1** que ha creado y configurado para la autenticación multifactor anteriormente en este laboratorio.

    >**Nota**: Esta vez, aparecerá el mensaje **Se ha detectado una actividad sospechosa.** De nuevo, esto es lo esperado, ya que esta cuenta está configurada con autenticación multifactor. Teniendo en cuenta el mayor riesgo de inicio de sesión asociado al uso del Navegador Tor, tendrá que usar la autenticación multifactor.

14. Use la opción **Comprobar** y especifique si desea comprobar su identidad mediante un mensaje de texto o una llamada.

15. Complete la comprobación y asegúrese de que ha iniciado sesión correctamente en el Panel de acceso de la aplicación.

16. Cierre la sesión de RDP. 

    >**Nota**: En este momento, ha probado dos inicios de sesión diferentes. A continuación, revisará los informes de Azure Identity Protection.

#### Tarea 5: Revisar informes de Azure AD Identity Protection

En esta tarea, revisará los informes Azure AD Identity Protection generados a partir de los inicios de sesión del Navegador Tor.

1. De nuevo en Azure Portal, use el filtro **Directorio + suscripción** para cambiar al inquilino **AdatumLab500-04** de Azure Active Directory.

2. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Seguridad**.

3. En el panel **Seguridad \| Introducción**, en la sección **Informe**, haga clic en **Usuarios de riesgo**. 

4. Revise el informe e identifique las entradas que hacen referencia a la cuenta de usuario **aaduser3**.

5. En la hoja **Seguridad \| Introducción**, en la sección **Informes**, haga clic en **Inicios de sesión de riesgo**. 

6. Revise el informe e identifique las entradas correspondientes al inicio de sesión con la cuenta de usuario **aaduser3**.

7. En **Informes**, haga clic en **Detecciones de riesgos**.

8. Revise el informe e identifique las entradas que representan el inicio de sesión desde la dirección IP anónima generada por el Navegador Tor. 

    >**Nota**: Los riesgos pueden tardar entre 10 y 15 minutos en aparecer en los informes.

> **Resultado**: ha habilitado Azure AD Identity Protection, ha configurado la directiva de riesgo de usuario y la directiva de riesgo de inicio de sesión, y ha validado la configuración de Azure AD Identity Protection mediante la simulación de eventos de riesgo.

**Limpieza de recursos**

> Es necesario quitar los recursos de protección de identidades que ya no usa. 

Siga estos pasos para deshabilitar las directivas de protección de identidades en el inquilino de Azure AD **AdatumLab500-04**.

1. En Azure Portal, vuelva a la hoja **AdatumLab500-04** del inquilino de Azure Active Directory.

2. En la hoja **AdatumLab500-04**, en la sección **Administrar**, haga clic en **Seguridad**.

3. En la hoja **Seguridad \| Introducción**, en la sección **Proteger**, haga clic en **Identity Protection**.

4. En la hoja **Identity Protection \| Información general**, haga clic en **Directiva de riesgo de usuario**.

5. En el panel **Identity Protection \| Directiva de riesgo de usuario**, establezca **Cumplimiento de directivas** en **Deshabilitar** y, a continuación, haga clic en **Guardar**.

6. En la hoja **Identity Protection \| Directiva de riesgo de usuario**, haga clic en **Directiva de riesgo de inicio de sesión**.

7. En el panel **Identity Protection \| Directiva de riesgo de inicio de sesión**, establezca **Cumplimiento de directivas** en **Deshabilitar** y, a continuación, haga clic en **Guardar**.

Siga estos pasos para detener la VM de Azure que aprovisionó anteriormente en el laboratorio.

1. En Azure Portal, establezca el filtro **Directorio + suscripción** en el inquilino de Azure AD asociado a la suscripción de Azure en la que implementó la VM de Azure **az500-04-vm1**.

2. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Máquinas virtuales** y presione la tecla **Entrar**.

3. En la hoja **Máquinas virtuales**, haga clic en la entrada **az500-04-vm1**. 
 
4. En la hoja **az500-04-vm1**, haga clic en **Detener** y, cuando se le pida confirmación, haga clic en **Aceptar**. 

>  No quite los recursos aprovisionados en este laboratorio, ya que el laboratorio de PIM depende de ellos.
