---
lab:
  title: '14: Azure Security Center'
  module: Module 04 - Manage Security Operations
ms.openlocfilehash: 33134a5d1f7f5138083c1889c04f3296c3f05586
ms.sourcegitcommit: 2eb153f2856445e5afaa218a012cb92e3d48f24b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625735"
---
# <a name="lab-14-azure-security-center"></a>Laboratorio 14: Azure Security Center
# <a name="student-lab-manual"></a>Manual de laboratorio para alumnos

## <a name="lab-scenario"></a>Escenario del laboratorio

Se le ha pedido que cree una prueba de concepto de un entorno basado en Security Center. En concreto, quiere:

- Configurar Security Center para supervisar una máquina virtual.
- Revisar las recomendaciones de Security Center para la máquina virtual.
- Implementar recomendaciones para la configuración de invitado y el acceso a máquinas virtuales Just-In-Time. 
- Revisar cómo se puede usar la puntuación de seguridad para determinar el progreso hacia la creación de una infraestructura más segura.

> Para todos los recursos de este laboratorio, se usa la región **Este de EE. UU.** Compruebe con el instructor que esta es la región que se va a usar para la clase. 

## <a name="lab-objectives"></a>Objetivos del laboratorio

En este laboratorio completará el ejercicio siguiente:

- Ejercicio 1: Implementación de Security Center

### <a name="exercise-1-implement-security-center"></a>Ejercicio 1: Implementación de Security Center

En este ejercicio completará las tareas siguientes:

- Tarea 1: Configuración de Security Center
- Tarea 2: Revisión de las recomendaciones de Security Center
- Tarea 3: Implementación de la recomendación de Security Center para habilitar el acceso a máquinas virtuales Just-In-Time

#### <a name="task-1-configure-security-center"></a>Tarea 1: Configuración de Security Center

En esta tarea, incorporará y configurará Security Center.

1. Inicie sesión en Azure Portal **`https://portal.azure.com/`** .

    >**Nota**: Inicie sesión en Azure Portal con una cuenta que tenga el rol Propietario o Colaborador en la suscripción de Azure que usa para este laboratorio.

1. En Azure Portal, use el cuadro de texto **Buscar recursos, servicios y documentos** en la parte superior de la página de Azure Portal, escriba **Security Center** y presione la tecla **Entrar**.

1. En el panel **Security Center \| Introducción**, haga clic en **Actualizar** y, a continuación, haga clic en **Instalar agentes**.
     
1. En el panel **Security Center \| Introducción**, en el menú vertical del lado izquierdo, en la sección **Administración**, haga clic en **Precios y configuración**.

1. En el panel **Security Center \| Precios y configuración**, haga clic en la entrada que representa su suscripción y, en el panel **Configuración \| Planes de Azure Defender**, asegúrese de que **Azure Defender activado** está seleccionado. 

    >**Nota**: Revise todas las características que están disponibles como parte del nivel de Azure Defender y asegúrese de que Azure Defender está activado para cada tipo de recurso. 

1. Si ha realizado cambios, haga clic en **Guardar**.

1. En el panel **Configuración \| Planes de Azure Defender**, seleccione **Habilitar todo** y haga clic en **Guardar**.

1. En el panel **Configuración \| Planes de Azure Defender**, en la barra de menús vertical del lado izquierdo, haga clic en **Aprovisionamiento automático**.

1. En el panel **Configuración \| Aprovisionamiento automático**, asegúrese de que **Aprovisionamiento automático** está establecido en **On** para el primer elemento **Agente de Log Analytics para máquinas virtuales de Azure**. 

1. En el panel **Configuración \| Aprovisionamiento automático**, en el menú vertical del lado izquierdo, haga clic en **Automatización del flujo de trabajo**.

1. En el panel **Configuración \| Automatización del flujo de trabajo**, haga clic en **+ Adición de automatización de flujo de trabajo**.

1. En el panel **Adición de automatización de flujo de trabajo**, revise la configuración disponible. 

    >**Nota**: Puede desencadenar alertas de detección de amenazas basadas en acciones y recomendaciones de Security Center. También puede configurar una acción basada en Logic Apps. 

1. En el panel **Adición de automatización de flujo de trabajo**, haga clic en **Cancelar**.

    >**Nota**: Security Center proporciona mucha información sobre las máquinas virtuales, como el estado de actualización del sistema, las configuraciones de seguridad del sistema operativo y la protección del punto de conexión.

1. Vuelva al panel **Security Center \| Precios y configuración** y haga clic en la entrada que representa el área de trabajo de Log Analytics que creó en el laboratorio anterior.

1. En el panel **Configuración \| Planes de Azure Defender**, asegúrese de que **Azure Defender activado** está seleccionado y haga clic en **Guardar**.


#### <a name="task-2-review-the-security-center-recommendation"></a>Tarea 2: Revisión de la recomendación de Security Center

En esta tarea, revisará las recomendaciones de Security Center. 

1. En Azure Portal, vuelva al panel **Security Center \| Introducción**. 

1. En el panel **Security Center \| Introducción**, seleccione el elemento **Puntuación de seguridad**.

    >**Nota**: Registre la puntuación actual si está disponible.

1. Vuelva al panel **Security Center \| Introducción** y seleccione **Recursos evaluados**.

1. En el panel **Inventario**, seleccione la entrada **myVM**.

    >**Nota**: Es posible que tenga que esperar unos minutos y actualizar la página del explorador para que aparezca la entrada.
    
1. En el panel **Estado de los recursos**, en la pestaña **Recomendaciones**, revise la lista de recomendaciones de **myVM**.


#### <a name="task-3-implement-the-security-center-recommendation-to-enable-just-in-time-vm-access"></a>Tarea 3: Implementación de la recomendación de Security Center para habilitar el acceso a máquinas virtuales Just-In-Time

En esta tarea, implementará la recomendación de Security Center de habilitar el acceso a máquinas virtuales Just-In-Time en la máquina virtual. 

1. En Azure Portal, vuelva al panel **Security Center \| Introducción** y seleccione el elemento **Azure Defender**.

1. En el panel **Azure Defender**, en la sección **Protección avanzada**, haga clic en el elemento **Acceso a máquinas virtuales Just-In-Time** y, en el panel **Acceso a máquinas virtuales Just-In-Time**, haga clic en **Try Just-In-Time VM access** (Probar acceso a máquinas virtuales Just-In-Time).

1. En **Acceso a máquinas virtuales Just-In-Time**, seleccione **Sin configurar** y, a continuación, haga clic en la entrada **myVM**.

    >**Nota**: Es posible que tenga que esperar unos minutos antes de que la entrada **myVM** esté disponible.

1. Seleccione **Habilitar JIT en 1 máquina virtual**.

1. En el panel **Configuración de acceso a la VM JIT**, en el extremo derecho de la fila que hace referencia al puerto **22**, haga clic en el botón de puntos suspensivos y, a continuación, haga clic en **Eliminar**.

1. En el panel **Configuración de acceso a la MV JIT**, seleccione **Guardar**.

    >**Nota**: Para supervisar el progreso de la configuración, haga clic en el icono **Notificaciones** de la barra de herramientas y vea el panel **Notificaciones**. 

    >**Nota**: La implementación de recomendaciones en este laboratorio puede tardar algún tiempo en reflejarse en la puntuación segura. Compruebe periódicamente la puntuación de seguridad para determinar el efecto de la implementación de estas características. 

> Resultados: ha incorporado Security Center y ha implementado recomendaciones de máquinas virtuales. 


>**Nota**: No quite los recursos de este laboratorio, ya que son necesarios para el laboratorio de Azure Sentinel.
