---
title: "Créer un service Recherche Azure dans le portail | Microsoft Docs"
description: Configurez un service Recherche Azure dans le portail.
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/07/2017
ms.author: heidist
ms.openlocfilehash: b31844dd0dad1168b12d91ce536589099ab16a7e
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Création d'un service Azure Search dans le portail

Découvrez comment créer ou provisionner un service Recherche Azure dans le portail. 

Vous préférez PowerShell ? Utilisez le [modèle de service](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manager. Pour plus d’informations, consultez [Gérer votre service Recherche Azure avec PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>S’abonner (payant ou gratuit)

[Ouvrez un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) et utilisez les crédits gratuits pour essayer les services payants d’Azure. Une fois les crédits épuisés, conservez le compte et continuez à utiliser les services Azure gratuits, tels que les sites Web. Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres pour demander à l’être.

Vous pouvez également [activer les avantages d’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Un abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants. 

## <a name="find-azure-search"></a>Localiser Recherche Azure
1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Cliquez sur le signe plus (« + ») dans le coin supérieur gauche.
3. Sélectionnez **Web + Mobile** > **Recherche Azure**.

![](./media/search-create-service-portal/find-search3.png)

## <a name="name-the-service-and-url-endpoint"></a>Nommer le service et le point de terminaison URL

Un nom de service fait partie du point de terminaison URL par le biais duquel les appels d’API sont émis : `https://your-service-name.search.windows.net`. Tapez le nom de votre service dans le champ **URL**. 

Configuration requise du nom du service :
   * il doit être unique dans l’espace de noms search.windows.net ;
   * entre 2 et 60 caractères ;
   * contenir des lettres minuscules, des chiffres ou des tirets (« - ») ;
   * éviter les tirets (« - ») pour les 2 premiers caractères ou le dernier ;
   * pas de tirets consécutifs (« -- »).

## <a name="select-a-subscription"></a>Sélectionner un abonnement
Si vous avez plusieurs abonnements, choisissez celui qui a également des services de stockage de données ou de fichiers. Recherche Azure peut détecter automatiquement Table Azure, Stockage Blob, SQL Database et Azure Cosmos DB pour l’indexation à l’aide [*d’indexeurs*](search-indexer-overview.md), mais uniquement pour les services dans le même abonnement.

## <a name="select-a-resource-group"></a>Sélectionner un groupe de ressources
Un groupe de ressources correspond à une collection de services et de ressources Azure utilisés ensemble. Par exemple, si vous utilisez Recherche Azure pour indexer une base de données SQL, ces deux services doivent faire partie du même groupe de ressources.

> [!TIP]
> La suppression d’un groupe de ressources supprime également les services qu’il contient. Pour les projets de prototype utilisant plusieurs services, le fait de les placer tous dans le même groupe de ressources facilite le nettoyage une fois le projet terminé. 

## <a name="select-a-hosting-location"></a>Sélectionner un emplacement d’hébergement 
En sa qualité de service Azure, Recherche Azure peut être hébergé dans les centres de données du monde entier. Veuillez noter que les [prix peuvent varier](https://azure.microsoft.com/pricing/details/search/) selon la zone géographique.

## <a name="select-a-pricing-tier-sku"></a>Sélectionner un niveau de tarification (SKU)
[Azure Search est actuellement disponible à différents niveaux tarifaires](https://azure.microsoft.com/pricing/details/search/): Gratuit, De base ou Standard. Chaque niveau a ses propres [capacité et limites](search-limits-quotas-capacity.md). Pour obtenir de l’aide, voir [Choisir un niveau tarifaire ou une référence (SKU)](search-sku-tier.md) .

Dans cette démonstration, nous avons choisi le niveau Standard pour notre service.

Vous ne pouvez pas changer de niveau tarifaire une fois le service créé. Si vous souhaitez un niveau plus élevé ou moins élevé, vous devez recréer le service.

## <a name="create-your-service"></a>Créer votre service

N’oubliez pas d’épingler votre service au tableau de bord pour y accéder facilement à chaque fois que vous vous connectez.

![](./media/search-create-service-portal/new-service3.png)

## <a name="scale-your-service"></a>Mettre à l’échelle le service
La création d’un service peut prendre plusieurs minutes (15 minutes ou plus selon le niveau). Une fois votre service approvisionné, vous pouvez le mettre à l’échelle en fonction de vos besoins. Comme vous avez choisi le niveau Standard pour votre service Azure Search, vous pouvez le mettre à l’échelle dans deux dimensions : réplicas et partitions. Si vous choisissez le niveau De base, vous pouvez uniquement ajouter des réplicas. Si vous configurez le service gratuit, la mise à l’échelle n’est pas disponible.

Les ***partitions*** permettent à votre service de stocker plus de documents et d’effectuer des recherches dans un plus grand nombre de documents.

Les ***réplicas*** permettent à votre service de gérer une charge supérieure de requêtes de recherche.

> [!Important]
> Un service doit avoir [2 réplicas pour SLA en lecture seule et 3 réplicas pour SLA en lecture/écriture](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Accédez à la page du service de recherche dans le portail Azure.
2. Dans le volet de navigation de gauche, sélectionnez **Paramètres** > **Mise à l’échelle**.
3. Utilisez le curseur pour ajouter des réplicas ou des partitions.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Chaque niveau a des [limites](search-limits-quotas-capacity.md) différentes quant au nombre total d’unités de recherche autorisées dans un même service (replicas * partitions = nombre total d’unités de recherche).

## <a name="when-to-add-a-second-service"></a>Quand ajouter un deuxième service

La grande majorité des clients n’utilise qu’un seul service, approvisionné à un niveau qui fournit le [bon équilibre de ressources](search-sku-tier.md). Un service peut héberger plusieurs index, soumis aux [limites maximales du niveau sélectionné](search-capacity-planning.md), chaque index étant isolé des autres. Dans la Recherche Azure, les requêtes ne peuvent être dirigées que vers un seul index, ce qui réduit les risques de récupération des données accidentelle ou intentionnelle à partir d’autres index du même service.

Bien que la plupart des clients utilisent un seul service, une redondance des services peut être nécessaire en cas d’exigences opérationnelles particulières, notamment :

+ Récupération d’urgence (panne du centre de données). La Recherche Azure ne fournit pas de basculement instantané en cas de panne. Pour obtenir de l’aide et des recommandations, consultez la page [Administration des services](search-manage.md).
+ Vos recherches sur la modélisation d’une architecture mutualisée ont déterminé que des services supplémentaires représentent la conception optimale. Pour plus d’informations, consultez la page [Conception pour une architecture mutualisée](search-modeling-multitenant-saas-applications.md).
+ Dans le cas d’applications déployées dans le monde entier, vous pouvez avoir besoin de disposer d’une instance de la Recherche Azure dans plusieurs régions afin de réduire la latence du trafic international de votre application.

> [!NOTE]
> Dans la Recherche Azure, vous ne pouvez pas séparer les charges de travail d’indexation et de requête ; par conséquent, il n’est jamais question de créer plusieurs services pour des charges de travail séparées. Un index est toujours interrogé sur le service dans lequel il a été créé (vous ne pouvez pas créer un index dans un service et le copier dans un autre).
>

Il n’est pas nécessaire de disposer d’un second service pour la haute disponibilité. La haute disponibilité des requêtes est atteinte si vous utilisez au moins deux réplicas dans le même service. Les mises à jour des réplicas sont séquentielles, ce qui signifie qu’au moins l’un d’eux est opérationnel lors du déploiement d’une mise à jour de service. Pour plus d’informations sur la disponibilité, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Étapes suivantes
Après avoir approvisionné un service Recherche Azure, vous pouvez [définir un index](search-what-is-an-index.md) afin de pouvoir télécharger vos données et effectuer des recherches dans vos données. 

> [!div class="nextstepaction"]
> [Utilisation d'Azure Search dans .NET](search-howto-dotnet-sdk.md)
