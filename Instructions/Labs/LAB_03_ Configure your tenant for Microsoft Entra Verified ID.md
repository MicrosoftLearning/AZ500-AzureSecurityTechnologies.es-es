---
lab:
  title: "03 - Configuración del inquilino para Microsoft\_Entra Verified ID"
  module: Module 01 - Manage Identity and Access
---
# Laboratorio 03: Configuración del inquilino para Microsoft Entra Verified ID
# Manual de laboratorio para alumnos

## Escenario del laboratorio

# Configuración del inquilino para Verified ID de Microsoft Entra

>[!Note] 
> Verifiable Credentials de Azure Active Directory es ahora Verified ID de Microsoft Entra y forma parte de la familia de productos Microsoft Entra. Obtenga más información sobre la familia de soluciones de identidad [Microsoft Entra](https://aka.ms/EntraAnnouncement) y empiece a trabajar en el [centro de administración unificado de Microsoft Entra](https://entra.microsoft.com).

Verified ID de Microsoft Entra es una solución de identidad descentralizada que le ayuda a proteger la organización. El servicio le permite emitir y comprobar credenciales. Los emisores pueden usar el servicio de Verified ID para emitir sus propias credenciales verificables personalizadas. Los comprobadores pueden usar la API REST gratuita del servicio para solicitar y aceptar con facilidad credenciales verificables en aplicaciones y servicios. En ambos casos, el inquilino de Azure AD debe configurarse para emitir sus propias credenciales verificables o comprobar la presentación de las credenciales verificables de un usuario que ha emitido un tercero. En caso de que sea un emisor y un comprobador, puede usar un inquilino de Azure AD único para emitir sus propias credenciales verificables y comprobar las de los otros.

En este tutorial obtendrá información sobre cómo configurar el inquilino de Azure AD para que use el servicio de credenciales verificables.

Específicamente, aprenderá sobre lo siguiente:

> - Crear una instancia de Azure Key Vault.
> - Configuración de un servicio de identificaciones verificadas
> - Registrar una aplicación en Azure AD.
> - Comprobación de la propiedad de un dominio en un identificador descentralizado (DID)

En el diagrama siguiente se muestra la arquitectura de Verified ID y el componente que configure.

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8d4d01c2-3110-421a-91a8-7b052bc8d793)


## Requisitos previos

- Asegúrese de que tiene el permiso de administrador global o de administrador de directivas de autenticación para el directorio que quiera configurar. Si no es el administrador global, necesita el permiso de administrador de aplicaciones para completar el registro de la aplicación, incluida la concesión de consentimiento del administrador.
- Asegúrese de que tiene el rol de colaborador para la suscripción de Azure o el grupo de recursos en el que va a implementar Azure Key Vault.

## Creación de un Almacén de claves

Azure Key Vault es un servicio en la nube que permite el almacenamiento seguro y el acceso de secretos y claves. El servicio de Verified ID almacena las claves públicas y privadas en Azure Key Vault. Estas claves se usan para firmar y comprobar las credenciales.

### Uso de Azure Portal para crear una instancia de Azure Key Vault.

1. Inicie una sesión en el explorador e inicie sesión en el [menú de Azure Portal.](https://portal.azure.com/)
   
2. En el cuadro de búsqueda de Azure Portal, escriba **Key Vault.**

3. En la lista de resultados, elija **Key Vault**.

4. En la sección Key Vaults, elija **Crear.**

5. En la pestaña **Aspectos Básicos** de **Crear almacén de claves**, escriba o seleccione esta información:
   
   |Configuración|Value|
   |---|---|
   |**Detalles del proyecto**|
   |Subscription|Seleccione su suscripción.|
   |Resource group|Escriba **azure-rg-1.** Seleccione **Aceptar**.|
   |**Detalles de instancia**|
   |Nombre del almacén de claves|Escriba **Contoso-vault2.**|
   |Region|Seleccione **Este de EE. UU**.|
   |Plan de tarifa|Valor predeterminado del sistema **Estándar**|
   |Días durante los cuales se conservarán los almacenes eliminados|Valor predeterminado del sistema **90**|

6. Seleccione la **pestaña Revisar y crear** o seleccione el botón azul Revisar y crear situado en la parte inferior de la página.
  
7. Seleccione **Crear**.

Tome nota de estas dos propiedades:

* **Nombre del almacén**: en este ejemplo es **Contoso-Vault2**. Utilizará este nombre para otros pasos.
* **URI de Key Vault**: en el ejemplo, el URI de Key Vault es `https://contoso-vault2.vault.azure.net/`. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

En este momento, su cuenta de Azure es la única autorizada para realizar operaciones en este nuevo almacén.

>[!NOTE]
>De manera predeterminada, la cuenta que crea un almacén es la única con acceso. El servicio de Verified ID necesita acceso al almacén de claves. Debe configurar el almacén de claves con directivas de acceso, de manera que permita a la cuenta que se ha usado durante la configuración crear y eliminar claves. La cuenta que se usó durante la configuración también necesita permisos para firmar de manera que pueda crear el enlace de dominio para Verified ID. Si usa la misma cuenta mientras realiza las pruebas, modifique la directiva predeterminada para conceder el permiso de firma de la cuenta, además de los permisos predeterminados concedidos a los creadores del almacén.

### Establecimiento de directivas de acceso para Key Vault

Un almacén de claves define si una entidad de seguridad especificada puede realizar operaciones en secretos y claves de Key Vault. Establezca directivas de acceso en el almacén de claves para la cuenta de administrador de Verified ID de Microsoft Entra y para la entidad de seguridad de la API de servicio de solicitud que ha creado.
Después de crear el almacén de claves, credenciales verificables genera un conjunto de claves que se usa para proporcionar seguridad de mensajes. Estas claves se almacenan en Key Vault. Use un conjunto de claves para firmar, actualizar y recuperar credenciales verificables.

### Establecimiento de directivas de acceso para el usuario administrador de Verified ID

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Vaya al almacén de claves que usa para este tutorial.

3. En **Configuración**, seleccione **Directivas de acceso**.

4. En **Agregar directivas de acceso**, en **USER**, seleccione la cuenta que usa para seguir este tutorial.

5. En **Permisos clave**, compruebe que los siguientes permisos estén seleccionados: **Obtener**, **Crear**, **Eliminar** y **Firmar**. De forma predeterminada, **Crear** y **Eliminar** ya están habilitados. **Firmar** debe ser el único permiso de claves que tenga que actualizar.

      ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/7c8a92ea-24f1-41e6-9656-869e8486af72)


6. Seleccione **Guardar** para guardar los cambios.

## Configuración de Verified ID

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b4b857a2-24b8-4c3f-9f5c-43d23b58427f)


Para configurar Verified ID, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Busque *Id. comprobado*. A continuación, seleccione **Verified ID**.

3. En el menú de la izquierda, seleccione **Configuración**.

4. En el menú central, seleccione **Definir configuración de la organización**

5. Configure la organización; para ello, proporcione la siguiente información:

    1. **Nombre de la organización**: escriba un nombre que haga referencia a su empresa en las identificaciones verificadas. Los clientes no ven este nombre.

    1. **Dominio de confianza**: introduzca el dominio especificado que se agrega a un punto de conexión de servicio del documento de identidad descentralizada (DID). El dominio es lo que enlaza la DID a algo tangible que el usuario puede saber sobre su empresa. Microsoft Authenticator y otras carteras digitales usan esta información para validar que el DID se ha vinculado al dominio. Si la cartera puede verificar el DID, se muestra un símbolo de verificación. Si la cartera no puede verificar el DID, esta informa al usuario de que la credencial fue emitida por una organización que no ha podido validar.

        >[!IMPORTANT]
        > El dominio no puede ser un redireccionamiento. De lo contrario, la DID y el dominio no se pueden vincular. Asegúrese de usar HTTPS para el dominio. Por ejemplo: `https://did.woodgrove.com`.

    1. **Almacén de claves**: seleccione el almacén de claves que creó anteriormente.

    1. En **Avanzado**, puede elegir el **sistema de confianza** que quiere usar para el inquilino. Puede elegir entre **Web** o **ION**. Web significa que el inquilino usa [did:web](https://w3c-ccg.github.io/did-method-web/) como el método DID e ION significa que usa [did:ion](https://identity.foundation/ion/).

        >[!IMPORTANT]
        > La única manera de cambiar el sistema de confianza es no participar en el servicio de identificaciones verificadas y rehacer la incorporación.

1. Seleccione **Guardar**.  

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b191676e-03e3-423f-aa28-1e3d2887ea07)


### Establecimiento de directivas de acceso para entidades de servicio de Verified ID

Al configurar Verified ID en el paso anterior, las directivas de acceso de Azure Key Vault se actualizan automáticamente para conceder los permisos necesarios a las entidades de servicio para Verified ID.  
Si alguna vez debe restablecer manualmente los permisos, la directiva de acceso debe tener un aspecto similar al siguiente.

| Entidad de servicio | AppId | Permisos de claves |
| -------- | -------- | -------- |
| Servicio de credenciales verificables | bb2a64ee-5d29-4b07-a491-25806dc854d3 | Obtener, firmar |
| Solicitud de servicio de credenciales verificables | 3db474b9-6a0c-4840-96ac-1fceb342124f | Firma |



![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/896b8ea0-b88b-43b8-a93b-4771defedfde)


## Registrar una aplicación en Azure AD

La aplicación debe obtener tokens de acceso cuando quiera llamar a la id. verificada por Microsoft Entra para que pueda emitir o comprobar las credenciales. Para obtener tokens de acceso, registre una aplicación web y conceda permiso de API para el servicio de solicitud de la id. verificada. Por ejemplo, siga estos pasos para una aplicación web:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta de administrador.

1. Si tiene acceso a varios inquilinos, seleccione **Directorio + suscripción**. A continuación, busque y seleccione el servicio **Azure Active Directory**.

1. En **Administrar**, seleccione **Registros de aplicaciones** >  y, luego, **Nuevo registro**.  

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/03ca3d13-5564-4ce2-b42c-f8333bc97fb5)


1. Escriba un nombre para mostrar para la aplicación. Por ejemplo, *verifiable-credentials-app*.

1. Para los **Tipos de cuenta admitidos**, seleccione **Solo cuentas de este directorio organizativo (Solo directorio predeterminado: inquilino único)** .

1. Seleccione **Registrar** para crear la aplicación.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d0a15737-c8a9-4522-990d-1cf6bc12cc1e)


### Concesión de permisos para obtener tokens de acceso

En este paso, concederá permisos a la entidad de servicio de **solicitud de servicio de credenciales verificables**.

Para asignar los permisos necesarios, siga estos pasos:

1. Permanezca en la página de detalles de la aplicación **verifiable-credentials-app**. Seleccione **Permisos de API** > **Agregar un permiso**.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/adf97022-2081-4273-8d61-f2b53628e989)

1. Seleccione **API usadas en mi organización**.

1. Busque la entidad de servicio de la **solicitud de servicio de credenciales verificables** y selecciónelo.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/01691eb2-ab7f-4778-9911-342eb9350f99)

1. Elija **Permiso de aplicación** y expanda **VerifiableCredential.Create.All**.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/3c5e008e-2ba5-417a-90ff-22e8f622ad34)


1. Seleccione **Agregar permisos**.

1. Seleccione **Grant admin consent for\<your tenant name\>**.

Puede optar por conceder permisos de emisión y presentación por separado si prefiere separar los ámbitos a diferentes aplicaciones.

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/640def45-e666-423e-bf69-598e8980b125)

## Registrar id. descentralizado y comprobar la propiedad del dominio

Una vez que Azure Key Vault está configurado y el servicio tiene una clave de firma, debe completar los pasos 2 y 3 de la configuración.

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/40e10177-b025-4d10-b16a-d66c06762c3f)

1. Vaya al servicio de identificaciones verificadas en Azure Portal.  
1. En el menú de la izquierda, seleccione **Configuración**.
1. En el menú central, seleccione **Comprobar la propiedad del dominio**.

# Comprobación de la propiedad de un dominio en un identificador descentralizado (DID)

## Comprobación de la propiedad del dominio y distribución del archivo did-configuration.json

El dominio deberá estar bajo su control y deberá tener el formato `https://www.example.com/`. 

1. En Azure Portal, vaya a la página VerifiedID.

1. Seleccione **Configurar**, después, **Comprobar la propiedad del dominio** y elija **Comprobar** para el dominio.

1. Copie o descargue el archivo `did-configuration.json`.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/342fa12f-2d0d-40cf-a2f9-8796a86f0824)

1. Hospede el archivo `did-configuration.json` en la ubicación especificada. Ejemplo: si especificó el dominio `https://www.example.com`, el archivo deberá hospedarse en esta dirección URL `https://www.example.com/.well-known/did-configuration.json`.
   No puede haber ninguna ruta de acceso adicional en la dirección URL que no sea el nombre `.well-known path`.

1. Cuando `did-configuration.json` esté disponible públicamente en la dirección URL .well-known/did-configuration.json, compruébelo. Para hacerlo, debe presionar el botón **Actualizar estado de comprobación**.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/49a06251-af56-49b2-9059-0bd4ca678da6)

> Resultado: Creó una instancia de Azure Key Vault correctamente, configuró el servicio de Id. comprobado, registró una aplicación en Azure AD y comprobó la propiedad del dominio para el identificador descentralizado (DID).

**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados. 
