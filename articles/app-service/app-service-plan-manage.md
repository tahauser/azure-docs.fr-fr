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
ms.openlocfilehash: 1dfe8a903e19ff524a1c4a0228e6aefcbe9ff183
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gérer un plan App Service dans Azure

Un [plan Azure App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) fournit les ressources nécessaires à l’exécution d’une application App Service. Ce guide montre comment gérer un plan App Service.

## <a name="create-an-app-service-plan"></a>Créer un plan App Service

> [!TIP]
> Si vous avez un environnement App Service, consultez [Créer un plan App Service dans un environnement App Service](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Vous pouvez créer un plan App Service vide ou en créer un dans le cadre de la création d’une application.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Nouveau** > **Web + Mobile**, puis sélectionnez **Application web** ou un autre genre d’application App Service.

2. Sélectionnez un plan App Service existant ou créez-en un pour la nouvelle application.

   ![Créez une application dans le portail Azure.][createWebApp]

   Pour créer un plan :

   a. Sélectionnez **[+] Créer nouveau**.

      ![Créez un plan App Service.][createASP] 

   b. Sous **Plan App Service**, entrez le nom du plan.

   c. Sous **Localisation**, sélectionnez une localisation appropriée.

   d. Sous **Niveau tarifaire**, sélectionnez un niveau tarifaire approprié pour le service. Sélectionnez **Afficher tout** pour afficher davantage d’options de tarification, telles que **Gratuit** et **Partagé**. Une fois que vous avez sélectionné le niveau de tarification, cliquez sur le bouton **Sélectionner** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Déplacer une application vers un autre plan App Service

Vous pouvez déplacer une application vers un autre plan App Service tant que les plans source et cible se trouvent dans _le même groupe de ressources et la même région géographique_.

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’application à déplacer.

2. Dans le menu, recherchez la section **Plan App Service**.

3. Sélectionnez **Changer le plan App Service** pour ouvrir le sélecteur de **plan App Service**.

   ![Sélecteur de plan App Service.][change] 

4. Dans le sélecteur de **plan App Service**, sélectionnez un plan existant vers lequel déplacer cette application.   

> [!IMPORTANT]
> La page **Sélectionner un plan App Service** est filtrée selon les critères suivants : 
> - Il existe dans le même groupe de ressources 
> - Il existe dans la même région géographique 
> - Il existe dans le même espace web  
> 
> Un _espace web_ est une construction logique dans App Service qui définit un regroupement de ressources du serveur. Une région géographique (par exemple, Ouest des États-Unis) contient plusieurs espaces web pour allouer des clients qui utilisent App Service. À l’heure actuelle, vous ne pouvez pas déplacer de ressources App Service entre des espaces web. 
> 

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Chaque plan a son propre niveau tarifaire. Par exemple, quand vous passez du niveau **Gratuit** au niveau **Standard** d’un site, toutes les applications affectées peuvent utiliser les fonctionnalités et ressources du niveau **Standard**. Par contre, le déplacement d’une application d’un plan de niveau supérieur vers un plan de niveau inférieur vous prive de certaines fonctionnalités. Si votre application utilise une fonctionnalité qui n’est pas disponible dans le plan cible, vous obtenez une erreur qui indique quelle fonctionnalité en cours d’utilisation n’est pas disponible. 

Par exemple, si l’une de vos applications utilise des certificats SSL, ce message d’erreur peut s’afficher :

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

Dans ce cas, avant de pouvoir déplacer l’application vers le plan cible, vous devez :
- Faire passer le plan cible à un niveau tarifaire supérieur (**De base** au minimum).
- Ou supprimer toutes les connexions SSL à votre application.

## <a name="move-an-app-to-a-different-region"></a>Déplacer une application vers une autre région

La région dans laquelle votre application s’exécute est la région où se trouve le plan App Service. Toutefois, vous ne pouvez pas changer la région d’un plan App Service. Si vous souhaitez exécuter votre application dans une autre région, vous pouvez également utiliser le clonage d’application. Le clonage crée une copie de votre application dans un plan App Service, nouveau ou existant, dans n’importe quelle région.

La commande **Cloner l’application** figure dans la section **Outils de développement** du menu.

> [!IMPORTANT]
> Le clonage présente des limitations. Pour en savoir plus sur celles-ci, consultez [Clonage de l’application Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Mettre à l’échelle un plan App Service

Pour faire évoluer le niveau tarifaire d’un plan App Service, consultez [Scalabilité verticale d’une application dans Azure](web-sites-scale.md).

Pour augmenter le nombre d’instances d’une application, consultez [Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Supprimer un plan App Service

Par défaut, pour éviter des frais inattendus, quand vous supprimez la dernière application d’un plan App Service, App Service supprime également le plan. Si vous choisissez de conserver le plan, faites-le passer au niveau **Gratuit** pour ne pas être facturé.

> [!IMPORTANT]
> Les plans App Service auxquels aucune application n’est associée engendrent encore des frais, car ils continuent à réserver les instances de machine virtuelle configurées.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Montée en puissance d’une application dans Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
