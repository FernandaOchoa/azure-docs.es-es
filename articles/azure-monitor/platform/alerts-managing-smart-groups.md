---
title: Administración de grupos inteligentes
description: Administración de grupos inteligentes creados en sus instancias de alerta
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018
ms.openlocfilehash: 763bfefcf71b0be43722b99f31641015a5991607
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105845"
---
# <a name="manage-smart-groups"></a>Administración de grupos inteligentes

Los [grupos inteligentes](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json) usan algoritmos de aprendizaje automático para agrupar las alertas en función de su concurrencia o similitud, a fin de que el usuario ahora pueda administrar grupos inteligentes en lugar de tener que administrar individualmente cada alerta. En este artículo le guiaremos por el proceso de obtener acceso a grupos inteligentes y de usarlos en Azure Monitor.

1. Para ver los grupos inteligentes creados para sus instancias de alerta puede seguir una de estas opciones:

     1. Hacer clic en **Grupos inteligentes** en la página **Resumen de alertas**.    
    ![Captura de pantalla que muestra la página de resumen de alertas con los grupos inteligentes resaltados.](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. Hacer clic en Alertas por grupos inteligentes en la página de todas las alertas.   
     ![Captura de pantalla que muestra la página de todas las alertas con alerta por grupo inteligente resaltado.](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. Esto le llevará a la vista con la lista de todos los grupos inteligentes creados mediante las instancias de alerta. En lugar de examinar varias alertas, ahora puede usar los grupos inteligentes.   
![Captura de pantalla que muestra la página de todas las alertas.](./media/alerts-managing-smart-groups/sg-list.jpg)

3. Al hacer clic en cualquier grupo inteligente, se abre la página de detalles, donde puede ver el motivo de la agrupación, junto con las alertas que lo componen. Esta agregación le permite lidiar con un único grupo inteligente, en lugar de examinar varias alertas.   
![Captura de pantalla que muestra la página de detalles.](./media/alerts-managing-smart-groups/sg-details.jpg)