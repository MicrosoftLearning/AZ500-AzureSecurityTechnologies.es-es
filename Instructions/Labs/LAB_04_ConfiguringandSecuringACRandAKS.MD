---
lab:
  title: '04: Configuración y protección de ACR y AKS'
  module: 'Module 01 - Secure compute, storage, and databases'
---

# Laboratorio 04: Configuración y protección de ACR y AKS
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que implemente una prueba de concepto con Azure Container Registry y Azure Kubernetes Service. En concreto, la prueba de concepto debe demostrar lo siguiente:

- Uso de Dockerfile para compilar una imagen.
- Uso de Azure Container Registry para almacenar imágenes.
- Configuración de una instancia de Azure Kubernetes Service.
- Protección de aplicaciones de contenedor y acceso a estas tanto de forma interna como externa. 

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Configuración y protección de ACR y AKS

## Configuración y protección de ACR y AKS

![imagen](https://user-images.githubusercontent.com/91347931/157532250-1104a829-792a-4b6d-beff-fe976e2d5d4b.png)

## Instrucciones

## Archivos de laboratorio:

- **\\Allfiles\\Labs\\09\\nginxexternal.yaml**
- **\\Allfiles\\Labs\\09\\nginxinternal.yaml**

### Ejercicio 1: Configuración y protección de ACR y AKS

### Tiempo estimado: 45 minutos

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

En este ejercicio completará las tareas siguientes:

- Tarea 1: Crear una instancia de Azure Container Registry
- Tarea 2: Crear un Dockerfile, compilar un contenedor e insertarlo en Azure Container Registry
- Tarea 3: Crear un clúster de Azure Kubernetes Service
- Tarea 4: Conceder permisos de clúster de AKS para acceder a ACR
- Tarea 5: Implementar un servicio externo en AKS
- Tarea 6: Comprobar que puede acceder a un servicio hospedado en AKS externo
- Tarea 7: Implementar un servicio interno en AKS
- Tarea 8: Comprobar que puede acceder a un servicio hospedado en AKS interno

#### Tarea 1: Crear una instancia de Azure Container Registry

En esta tarea, creará un grupo de recursos para el laboratorio en Azure Container Registry.

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicia sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usas para este laboratorio y el rol Administrador global en el inquilino de Microsoft Entra asociado a esa suscripción.

2. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. Si se le solicita, haga clic en **Bash** y en **Crear almacenamiento**.

3. Asegúrese de que **Bash** está seleccionado en el menú desplegable de la esquina superior izquierda del panel de Cloud Shell.

4. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para crear un grupo de recursos para este laboratorio:

    ```sh
    az group create --name AZ500LAB09 --location eastus
    ```

5. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para comprobar que se ha creado el grupo de recursos:

    ```
    az group list --query "[?name=='AZ500LAB09']" -o table
    ```

6. En la sesión de Bash en el panel Cloud Shell, ejecute lo siguiente para crear una instancia de Azure Container Registry (ACR) (el nombre de la instancia de ACR debe ser único globalmente): 

    ```sh
    az acr create --resource-group AZ500LAB09 --name az500$RANDOM$RANDOM --sku Basic
    ```

7. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para confirmar que se ha creado la nueva instancia de ACR:

    ```sh
    az acr list --resource-group AZ500LAB09
    ```

    >**Nota**: Registre el nombre del ACR. Lo necesitará en la próxima tarea.

#### Tarea 2: Crear un Dockerfile, compilar un contenedor e insertarlo en Azure Container Registry

En esta tarea, creará un Dockerfile, compilará una imagen a partir del Dockerfile e implementará la imagen en la instancia de ACR. 

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para crear un Dockerfile para crear una imagen basada en Nginx: 

    ```sh
    echo FROM nginx > Dockerfile
    ```

2. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para compilar una imagen a partir del Dockerfile e insertar la imagen en la nueva instancia de ACR. 

    >**Nota**: El punto final es obligatorio al final de la línea de comandos. Designa el directorio actual como la ubicación de Dockerfile. 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az acr build --resource-group AZ500LAB09 --image sample/nginx:v1 --registry $ACRNAME --file Dockerfile .
    ```

    >**Nota**: Espere a que el comando se complete correctamente. Esto puede tardar unos 2 minutos.

3. Cierre el panel de Cloud Shell.

4. En Azure Portal, vaya al grupo de recursos **AZ500Lab09** y, en la lista de recursos, haga clic en la entrada que representa la instancia de Azure Container Registry que aprovisionó en la tarea anterior.

5. En la hoja Registro de contenedor, en la sección **Servicios**, haga clic en **Repositorios**. 

6. Compruebe que la lista de repositorios incluye la nueva imagen de contenedor denominada **sample/nginx**.

7. Haga clic en la entrada **sample/nginx** y compruebe la presencia de la etiqueta **v1** que identifica la versión de la imagen.

8. Haga clic en la entrada **v1** para ver el manifiesto de imagen.

    >**Nota**: El manifiesto incluye la síntesis del mensaje sha256, la fecha de creación del manifiesto y las entradas de la plataforma. 

#### Tarea 3: Crear un clúster de Azure Kubernetes Service

En esta tarea, creará un servicio de Azure Kubernetes y revisará los recursos implementados. 

1. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página, escriba **Servicios de Kubernetes** y presione la tecla **Entrar**.

2. En el panel **Servicios de Kubernetes**, haga clic en **+ Crear** y, en el menú desplegable, haga clic en **+ Crear clúster de Kubernetes**.

3. En la pestaña **Aspectos básicos** de la hoja **Crear clúster de Kubernetes**, seleccione **Configuración preestablecida del clúster** y seleccione **Desarrollo/pruebas ($)** . Ahora, especifique las siguientes opciones de configuración (deje las demás con los valores predeterminados):

    |Configuración|Valor|
    |----|----|
    |Suscripción|nombre de la suscripción de Azure que usa en este laboratorio|
    |Resource group|**AZ500LAB09**|
    |Nombre del clúster de Kubernetes|**MyKubernetesCluster**|
    |Region|**(EE. UU.) Este de EE. UU.**|
    |Zonas de disponibilidad |**None**|
    |Método de escala|**Manual**|
    |Recuento de nodos|**1**|

4. Haga clic en **Siguiente: Grupos de nodos >** y, en la pestaña **Grupos de nodos** de la hoja **Crear un clúster de Kubernetes**, configure las siguientes opciones (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |----|----|
    |Habilitar nodos virtuales|Casilla desactivada|
    
5. Haga clic en **Siguiente: Acceso >** , en la pestaña **Acceso** de la hoja **Crear un clúster de Kubernetes**, acepte los valores predeterminados y haga clic en **Siguiente: Redes >** . 

6. En la pestaña **Redes** de la hoja **Crear un clúster de Kubernetes**, especifique las siguientes opciones (deje las demás con los valores predeterminados):

    |Configuración|Value|
    |----|----|
    |Configuración de red|**Azure CNI**|
    |Prefijo del nombre DNS|**Deje el valor predeterminado**|

    >**Nota**: AKS se puede configurar como un clúster privado. Se asigna una IP privada al servidor de API para garantizar que el tráfico de red entre el servidor de API y los grupos de nodos permanece solo en la red privada. Para obtener más información, visite la página [Creación de un clúster privado de Azure Kubernetes Service](https://docs.microsoft.com/en-us/azure/aks/private-clusters).

7. Haga clic en **Siguiente: Integraciones >** y, en la pestaña **Integraciones** de la hoja **Crear un clúster de Kubernetes**, establezca **Container monitoring** (Supervisión de contenedores) en **Deshabilitado**. 

    >**Nota**: En escenarios de producción, le gustaría habilitar la supervisión. La supervisión está deshabilitada en este caso, ya que no se trata en el laboratorio. 

8. Haga clic en **Revisar + crear** y, después, en **Crear**.

    >**Nota**: Espere a que la implementación se complete. Esto puede tardar unos 10 minutos.

9. Cuando se complete la implementación, en Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** de la parte superior de la página, escriba **Grupos de recursos** y presione la tecla **Entrar**.

10. En la hoja **Grupos de recursos**, en la lista de grupos de recursos, observe el nuevo grupo de recursos denominado **MC_AZ500LAB09_MyKubernetesCluster_eastus**, que contiene los componentes de los nodos de AKS. Revise los recursos de este grupo de recursos. 
    
11. Vuelva a la hoja **Grupos de recursos** y haga clic en la entrada **AZ500LAB09**. 

    >**Nota**: En la lista de recursos, observe el clúster de AKS y la red virtual correspondiente.

12. En Azure Portal, abra una sesión de Bash en Cloud Shell. 

    >**Nota**: Asegúrese de que **Bash** esté seleccionado en el menú desplegable superior izquierdo del panel de Cloud Shell.

13. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para conectarse al clúster de Kubernetes:

    ```sh
    az aks get-credentials --resource-group AZ500LAB09 --name MyKubernetesCluster
    ```

14. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para enumerar los nodos del clúster de Kubenetes: 

    ```sh
    kubectl get nodes
    ```

    >**Nota**: Compruebe que el **Estado** del nodo del clúster aparece como **Listo**.

#### Tarea 4: Conceder permisos al clúster de AKS para acceder a ACR y administrar su red virtual

En esta tarea, concederá al clúster de AKS permiso para acceder a ACR y administrar su red virtual. 

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para configurar el clúster de AKS para usar la instancia de Azure Container Registry que creó anteriormente en este laboratorio. 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az aks update -n MyKubernetesCluster -g AZ500LAB09 --attach-acr $ACRNAME
    ```

    >**Nota**: Este comando concede la asignación de roles "acrpull" a la instancia de ACR. 

    >**Nota**: Este comando puede tardar unos minutos en completarse. 

2. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para conceder al clúster de AKS el rol Colaborador para su red virtual. 

    ```sh
    RG_AKS=AZ500LAB09

    RG_VNET=MC_AZ500LAB09_MyKubernetesCluster_eastus    

    AKS_VNET_NAME=aks-vnet-30198516
    
    AKS_CLUSTER_NAME=MyKubernetesCluster
    
    AKS_VNET_ID=$(az network vnet show --name $AKS_VNET_NAME --resource-group $RG_VNET --query id -o tsv)
    
    AKS_MANAGED_ID=$(az aks show --name $AKS_CLUSTER_NAME --resource-group $RG_AKS --query identity.principalId -o tsv)
    
    az role assignment create --assignee $AKS_MANAGED_ID --role "Contributor" --scope $AKS_VNET_ID
    ```

#### Tarea 5: Implementar un servicio externo en AKS

En esta tarea, descargará los archivos de manifiesto, editará el archivo YAML y aplicará los cambios al clúster. 

1. En la sesión de Bash en el panel de Cloud Shell, haga clic en el icono **Cargar/Descargar archivos**, en el menú desplegable, haga clic en **Cargar**; en el cuadro de diálogo **Abrir**, navegue hasta la ubicación donde descargó los archivos de laboratorio, seleccione **\\Allfiles\\Labs\\09\\nginxexternal.yaml** y haga clic en **Abrir**. A continuación, seleccione **\\Allfiles\\Labs\\09\\nginxinternal.yaml** y haga clic en **Abrir**.

2. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para identificar el nombre de la instancia de Azure Container Registry:

    ```sh
    echo $ACRNAME
    ```

    >**Nota**: Registre el nombre de la instancia de Azure Container Registry. Lo necesitará más adelante en esta tarea.

3. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para abrir el archivo nginxexternal.yaml, para poder editar su contenido. 

    ```sh
    code ./nginxexternal.yaml
    ```

    >**Nota**: Este es el archivo .yaml *externo*.

4. En el panel del editor, desplácese hacia abajo hasta la **línea 24** y reemplace el marcador de posición **`<ACRUniquename>`** por el nombre de la instancia de ACR.

5. En el panel del editor, en la esquina superior derecha, haga clic en el icono de **puntos suspensivos**, haga clic en **Guardar** y, a continuación, haga clic en **Cerrar editor**. 

6. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para aplicar el cambio en el clúster:

    ```sh
    kubectl apply -f nginxexternal.yaml
    ```

7. En la sesión de Bash en el panel de Cloud Shell, revise la salida del comando que ejecutó en la tarea anterior para comprobar que se han creado la implementación y el servicio correspondiente. 

    ```
    deployment.apps/nginxexternal created
    service/nginxexternal created
    ```

#### Tarea 6: Comprobar que puede acceder a un servicio hospedado en AKS externo

En esta tarea, compruebe que se puede acceder externamente al contenedor mediante la dirección IP pública.

1. En la sesión de Bash del panel de Cloud Shell, ejecute lo siguiente para recuperar la información sobre el servicio nginxexternal, incluidos el nombre, el tipo, las direcciones IP y los puertos. 

    ```sh
    kubectl get service nginxexternal
    ```

2. En la sesión de Bash en el panel de Cloud Shell, revise la salida y registre el valor en la columna External-IP. Esta información la necesitará en el siguiente paso. 

3. Abra una nueva pestaña del explorador y vaya a la dirección IP que identificó en el paso anterior.

4. Asegúrese de la página **Bienvenido a nginx** se muestra. 

#### Tarea 7: Implementar un servicio interno en AKS

En esta tarea, implementará el servicio de acceso interno en AKS. 

1. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para abrir el archivo nginxinternal.yaml, para poder editar su contenido. 

    ```sh
    code ./nginxinternal.yaml
    ```

    >**Nota**: Este es el archivo .yaml *interno*.

2. En el panel del editor, desplácese hacia abajo hasta la línea que contiene la referencia a la imagen de contenedor y reemplace el marcador de posición **`<ACRUniquename>`** por el nombre de la instancia de ACR.

3. En el panel del editor, en la esquina superior derecha, haga clic en el icono de **puntos suspensivos**, haga clic en **Guardar** y, a continuación, haga clic en **Cerrar editor**. 

4. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para aplicar el cambio en el clúster:

    ```sh
    kubectl apply -f nginxinternal.yaml
    ```

5.  En la sesión de Bash en el panel de Cloud Shell, revise la salida para comprobar que se han creado la implementación y el servicio:

    ```
    deployment.apps/nginxinternal created
    service/nginxinternal created
    ```

6. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para recuperar la información sobre el servicio nginxinternal, incluidos el nombre, el tipo, las direcciones IP y los puertos. 

    ```sh
    kubectl get service nginxinternal
    ```

7. En la sesión de Bash en el panel de Cloud Shell, revise la salida. La dirección de External-IP es, en este caso, una dirección IP privada. Si está en estado **Pendiente**, vuelva a ejecutar el comando anterior.

    >**Nota**: Registre esta dirección IP. Lo necesitará en la próxima tarea. 

    >**Nota**: Para acceder al punto de conexión de servicio interno, se conectará de manera interactiva a uno de los pods que se ejecutan en el clúster. 

    >**Nota**: También puede usar la dirección de CLUSTER-IP.

#### Tarea 8: Comprobar que puede acceder a un servicio hospedado en AKS interno

En esta tarea, usará uno de los pods que se ejecutan en el clúster de AKS para acceder al servicio interno. 

1. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para enumerar los pods del espacio de nombres predeterminado en el clúster de AKS:

    ```sh
    kubectl get pods
    ```

2. En la lista de pods, copie la primera entrada en la columna **NAME**.

    >**Nota**: Este es el pod que usará en los pasos posteriores.

3. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para conectarse de manera interactiva al primer pod (reemplace el marcador de posición `<pod_name>` por el nombre que copió en el paso anterior):

    ```sh
    kubectl exec -it <pod_name> -- /bin/bash
    ```

4. En la sesión de Bash en el panel de Cloud Shell, ejecute lo siguiente para comprobar que el sitio web de nginx está disponible a través de la dirección IP privada del servicio (reemplace el marcador de posición `<internal_IP>` por la dirección IP que registró en la tarea anterior):

    ```sh
    curl http://<internal_IP>
    ```

5. Cierre el panel de Cloud Shell.

> Resultado: ha configurado y protegido ACR y AKS.


**Limpieza de recursos**

> No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no se generarán costes inesperados.

1. Haga clic en el primer icono de la esquina superior derecha de Azure Portal para abrir Cloud Shell. 

2. En el menú desplegable superior izquierdo del panel de Cloud Shell, seleccione **PowerShell** y, cuando se le solicite, haga clic en **Confirmar**.

3. En la sesión de PowerShell en el panel de Cloud Shell, ejecute lo siguiente para quitar los grupos de recursos que creó en este laboratorio:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB09" -Force -AsJob
    ```

4.  Cierre el panel de **Cloud Shell**. 
