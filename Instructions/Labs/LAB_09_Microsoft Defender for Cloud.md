---
lab:
  title: "09: Microsoft\_Defender for Cloud"
  module: Module 03 - Manage security posture by using Microsoft Defender for Cloud
---

# Laboratorio 09: Microsoft Defender for Cloud
# Manual de laboratorio para alumnos

## Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto de un entorno basado en Microsoft Defender for Cloud. En concreto, quiere:

- Configurar características de seguridad mejorada de Microsoft Defender for Cloud para supervisar una máquina virtual.
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

#### Tarea 1: Configuración de características de seguridad mejoradas de Microsoft Defender for Cloud para servidores

En esta tarea, incorporarás y configurarás características de seguridad mejorada de Microsoft Defender for Cloud para servidores.

1. Inicia una sesión del navegador e inicia sesión en la [suscripción a Azure.](https://azure.microsoft.com/en-us/free/?azure-portal=true) al que tienes acceso administrativo.

2. En Azure Portal, en el cuadro de texto Buscar recursos, servicios y documentos de la parte superior de la página, escribe Microsoft Defender for Cloud y pulsa la tecla Entrar 

3. En Microsoft Defender for Cloud, hoja de Administración, ve a Configuración del entorno. Expande las carpetas de configuración del entorno hasta que se muestre la sección suscripción y, después, haz clic en la suscripción para ver los detalles.

4. En la hoja Configuración, en Planes de Defender, expande Protección de cargas de trabajo en la nube (CWP).
  
5. En la lista Plan de protección de cargas de trabajo en la nube (CWP), selecciona Servidores. En la parte derecha de la página, cambia el Estado de Desactivado a Activado y, después, haz clic en Guardar.
  
6. Para revisar los detalles del Plan 2 de Microsoft Defender para servidores, selecciona Cambiar plan >.

>**Nota**: al habilitar el plan de servidores protección de cargas de trabajo en la nube (CWP) de Desactivado a Activado, se habilita el Plan 2 de Microsoft Defender para servidores.

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
