---
title: Instrucciones hospedadas en línea
permalink: index.html
layout: home
ms.openlocfilehash: 7ad42e4108b96ec131049fa1035ab1d112d95d90
ms.sourcegitcommit: 2eb153f2856445e5afaa218a012cb92e3d48f24b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132625728"
---
# <a name="content-directory"></a>Directorio de contenido

Los archivos necesarios para los laboratorios se pueden [descargar aquí](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/archive/master.zip)

A continuación se enumeran hipervínculos a cada uno de los ejercicios de laboratorio y demostraciones.

## <a name="labs"></a>Laboratorios

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| Módulo | Laboratorio |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
