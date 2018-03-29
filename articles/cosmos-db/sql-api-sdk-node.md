---
title: 'Azure Cosmos DB : API, Kit SDK et ressources Node.js SQL | Microsoft Docs'
description: Tout savoir sur l’API et le Kit SDK Node.js SQL, y compris les dates de sortie, les dates de déclassement et les modifications effectuées entre chaque version du kit SDK Node.js Azure Cosmos DB.
services: cosmos-db
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 1/4/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58d043ac7037567d37321cb874804ad0e6cb5ed8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Kit SDK Node.js Azure Cosmos DB pour API SQL : notes de publication et ressources
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

<table>

<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**Documentation de l’API**</td><td>[Documentation de référence de l’API Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**Instructions d’installation du Kit de développement logiciel (SDK)**</td><td>[Instructions d’installation](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Contribution au Kit de développement logiciel (SDK)**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Exemples**</td><td>[Exemples de code Node.js](sql-api-nodejs-samples.md)</td></tr>

<tr><td>**Didacticiel de prise en main**</td><td>[Prise en main du Kit de développement logiciel (SDK) Node.js](sql-api-nodejs-get-started.md)</td></tr>

<tr><td>**Didacticiel d’application web**</td><td>[Générer une application web Node.js à l’aide d’Azure Cosmos DB](sql-api-nodejs-application.md)</td></tr>

<tr><td>**Plateforme actuellement prise en charge**</td><td> 
[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/)<br/> 
[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> 
[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> 
[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 
</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

### <a name="1.14.2"/>1.14.2</a>
* Documentation mise à jour pour référencer Azure Cosmos DB au lieu d’Azure DocumentDB.
* Ajout de prise en charge pour le paramètre proxyUrl dans ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Correctif mineur pour les systèmes de fichiers respectant la casse.

### <a name="1.14.0"/>1.14.0</a>
* Ajout de la prise en charge de la cohérence de session.
* Cette version du kit de développement logiciel (SDK) nécessite la dernière version de l’émulateur Azure Cosmos DB, que vous pouvez télécharger à l’adresse https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Requêtes Splitproofed entre partitions.
* Ajout de la prise en charge des liens de ressources comprenant des barres obliques de début et de fin (et des tests correspondants).

### <a name="1.12.2"/>1.12.2</a>
*   Documentation npm mise à jour.

### <a name="1.12.1"/>1.12.1</a>
* Correction d’un bogue dans executeStoredProcedure où les documents impliqués disposaient de caractères Unicode spéciaux (LS, PS).
* Correction d’un bogue lors de la gestion de documents contenant des caractères Unicode dans la clé de partition.
* Prise en charge rétablie de la création de collections avec le support de nom. Problème GitHub n° 114.
* Prise en charge rétablie du jeton d’autorisation. Problème GitHub n° 178.

### <a name="1.12.0"/>1.12.0</a>
* Ajout de la prise en charge d’un nouveau [niveau de cohérence](consistency-levels.md) nommé ConsistentPrefix.
* Ajout de la prise en charge de UriFactory.
* Correction d’un bogue de prise en charge des caractères Unicode. Problème GitHub n° 171.

### <a name="1.11.0"/>1.11.0</a>
* Ajout de la prise en charge des requêtes d’agrégation (COUNT, MIN, MAX, SUM et AVG).
* Ajout de l’option permettant de contrôler le degré de parallélisme des requêtes entre les partitions.
* Ajout de l’option permettant de désactiver la vérification SSL en cas d’exécution sur l’émulateur Azure Cosmos DB.
* Débit minimal réduit sur les collections partitionnées de 10 100 unités de demande/s à 2 500 unités de demande/s.
* Correction du bogue de jeton de continuation pour la collection à partition unique. Problème GitHub n° 107.
* Correction du bogue executeStoredProcedure lors de la gestion du 0 comme paramètre unique. Problème GitHub n° 155.

### <a name="1.10.2"/>1.10.2</a>
* En-tête agent utilisateur fixe pour inclure la version du Kit de développement logiciel (SDK).
* Nettoyage de code mineur.

### <a name="1.10.1"/>1.10.1</a>
* Désactiver la vérification SSL lors de l’utilisation du Kit de développement logiciel (SDK) pour cibler l’émulateur (hostname=localhost).
* Ajout de la prise en charge de l’activation de la journalisation de script pendant l’exécution de la procédure stockée.

### <a name="1.10.0"/>1.10.0</a>
* Ajout de la prise en charge des requêtes parallèles sur plusieurs partitions.
* Ajout de la prise en charge des requêtes TOP/ORDER BY pour les collections partitionnées.

### <a name="1.9.0"/>1.9.0</a>
* Ajout de la prise en charge d’une stratégie de nouvelle tentative pour les requêtes limitées. (Les requêtes limitées reçoivent une exception de taux de requête excessif, code d’erreur 429.) Par défaut, Azure Cosmos DB accepte neuf nouvelles tentatives pour chaque requête lorsque le code d’erreur 429 est renvoyé, conformément à l’heure de retryAfter spécifiée dans l’en-tête de réponse. Il est désormais possible de définir un intervalle fixe de nouvelle tentative dans la propriété RetryOptions sur l’objet ConnectionPolicy, si vous souhaitez ignorer le temps retryAfter retourné par le serveur entre chaque nouvelle tentative. Azure Cosmos DB attend maintenant au maximum 30 secondes pour chaque requête limitée (quel que soit le nombre de nouvelles tentatives) et renvoie la réponse avec un code d’erreur 429. Cette durée peut également être remplacée dans la propriété RetryOptions sur l’objet ConnectionPolicy.
* Cosmos DB renvoie maintenant x-ms-throttle-retry-count et x-ms-throttle-retry-wait-time-ms comme en-têtes de réponse dans chaque requête pour signaler le nombre limite de nouvelles tentatives et le cumul de temps d’attente observé par la requête entre les nouvelles tentatives.
* La classe RetryOptions a été ajoutée pour exposer la propriété RetryOptions sur la classe ConnectionPolicy, qui peut être utilisée pour substituer certaines des options de nouvelle tentative par défaut.

### <a name="1.8.0"/>1.8.0</a>
* Ajout de la prise en charge des comptes de base de données de plusieurs régions.

### <a name="1.7.0"/>1.7.0</a>
* Ajout de la fonctionnalité de durée de vie (TTL) pour les documents.

### <a name="1.6.0"/>1.6.0</a>
* Implémentation des [collections partitionnées](partition-data.md) et des [niveaux de performances définis par l’utilisateur](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Résolution du bogue RangePartitionResolver.resolveForRead là où les liens n’étaient pas renvoyés en raison d’une concaténation incorrecte des résultats.

### <a name="1.5.5"/>1.5.5</a>
* Résout hashParitionResolver resolveForRead() : levait une exception si aucune clé de partition n’était fournie, au lieu de renvoyer une liste de tous les liens enregistrés.

### <a name="1.5.4"/>1.5.4</a>
* Résolution du problème [n° 100](https://github.com/Azure/azure-documentdb-node/issues/100) : Agent HTTPS dédié : éviter de modifier l’agent global pour les besoins d’Azure Cosmos DB. Utilisez un agent dédié pour toutes les demandes de la bibliothèque.

### <a name="1.5.3"/>1.5.3</a>
* Résolution du problème [n° 81](https://github.com/Azure/azure-documentdb-node/issues/81) : gestion correcte des tirets dans les ID de média.

### <a name="1.5.2"/>1.5.2</a>
* Résolution du problème [n° 95](https://github.com/Azure/azure-documentdb-node/issues/95) : avertissement de fuite de l’écouteur EventEmitter.

### <a name="1.5.1"/>1.5.1</a>
* Résolution du problème [n° 92](https://github.com/Azure/azure-documentdb-node/issues/90) : dossier Hash renommé en hash pour les systèmes respectant la casse.

### <a name="1.5.0"/>1.5.0</a>
* Implémentation de la prise en charge du partitionnement via l’ajout de programmes de résolution de partitions de hachage et de plage.

### <a name="1.4.0"/>1.4.0</a>
* Implémentation de l’opération Upsert. Nouvelles méthodes upsertXXX sur documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Ignorée pour aligner le numéro de version avec les autres Kits de développement logiciel (SDK).

### <a name="1.2.2"/>1.2.2</a>
* Fractionnement du wrapper des promesses Q dans un nouveau dépôt.
* Mise à jour du fichier de package pour le Registre npm.

### <a name="1.2.1"/>1.2.1</a>
* Implémentation du routage basé sur l’ID.
* Résolution du problème [n° 49](https://github.com/Azure/azure-documentdb-node/issues/49) - propriété actuelle en conflit avec la méthode current().

### <a name="1.2.0"/>1.2.0</a>
* Ajout de la prise en charge de l’index géospatial.
* Validation de la propriété ID pour toutes les ressources. Les ID des ressources ne peuvent pas contenir les caractères ?, /, #, &#47;&#47; ou se terminer par un espace.
* Ajout du nouvel en-tête « progression de la transformation de l’index » à ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implémente la stratégie d’indexation V2.

### <a name="1.0.3"/>1.0.3</a>
* Problème [n° 40](https://github.com/Azure/azure-documentdb-node/issues/40) : implémentation des configurations eslint et grunt dans le Kit de développement logiciel (SDK) principal et de promesse.

### <a name="1.0.2"/>1.0.2</a>
* Problème [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Le wrapper de promesses n’inclut pas d’en-tête avec erreur.

### <a name="1.0.1"/>1.0.1</a>
* Implémentation de la possibilité de créer une requête pour les conflits en ajoutant readConflicts, readConflictAsync et queryConflicts.
* Mise à jour de la documentation de l’API.
* Problème [n° 41](https://github.com/Azure/azure-documentdb-node/issues/41) : Erreur client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>
* Kit de développement logiciel (SDK) GA

## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft envoie une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible.

Le service rejette toute requête envoyée à Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.14.2](#1.14.2) |21 décembre 2017 |--- |
| [1.14.1](#1.14.1) |10 novembre 2017 |--- |
| [1.14.0](#1.14.0) |9 novembre 2017 |--- |
| [1.13.0](#1.13.0) |11 octobre 2017 |--- |
| [1.12.2](#1.12.2) |10 août 2017 |--- |
| [1.12.1](#1.12.1) |10 août 2017 |--- |
| [1.12.0](#1.12.0) |10 mai 2017 |--- |
| [1.11.0](#1.11.0) |16 mars 2017 |--- |
| [1.10.2](#1.10.2) |27 janvier 2017 |--- |
| [1.10.1](#1.10.1) |22 décembre 2016 |--- |
| [1.10.0](#1.10.0) |3 octobre 2016 |--- |
| [1.9.0](#1.9.0) |7 juillet 2016 |--- |
| [1.8.0](#1.8.0) |14 juin 2016 |--- |
| [1.7.0](#1.7.0) |26 avril 2016 |--- |
| [1.6.0](#1.6.0) |29 mars 2016 |--- |
| [1.5.6](#1.5.6) |8 mars 2016 |--- |
| [1.5.5](#1.5.5) |2 février 2016 |--- |
| [1.5.4](#1.5.4) |1er février 2016 |--- |
| [1.5.2](#1.5.2) |26 janvier 2016 |--- |
| [1.5.2](#1.5.2) |22 janvier 2016 |--- |
| [1.5.1](#1.5.1) |4 janvier 2016 |--- |
| [1.5.0](#1.5.0) |31 décembre 2015 |--- |
| [1.4.0](#1.4.0) |6 octobre 2015 |--- |
| [1.3.0](#1.3.0) |6 octobre 2015 |--- |
| [1.2.2](#1.2.2) |10 septembre 2015 |--- |
| [1.2.1](#1.2.1) |15 août 2015 |--- |
| [1.2.0](#1.2.0) |5 août 2015 |--- |
| [1.1.0](#1.1.0) |9 juillet 2015 |--- |
| [1.0.3](#1.0.3) |4 juin 2015 |--- |
| [1.0.2](#1.0.2) |23 mai 2015 |--- |
| [1.0.1](#1.0.1) |15 mai 2015 |--- |
| [1.0.0](#1.0.0) |8 avril 2015 |--- |

## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

