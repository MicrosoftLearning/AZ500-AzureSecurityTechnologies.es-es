---
lab:
  title: "12 - Microsoft\_Defender for Cloud"
  module: Module 04 - Microsoft Defender for Cloud
---

# Laboratorio 12: Microsoft Defender for Cloud
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto de un entorno basado en Microsoft Defender for Cloud. En concreto, quiere:

- Configure Microsoft Defender for Cloud para supervisar una máquina virtual.
- Revise las recomendaciones de Microsoft Defender for Cloud para la máquina virtual.
- Implementar recomendaciones para la configuración de invitado y el acceso a máquinas virtuales Just-In-Time. 
- Revisar cómo se puede usar la puntuación de seguridad para determinar el progreso hacia la creación de una infraestructura más segura.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Implementación de Microsoft Defender for Cloud

## Diagrama de Microsoft Defender for Cloud

![imagen](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c31055cc-de95-41f6-adef-f09d756a68eb)

## Instrucciones

### Ejercicio 1: Implementación de Microsoft Defender for Cloud

En este ejercicio completará las tareas siguientes:

- Tarea 1: Configuración de Microsoft Defender for Cloud
- Tarea 2: Revisión de las recomendaciones de Microsoft Defender for Cloud
- Tarea 3: Implementación de las recomendaciones de Microsoft Defender for Cloud para habilitar el acceso a máquinas virtuales Just-In-Time

#### Tarea 1: Configuración de Microsoft Defender for Cloud

En esta tarea, incorporará y configurará Microsoft Defender for Cloud.

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

2. En Azure Portal, en el cuadro de texto **Buscar recursos, servicios y documentos** de la parte superior de la página, escriba **Microsoft Defender for Cloud** y presione la tecla **Entrar**.

3. En el panel de navegación izquierdo, seleccione **Introducción**. En el panel **Microsoft Defender for Cloud \| Introducción**, clic en **Actualizar**.
     
4. En la hoja **Microsoft Defender for Cloud \| Introducción**, dentro de la pestaña Instalar agentes, desplácese hacia abajo y haga clic en **Instalar agentes**. 

5. En el panel **Microsoft Defender for Cloud \| Introducción**, dentro de la pestaña **Actualizar**, desplácese hacia abajo hasta que la sección **Seleccionar áreas de trabajo con características de seguridad mejoradas** esté visible, active el **plan de Microsoft Defender** seleccionando el área de trabajo de Log Analytics y, a continuación, haga clic en el botón azul grande Actualizar.  

    >**Nota**: Revise todas las características que están disponibles como parte de los planes de Microsoft Defender. 

6. Vaya a **Microsoft Defender for Cloud** y, en el panel de navegación izquierdo de la sección Administración, haga clic en **Configuración del entorno**.

7. En la hoja **Microsoft Defender for Cloud\| Configuración del entorno**, desplácese hacia abajo, expanda hasta que aparezca la suscripción y haga clic en la suscripción correspondiente. 

8. En la hoja **Configuración \| Planes de Defender**, seleccione **Habilitar todos los planes** y haga clic en **Guardar**.

9. Vuelva a la hoja **Microsoft Defender for Cloud \| Configuración de entorno**, expanda hasta que aparezca la suscripción y haga clic en la entrada que representa el área de trabajo de Log Analytics que ha creado en el laboratorio anterior.

10. En la hoja **Configuración \| Planes de Defender**, asegúrese de que todas las opciones estén activadas. Si es necesario, haga clic en **Habilitar todos los planes** y, después, haga clic en **Guardar**.

11. Seleccione **Recopilación de datos** en la hoja **Configuración\| Planes de Defender**. Haga clic en **Todos los eventos** y **Guardar**.

#### Tarea 2: Revisión de las recomendaciones de Microsoft Defender for Cloud

En esta tarea, revisará las recomendaciones de Microsoft Defender for Cloud. 

1. En Azure Portal, vuelva al panel **Microsoft Defender for Cloud \| Información general**. 

2. En el panel **Microsoft Defender for Cloud \| Información general**, revise el elemento **Puntuación de seguridad**.

    >**Nota**: Registre la puntuación actual si está disponible.

3. Vuelva a la hoja **Microsoft Defender for Cloud \| Información general** y seleccione **Recursos evaluados**.

4. En la hoja **Inventario**, haga clic en la entrada **myVM**.

    >**Nota**: Es posible que tenga que esperar unos minutos y actualizar la página del explorador para que aparezca la entrada.
    
5. En el panel **Estado de los recursos**, en la pestaña **Recomendaciones**, revise la lista de recomendaciones de **myVM**.

#### Tarea 3: Implementación de las recomendaciones de Microsoft Defender for Cloud para habilitar el acceso a máquinas virtuales Just-In-Time

En esta tarea, implementará las recomendaciones de Microsoft Defender for Cloud para habilitar el acceso a máquinas virtuales Just-In-Time en la máquina virtual. 

1. En Azure Portal, vuelva a la hoja **Microsoft Defender for Cloud \| Información general** y haga clic en **Protecciones de cargas de trabajo** en el mosaico **Seguridad en la nube** en el panel de navegación izquierdo.

2. En la hoja **Microsoft Defender for Cloud \| Protecciones de cargas de trabajo**, desplácese hacia abajo hasta la sección **Protección avanzada** y haga clic en el icono **Acceso de máquina virtual Just-In-Time**.

3. En la hoja **Acceso de máquina virtual Just-In-Time**, en la sección **Máquinas virtuales**, seleccione **Sin configurar** y luego seleccione la casilla para la entrada **myVM**.

    >**Nota**: Es posible que tenga que esperar unos minutos y actualizar la página del explorador y vuelva a seleccionar **No configurada** para que aparezca la entrada.

4. Haga clic en la opción **Habilitar JIT en una máquina virtual** en el extremo derecho de la sección **Máquinas virtuales**.

5. En el panel **Configuración de acceso a la VM JIT**, en el extremo derecho de la fila que hace referencia al puerto **22**, haga clic en el botón de puntos suspensivos y, a continuación, haga clic en **Eliminar**.

6. En el panel **Configuración de acceso a la MV JIT**, seleccione **Guardar**.

    >**Nota**: Para supervisar el progreso de la configuración, haga clic en el icono **Notificaciones** de la barra de herramientas y vea el panel **Notificaciones**. 

    >**Nota**: La implementación de recomendaciones en este laboratorio puede tardar algún tiempo en reflejarse en la puntuación segura. Compruebe periódicamente la puntuación de seguridad para determinar el efecto de la implementación de estas características. 

> Ha instalado Microsoft Defender for Cloud y ha implementado recomendaciones de máquina virtual. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.
