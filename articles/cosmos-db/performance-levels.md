---
title: "Niveaux de performances Azure Cosmos DB supprimés | Microsoft Docs"
description: "Découvrez les niveaux de performances S1, S2 et S3 qui étaient disponibles dans Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4cab6297e45dc7a14826d7bb10cf22a8a300355f
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Mise hors service des niveaux de performances S1, S2 et S3

> [!IMPORTANT] 
> Les niveaux de performances S1, S2 et S3 abordés dans cet article vont être mis hors service et ne sont plus disponibles pour les nouveaux comptes Azure Cosmos DB.
>

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Cet article fournit une vue d’ensemble des niveaux de performances S1, S2 et S3 et explique comment les collections qui les utilisent seront migrées vers des collections à partition unique avant la fin de l’année 2017. Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

- [Pourquoi les niveaux de performances S1, S2 et S3 vont-ils être mis hors service ?](#why-retired)
- [En quoi les collections à partition unique et les collections partitionnées se différencient-elles des niveaux de performances S1, S2 et S3 ?](#compare)
- [Comment assurer un accès ininterrompu à mes données ?](#uninterrupted-access)
- [Qu’est-ce qui va changer au niveau de ma collection suite à la migration ?](#collection-change)
- [Qu’est-ce qui va changer au niveau de ma facturation suite à la migration vers des collections à partition unique ?](#billing-change)
- [Que se passe-t-il si j’ai besoin de plus de 10 Go de stockage ?](#more-storage-needed)
- [Puis-je modifier les niveaux de performances entre S1, S2 et S3 avant la migration planifiée ?](#change-before)
- [Comment serai-je informé de la migration de ma collection ?](#when-migrated)
- [Comment procéder à la migration des niveaux de performances S1, S2 et S3 vers des collections à partition unique par moi-même ?](#migrate-diy)
- [Quelles sont les conséquences pour moi en tant que client Contrat Entreprise ?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Pourquoi les niveaux de performances S1, S2 et S3 vont-ils être mis hors service ?

Les niveaux de performances S1, S2 et S3 n’offrent pas la même flexibilité qu’Azure Cosmos DB standard. Avec les niveaux de performances S1, S2 et S3, le débit et la capacité de stockage étaient prédéfinis et n’offraient aucune élasticité. Azure Cosmos DB offre désormais la possibilité de personnaliser votre débit et votre stockage, ce qui vous garantit une plus grande flexibilité vis-à-vis de l’évolution de vos besoins.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>En quoi les collections à partition unique et les collections partitionnées se différencient-elles des niveaux de performances S1, S2 et S3 ?

Le tableau suivant compare les options de débit et de stockage disponibles dans les collections à partition unique, les collections partitionnées et les niveaux de performances S1, S2 et S3. Voici un exemple pour la région Est des États-Unis 2 :

|   |Collection partitionnée|Collection à partition unique|S1|S2|S3|
|---|---|---|---|---|---|
|Débit maximal|Illimité|10 000 RU/s|250 RU/s|1 000 RU/s|2 500 RU/s|
|Débit minimal|2 500 RU/s|400 RU/s|250 RU/s|1 000 RU/s|2 500 RU/s|
|Stockage maximal|Illimité|10 Go|10 Go|10 Go|10 Go|
|Prix (mensuel)|Débit : 6 USD / 100 RU/s<br><br>Stockage : 0,25 USD/Go|Débit : 6 USD / 100 RU/s<br><br>Stockage : 0,25 USD/Go|25 USD|50 USD|100 USD|

Vous êtes un client Contrat Entreprise ? Si oui, voir [Quelles sont les conséquences pour moi en tant que client Contrat Entreprise ?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Comment assurer un accès ininterrompu à mes données ?

Vous n’avez rien à faire : Cosmos DB se charge de la migration pour vous. Si vous avez une collection S1, S2 ou S3, votre collection actuelle sera migrée vers une collection à partition unique à la fin de l’année 2017. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Qu’est-ce qui va changer au niveau de ma collection suite à la migration ?

Si vous avez une collection S1, elle sera migrée vers une collection à partition unique dotée d’un débit de 400 RU/s, ce qui correspond au débit le plus bas disponible pour les collections à partition unique. Toutefois, le coût d’un débit de 400 RU/s dans une collection à partition unique est approximativement identique à celui que vous payez pour votre collection S1 dotée d’un débit de 250 RU/s : vous ne payez pas pour les 150 RU/s disponibles supplémentaires.

Si vous avez une collection S2, elle sera migrée vers une collection à partition unique dotée d’un débit de 1 000 RU/s. Vous n’observerez aucun changement de niveau de débit.

Si vous avez une collection S3, elle sera migrée vers une collection à partition unique dotée d’un débit de 2 500 RU/s. Vous n’observerez aucun changement de niveau de débit.

Dans chacun de ces cas, une fois la migration de votre collection effectuée, vous serez en mesure de personnaliser votre niveau de débit, ou de le mettre à l’échelle selon vos besoins afin de fournir un accès à faible latence à vos utilisateurs. Pour modifier le niveau de débit une fois votre collection migrée, il vous suffit d’ouvrir votre compte Cosmos DB dans le portail Azure, puis de cliquer sur Mettre à l’échelle, de choisir votre collection et enfin d’ajuster le niveau du débit, comme illustré dans la capture d’écran suivante :

![Mise à l’échelle du débit dans le portail Azure](./media/performance-levels/portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>Qu’est-ce qui va changer au niveau de ma facturation suite à la migration vers des collections à partition unique ?

Supposons que vous avez 10 collections S1, d’une capacité de stockage de 1 Go chacune, dans la région Est des États-Unis et que vous migrez ces 10 collections S1 vers 10 collections à partition unique dotées d’un débit de 400 RU/s (niveau minimal). Votre facture se présentera comme suit si vous conservez les 10 collections à partition unique pendant un mois complet :

![Comparaison entre la tarification de 10 collections S1 et de 10 collections utilisant la tarification d’une collection à partition unique](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Que se passe-t-il si j’ai besoin de plus de 10 Go de stockage ?

Que vous disposiez d’une collection avec un niveau de performances S1, S2 ou S3 ou d’une collection à partition unique disposant de 10 Go de stockage disponible, vous pouvez utiliser l’outil de migration de données Cosmos DB pour migrer vos données vers une collection partitionnée bénéficiant d’un stockage quasi illimité. Pour plus d’informations sur les avantages d’une collection partitionnée, voir [Partitionnement et mise à l’échelle dans Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Puis-je modifier les niveaux de performances entre S1, S2 et S3 avant la migration planifiée ?

Seuls les comptes existants avec des niveaux de performances S1, S2 et S3 seront en mesure de modifier et d’ajuster la hiérarchie des niveaux de performances via le portail ou par programme. Si vous remplacez une collection S1, S2 ou S3 par une collection à partition unique, vous ne pouvez plus revenir aux niveaux de performances S1, S2 ou S3.

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>Comment serai-je informé de la migration de ma collection ?

La migration aura lieu en fin d’année 2017. Si vous avez une collection qui utilise les niveaux de performances S1, S2 ou S3, l’équipe Cosmos DB vous contactera par e-mail avant de lancer la migration. Une fois la migration terminée, le portail Azure indiquera que votre collection utilise la tarification Standard.

![Confirmation de la migration de votre collection vers le niveau tarifaire Standard](./media/performance-levels/portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Comment procéder à la migration des niveaux de performances S1, S2 et S3 vers des collections à partition unique par moi-même ?

Vous pouvez effectuer la migration des niveaux de performance S1, S2 et S3 vers des collections à partition unique via le portail Azure ou par programme. Vous pouvez effectuer cette opération avant la migration planifiée pour tirer parti des options de débit flexibles disponibles pour les collections à partition unique. Sinon, nous nous chargerons de la migration de vos collections en fin d’année 2017.

**Migration vers des collections à partition unique à l’aide du portail Azure**

1. Dans le [**portail Azure**](https://portal.azure.com), cliquez sur **Azure Cosmos DB**, puis sélectionnez le compte Cosmos DB à modifier. 
 
    Si **Azure Cosmos DB** ne figure pas dans la barre de lancement, cliquez sur >, accédez à **Bases de données**, sélectionnez **Azure Cosmos DB**, puis sélectionnez le compte.  

2. Dans le menu de la ressource, sous **Conteneurs**, cliquez sur **Mettre à l’échelle**, sélectionnez la collection à modifier dans la liste déroulante, puis cliquez sur **Niveau tarifaire**. Les comptes utilisant un débit prédéfini ont un niveau tarifaire de S1, S2 ou S3.  Dans la page **Choisir votre niveau tarifaire**, cliquez sur **Standard** pour basculer vers le débit défini par l’utilisateur, puis cliquez sur **Sélectionner** pour enregistrer vos modifications.

    ![Capture d’écran de la page Paramètres montrant où modifier la valeur de débit](./media/performance-levels/change-performance-set-thoughput.png)

3. De retour dans la page **Mettre à l’échelle**, le **Niveau tarifaire** est défini sur **Standard** et la zone **Débit (UR/s)** est affichée avec une valeur par défaut de 400. Définissez le débit entre 400 et 10 000 [unités de requête](request-units.md)/seconde (unités de requête/s). **L’Estimation de la facture mensuelle** au bas de la page se met à jour automatiquement pour donner une estimation du coût mensuel. 

    >[!IMPORTANT] 
    > Une fois que vous enregistrez vos modifications et que vous passez au niveau tarifaire Standard, vous ne pouvez pas restaurer les niveaux de performances S1, S2 ou S3.

4. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

    Si vous déterminez que vous avez besoin d’un débit supérieur (plus de 10 000 unités de requête/s) ou d’une plus grande capacité de stockage (plus de 10 Go), vous pouvez créer une collection partitionnée. Pour migrer une collection à partition unique vers une collection partitionnée, voir [Migration de collections à partition unique vers des collections partitionnées](sql-api-partition-data.md#migrating-from-single-partition).

    > [!NOTE]
    > Le passage du niveau S1, S2 ou S3 au niveau Standard peut prendre jusqu’à deux minutes.
    > 
    > 

**Migration vers des collections à partition unique à l’aide du Kit SDK .NET**

Vous pouvez également modifier les niveaux de performances de vos collections via les Kits de développement logiciel (SDK) Azure Cosmos DB. Cette section couvre uniquement la modification du niveau de performances d’une collection à l’aide de [l’API .NET SQL](sql-api-sdk-dotnet.md), mais le processus est similaire pour nos autres SDK.

Voici un extrait de code permettant de remplacer le débit de la collection par 5 000 unités de requête par seconde :
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Visitez [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) pour afficher des exemples supplémentaires et en savoir plus sur nos méthodes d’offre :

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Quelles sont les conséquences pour moi en tant que client Contrat Entreprise ?

Les clients Contrat Entreprise bénéficieront d’une protection en matière de tarification jusqu’à la fin de leur contrat actuel.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la tarification et la gestion des données avec Azure Cosmos DB, explorez les ressources suivantes :

1.  [Partitioning data in Cosmos DB (Partitionnement des données dans Cosmos DB)](sql-api-partition-data.md). Découvrez la différence entre les conteneurs à partition unique et les conteneurs partitionnés, et bénéficiez de conseils concernant l’implémentation d’une stratégie de partitionnement pour une mise à l’échelle en toute transparence.
2.  [Tarification Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Apprenez-en davantage sur le coût de l’approvisionnement du débit et de la consommation du stockage.
3.  [Unités de requête](request-units.md). Découvrez la consommation de débit pour les différents types d’opérations, telles que les opérations de lecture, d’écriture et de requête.
