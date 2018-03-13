---
title: "Montée en puissance d’une application dans Azure | Microsoft Docs"
description: "Découvrez comment activer la montée en puissance d’une application dans Azure App Service pour ajouter des capacités et des fonctionnalités."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.openlocfilehash: f6244e3f739424be169f1ea117500159bd5e4254
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="scale-up-an-app-in-azure"></a>Montée en puissance d’une application dans Azure

> [!NOTE]
> Le nouveau niveau **PremiumV2** offre des processeurs plus rapides, un stockage SSD et le double de ratio mémoire-cœur des niveaux tarifaires précédents. Avec cet avantage de performances, vous pouvez économiser de l’argent en exécutant vos applications sur moins d’instances. Pour savoir comment monter en puissance jusqu’au niveau **PremiumV2**, voir [Configurer le niveau PremiumV2 pour App Service](app-service-configure-premium-tier.md).
>

Cet article décrit la mise à l’échelle d’une application web dans Azure App Service. Il existe deux workflows de mise à l’échelle : montée en puissance et augmentation de la taille des instances. Cet article décrit le workflow de montée en puissance.

* [Montée en puissance](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): bénéficiez d’un surcroît de capacité d’UC, de mémoire et d’espace disque, ainsi que de fonctionnalités supplémentaires, comme des machines virtuelles dédiées, des domaines et des certificats personnalisés, des emplacements intermédiaires, la mise à l’échelle automatique, et bien davantage. Pour monter en puissance en modifiant le niveau tarifaire du plan App Service auquel appartient votre application.
* [Augmentation de la taille des instances](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): augmentez le nombre d’instances de machine virtuelle qui exécutent votre application.
  Ce nombre peut atteindre 20 instances, en fonction de votre niveau tarifaire. La solution [Environnements App Service](environment/intro.md) au niveau **Isolé** augmente votre capacité de montée en puissance parallèle à 100 instances. Pour plus d’informations sur l’augmentation de la taille des instances, voir [Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md). Vous y trouverez comment utiliser la mise à l’échelle automatique, qui permet de mettre à l’échelle le nombre d’instances automatiquement en fonction des planifications et des règles prédéfinies.

Ces paramètres de mise à l’échelle sont applicables en quelques secondes et affectent toutes les applications de votre [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Ils ne nécessitent pas de modifier votre code ou de redéployer votre application.

Pour plus d’informations sur la tarification et les fonctionnalités de chaque plan App Service, voir [Détails de la tarification - App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Avant de changer le niveau **Gratuit** d’un plan App Service, commencez par supprimer les [limites de dépense](https://azure.microsoft.com/pricing/spending-limits/) en place pour votre abonnement Azure. Pour voir ou modifier les options de votre abonnement Microsoft Azure App Service, consultez la page [Abonnements Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Montée en puissance de votre niveau de tarification
1. Dans votre navigateur, ouvrez le [portail Azure][portal].
2. Dans la page de votre application App Service, cliquez sur **Tous les paramètres**, puis sur **Monter en puissance**.
   
    ![Accédez à la montée en puissance pour votre application Azure.][ChooseWHP]
3. Choisissez votre niveau, puis cliquez sur **Sélectionner**.
   
    Dans l’onglet **Notifications**, la mention **RÉUSSITTE** clignote en vert une fois l’opération terminée.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Mettre à l’échelle des ressources associées
Si votre application dépend d’autres services, tels qu’Azure SQL Database ou Stockage Microsoft Azure, vous pouvez faire monter en puissance ces ressources séparément. Ces ressources ne sont pas gérées par le plan App Service.

1. Dans **Essentials**, cliquez sur le lien **Groupe de ressources**.
   
    ![Montée en puissance des ressources connexes de votre application Azure](./media/web-sites-scale/RGEssentialsLink.png)
2. Dans la partie **Résumé** de la page **Groupe de ressources**, cliquez sur une ressource à mettre à l’échelle. La capture d’écran ci-après illustre une ressource Base de données SQL et une ressource Azure Storage.
   
    ![Accéder à la page du groupe de ressources pour activer la montée en puissance de votre application Azure](./media/web-sites-scale/ResourceGroup.png)
3. Pour une ressource Base de données SQL, cliquez sur **Paramètres** > **Niveau tarifaire** pour mettre à l’échelle le niveau tarifaire.
   
    ![Montée en puissance de la base de données SQL principale pour votre application Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    Vous pouvez également activer la [géoréplication](../sql-database/sql-database-geo-replication-overview.md) pour votre instance de base de données SQL.
   
    Dans le cas d’une ressource Azure Storage, cliquez sur **Paramètres** > **Configuration** pour faire évoluer vos options de stockage.
   
    ![Montée en puissance du compte de stockage Azure utilisé par votre application Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparer les niveaux tarifaires
Pour plus d’informations, par exemple sur les tailles de machine virtuelle pour chaque niveau tarifaire, voir [Tarification App Service](https://azure.microsoft.com/pricing/details/web-sites/).
Pour un tableau des limites, quotas et contraintes du service, ainsi que des fonctionnalités prises en charge dans chaque niveau, voir [Limites d’App Service](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la tarification, le support et les contrats SLA, accédez aux liens suivants :
  
    [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Plans de support Microsoft Azure](https://azure.microsoft.com/support/plans/)
  
    [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/)
  
    [Tarification – Base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Tailles des machines virtuelles et de Service cloud pour Microsoft Azure][vmsizes]
  
* Pour plus d’informations sur les meilleures pratiques liées à Azure App Service, notamment la création d’une architecture évolutive et résiliente, voir [Best Practices: Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)(Meilleures pratiques : Azure App Service Web Apps).
* Pour visionner des vidéos concernant la mise à l’échelle des applications App Service, consultez les ressources suivantes :
  
  * [Quand mettre à l’échelle Sites Web Azure - avec Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Mise à l’échelle automatique de Sites Web Azure, unité centrale ou planification - avec Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Mise à l’échelle de Sites Web Azure - avec Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
