---
title: "Modélisation d’une architecture mutualisée dans la Recherche Azure | Microsoft Docs"
description: "Découvrez les modèles de conception courants pour les applications SaaS mutualisées lors de l’utilisation de Recherche Azure."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 72e9696a-553b-47dc-9e05-a82db0ebf094
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: ashmaka
ms.openlocfilehash: 622ae64e118dd2498aff0bf2e9f6c1dbfb0ab045
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Modèles de conception pour les applications SaaS mutualisées et Recherche Azure
Une application mutualisée est une application qui fournit les mêmes services et fonctionnalités à plusieurs clients qui ne peuvent pas voir ni partager les données d’un autre client. Ce document aborde les stratégies d’isolation de client pour les applications mutualisées conçues avec Recherche Azure.

## <a name="azure-search-concepts"></a>Concepts de Recherche Azure
En tant que solution SaaS (search-as-a-service), Recherche Azure permet aux développeurs d’ajouter des expériences de recherche enrichies dans les applications sans avoir à gérer d’infrastructure, ni devenir un expert en matière de récupération d’informations. Les données sont téléchargées vers le service, puis stockées dans le cloud. À l’aide de requêtes simples dans l’API Recherche Azure, les données peuvent ensuite être modifiées et faire l’objet de recherches. [Cet article](http://aka.ms/whatisazsearch)présente une vue d’ensemble du service. Avant d’aborder les modèles de conception, il est important de comprendre certains concepts de Recherche Azure.

### <a name="search-services-indexes-fields-and-documents"></a>Rechercher des services, des index, des champs et des documents
Lorsque vous utilisez Recherche Azure, vous vous abonnez à un *service de recherche*. Lorsque les données sont téléchargées vers Recherche Azure, elles sont stockées dans un *index* au sein du service de recherche. Un seul service peut contenir plusieurs index. Pour utiliser les concepts familiers des bases de données, le service de recherche peut être comparé à une base de données, tandis que les index au sein d’un service peuvent être comparés aux tables dans une base de données.

Chaque index au sein d’un service de recherche possède son propre schéma, qui est défini par un certain nombre de *champs*personnalisables. Les données sont ajoutées à un index Recherche Azure sous la forme de *documents*individuels. Chaque document doit être téléchargé dans un index spécifique et doit respecter le schéma de cet index. Lors de la recherche de données à l’aide de Recherche Azure, les requêtes de recherche en texte intégral sont exécutées sur un index spécifique.  Pour comparer ces concepts à ceux d’une base de données, les champs peuvent être comparés aux colonnes d’une table et les documents peuvent être comparés aux lignes.

### <a name="scalability"></a>Extensibilité
Tout service Recherche Azure dans le [niveau tarifaire](https://azure.microsoft.com/pricing/details/search/) Standard peut évoluer en deux dimensions : stockage et disponibilité.

* *partitions* pour augmenter le stockage d’un service de recherche.
* *réplicas* à un service pour augmenter le débit des requêtes qu’un service de recherche peut gérer.

L’ajout et la suppression de partitions et de réplicas lorsque nécessaire permet à la capacité du service de recherche d’augmenter en fonction de la quantité de données et du trafic demandés par l’application. Pour qu’un service de recherche obtienne un [Contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/search/v1_0/)en lecture, deux réplicas sont nécessaires. Pour qu’un service obtienne un [Contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/search/v1_0/)en lecture et en écriture, trois réplicas sont nécessaires.

### <a name="service-and-index-limits-in-azure-search"></a>Limites du service et de l’index dans Recherche Azure
Il existe différents [niveaux tarifaires](https://azure.microsoft.com/pricing/details/search/) dans la Recherche Azure et chaque niveau présente des [limites et quotas](search-limits-quotas-capacity.md) différents. Certaines de ces limites se situent au niveau du service, certaines au niveau de l’index, et d’autres au niveau de la partition.

|  | De base | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Nombre maximal de réplicas par service |3 |12 |12 |12 |12 |
| Nombre maximal de partitions par service |1 |12 |12 |12 |3 |
| Nombre maximal d’unités de recherche (réplicas*partitions) par service |3 |36 |36 |36 |36 (3 partitions max.) |
| Nombre maximal de documents par service |1 million |180 millions |720 millions |1,4 milliard |600 millions |
| Stockage maximal par service |2 Go |300 Go |1,2 To |2,4 To |600 Go |
| Nombre maximal de documents par partition |1 million |15 millions |60 millions |120 millions |200 millions |
| Stockage maximal par partition |2 Go |25 Go |100 Go |200 Go |200 Go |
| Nombre maximal d’index par service |5 |50 |200 |200 |3000 (1 000 index max. par partition) |

#### <a name="s3-high-density"></a>Haute densité S3
Dans le niveau tarifaire S3 de Recherche Azure, il existe une option pour le mode haute densité (HD) conçu spécifiquement pour les scénarios mutualisés. Dans de nombreux cas, il est nécessaire de prendre en charge un grand nombre de clients plus petits sous un seul service pour tirer parti des avantages de simplicité et de rentabilité.

S3 HD permet de réunir les nombreux index de petite taille sous la gestion d’un seul service de recherche en troquant la possibilité d’augmenter la taille des instances des index à l’aide de partitions contre la possibilité d’héberger plus d’index dans un seul service.

Concrètement, un service S3 peut avoir entre 1 et 200 index qui, ensemble, peuvent héberger jusqu’à 1,4 milliard de documents. À l’inverse, un niveau S3 HD ne permettrait aux index individuels que d’atteindre 1 million de documents, mais il peut gérer jusqu’à 1 000 index par partition (jusqu’à 3 000 par service), soit au total 200 millions de documents par partition (jusqu’à 600 millions par service).

## <a name="considerations-for-multitenant-applications"></a>Considérations relatives aux applications mutualisées
Les applications mutualisées doivent distribuer efficacement les ressources entre les clients tout en conservant un certain niveau de confidentialité entre les différents clients. Il existe quelques considérations à prendre en compte lors de la conception de l’architecture pour ce type d’application :

* *Isolation des clients :* les développeurs d’applications doivent prendre les mesures appropriées pour s’assurer qu’aucun client ne bénéficie d’un accès non autorisé ou non désiré aux données d’autres clients. Au-delà de la confidentialité des données, les stratégies d’isolation des clients nécessitent une gestion efficace des ressources partagées et la protection contre les voisins bruyants.
* *Coût des ressources cloud :* comme pour toute autre application, les solutions logicielles doivent rester compétitives au niveau du coût en tant que composant d’une application mutualisée.
* *Facilité des opérations :* lors du développement d’une architecture mutualisée, l’impact sur les opérations et la complexité de l’application est un facteur important. La Recherche Azure bénéficie d’un [Contrat de niveau de service de 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Envergure internationale :* les applications mutualisées devront peut-être servir efficacement des clients qui sont répartis dans le monde entier.
* *Évolutivité :* les développeurs d’applications doivent trouver l’équilibre entre le fait de maintenir un niveau de complexité des applications suffisamment faible et la conception de l’application de façon à ce qu’elle évolue avec le nombre de clients, ainsi que la taille des données et la charge de travail des clients.

Recherche Azure propose quelques limites qui peuvent être utilisées pour isoler les données et la charge de travail des clients.

## <a name="modeling-multitenancy-with-azure-search"></a>Modélisation d’une architecture mutualisée avec Recherche Azure
Dans le cas d’un scénario mutualisé, le développeur de l’application consomme un ou plusieurs services de recherche et répartit les clients entre les services, les index ou les deux. Recherche Azure offre quelques modèles courants pour la modélisation d’un scénario mutualisé :

1. *Index par client :* chaque client a son propre index dans un service de recherche qui est partagé avec d’autres clients.
2. *Service par client :* chaque client possède son propre service Recherche Azure dédié, pour un niveau de séparation maximal entre les données et la charge de travail.
3. *Combinaison des deux :* les clients les plus volumineux et actifs se voient attribuer des services dédiés, tandis que les clients plus petits se voient attribuer des index individuels au sein de services partagés.

## <a name="1-index-per-tenant"></a>1. Index par client
![Une image du modèle d’index par client](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Dans un modèle d’index par client, plusieurs clients occupent un seul service Recherche Azure où chaque client a son propre index.

Les clients bénéficient de l’isolation des données, car toutes les requêtes de recherche et les opérations sur les documents sont émises au niveau de l’index dans Recherche Azure. Dans la couche d’application, il est entendu qu’il est nécessaire de diriger le trafic des divers clients vers les index appropriés, tout en gérant les ressources au niveau du service pour tous les clients.

Un attribut clé du modèle d’index par client est la possibilité pour le développeur d’applications d’augmenter la capacité d’un service de recherche pour les clients de l’application. Si la répartition de la charge de travail est inégale entre les clients, la combinaison optimale des clients peut être distribuée entre les index d’un service de recherche pour prendre en charge un nombre de clients très actifs, gourmands en ressources, tout en gérant simultanément une longue file de clients moins actifs. Le compromis réside dans l’incapacité du modèle à gérer les situations où chaque client est simultanément très actif.

Le modèle d’index par client sert de base à un modèle de coût variable, où un service Recherche Azure entier est acheté d’avance, puis rempli avec des clients. Cela permet d’attribuer la capacité inutilisée aux essais et aux comptes gratuits.

Pour les applications avec une envergure internationale, le modèle d’index par client n’est peut-être pas le plus efficace. Si les clients d’une application sont répartis dans le monde entier, un service distinct peut être nécessaire pour chaque région, ce qui peut dupliquer les coûts pour chacun d’eux.

Recherche Azure permet la croissance des index individuels et du nombre total d’index. Si un niveau tarifaire approprié est choisi, des réplicas et des partitions peuvent être ajoutés au service de recherche entier lorsqu’un index individuel au sein du service devient trop important en termes de stockage ou de trafic.

Si le nombre total d’index devient trop important pour un seul service, un autre service doit être configuré pour prendre en charge les nouveaux clients. Si les index doivent être déplacés entre les services de recherche lorsque de nouveaux services sont ajoutés, les données de l’index doivent être copiées manuellement d’un index vers l’autre car le déplacement d’un index n’est pas autorisé dans Recherche Azure.

## <a name="2-service-per-tenant"></a>2. Service par client
![Une image du modèle de service par client](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Dans une architecture de service par client, chaque client possède son propre service de recherche.

Dans ce modèle, l’application atteint le niveau maximal d’isolation pour ses clients. Chaque service bénéficie d’un débit et d’un stockage dédiés pour gérer les requêtes de recherche, ainsi que des clés d’API distinctes.

Pour les applications où chaque client a une grande envergure ou si la charge de travail varie peu d’un client à l’autre, le modèle de service par client constitue un choix efficace car les ressources ne sont pas partagées entre les charges de travail de différents clients.

Un modèle de service par client offre également l’avantage d’un modèle à coût prévisible et fixe. Il n’existe aucun investissement initial pour un service de recherche entier jusqu'à ce qu’il y ait un client pour le remplir. Cependant, le coût par client est supérieur au modèle d’index par client.

Le modèle de service par client constitue une solution efficace pour les applications d’envergure internationale. Avec des clients répartis géographiquement, il est facile de placer le service de chaque client dans la région appropriée.

Les défis liés à la mise à l’échelle de ce modèle se présentent lorsque la croissance des clients individuels surpasse celle de leur service. Actuellement, Recherche Azure ne prend pas en charge la mise à niveau du niveau de tarification d’un service de recherche, donc toutes les données doivent être copiées manuellement vers un nouveau service.

## <a name="3-mixing-both-models"></a>3. Combinaison des deux modèles
Un autre modèle pour la modélisation mutualisée est de combiner les stratégies d’index par client et de service par client.

En mélangeant les deux modèles, les clients les plus volumineux d’une application peuvent occuper des services dédiés, tandis que la longue file de clients moins actifs et plus petits peut occuper des index dans un service partagé. Ce modèle garantit que les clients les plus volumineux bénéficient toujours de performances élevées du service tout en protégeant les clients plus petits des voisins bruyants.

Cependant, l’implémentation de cette stratégie repose sur la capacité à prévoir quels clients nécessiteront un service dédié au lieu d’un index dans un service partagé. La complexité de l’application augmente avec la nécessité de gérer les deux modèles d’architecture mutualisée.

## <a name="achieving-even-finer-granularity"></a>Atteindre une granularité encore plus fine
Les modèles de conception ci-dessus pour les scénarios mutualisés dans Recherche Azure supposent qu’il existe une étendue uniforme, où chaque client constitue une instance entière d’une application. Toutefois, les applications peuvent parfois gérer plusieurs étendues plus petites.

Si les modèles service par client et index par client ne sont pas des étendues suffisamment petites, il est possible de modéliser un index pour atteindre un degré de granularité encore plus fin.

Pour qu’un seul index se comporte différemment pour des points de terminaison clients différents, il est possible d’ajouter un champ à un index qui désigne une certaine valeur pour chaque client possible. À chaque fois qu’un client appelle Recherche Azure pour interroger ou modifier un index, le code de l’application cliente spécifie la valeur appropriée pour ce champ à l’aide de la fonctionnalité [filter](https://msdn.microsoft.com/library/azure/dn798921.aspx) de Recherche Azure au moment de la requête.

Cette méthode peut être utilisée pour obtenir une fonctionnalité de comptes d’utilisateurs distincts, de niveaux d’autorisation distincts et même d’applications distinctes.

> [!NOTE]
> Utiliser l’approche décrite ci-dessus pour configurer un index unique pour servir plusieurs locataires affecte la pertinence des résultats de recherche. Les notes de pertinence de la recherche sont calculées au niveau de l’index et non au niveau du locataire. Ainsi, toutes les données des locataires sont incorporées dans les statistiques sous-jacentes des notes de pertinence, comme la fréquence des termes.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Recherche Azure est un outil de premier choix pour de nombreuses applications, [en savoir plus sur les fonctionnalités puissantes du service](http://aka.ms/whatisazsearch). Lorsque vous évaluez les différents modèles de conception pour les applications mutualisées, consultez les [différents niveaux tarifaires](https://azure.microsoft.com/pricing/details/search/) et les [limites de service](search-limits-quotas-capacity.md) respectives pour mieux adapter la Recherche Azure aux architectures et aux charges de travail de toutes tailles.

Toutes questions relatives à Recherche Azure et les scénarios partagés au sein d’une architecture mutualisée peuvent être envoyés vers azuresearch_contact@microsoft.com.

