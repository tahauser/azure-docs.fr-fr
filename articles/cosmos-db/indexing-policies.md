---
title: Stratégies d’indexation d’Azure Cosmos DB | Microsoft Docs
description: Comprendre le fonctionnement de l’indexation dans Azure Cosmos DB. Découvrez comment configurer et modifier la stratégie d’indexation pour bénéficier d’une indexation automatique et de meilleures performances.
keywords: fonctionnement de l’indexation, indexation automatique, base de données d’indexation
services: cosmos-db
documentationcenter: ''
author: rafats
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 5610c5fdc6a04f9ef13d2e4592f0d7e5d8eba30c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Comment Azure Cosmos DB indexe-t-il les données ?

Par défaut, toutes les données d’Azure Cosmos DB sont indexées. Bien que de nombreux clients apprécient de laisser Azure Cosmos DB gérer automatiquement tous les aspects de l’indexation, vous pouvez spécifier une *stratégie d’indexation* personnalisée pour les collections lors de la création dans Azure Cosmos DB. Les stratégies d’indexation dans Azure Cosmos DB sont plus flexibles et puissantes que les index secondaires proposés dans d’autres plateformes de base de données. Dans Azure Cosmos DB, vous pouvez concevoir et personnaliser la forme de l’index sans sacrifier la flexibilité du schéma. 

Pour assimiler les mécanismes de l’indexation dans Azure Cosmos DB, vous devez comprendre qu’en gérant la stratégie d’indexation, vous pouvez trouver un bon compromis entre les coûts de stockage d’index, le débit d’écritures et de requêtes et la cohérence des requêtes.  

Dans la vidéo suivante, le gestionnaire de programmes Azure Cosmos DB Andrew Liu montre les capacités d’indexation automatique d’Azure Cosmos DB, ainsi que la manière de régler et configurer la stratégie d’indexation sur votre conteneur Azure Cosmos DB. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

Dans cet article, nous examinons en détail les stratégies d’indexation d’Azure Cosmos DB, la personnalisation d’une stratégie d’indexation et les compromis associés. 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Comment puis-je remplacer les propriétés à inclure ou à exclure de l'indexation ?
* Comment puis-je configurer l'index pour des mises à jour éventuelles ?
* Comment puis-je configurer l’indexation afin d’effectuer des requêtes ORDER BY ou de plage ?
* Comment puis-je apporter des modifications à la stratégie d'indexation d'une collection ?
* Comment puis-je comparer le stockage et les performances des différentes stratégies d'indexation ?

## Personnaliser la stratégie d’indexation d’une collection <a id="CustomizingIndexingPolicy"></a>  
Vous pouvez personnaliser les compromis entre stockage, performances d’écriture et de requête, et cohérence des requêtes en remplaçant la stratégie d’indexation par défaut sur une collection Azure Cosmos DB. Vous pouvez configurer les aspects suivants :

* **Inclure ou exclure des documents et des chemin dans l’index**. Vous pouvez exclure ou inclure des documents spécifiques dans l’index quand vous insérez ou remplacez les documents dans la collection. Vous pouvez également inclure ou exclure des propriétés JSON spécifiques, également appelées *chemins*, de l’indexation dans les documents qui sont inclus dans un index. Les chemins incluent des caractères génériques.
* **Configurer plusieurs types d’index**. Pour chaque chemin inclus, vous pouvez spécifier le type d’index requis par le chemin pour une collection. Vous pouvez spécifier le type d’index en fonction des données du chemin, de la charge de travail de requête attendue et de la « précision » numérique/de chaîne.
* **Configurer des modes de mise à jour d’index**. Azure Cosmos DB prend en charge trois modes d’indexation : Cohérent, Différé et Aucun. Vous pouvez configurer les modes d’indexation par le biais de la stratégie d’indexation sur une collection Azure Cosmos DB. 

L’extrait de code Microsoft .NET suivant montre comment définir une stratégie d’indexation personnalisée quand vous créez une collection. Dans cet exemple, nous définissons la stratégie avec un index de plage pour les chaînes et les chiffres à la précision maximale. Vous pouvez utiliser cette stratégie pour exécuter des requêtes ORDER BY sur des chaînes.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Le schéma JSON pour la stratégie d’indexation a changé avec la version 2015-06-03 de l’API REST. Avec cette version, le schéma JSON pour la stratégie d’indexation prend en charge les index de plage sur les chaînes. Le Kit de développement logiciel (SDK) .NET 1.2.0 et les Kits de développement logiciel (SDK) Java, Python et Node.js 1.1.0 prennent en charge le nouveau schéma de stratégie. Les versions antérieures du SDK utilisent la version 2015-04-08 de l’API REST. Elles prennent en charge le schéma antérieur pour la stratégie d’indexation.
> 
> Par défaut, Azure Cosmos DB indexe toutes les propriétés de chaîne au sein des documents, de manière cohérente, avec un index de hachage. Il indexe toutes les propriétés numériques dans les documents de manière cohérente avec un index de plage.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Personnaliser la stratégie d’indexation dans le portail

Vous pouvez changer la stratégie d’indexation d’une collection dans le portail Azure : 

1. Dans le portail, accédez à votre compte Azure Cosmos DB et sélectionnez votre collection. 
2. Dans le menu de navigation de gauche, sélectionnez **Paramètres**, puis **Stratégie d’indexation**. 
3. Sous **Stratégie d’indexation**, changez votre stratégie d’indexation, puis sélectionnez **OK**. 

### Modes d’indexation de base de données <a id="indexing-modes"></a>  
Azure Cosmos DB prend en charge trois modes d’indexation que vous pouvez configurer par le biais de la stratégie d’indexation sur une collection Azure Cosmos DB : Cohérent, Différé et Aucun.

**Cohérent** : si la stratégie d’une collection Azure Cosmos DB est cohérente, les requêtes sur une collection Azure Cosmos DB spécifique suivent le même niveau de cohérence que celui spécifié pour les lectures ponctuelles (fort, session, obsolescence limitée, éventuel). L’index est mis à jour de façon synchrone lors de la mise à jour du document (insertion, remplacement, mise à jour et suppression d’un document dans une collection Azure Cosmos DB).

L’indexation cohérente prend en charge des requêtes cohérentes au détriment de la réduction éventuelle du débit d’écriture. Cette réduction dépend des chemins uniques qui doivent être indexés et du « niveau de cohérence ». Le mode d’indexation Cohérent est conçu pour les charges de travail « écrire rapidement, interroger immédiatement ».

**Différé** : l’index est mis à jour de façon asynchrone quand une collection Azure Cosmos DB est inactive, autrement dit quand la capacité de débit de la collection n’est pas entièrement exploitée pour traiter les requêtes de l’utilisateur. Le mode d’indexation « différé » peut être approprié pour les charges de travail « ingérer maintenant, interroger plus tard » nécessitant une ingestion des documents. Notez que vous pourriez obtenir des résultats incohérents, car les données sont ingérées et indexées lentement. Cela signifie que les requêtes COUNT ou des résultats de requête spécifiques risquent de ne pas être cohérents ou répétables à un moment donné. 

L’index est généralement en mode de rattrapage avec les données ingérées. Avec l’indexation différée, les changements de durée de vie (TTL) provoquent la suppression et la recréation de l’index. Cela rend les résultats de requête et les requêtes COUNT incohérents pendant un certain laps de temps. Pour cette raison, la plupart des comptes Azure Cosmos DB doivent utiliser le mode d’indexation Cohérent.

**Aucun**: une collection en mode d’indexation « Aucun » n’est associée à aucun index. Ce mode est souvent employé si Azure Cosmos DB est utilisé en tant que stockage de clés-valeurs et si les documents ne sont accessibles que par le biais de leur propriété ID. 

> [!NOTE]
> La configuration de la stratégie d’indexation en mode Aucun a pour effet secondaire de supprimer un index existant. Utilisez-la si vos modèles d’accès ne nécessitent que l’attribut ID ou self-link (lien réflexif).
> 
> 

Le tableau suivant indique la cohérence des requêtes en fonction du mode d'indexation (Cohérent et Différé) qui a été configuré pour la collection et du niveau de cohérence spécifié pour la requête. Cela s’applique aux requêtes effectuées à l’aide de n’importe quelle interface : API REST, SDK ou à partir de déclencheurs et de procédures stockées. 

|Cohérence|Mode d’indexation : Cohérent|Mode d’indexation : Différé|
|---|---|---|
|Remarque|Remarque|Eventual (Éventuel)|
|Bounded staleness (En fonction de l'obsolescence)|Bounded staleness (En fonction de l'obsolescence)|Eventual (Éventuel)|
|session|session|Eventual (Éventuel)|
|Eventual (Éventuel)|Eventual (Éventuel)|Eventual (Éventuel)|

Azure Cosmos DB retourne une erreur pour les requêtes effectuées sur les collections en mode d’indexation Aucun. Les requêtes peuvent toujours être exécutées en tant qu’analyses par le biais de l’en-tête explicite **x-ms-documentdb-enable-scan** dans l’API REST ou de l’option de requête **EnableScanInQuery** à l’aide du SDK .NET. Certaines fonctions de requêtes, telles que ORDER BY, ne sont pas prises en charge en tant qu’analyses avec **EnableScanInQuery**.

Le tableau suivant indique la cohérence des requêtes en fonction du mode d’indexation (Cohérent, Différé et Aucun) quand **EnableScanInQuery** est spécifié.

|Cohérence|Mode d’indexation : Cohérent|Mode d’indexation : Différé|Mode d’indexation : Aucun|
|---|---|---|---|
|Remarque|Remarque|Eventual (Éventuel)|Remarque|
|Bounded staleness (En fonction de l'obsolescence)|Bounded staleness (En fonction de l'obsolescence)|Eventual (Éventuel)|Bounded staleness (En fonction de l'obsolescence)|
|session|session|Eventual (Éventuel)|session|
|Eventual (Éventuel)|Eventual (Éventuel)|Eventual (Éventuel)|Eventual (Éventuel)|

L’exemple suivant montre comment utiliser le SDK .NET d’Azure Cosmos DB pour créer une collection Azure Cosmos DB avec une indexation cohérente de toutes les insertions de document.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Chemins d’accès de l’index
Azure Cosmos DB modélise les documents JSON et l’index sous forme d’arborescences. Vous pouvez paramétrer les stratégies pour les chemins dans l’arborescence. Dans les documents, vous pouvez choisir les chemins à inclure ou à exclure de l’indexation. Il peut en résulter de meilleures performances d’écriture et un stockage des index inférieur pour les scénarios dans lesquels les modèles de requête sont connus au préalable.

Le chemin des index commence par la racine et se termine généralement par l’opérateur générique ?, ?, ce qui signifie qu’il y a plusieurs valeurs possibles pour le préfixe. Par exemple, pour traiter SELECT * FROM Families F WHERE F.familyName = "Andersen", vous devez inclure un chemin d’index pour /familyName/? dans la stratégie d’index de la collection.

Les chemins d'index peuvent aussi utiliser l'opérateur générique \* pour spécifier le comportement des chemins de manière récursive sous le préfixe. Par exemple, /payload/* permet d’exclure de l’indexation tout ce qui figure sous la propriété « payload ».

Voici les modèles courants de spécification des chemins d'index :

| path                | Description/cas d'utilisation                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Chemin par défaut de la collection. Récursif et s’applique à toute l’arborescence du document.                                                                                                                                                                                                                                   |
| /prop/?             | Chemin d’index requis pour traiter les requêtes comme les suivantes (avec, respectivement, les types hachage ou plage) :<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /prop/*             | Chemin d'index pour tous les chemins d'accès sous l'étiquette spécifiée. Fonctionne avec les requêtes suivantes<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | Chemin d’accès de l’index requis pour traiter l’itération et les requêtes JOIN dans les tableaux de scalaires comme ["a", "b", "c"] :<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[] /subprop/? | Chemin d’accès de l’index requis pour traiter l’itération et les requêtes JOIN dans les tableaux d’objets comme [{subprop: "a"}, {subprop: "b"}] :<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | Chemin d’index requis pour traiter les requêtes (avec, respectivement, les types hachage ou plage) :<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Quand vous définissez des chemins d’index personnalisé, vous devez spécifier la règle d’indexation par défaut pour la totalité de l’arborescence du document, désignée par le chemin spécial « /* ». 
> 
> 

L’exemple suivant configure un chemin spécifique avec un index de plage et une valeur de précision personnalisée de 20 octets :

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Types de données d’index, genres d’index et précisions d’index
Vous avez plusieurs options quand vous configurez la stratégie d’indexation pour un chemin. Vous pouvez spécifier une ou plusieurs définitions d’indexation pour chaque chemin d’accès :

* **Type de données** : Chaîne, Nombre, Point, Polygone ou LineString (ne pouvant contenir qu’une seule entrée par type de données par chemin).
* **Genre d’index** : Hachage (requêtes d’égalité), Plage (requêtes d’égalité, de plage ou ORDER BY) ou Spatial (requêtes spatiales).
* **Précision** : pour les index de hachage, cette valeur varie de 1 à 8 pour les chaînes et les nombres. La valeur par défaut est 3. Pour un index de plage, cette valeur peut être -1 (précision maximale). Elle peut varier entre 1 et 100 (précision maximale) pour les valeurs de chaîne ou numériques.

#### <a name="index-kind"></a>Type d’index
Azure Cosmos DB prend en charge les genres d’index de hachage et de plage pour chaque chemin qui peut être configuré pour le type de données Chaîne ou Nombre, ou les deux.

* **Hachage** prend en charge les requêtes d’égalité efficaces et JOIN. Dans la plupart des cas d’utilisation, les index de hachage ne nécessitent pas une précision plus élevée que la valeur par défaut de trois octets. Le type de données peut être Chaîne ou Nombre.
* **Plage** prend en charge les requêtes d’égalité efficaces, les requêtes de plage (avec >, <, >=, <=, !=) et les requêtes ORDER BY. Par défaut, les requêtes ORDER BY nécessitent également une précision d’index maximale (-1). Le type de données peut être Chaîne ou Nombre.

Azure Cosmos DB prend également en charge le genre d’index spatial pour chaque chemin qui peut être spécifié pour les types de données Point, Polygone ou LineString. La valeur dans le chemin d’accès spécifié doit être un fragment GeoJSON valide, comme `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Spatial** prend en charge les requêtes spatiales efficaces (within et distance) Le type de données peut être Point, Polygone ou LineString.

> [!NOTE]
> Azure Cosmos DB prend en charge l’indexation automatique des types de données Points, Polygone et LineString.
> 
> 

Voici les types d'index pris en charge et les exemples de requêtes qui peuvent être traitées :

| Type d’index | Description/cas d'utilisation                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hachage       | Le hachage disposant de l’élément /prop? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Le hachage disposant de l’élément /props/[]/? (ou / ou /props/) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Plage      | La plage disposant de l’élément /prop/? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| spatial     | La plage disposant de l’élément /prop/? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --avec indexation sur les points activée<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --avec indexation sur les polygones activée              |

Par défaut, une erreur est retournée pour les requêtes avec des opérateurs de plage tels que >= s’il n’existe aucun index de plage (de n’importe quelle précision) pour signaler qu’une analyse peut être requise pour traiter la requête. Les requêtes peuvent être effectuées sans index de plage à l’aide de l’en-tête**x-ms-documentdb-enable-scan** dans l’API REST ou l’option de requête **EnableScanInQuery** à l’aide du SDK .NET. Si d’autres filtres de la requête peuvent être utilisés par Azure Cosmos DB sur l’index, aucune erreur n’est retournée.

Les mêmes règles s'appliquent pour les requêtes spatiales. Par défaut, une erreur est renvoyée pour les requêtes spatiales s’il n’existe aucun index spatial et qu’aucun autre filtre ne peut être fourni à partir de l’index. Elles peuvent être effectuées en tant qu’analyse à l’aide de **x-ms-documentdb-enable-scan** ou **EnableScanInQuery**.

#### <a name="index-precision"></a>Précision d’index
Vous pouvez utiliser la précision de l’index pour trouver un compromis entre le traitement du stockage de l’index et les performances des requêtes. Pour les nombres, nous recommandons d’utiliser la configuration de précision par défaut définie sur -1 (valeur maximale). Comme les nombres correspondent à huit octets dans JSON, cela équivaut à une configuration de huit octets. Si vous choisissez une valeur inférieure pour la précision, par exemple de un à sept, les valeurs de certaines plages sont mappées à la même entrée d’index. Ainsi, vous réduisez l’espace de stockage d’index, mais l’exécution des requêtes devra peut-être traiter davantage de documents. Elle consommera donc plus de débit dans les unités de requête.

La configuration de la précision d’index est plus pratique avec les plages de chaînes. Les chaînes pouvant avoir n’importe quelle longueur arbitraire, le choix de la précision d’index peut affecter les performances des requêtes de plage de chaînes. Il peut également affecter la quantité d’espace de stockage d’index nécessaire. Les index de plage de chaînes peuvent être configurés avec une valeur comprise entre 1 et 100, ou la valeur de précision maximale (-1). Si vous souhaitez exécuter des requêtes ORDER BY sur des propriétés de chaîne, vous devez spécifier une précision de -1 pour les chemins correspondants.

Les index spatiaux utilisent toujours la précision d’index par défaut pour tous les types (Point, LineString et Polygone). La précision d’index par défaut pour les index spatiaux ne peut pas être substituée. 

L’exemple suivant montre comment augmenter la précision des index de plage d’une collection à l’aide du SDK .NET. 

**Créer une collection avec une précision d'index personnalisée**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB retourne une erreur quand une requête utilise ORDER BY mais n’a pas d’index de plage pour le chemin avec la précision maximale. 
> 
> 

De même, vous pouvez exclure complètement les chemins de l’indexation. L’exemple suivant montre comment exclure une section entière des documents (une *sous-arborescence*) de l’indexation à l’aide de l’opérateur générique \*.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Adopter ou ignorer l’indexation
Vous pouvez choisir si vous souhaitez que la collection indexe automatiquement tous les documents. Par défaut, tous les documents sont indexés automatiquement, mais vous pouvez désactiver l’indexation automatique. Quand l’indexation est désactivée, les documents sont accessibles uniquement par le biais de leurs liens réflexifs ou de requêtes avec l’ID de document.

Si l'indexation automatique est désactivée, vous ne pouvez continuer à ajouter des documents spécifiques à l'index que de façon sélective. À l’inverse, vous pouvez laisser l’indexation automatique activée et choisir d’exclure de façon sélective des documents spécifiques. Les configurations d’indexation activée/désactivée sont utiles quand vous n’avez qu’un sous-ensemble de documents à interroger.

L’exemple suivant montre comment inclure un document explicitement à l’aide du [SDK .NET de l’API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) et de la propriété [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Modifier la stratégie d’indexation d’une collection
Dans Azure Cosmos DB, vous pouvez apporter des modifications à la stratégie d’indexation d’une collection à la volée. Un changement dans la stratégie d’indexation sur une collection Azure Cosmos DB peut entraîner une modification de la forme de l’index. Le changement affecte les chemins qui peuvent être indexés, leur précision et le mode de cohérence de l’index proprement dit. Un changement dans la stratégie d’indexation nécessite donc une transformation de l’ancien index en un nouvel index. 

**Transformations d’index en ligne**

![Mécanismes de l’indexation – Transformations d’index en ligne Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

Les transformations d’index s’effectuent en ligne. Cela signifie que les documents indexés par l’ancienne stratégie sont transformés efficacement par la nouvelle stratégie, *sans affecter la disponibilité de l’écriture ou le débit provisionné* de la collection. La cohérence des opérations de lecture et d’écriture effectuées à l’aide de l’API REST, des SDK ou à partir des déclencheurs et des procédures stockées n’est pas affectée au cours de la transformation de l’index. Aucune dégradation de performances, ou interruption de vos applications, ne se produit quand vous modifiez une stratégie d’indexation.

Toutefois, lors de la transformation de l'index, les requêtes sont cohérentes, et ce, quelle que soit la configuration du mode d'indexation (mode Cohérent ou Différé). Cela s’applique également aux requêtes effectuées à l’aide de n’importe quelle interface : API REST, SDK ou à partir de déclencheurs et de procédures stockées. Tout comme avec l’indexation différée, la transformation de l’index est exécutée de façon asynchrone en arrière-plan sur les réplicas à l’aide de ressources d’échange disponibles pour un réplica spécifique. 

Les transformations d’index s’effectuent également sur place. Azure Cosmos DB ne conserve pas deux copies de l’index, et ne remplace pas l’ancien index par le nouveau. Cela signifie qu’aucun espace disque supplémentaire n’est nécessaire ou utilisé dans vos collections pendant que les transformations d’index ont lieu.

Quand vous changez la stratégie d’indexation, les changements sont appliqués pour passer de l’ancien index au nouvel index en fonction des configurations de mode d’indexation. Les configurations de mode d’indexation jouent un rôle plus important que d’autres valeurs telles que les chemins inclus/exclus, les genres d’index et les précisions. 

Si vos anciennes et nouvelles stratégies utilisent toutes deux l’indexation cohérente, Azure Cosmos DB effectue une transformation d’index en ligne. Vous ne pouvez pas appliquer une autre modification de stratégie d’indexation qui a le mode d’indexation Cohérent pendant que la transformation est en cours. Vous pouvez toutefois opter pour le mode d’indexation Différé ou Aucun quand une transformation est en cours. 

* Quand vous basculez vers le mode Différé, le changement de stratégie d’index prend effet immédiatement. Azure Cosmos DB commence à recréer l’index de façon asynchrone. 
* Lorsque vous basculez vers le mode Aucun, l’index est supprimé immédiatement. Opter pour le mode Aucun peut s’avérer très utile quand vous souhaitez annuler une transformation en cours et utiliser une nouvelle stratégie d’indexation. 

L’extrait de code suivant montre comment faire basculer la stratégie d’indexation d’une collection du mode Cohérent au mode Différé. Si vous utilisez le SDK .NET, vous pouvez déclencher un changement de stratégie d’indexation à l’aide de la nouvelle méthode **ReplaceDocumentCollectionAsync**.

**Faire basculer la stratégie d’indexation du mode Cohérent au mode Différé**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Suivre la progression de la transformation d’index**

Vous pouvez suivre le pourcentage de progression de la transformation en index Cohérent à l’aide de la propriété de réponse **IndexTransformationProgress** d’un appel **ReadDocumentCollectionAsync**. D’autres SDK, ainsi que l’API REST, prennent en charge des propriétés et des méthodes équivalentes pour apporter des modifications de stratégie d’indexation. Vous pouvez vérifier la progression d’une transformation d’index en index Cohérent en appelant **ReadDocumentCollectionAsync** : 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * La propriété **IndexTransformationProgress** s’applique uniquement lors de la transformation en index Cohérent. Utilisez la propriété **ResourceResponse.LazyIndexingProgress** pour le suivi des transformations en index Différé.
> * Les propriétés **IndexTransformationProgress** et **LazyIndexingProgress** sont remplies uniquement pour une collection non partitionnée, autrement dit une collection créée sans clé de partition.
>

Vous pouvez supprimer l'index d’une collection en optant pour le mode d'indexation Aucun. Ceci peut s’avérer très utile si vous souhaitez annuler une transformation en cours puis en démarrer immédiatement une nouvelle.

**Supprimer l’index d’une collection**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Quand pouvez-vous modifier la stratégie d’indexation dans vos collections Azure Cosmos DB ? Les scénarios d'utilisation les plus courants sont les suivants :

* Fournir des résultats cohérents lors du bon déroulement de l’opération, mais revenir au mode d’indexation Différé lors de l’importation de données en bloc
* Commencer à utiliser de nouvelles fonctionnalités d’indexation sur vos collections Azure Cosmos DB actuelles. Par exemple, utiliser l’interrogation géospatiale, qui nécessite le genre d’index Spatial, ou des requêtes de plage de chaînes/ORDER BY, qui nécessitent le genre d’index Plage de chaînes.
* Sélectionner les propriétés à indexer et les modifier au fil du temps
* Ajuster la précision d’indexation pour améliorer les performances de requête ou réduire le stockage utilisé

> [!NOTE]
> Pour modifier la stratégie d’indexation à l’aide de **ReplaceDocumentCollectionAsync**, vous devez utiliser la version 1.3.0 ou ultérieure du SDK .NET.
> 
> Pour que la transformation d’index se déroule correctement, vérifiez qu’il existe suffisamment d’espace libre sur la collection. Si la collection atteint son quota de stockage, la transformation d’index est interrompue. La transformation d’index reprend automatiquement dès que de l’espace de stockage est disponible, par exemple si vous supprimez certains documents.
> 
> 

## <a name="performance-tuning"></a>Réglage des performances
Les API SQL fournissent des informations sur les mesures des performances, telles que le stockage d’index utilisé et le coût du débit (unités de requête) pour chaque opération. Vous pouvez utiliser ces informations pour comparer différentes stratégies d’indexation et ajuster les performances.

Pour vérifier le quota de stockage et l’utilisation d’une collection, exécutez une requête **HEAD** ou **GET** sur la ressource de collection. Ensuite, examinez les en-têtes **x-ms-request-quota** et **x-ms-request-usage**. Dans le Kit de développement logiciel (SDK) .NET, les propriétés [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) et [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) de [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) contiennent ces valeurs correspondantes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Pour mesurer la surcharge de l’indexation sur chaque opération d’écriture (création, mise à jour ou suppression), inspectez l’en-tête **x-ms-request-charge** (ou la propriété [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) équivalente dans [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) dans le SDK .NET) qui permet de mesurer le nombre d’unités de requête consommées par ces opérations.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Modifications apportées à la spécification de la stratégie d'indexation
Un changement dans le schéma de la stratégie d’indexation a été introduit le 7 juillet 2015 avec la version 2015-06-03 de l’API REST. Les classes correspondantes dans les versions du Kit de développement logiciel (SDK) ont de nouvelles implémentations pour correspondre au schéma. 

Les modifications suivantes ont été implémentées dans la spécification JSON :

* La stratégie d’indexation prend en charge les index de plage pour les chaînes.
* Chaque chemin d’accès peut avoir plusieurs définitions d’index, un pour chaque type de données.
* La précision d’indexation prend en charge les nombres de 1 à 8, les chaînes de 1 à 100, et -1 (précision maximale).
* Les segments des chemins ne nécessitent pas de doubles guillemets pour l’échappement de chaque chemin. Par exemple, vous pouvez ajouter un chemin pour **/title/?** au lieu de **/"title"/?**.
* Le chemin racine qui représente « tous les chemins » peut être représenté comme **/\*** (en plus de **/**).

Si vous avez du code qui provisionne des collections avec une stratégie d’indexation personnalisée écrite avec la version 1.1.0 du SDK .NET ou une version antérieure, pour passer à la version 1.2.0 du SDK vous devez changer le code de votre application pour gérer ces modifications. Si vous n’avez pas de code qui configure la stratégie d’indexation, ou si vous envisagez de continuer à utiliser une version antérieure du SDK, aucun changement n’est nécessaire.

Pour obtenir une comparaison pratique, voici un exemple de stratégie d’indexation personnalisée écrite à l’aide de l’API REST version 2015-06-03, suivi de la même stratégie d’indexation écrite à l’aide de l’API REST antérieure de version 2015-04-08.

**JSON de stratégie d’indexation actuel (API REST version 2015-06-03)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }


**JSON de stratégie d’indexation antérieur (API REST version 2015-04-08)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des exemples de gestion de stratégie d’index et en savoir plus sur le langage de requête d’Azure Cosmos DB, suivez les liens ci-dessous :

* [Exemples de code de gestion d’index .NET de l’API SQL](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Opérations sur les collections d’API REST SQL](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Requête avec SQL](sql-api-sql-query.md)

