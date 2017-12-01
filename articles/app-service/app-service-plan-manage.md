---
title: "Gérer un plan App Service dans Azure | Microsoft Docs"
description: "Découvrez comment effectuer différentes tâches pour gérer un plan App Service."
keywords: "app service, azure app service, mise à l’échelle, plan app service, changer, créer, gérer, gestion"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 5369946b1e8a4851ee940cf6fe91a1bdb94db5f3
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gérer un plan App Service dans Azure

Un [plan App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) fournit les ressources nécessaires à l’exécution d’une application App Service. Ce guide de procédures montre comment gérer un plan App Service. 

## <a name="create-an-app-service-plan"></a>Créer un plan App Service

> [!TIP]
> Si vous avez un environnement App Service, consultez [Créer un plan App Service dans un environnement App Service](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Vous pouvez créer un plan App Service vide ou dans le cadre de la création d’une application.

Dans le [portail Azure](https://portal.azure.com), cliquez sur **Nouveau** > **Web et mobilité**, puis sélectionnez **Application web** ou tout autre type d’application App Service.

![Créer une application dans le portail Azure.][createWebApp]

Vous pouvez ensuite sélectionner un plan App Service existant ou créer un plan pour la nouvelle application.

 ![Créer un plan App Service.][createASP]

Pour créer un plan App Service, cliquez sur **[+] Créer nouveau**, saisissez le nom du **plan App Service**, puis sélectionnez un **emplacement**. Cliquez sur **Niveau de tarification**, puis sélectionnez un niveau de tarification approprié pour le service. Sélectionnez **Afficher tout** pour afficher davantage d’options de tarification, telles que **Gratuit** et **Partagé**. 

Une fois que vous avez sélectionné le niveau de tarification, cliquez sur le bouton **Sélectionner** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Déplacer une application vers un autre plan App Service

Vous pouvez déplacer une application vers un autre plan App Service tant que le plan source et le plan cible se trouvent dans les _mêmes groupe de ressources et région géographique_.

Pour déplacer une application vers un autre plan, accédez à l’application à déplacer dans le [portail Azure](https://portal.azure.com).

Dans le **Menu**, recherchez la section **Plan App Service**.

Sélectionnez **Changer le plan App Service** pour démarrer le processus.

**Modifier le Plan App Service** ouvre le sélecteur de **plan App Service**. Sélectionnez le plan vers lequel déplacer cette application. 

> [!IMPORTANT]
> La page **Sélectionner un plan App Service** est filtrée selon les critères suivants : 
> - Il existe dans le même groupe de ressources 
> - Il existe dans la même région géographique 
> - Il existe dans le même espace web  
> 
> Un _espace web_ est une construction logique dans App Service qui définit un regroupement de ressources du serveur. Une région géographique (par exemple, ouest des États-Unis) contient plusieurs espaces web pour allouer des clients à l’aide d’App Service. Les ressources App Service ne peuvent pas être déplacées entre des espaces web. 
> 

![Sélecteur de plan App Service.][change]

Chaque plan a son propre niveau de tarification. Par exemple, quand vous déplacez un site du niveau **Gratuit** au niveau **Standard**, toutes les applications affectées peuvent utiliser les fonctionnalités et ressources du niveau **Standard**. Toutefois, le déplacement d’une application depuis un plan de niveau supérieur vers un plan de niveau inférieur vous prive de certaines fonctionnalités. Si votre application utilise une fonctionnalité qui n’est pas disponible dans le plan cible, vous obtenez une erreur qui indique quelle fonctionnalité en cours d’utilisation n’est pas disponible. Par exemple, si une de vos applications utilise des certificats SSL, vous pouvez voir le message d’erreur : `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`Dans ce cas, vous devez passer le niveau tarifaire du plan cible au niveau **De base** ou supérieur, ou vous devez supprimer toutes les connexions SSL à votre application, afin de pouvoir déplacer l’application vers le plan cible.

## <a name="move-an-app-to-a-different-region"></a>Déplacer une application vers une autre région

La région dans laquelle votre application s’exécute est la région où se trouve le plan App Service. Toutefois, vous ne pouvez pas changer la région d’un plan App Service. Si vous souhaitez exécuter votre application dans une autre région, vous pouvez également utiliser le clonage d’application. Le clonage crée une copie de votre application dans un plan App Service, nouveau ou existant, dans n’importe quelle région.

La commande **Cloner l’application** figure dans la section **Outils de développement** du menu.

> [!IMPORTANT]
> Pour plus d’informations sur les limites du clonage, voir [Clonage de l’application Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Mettre à l’échelle un plan App Service

Pour faire évoluer le niveau tarifaire d’un plan App Service, consultez [Scalabilité verticale d’une application dans Azure](web-sites-scale.md).

Pour augmenter le nombre d’instances d’une application, consultez [Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Supprimer un plan App Service

Par défaut, pour éviter des frais inattendus, quand vous supprimez la dernière application d’un plan App Service, App Service supprime également le plan. Si vous choisissez de conserver le plan, vous devez passer le plan au niveau **Gratuit** afin de ne pas être facturé.

> [!IMPORTANT]
> Les **plans App Service** auxquels aucune application n’est associée impliquent tout de même des frais, car ils continuent à réserver les instances de machine virtuelle configurées.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Montée en puissance d’une application dans Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
