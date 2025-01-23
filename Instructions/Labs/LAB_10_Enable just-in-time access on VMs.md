---
lab:
  title: '10: Habilitar acceso Just-In-Time en máquinas virtuales'
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# Laboratorio 10: Habilitación de acceso Just-In-Time en máquinas virtuales

# Manual de laboratorio para alumnos

## Escenario del laboratorio

Como ingeniero de seguridad de Azure en una empresa de servicios financieros, eres responsable de proteger los recursos de Azure, incluidas las máquinas virtuales (VM) que hospedan aplicaciones críticas. El equipo de seguridad ha identificado que el acceso abierto continuo a las máquinas virtuales aumenta el riesgo de ataques por fuerza bruta y acceso no autorizado. Para mitigar esto, el director de seguridad de la información (CISO) ha solicitado que habilites el acceso a máquinas virtuales Just-In-Time (JIT) en una máquina virtual de Azure específica que se usa para procesar transacciones financieras.

## Objetivos del laboratorio

En este laboratorio completará los ejercicios siguientes:

- Ejercicio 1: Habilitar JIT en tus máquinas virtuales desde Azure Portal.

- Ejercicio 2: Solicitar acceso a una VM con JIT habilitado desde Azure Portal.

## Instrucciones del ejercicio 

### Ejercicio 1: Habilitar JIT en tus máquinas virtuales desde máquinas virtuales de Azure

>**Nota**: puedes habilitar JIT en una máquina virtual desde las páginas de máquinas virtuales de Azure de Azure Portal.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **máquinas virtuales**. En los resultados de la búsqueda, seleccione **Máquinas virtuales**.

2. Seleccione **myVM**.
 
3. Selecciona **Configuración** en la sección **Configuración** de **myVM.**
   
4. En **Acceso de máquina virtual Just-In-Time**, selecciona **Habilitar Just-In-Time.**

5. En **Acceso de máquina virtual Just-In-Time,** haz clic en el vínculo que dice **Abrir Microsoft Defender for Cloud.**

6. De forma predeterminada, el acceso Just-In-Time para la máquina virtual usa esta configuración:

   - Máquinas de Windows
   
     - Puerto RDP: 3389
     - Acceso máximo permitido: tres horas
     - Direcciones IP de origen permitidas: cualquiera

   - Equipos con Linux
     - Puerto SSH: 22
     - Acceso máximo permitido: tres horas
     - Direcciones IP de origen permitidas: cualquiera
   
7. De forma predeterminada, el acceso Just-In-Time para la máquina virtual usa esta configuración:

   - En la pestaña **Configurado**, haz clic con el botón derecho en la máquina virtual a la que deseas agregar un puerto y selecciona Editar.

   ![imagen](https://github.com/user-attachments/assets/aa4ded55-c5b1-4d40-b5a0-a4c33b9eb81b)
   
   - En **Configuración de acceso de máquina virtual JIT** , puedes modificar la configuración existente de un puerto protegido, o bien agregar un puerto personalizado.
   - Cuando hayas terminado de editar los puertos, selecciona **Guardar**.   

### Ejercicio 2: Solicitar acceso a una máquina virtual habilitada para JIT desde la página de conexión de la máquina virtual de Azure.

>**Nota**: cuando una máquina virtual tiene JIT habilitado, tienes que solicitar acceso para conectarte a ella. Puedes solicitar acceso de cualquiera de las maneras admitidas, independientemente de cómo hayas habilitado JIT.
   
1. En Azure Portal, abre las páginas de máquinas virtuales.

2. Selecciona la máquina virtual a la que deseas conectarte y abre la página **Conectar**.

   - Azure comprueba si JIT está habilitado en esa máquina virtual.

        - Si JIT no está habilitado para la máquina virtual, se te pedirá que lo habilites.
    
        - Si JIT está habilitado, selecciona **Solicitar acceso** para pasar una solicitud de acceso con la dirección IP de solicitud, el intervalo de tiempo y los puertos que se configuraron para esa máquina virtual.
    
   ![imagen](https://github.com/user-attachments/assets/f5d0b67c-7731-4261-b0eb-a56c505dadd4)

> **Resultados**: has explorado varios métodos sobre cómo habilitar JIT en las máquinas virtuales y cómo solicitar acceso a las máquinas virtuales que tienen JIT habilitado en Microsoft Defender for Cloud.
