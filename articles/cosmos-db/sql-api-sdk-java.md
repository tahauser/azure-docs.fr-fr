---
title: 'Azure Cosmos DB : API Java SQL, Kit SDK et ressources | Microsoft Docs'
description: Découvrez l’API et le Kit de développement logiciel (SDK) Java SQL, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit de développement logiciel (SDK) Java SQL d’Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 11/14/2017
ms.author: khdang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8917e85ce5fb0e43593c34fc83046ec727e4aac
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Kit SDK Java Azure Cosmos DB pour API SQL : notes de publication et ressources
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

Le Kit de développement logiciel (SDK) de l’API Java SQL prend en charge les opérations synchrones. Pour la prise en charge asynchrone, utilisez le [Kit de développement (SDK) Java asynchrone de l’API SQL](sql-api-sdk-async-java.md). 

<table>

<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**Documentation de l’API**</td><td>[Documentation de référence sur l’API Java](/java/api/com.microsoft.azure.documentdb)</td></tr>

<tr><td>**Contribution au Kit de développement logiciel (SDK)**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Prise en main**</td><td>[Bien démarrer avec le Kit de développement logiciel (SDK) Java](sql-api-java-get-started.md)</td></tr>

<tr><td>**Didacticiel d’application web**</td><td>[Développement d’applications web avec Azure Cosmos DB](sql-api-java-application.md)</td></tr>

<tr><td>**Runtime minimal pris en charge**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Amélioration des performances de la sérialisation Json.
* Cette version du kit de développement logiciel (SDK) nécessite la dernière version de l’émulateur Azure Cosmos DB, que vous pouvez télécharger à l’adresse https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Changements internes concernant les bibliothèques Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Correction d’un problème de lecture des plages de clés de partition uniques.
* Correction d’un problème d’analyse d’ID de ressource affectant les bases de données avec des noms courts.
* Correction d’un problème dû à l’encodage des clés de partition.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Correctifs de bogues critiques pour demander le traitement lors de fractionnements de partition.
* Correction d’un problème avec les niveaux de cohérence Strong et BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Prise en charge ajoutée pour un nouveau niveau de cohérence nommé ConsistentPrefix.
* Correction d’un bogue de lecture de collection en mode de session.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Activation de la prise en charge de la collection partitionnée avec au minimum 2 500 RU/seconde et des incréments d’évolution de 100 RU/seconde.
* Correction d’un bogue dans l’assembly natif, qui pouvait entraîner des exceptions NullRef pour certaines requêtes.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Correction d’un bogue dans la configuration du moteur de requête qui peut provoquer des exceptions pour les requêtes en mode de passerelle.
* Correction de quelques bogues dans le conteneur de session qui peut provoquer une exception « Ressource propriétaire introuvable » pour les demandes dès la création de la collection.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Ajout de la prise en charge des requêtes d’agrégation (COUNT, MIN, MAX, SUM et AVG). Consultez l’article [Aggregation support (Prise en charge de l’agrégation)](sql-api-sql-query.md#Aggregates).
* Ajout de la prise en charge de la modification de flux.
* Ajout de la prise en charge des informations relatives aux quotas de collections via RequestOptions.setPopulateQuotaInfo.
* Ajout de la prise en charge de l’enregistrement de script de procédure stockée via RequestOptions.setScriptLoggingEnabled.
* Correction d’un bogue dans lequel la requête en mode DirectHttps peut se bloquer lorsqu’elle rencontre des échecs de limitation.
* Correction d’un bogue dans le mode de cohérence de session.
* Correction d’un bogue susceptible d’entraîner l’exception NullReferenceException dans HttpContext lorsque le taux de demandes est élevé.
* Amélioration des performances du mode DirectHttps.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Ajout de la prise en charge simple d’un proxy basé sur les instances d’un client avec l’API ConnectionPolicy.setProxy().
* Ajout de l’API DocumentClient.close() permettant de fermer correctement l’instance DocumentClient.
* Amélioration des performances de requête en mode de connectivité directe en dérivant le plan de requête à partir de l’assembly natif au lieu de la passerelle.
* Définissez FAIL_ON_UNKNOWN_PROPERTIES = false, de sorte que les utilisateurs n’aient pas besoin de définir JsonIgnoreProperties dans leur POJO.
* Journalisation refactorisée pour utiliser SLF4J.
* Correction de quelques autres bogues dans un lecteur de cohérence.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Correction d’un bogue dans la gestion des connexions pour éviter les fuites de connexion en mode de connectivité directe.
* Correction d’un bogue dans la requête TOP des exceptions NullReference peuvent être levées.
* Amélioration des performances avec la réduction du nombre d’appels réseau pour les caches internes.
* Ajout d’un code d’état, d’un ID d’activité et d’un URI de requête dans DocumentClientException pour une meilleure résolution des problèmes.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Résolution d’un problème de gestion des connexions pour plus de stabilité.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Ajout de la prise en charge du niveau de cohérence BoundedStaleness.
* Ajout de la prise en charge de la connectivité directe pour les opérations CRUD pour les collections partitionnées.
* Correction d’un bogue dans l’interrogation d’une base de données avec SQL.
* Correction d’un bogue dans le cache de session où le jeton de session peut être défini de manière incorrecte.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Ajout de la prise en charge des requêtes parallèles sur plusieurs partitions.
* Ajout de la prise en charge des requêtes TOP/ORDER BY pour les collections partitionnées.
* Ajout de la prise en charge de la cohérence forte.
* Ajout de la prise en charge des requêtes en fonction du nom lors de l’utilisation de la connectivité directe.
* Correction visant à rendre ActivityId cohérent entre toutes les nouvelles tentatives de requête.
* Correction d’un bogue lié au cache de session lors de la nouvelle création d’une collection avec le même nom.
* Ajout des types de données Polygone et LineString lors de la spécification de la stratégie d’indexation de collection pour les requêtes spatiales à délimitation géographique.
* Résolution des problèmes liés à Java Doc pour Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Correction d’un bogue dans PartitionKeyDefinitionMap pour la mise en cache de collections de partitions uniques, sans aucune extraction supplémentaire des demandes de clés de partition.
* Correction d’un bogue pour l’absence de nouvelle tentative en cas de valeur de clé de partition incorrecte.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Ajout de la prise en charge des comptes de base de données de plusieurs régions.
* Ajout de la prise en charge d’une nouvelle tentative automatique sur les requêtes limitées avec options de personnalisation du nombre maximum de tentatives et du délai d’attente maximum entre deux tentatives.  Voir RetryOptions et ConnectionPolicy.getRetryOptions().
* Propriété IPartitionResolver déconseillée basée sur un code de partitionnement personnalisé. Utilisez des collections partitionnées pour bénéficier d’un niveau de stockage et de débit supérieur.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Ajout de la prise en charge d’une stratégie de nouvelle tentative pour la limitation.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Ajout de la prise en charge de la durée de vie (TTL) pour les documents.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implémentation des [collections partitionnées](partition-data.md) et des [niveaux de performances définis par l’utilisateur](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Correction d’un bogue dans HashPartitionResolver pour générer des valeurs de hachage en little-endian dans un souci de cohérence avec les autres kits de développement logiciel.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Ajoutez des programmes de résolution de partitions par hachage et par spécification de plages de valeurs pour vous aider lors du partitionnement des applications sur plusieurs partitions.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implémentation de l’opération Upsert. Nouvelles méthodes upsertXXX ajoutées pour prendre en charge la fonctionnalité Upsert.
* Implémenter l'ID en fonction du routage. Aucune modification d'API publique, toutes les modifications en interne.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Version ignorée pour aligner le numéro de version avec les autres Kits de développement logiciel (SDK)

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Prise en charge de l'index géospatial
* Validation de la propriété ID pour toutes les ressources. Les ID des ressources ne peuvent pas contenir les caractères ?, /, #, \, ou se terminer par un espace.
* Ajout du nouvel en-tête « progression de la transformation de l’index » à ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implémente la stratégie d'indexation V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Kit SDK GA

## <a name="release-and-retirement-dates"></a>Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit de développement logiciel (SDK) actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du Kit de développement logiciel (SDK) dès que possible.

Le service rejette toute requête envoyée à Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé.

> [!WARNING]
> Toutes les versions du Kit de développement logiciel (SDK) SQL pour Java antérieures à la version **1.0.0** ont été supprimées le **29 février 2016**.
> 
> 

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.15.0](#1.15.0) |14 novembre 2017 |--- |
| [1.14.0](#1.14.0) |28 octobre 2017 |--- |
| [1.13.0](#1.13.0) |25 août 2017 |--- |
| [1.12.0](#1.12.0) |11 juillet 2017 |--- |
| [1.11.0](#1.11.0) |10 mai 2017 |--- |
| [1.10.0](#1.10.0) |11 mars 2017 |--- |
| [1.9.6](#1.9.6) |21 février 2017 |--- |
| [1.9.5](#1.9.5) |31 janvier 2017 |--- |
| [1.9.4](#1.9.4) |24 novembre 2016 |--- |
| [1.9.3](#1.9.3) |30 octobre 2016 |--- |
| [1.9.2](#1.9.2) |28 octobre 2016 |--- |
| [1.9.1](#1.9.1) |26 octobre 2016 |--- |
| [1.9.0](#1.9.0) |3 octobre 2016 |--- |
| [1.8.1](#1.8.1) |30 juin 2016 |--- |
| [1.8.0](#1.8.0) |14 juin 2016 |--- |
| [1.7.1](#1.7.1) |30 avril 2016 |--- |
| [1.7.0](#1.7.0) |27 avril 2016 |--- |
| [1.6.0](#1.6.0) |29 mars 2016 |--- |
| [1.5.1](#1.5.1) |31 décembre 2015 |--- |
| [1.5.0](#1.5.0) |4 décembre 2015 |--- |
| [1.4.0](#1.4.0) |5 octobre 2015 |--- |
| [1.3.0](#1.3.0) |5 octobre 2015 |--- |
| [1.2.0](#1.2.0) |5 août 2015 |--- |
| [1.1.0](#1.1.0) |9 juillet 2015 |--- |
| [1.0.1](#1.0.1) |12 mai 2015 |--- |
| [1.0.0](#1.0.0) |7 avril 2015 |--- |
| 0.9.5-prelease |9 mars 2015 |29 février 2016 |
| 0.9.4-prelease |17 février 2015 |29 février 2016 |
| 0.9.3-prelease |13 janvier 2015 |29 février 2016 |
| 0.9.2-prelease |19 décembre 2014 |29 février 2016 |
| 0.9.1-prelease |19 décembre 2014 |29 février 2016 |
| 0.9.0-prelease |10 décembre 2014 |29 février 2016 |

## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

