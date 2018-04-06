---
title: Basculement régional dans Azure Cosmos DB | Microsoft Docs
description: Découvrez le fonctionnement du basculement manuel et automatique avec Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: jhubbard
editor: ''
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a4bdc49c5ab36a5026095b5d7b6f9856b020e1b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Basculement régional automatique pour la continuité des activités dans Azure Cosmos DB
Azure Cosmos DB simplifie la distribution globale de données en gérant complètement les [comptes de bases de données multirégions](distribute-data-globally.md) qui fournissent des compromis clairs entre cohérence, disponibilité et performance, le tout avec les garanties correspondantes. Les comptes Cosmos DB offrent des capacités de haute disponibilité, des latences inférieures à 10 millisecondes, des [niveaux de cohérence bien définis](consistency-levels.md), un basculement régional transparent avec des API multihébergement et la possibilité de mettre à l’échelle le débit et le stockage dans le monde entier de manière flexible. 

Cosmos DB prend en charge des basculements explicites et basés sur des stratégies qui vous permettent de contrôler le comportement du système de bout en bout en cas de défaillance. Dans cet article, nous allons répondre aux questions suivantes :

* Comment fonctionnent les basculements manuels dans Cosmos DB ?
* Comment fonctionnent les basculements automatiques dans Cosmos DB et que se passe-t-il lorsqu’un centre de données tombe en panne ?
* Comment utiliser les basculements manuels dans différentes architectures d’application ?

Cette vidéo réalisée par le responsable du programme Azure Cosmos DB, qui illustre les fonctionnalités de distribution globale, présente notamment les fonctionnalités de basculement régional.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Configuration d'applications multi-régions
Avant de nous plonger dans les modes de basculement, nous allons examiner comment vous pouvez configurer une application pour tirer parti de plusieurs régions disponibles et être résilient face aux basculements régionaux.

* Tout d’abord, déployez votre application multi-régions
* Pour garantir un accès à faible latence à partir de chaque région de votre application, configurez la [liste des régions préférées](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) pour chaque région par le biais de l'un des kits de développement logiciel (SDK) pris en charge.

L’extrait de code suivant montre comment initialiser une application multi-régions. Ici, le compte Azure Cosmos DB `contoso.documents.azure.com` est configuré avec deux régions : États-Unis de l’Ouest et Europe du Nord. 

* L’application est déployée dans la région États-Unis de l'Ouest (par exemple, à l’aide d'Azure App Service) 
* Configuré avec `West US` en tant que première région préférée pour des lectures à faible latence
* Configuré avec `North Europe` comme deuxième région préférée (pour une haute disponibilité durant les défaillances régionales)

Dans l’API SQL, cette configuration ressemble à l’extrait de code suivant :

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
    usConnectionPolicy);
```

L’application est également déployée dans la région Europe du Nord, dans l’ordre inversé des régions préférées. Autrement dit, la région Europe du Nord est spécifiée en premier pour les lectures à faible latence. La région États-Unis de l'Ouest est ensuite spécifiée comme deuxième région préférée pour une haute disponibilité durant les défaillances régionales.

Le schéma d’architecture suivant montre un déploiement d’application multirégion où Cosmos DB et l’application sont configurés de manière à être disponibles dans quatre régions géographiques Azure.  

![Déploiement d’une application distribuée dans le monde entier avec Azure Cosmos DB](./media/regional-failover/app-deployment.png)

À présent, nous allons voir comment le service Cosmos DB gère les défaillances régionales par le biais de basculements automatiques. 

## <a id="AutomaticFailovers"></a>Basculements automatiques
Dans des cas rares de panne régionale Azure ou de panne de centre de données, Cosmos DB déclenche automatiquement le basculement de tous les comptes Cosmos DB présents dans la région affectée. 

**Que se passe-t-il si une région de lecture connaît une panne ?**

Les comptes Cosmos DB avec une région de lecture dans l’une des régions affectées sont automatiquement déconnectés de leur région d’écriture et marqués comme hors connexion. Les kits de développement logiciel (SDK) Cosmos DB implémentent un protocole de découverte régionale qui leur permet de détecter automatiquement si une région est disponible et de rediriger les appels en lecture vers la prochaine région disponible dans la liste des régions préférées. Si aucune des régions dans la liste des régions préférées n'est disponible, les appels sont automatiquement acheminés vers la zone d’écriture en cours. Aucune modification n’est nécessaire dans votre code d’application pour gérer les basculements régionaux. Pendant tout le processus, des garanties de cohérence continuent à être respectées par Cosmos DB.

![Défaillances de la région de lecture dans Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

Une fois que la région affectée récupère après une panne, tous les comptes Cosmos DB affectés dans la région sont récupérés automatiquement par le service. Les comptes Cosmos DB ayant une région de lecture dans la région affectée sont automatiquement synchronisés sur la région d’écriture en cours et mis en ligne. Les kits de développement logiciel (SDK) Cosmos DB détectent la disponibilité de la nouvelle région et évaluent si la région doit être sélectionnée en tant que région de lecture en cours sur la base de la liste des régions préférées configurée par l’application. Les lectures suivantes sont redirigées vers la région récupérée sans modification nécessaire de votre code d’application.

**Que se passe-t-il si une région d'écriture connaît une panne ?**

Si la région affectée est la zone d’écriture actuelle et que le basculement automatique est activé pour le compte Azure Cosmos DB, la région est automatiquement marquée comme hors connexion. Ensuite, une autre région est promue comme région d’écriture pour le compte Azure Cosmos DB affecté. Vous pouvez activer le basculement automatique et contrôler totalement l’ordre de sélection des régions pour vos comptes Azure Cosmos DB par le biais du portail Azure ou [par programmation](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Priorités de basculement pour Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Pendant les basculements automatiques, Azure Cosmos DB choisit automatiquement la prochaine région d’écriture d’un compte Azure Cosmos DB donné en fonction de l’ordre de priorité spécifié. Les applications peuvent utiliser la propriété WriteEndpoint de la classe DocumentClient pour détecter le changement de région d’écriture.

![Défaillances de la région d’écriture dans Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

Une fois que la région affectée récupère après une panne, tous les comptes Cosmos DB affectés dans la région sont récupérés automatiquement par le service. 

* Les données présentes dans la région d’écriture précédente qui n’ont pas été répliquées vers les régions de lecture pendant la panne sont publiées en tant que flux de conflit. Les applications peuvent lire le flux de conflit, résoudre les conflits en fonction de la logique propre à l’application, et réécrire les données mises à jour dans le compte Azure Cosmos DB comme il convient. 
* La région d’écriture précédente est recréée en tant que région de lecture et remise en ligne automatiquement. 
* Vous pouvez reconfigurer la région de lecture qui a été remise en ligne automatiquement en tant que région d’écriture en effectuant un basculement manuel par le biais du portail Azure ou [par programmation](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

L’extrait de code suivant montre comment traiter les conflits une fois que la région affectée a récupéré suite à la panne.

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>Basculements manuels

Outre les basculements automatiques, la région d’écriture en cours d’un compte Cosmos DB donné peut être modifiée manuellement de manière dynamique en l’une des régions de lecture existantes. Les basculements manuels peuvent être lancés par le biais du portail Azure ou [par programme](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Les basculements manuels assurent **qu’aucune perte de données** ni **aucune diminution de la disponibilité** ne se produit et que l’état d’écriture est transféré correctement de l’ancienne région d’écriture vers la nouvelle pour le compte Cosmos DB spécifié. Comme dans les basculements automatiques, le kit de développement logiciel (SDK) Cosmos DB gère automatiquement les modifications de région d’écriture pendant les basculements manuels et garantit que les appels sont automatiquement redirigés vers la nouvelle zone d’écriture. Aucune modification de code ou de configuration n’est nécessaire dans votre application pour gérer les basculements. 

![Basculements manuels dans Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Voici certains des scénarios courants où le basculement manuel peut être utile :

**Suivre le modèle d’horloge** : si vos applications ont des schémas de trafic prévisible basés sur l’heure, vous pouvez modifier régulièrement l’état de l’écriture sur la région géographique plus active selon l’heure.

**Mise à jour du service** : certains déploiements d’applications distribués globalement peuvent impliquer la redirection du trafic vers une région différente par le biais du Traffic Manager lors de la mise à jour de service planifiée. De tels déploiements d’applications peuvent utiliser le basculement manuel pour conserver l’état d’écriture de la région où il y aura un trafic actif pendant la mise à jour du service.

**Simulations de continuité d’activité et de récupération d’urgence (Business Continuity and Disaster Recovery, BCDR) et Haute disponibilité et récupération d’urgence (High Availability and Disaster Recovery, HADR)** : la plupart des applications d’entreprise incluent des tests de continuité d’activité dans le cadre de leurs processus de développement et de mise sur le marché. Les tests BCDR et HADR sont souvent une étape importante de certification de conformité et de garantie de disponibilité en cas de pannes régionales. Vous pouvez tester la disponibilité de BCDR dans vos applications qui utilisent Cosmos DB pour le stockage en déclenchant un basculement manuel de votre compte Cosmos DB et/ou en ajoutant et en supprimant une région dynamiquement.

Dans cet article, nous avons examiné le fonctionnement des basculements manuels et automatiques dans Cosmos DB et comment vous pouvez configurer vos comptes Cosmos DB et les applications de manière qu’ils soient disponibles au niveau mondial. Avec la prise en charge de la réplication au niveau mondial de Cosmos DB, vous pouvez améliorer la latence de bout en bout et garantir une haute disponibilité même en cas de défaillance de la région. 

## <a id="NextSteps"></a>Étapes suivantes
* Découvrez-en plus sur la manière dont Cosmos DB prend en charge la [distribution mondiale](distribute-data-globally.md)
* Découvrez-en plus sur [la cohérence globale avec Azure Cosmos DB](consistency-levels.md)
* Développez en mode multirégion à l’aide de [l’API SQL](tutorial-global-distribution-sql-api.md) d’Azure Cosmos DB
* Découvrez comment créer [des architectures d’écriture multirégions](multi-region-writers.md) avec Azure Cosmos DB

