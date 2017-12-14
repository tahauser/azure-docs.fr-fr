---
title: "Stratégies d’indexation d’Azure Cosmos DB | Microsoft Docs"
description: "Comprendre le fonctionnement de l’indexation dans Azure Cosmos DB. Découvrez comment configurer et modifier la stratégie d’indexation pour bénéficier d’une indexation automatique et de meilleures performances."
keywords: "fonctionnement de l’indexation, indexation automatique, base de données d’indexation"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: 53bf756963c305b8b31ac1a90d219f143522d051
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Comment Azure Cosmos DB indexe-t-il les données ?

Par défaut, toutes les données d’Azure Cosmos DB sont indexées. Bien que de nombreux clients apprécient de laisser Azure Cosmos DB gérer automatiquement tous les aspects de l’indexation, cette solution permet également de spécifier une **stratégie d’indexation** personnalisée pour les collections lors de la création. Les stratégies d’indexation dans Azure Cosmos DB sont plus flexibles et plus puissantes que les index secondaires proposés dans d’autres plateformes de base de données, car elles vous permettent de concevoir et de personnaliser la forme de l’index sans pour autant sacrifier la flexibilité du schéma. Pour assimiler les mécanismes de l’indexation dans Azure Cosmos DB, vous devez comprendre qu’en gérant la stratégie d’indexation, vous pouvez trouver un bon compromis entre les coûts de stockage d’index, le débit d’écritures et de requêtes et la cohérence des requêtes.  

Dans cet article, nous examinons en détail les stratégies d’indexation d’Azure Cosmos DB, la personnalisation d’une stratégie d’indexation et les compromis associés. 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Comment puis-je remplacer les propriétés à inclure ou à exclure de l'indexation ?
* Comment puis-je configurer l'index pour des mises à jour éventuelles ?
* Comment puis-je configurer l'indexation afin d'effectuer des requêtes Order By ou de plage ?
* Comment puis-je apporter des modifications à la stratégie d'indexation d'une collection ?
* Comment puis-je comparer le stockage et les performances des différentes stratégies d'indexation ?

## <a id="CustomizingIndexingPolicy"></a> Personnalisation de la stratégie d’indexation d’une collection
Les développeurs peuvent personnaliser les compromis entre stockage, performances d’écriture/de requête et cohérence des requêtes, en remplaçant la stratégie d’indexation par défaut sur une collection Azure Cosmos DB et en configurant les aspects suivants.

* **Inclusion/Exclusion des documents et chemins d’accès vers/à partir de l’index**. Les développeurs peuvent choisir certains documents à exclure ou inclure dans l'index au moment de les insérer ou de les remplacer dans la collection. Les développeurs peuvent également inclure ou exclure certaines propriétés JSON, ou chemins d’accès (y compris des modèles génériques), à indexer dans tous les documents qui sont inclus dans un index.
* **Configuration de plusieurs types d’index**. Pour chacun des chemins d'accès inclus, les développeurs peuvent également spécifier le type d'index qu'ils ont besoin pour une collection en fonction de leurs données, de la charge de travail des requêtes et de la « précision » numérique/de chaîne de chaque chemin d'accès.
* **Configuration des modes de mise à jour d’index**. Azure Cosmos DB prend en charge trois modes d’indexation qui peuvent être configurés via la stratégie d’indexation sur une collection Azure Cosmos DB : Cohérent, Différé et Aucun. 

L’extrait de code .NET suivant montre comment définir une stratégie d’indexation personnalisée lors de la création d’une collection. Ici, nous définissons la stratégie avec un index de plage pour les chaînes et les chiffres à la précision maximale. Cette stratégie nous permet d’exécuter des requêtes Trier par sur les chaînes.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Le schéma JSON de la stratégie d’indexation a été modifié avec la version de l’API REST 2015-06-03 pour prendre en charge les index de plage dans des chaînes. Le Kit de développement logiciel (SDK) .NET 1.2.0 et les Kits de développement logiciel (SDK) Java, Python et Node.js 1.1.0 prennent en charge le nouveau schéma de stratégie. Des Kits de développement (SDK) plus anciens utilisent la version 2015-04-08 de l'API REST et prennent en charge le schéma de stratégie d'indexation plus ancien.
> 
> Par défaut, Azure Cosmos DB indexe toutes les propriétés de chaîne au sein des documents, de manière cohérente, avec un index de hachage et des propriétés numériques avec un index de plage.  
> 
> 

### <a name="customizing-the-indexing-policy-using-the-portal"></a>Personnalisation de la stratégie d’indexation à l’aide du portail

Vous pouvez modifier la stratégie d’indexation d’une collection à l’aide du portail Azure. Ouvrez votre compte Azure Cosmos DB dans le portail Azure, sélectionnez votre collection et dans le menu de navigation gauche, cliquez sur **Paramètres**, puis sur **stratégie d’indexation**. Dans le panneau **Stratégie d’indexation**, modifiez votre stratégie d’indexation, puis cliquez sur **OK** pour enregistrer vos modifications. 

### <a id="indexing-modes"></a>Modes d’indexation de base de données
Azure Cosmos DB prend en charge trois modes d’indexation qui peuvent être configurés via la stratégie d’indexation sur une collection Azure Cosmos DB : Cohérent, Différé et Aucun.

**Cohérent** : si la stratégie d’une collection Azure Cosmos DB est désignée comme « cohérente », les requêtes sur une collection Azure Cosmos DB donnée suivent le même niveau de cohérence que celui spécifié pour les lectures ponctuelles (c.-à-d., fort, en fonction de l’obsolescence, session ou éventuel). L’index est mis à jour de façon synchrone lors de la mise à jour du document (par ex. l’insertion, le remplacement, la mise à jour et la suppression d’un document dans une collection Azure Cosmos DB).  L’indexation cohérente prend en charge des requêtes cohérentes au détriment de la réduction éventuelle du débit d'écriture. Cette réduction dépend des chemins d'accès uniques qui doivent être indexés et du « niveau de cohérence ». Le mode d’indexation Cohérent est conçu pour les charges de travail « écrire rapidement, interroger immédiatement ».

**Différé** : l’index est mis à jour de façon asynchrone lorsqu’une collection Azure Cosmos DB est inactive. En d’autres termes, la capacité de débit de la collection n’est pas entièrement exploitée pour traiter les requêtes de l’utilisateur. Pour les charges de travail « ingérer maintenant, interroger plus tard » nécessitant une ingestion des documents, le mode d'indexation « différé » peut être approprié. Veuillez noter que vous pourriez obtenir des résultats incohérents, car les données sont ingérées et indexées lentement. Il n’est pas garanti que les résultats de vos requêtes de comptage ou requêtes spécifiques soient corrects ou répétables jusqu’à ce que les données soient indexées. L’index est en général en mode de rattrapage. Indexation différée WRT : la modification TTL entraîne l’abandon et la recréation de l’index, donc cette activité peut entraîner des résultats inattendus. La plupart des clients doit utiliser l’indexation cohérente.

**Aucun**: une collection en mode « Aucun » ne comporte aucun index associé. Ce mode est souvent employé si Azure Cosmos DB est utilisé en tant que stockage de clés-valeurs et si les documents ne sont accessibles que via leur propriété ID. 

> [!NOTE]
> La configuration de la stratégie d’indexation en mode « Aucun » a pour effet secondaire de supprimer un index existant. Utilisez-la si vos modèles d'accès ne requièrent que l’attribut « id » et/ou « self-link » (lien réflexif).
> 
> 

Le tableau suivant indique la cohérence des requêtes en fonction du mode d'indexation (Cohérent et Différé) qui a été configuré pour la collection et du niveau de cohérence spécifié pour la requête. Cela s'applique aux requêtes effectuées à l'aide de n'importe quelle interface : API REST, Kit de développement logiciel (SDK) ou à partir de déclencheurs et de procédures stockées. 

|Cohérence|Mode d’indexation : Cohérent|Mode d’indexation : Différé|
|---|---|---|
|Remarque|Remarque|Eventual (Éventuel)|
|Obsolescence limitée|Obsolescence limitée|Eventual (Éventuel)|
|Session|Session|Eventual (Éventuel)|
|Eventual (Éventuel)|Eventual (Éventuel)|Eventual (Éventuel)|

Azure Cosmos DB renvoie une erreur pour les requêtes effectuées sur les collections en mode d’indexation « Aucun ». Les requêtes peuvent toujours être exécutées comme des analyses via l’en-tête explicite `x-ms-documentdb-enable-scan` dans l’API REST ou l’option de demande `EnableScanInQuery` à l’aide du Kit de développement logiciel (SDK) .NET. Certaines fonctions de requêtes, telles que ORDER BY, ne sont pas prises en charge en tant qu’analyses avec `EnableScanInQuery`.

Le tableau suivant indique la cohérence des requêtes en fonction du mode d'indexation (Cohérent, Différé et Aucun) qui a été configuré lorsque EnableScanInQuery est spécifié.

|Cohérence|Mode d’indexation : Cohérent|Mode d’indexation : Différé|Mode d’indexation : Aucun|
|---|---|---|---|
|Remarque|Remarque|Eventual (Éventuel)|Remarque|
|Obsolescence limitée|Obsolescence limitée|Eventual (Éventuel)|Obsolescence limitée|
|Session|Session|Eventual (Éventuel)|Session|
|Eventual (Éventuel)|Eventual (Éventuel)|Eventual (Éventuel)|Eventual (Éventuel)|

L’exemple suivant montre comment utiliser le Kit de développement logiciel (SDK) .NET d’Azure Cosmos DB pour créer une collection Azure Cosmos DB avec une indexation automatique cohérente de toutes les insertions de document.

     // Default collection creates a hash index for all string fields and a range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Chemins d’accès de l’index
Azure Cosmos DB modélise les documents JSON et l’index sous la forme d’arborescences, et vous permet de les adapter aux stratégies pour les chemins d’accès dans l’arborescence. Dans les documents, vous pouvez choisir les chemins d'accès qui doivent être inclus ou exclus de l'indexation. Il peut en résulter de meilleures performances d'écriture et un stockage des index inférieur pour les scénarios lorsque les modèles de requête sont connus au préalable.

Le chemin des index commence par la racine et se termine généralement par l’opérateur générique ?, ce qui signifie qu’il y a plusieurs valeurs possibles pour le préfixe. Par exemple, pour traiter SELECT * FROM Families F WHERE F.familyName = "Andersen", vous devez inclure un chemin d’index pour /familyName/? dans la stratégie d’index de la collection.

Les chemins d’index peuvent aussi utiliser l’opérateur générique * pour spécifier le comportement des chemins de manière récursive sous le préfixe. Par exemple, /payload/* permet d’exclure de l’indexation tout ce qui figure sous la propriété « payload ».

Voici les modèles courants de spécification des chemins d'index :

| Chemin                | Description/cas d'utilisation                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Chemin par défaut de la collection. Récursif et s'applique à toute l'arborescence du document.                                                                                                                                                                                                                                   |
| /prop/?             | Chemin d’index requis pour traiter les requêtes comme les suivantes (avec, respectivement, les types hachage ou plage) :<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /prop/*             | Chemin d'index pour tous les chemins d'accès sous l'étiquette spécifiée. Fonctionne avec les requêtes suivantes<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | Chemin d’accès de l’index requis pour traiter l’itération et les requêtes JOIN dans les tableaux de scalaires comme ["a", "b", "c"] :<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[] /subprop/? | Chemin d’accès de l’index requis pour traiter l’itération et les requêtes JOIN dans les tableaux d’objets comme [{subprop: "a"}, {subprop: "b"}] :<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | Chemin d’index requis pour traiter les requêtes (avec, respectivement, les types hachage ou plage) :<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Lors de la définition des chemins d’accès de l’index personnalisé, il est nécessaire de spécifier la règle d’indexation par défaut pour la totalité de l’arborescence du document, désignée par le chemin d’accès spécial « /* ». 
> 
> 

L’exemple suivant configure un chemin d’accès spécifique avec l’indexation de plage et une valeur personnalisée de précision de 20 octets :

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


### <a name="index-data-types-kinds-and-precisions"></a>Types de données d’index, types d’index et précisions d’index
Maintenant que nous avons vu comment spécifier des chemins d’accès, examinons les options que nous pouvons utiliser pour configurer la stratégie d’indexation pour un chemin d’accès. Vous pouvez spécifier une ou plusieurs définitions d’indexation pour chaque chemin d’accès :

* Type de données : **chaîne**, **nombre**, **point**, **polygone** ou **LineString** (ne pouvant contenir qu’une seule entrée par type de données par chemin d’accès)
* Genre d’index : **hachage** (requêtes d’égalité) ou **plage** (requêtes d’égalité, de plage ou requêtes Trier par) ou **spatial** (demandes spatiales) 
* Précision : pour les index de hachage, cette valeur varie de 1 à 8 pour les chaînes et les nombres, avec une valeur par défaut de 3. Pour les index de plage, cette valeur peut être -1 (précision maximale) et varie entre 1 et 100 (précision maximale) pour les valeurs de chaîne ou numériques.

#### <a name="index-kind"></a>Type d’index
Azure Cosmos DB prend en charge les types d’index de hachage et de plage pour chaque chemin d’accès (qui peuvent être configurés pour les chaînes, les nombres ou les deux).

* **Hachage** prend en charge les requêtes d’égalité efficaces et JOIN. Dans la plupart des cas d’utilisation, les index de hachage ne nécessitent pas une précision plus élevée que la valeur par défaut de 3 octets. Le type de données peut être Chaîne ou Nombre.
* **Plage** prend en charge les requêtes d’égalité efficaces, les requêtes de plage (avec &gt;, &lt;, &gt;=, &lt;=, !=) et les requêtes Trier par. Par défaut, les requêtes Trier par nécessitent également une précision d’index maximale (-1). Le type de données peut être Chaîne ou Nombre.

Azure Cosmos DB prend également en charge le type d’index spatial pour chaque chemin d’accès, qui peut être spécifié pour les types de données Point, Polygone ou LineString. La valeur dans le chemin d’accès spécifié doit être un fragment GeoJSON valide, comme `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Spatial** prend en charge les requêtes spatiales efficaces (within et distance) Le type de données peut être Point, Polygone ou LineString.

> [!NOTE]
> Azure Cosmos DB prend en charge l’indexation automatique des points, polygones et lineStrings.
> 
> 

Voici les types d'index pris en charge et les exemples de requêtes qui peuvent être traitées :

| Type d’index | Description/cas d'utilisation                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hachage       | Le hachage disposant de l’élément /prop? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Le hachage disposant de l’élément /props/[]/? (ou / ou /props/) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Plage      | La plage disposant de l’élément /prop/? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| spatial     | La plage disposant de l’élément /prop/? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --avec indexation sur les points activée<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --avec indexation sur les polygones activée              |

Par défaut, une erreur est renvoyée pour les requêtes disposant d’opérateurs de plage tels que >= s'il n'existe aucun index de plage (de n’importe quelle précision) pour signaler qu'une analyse peut être requise pour traiter la requête. Les requêtes peuvent être effectuées sans index de plage à l’aide de l’en-tête x-ms-documentdb-enable-scan header dans l’API REST ou l’option de requête EnableScanInQuery à l’aide du Kit de développement logiciel (SDK) .NET. Si d’autres filtres de la requête peuvent être utilisés par Azure Cosmos DB sur l’index, aucune erreur ne vous sera renvoyée.

Les mêmes règles s'appliquent pour les requêtes spatiales. Par défaut, une erreur est renvoyée pour les requêtes spatiales s’il n’existe aucun index spatial et qu’aucun autre filtre ne peut être fourni à partir de l’index. Elles peuvent être effectuées en tant qu'analyse à l'aide de x-ms-documentdb-enable-scan/EnableScanInQuery.

#### <a name="index-precision"></a>Précision d’index
La précision d’index vous permet de trouver un compromis entre le traitement du stockage de l’index et les performances des requêtes. Pour les nombres, nous recommandons d’utiliser la configuration de précision par défaut définie sur -1 (« valeur maximale »). Comme les nombres correspondent à 8 octets dans JSON, cela équivaut à une configuration de 8 octets. Si vous choisissez une valeur inférieure pour la précision, par exemple 1 à 7, les valeurs de certaines plages sont mappées à la même entrée d’index. Ce faisant, vous réduisez l’espace de stockage des index, mais l’exécution des requêtes peut devoir traiter plus de documents et, par conséquent, consommer davantage de débit, c’est-à-dire d’unités de demande.

La configuration de la précision d’index est plus pratique avec les plages de chaînes. Comme les chaînes peuvent avoir n’importe quelle longueur arbitraire, le choix de la précision d’index peut avoir des conséquences sur les performances des requêtes de plage de chaînes et sur l’espace de stockage requis pour les index. Les index de plage de chaînes peuvent être configurés avec une valeur comprise entre 1 et 100, ou la valeur de précision maximale (-1). Si vous souhaitez exécuter des requêtes Trier par sur les propriétés de chaîne, vous devez spécifier une précision de -1 pour les chemins d'accès correspondants.

Les index spatiaux utilisent toujours la précision d’index par défaut pour les types (Points, LineStrings et Polygones) et ne peuvent pas être remplacés. 

L’exemple suivant montre comment augmenter la précision des index de plage d’une collection à l’aide du Kit de développement (SDK) .NET. 

**Créer une collection avec une précision d'index personnalisée**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for Strings to range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB renvoie une erreur lorsqu’une requête utilise Trier par, mais n’a pas d’index de plage pour le chemin d’accès avec la précision maximale. 
> 
> 

De même, des chemins d’accès peuvent être exclus complètement de l’indexation. L’exemple suivant montre comment exclure toute une section de documents (également appelée sous-arborescence) de l’indexation à l’aide du caractère générique « * ».

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opting-in-and-opting-out-of-indexing"></a>Activation ou désactivation de l’indexation
Vous pouvez choisir si vous souhaitez que la collection indexe automatiquement tous les documents. Par défaut, tous les documents sont indexés automatiquement, mais vous pouvez choisir de désactiver cette option. Lorsque l’indexation est désactivée, les documents sont accessibles uniquement par le biais de leurs liens réflexifs ou de requêtes avec l’ID.

Si l'indexation automatique est désactivée, vous ne pouvez continuer à ajouter des documents spécifiques à l'index que de façon sélective. À l'inverse, vous pouvez laisser l'indexation automatique activée et choisir ne d'exclure de façon sélective que des documents spécifiques. Les configurations d'indexation activée/désactivée sont utiles lorsque vous n'avez qu'un sous-ensemble de documents à interroger.

Ainsi, l’exemple suivant montre comment inclure un document explicitement à l’aide du [Kit de développement logiciel (SDK) .NET de l’API DocumentDB](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-sdk-dotnet) et de la propriété [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modifying-the-indexing-policy-of-a-collection"></a>Modification de la stratégie d'indexation d'une collection
Azure Cosmos DB vous permet d’apporter des modifications à la stratégie d’indexation d’une collection à la volée. Une modification de stratégie d’indexation dans une collection Azure Cosmos DB peut entraîner une modification de la forme de l’index, notamment les chemins d’accès indexables, leur précision et le modèle de cohérence de l’index. Pour modifier cette stratégie, vous devez donc opter pour un nouvel index. 

**Transformations d'index en ligne**

![Mécanismes de l’indexation – Transformations d’index en ligne Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

Les transformations d’index sont effectuées en ligne, ce qui signifie que les documents indexés par l’ancienne stratégie sont transformés efficacement par la nouvelle stratégie, **sans affecter la disponibilité de l’écriture ou le débit approvisionné** de la collection. La cohérence des opérations de lecture et d'écriture effectuées à l'aide de l'API REST, des Kits de développement logiciel ou à partir des déclencheurs et des procédures stockées n'est pas affectée au cours de la transformation de l'index. Cela signifie qu’aucune dégradation de performances, ou interruption de vos applications, n’est effectuée lorsque vous modifiez une stratégie d'indexation.

Toutefois, lors de la transformation de l'index, les requêtes sont cohérentes, et ce, quelle que soit la configuration du mode d'indexation (mode Cohérent ou Différé). Cela s'applique également aux requêtes effectuées à l'aide de n'importe quelle interface : API REST, Kit de développement logiciel (SDK) ou à partir de déclencheurs et de procédures stockées. Tout comme avec l'indexation Différé, la transformation de l'index est exécutée de façon asynchrone en arrière-plan sur les réplicas à l'aide de ressources d’échange disponibles pour un réplica donné. 

Les transformations d’index sont également effectuées **in situ**. Cela signifie qu’Azure Cosmos DB ne conserve pas deux copies de l’index mais remplace l’ancien par le nouveau. Cela signifie qu'aucun espace disque supplémentaire n’est requis ou utilisé dans vos collections lors de l'exécution des transformations d’index.

Lorsque vous modifiez une stratégie d'indexation, ces modifications qui sont appliquées pour passer de l'ancien index à un nouveau dépendent plus des configurations du mode d’indexation que d'autres valeurs telles que les chemins d'accès inclus/exclus, les types d'index et les précisions. Si vos anciennes et nouvelles stratégies utilisent l’indexation cohérente, Azure Cosmos DB effectue une transformation d’index en ligne. Vous ne pouvez pas appliquer une autre modification de stratégie d'indexation via le mode d'indexation Cohérent lors de la transformation.

Vous pouvez toutefois opter pour le mode d'indexation Différé ou Aucun lorsqu'une transformation est en cours. 

* Lorsque vous optez pour le mode Différé, la modification de la stratégie d’indexation prend effet immédiatement et Azure Cosmos DB démarre la recréation de l’index de façon asynchrone. 
* Lorsque vous optez pour le mode Aucun, l'index est immédiatement désactivé. Opter pour le mode Aucun peut s’avérer très utile lorsque vous souhaitez annuler une transformation en cours et utiliser une nouvelle stratégie d'indexation. 

Si vous utilisez le Kit de développement logiciel (SDK) .NET, vous pouvez lancer une modification de stratégie d’indexation en utilisant la nouvelle méthode **ReplaceDocumentCollectionAsync** et suivre la progression, en pourcentage, de la transformation d’index à l’aide de la propriété Response **IndexTransformationProgress** à partir d’un appel **ReadDocumentCollectionAsync**. D’autres Kits de développement logiciel (SDK), ainsi que l'API REST, prennent en charge des propriétés et des méthodes équivalentes pour apporter des modifications de stratégie d'indexation.

Voici un extrait de code qui vous indique comment faire passer la stratégie d'indexation d'une collection, du mode Cohérent au mode Différé.

**Faire passer la stratégie d’indexation du mode Cohérent au mode Différé**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


Vous pouvez vérifier la progression d'une transformation d'index en appelant ReadDocumentCollectionAsync, par exemple, comme illustré ci-dessous.

**Suivre la progression de la transformation d’index**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

Vous pouvez supprimer l'index d’une collection en optant pour le mode d'indexation Aucun. Ceci peut s’avérer très utile si vous souhaitez annuler une transformation en cours et en démarrer une nouvelle immédiatement.

**Suppression de l'index d’une collection**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Quand pouvez-vous modifier la stratégie d’indexation dans vos collections Azure Cosmos DB ? Les scénarios d'utilisation les plus courants sont les suivants :

* Fournir des résultats cohérents lors du bon déroulement de l’opération, mais revenir à l'indexation différée lors de l'importation de données en bloc
* Commencer à utiliser de nouvelles fonctionnalités d’indexation sur vos collections Azure Cosmos DB, telles que les requêtes géospatiales nécessitant le type d’index spatial, ou les requêtes de tri/plage de chaîne qui requièrent le type d’index de plage de chaîne
* Sélectionner les propriétés à indexer et les modifier au fil du temps
* Ajuster la précision d'indexation pour améliorer les performances de requête ou réduire le stockage utilisé

> [!NOTE]
> Pour modifier la stratégie d'indexation à l'aide de ReplaceDocumentCollectionAsync, vous devez utiliser la version 1.3.0 (ou une version ultérieure) du Kit de développement logiciel (SDK) .NET
> 
> Pour que la transformation d’index se déroule correctement, vous devez vous assurer qu’il existe suffisamment d’espace libre sur la collection. Si la collection atteint son quota de stockage, la transformation d’index est interrompue. La transformation d’index reprend automatiquement dès que de l’espace de stockage est disponible, par exemple, si vous supprimez certains documents.
> 
> 

## <a name="performance-tuning"></a>Réglage des performances
Les API DocumentDB fournissent des informations sur les mesures des performances telles que le stockage d’index utilisé et le coût du débit (unités de demande) pour chaque opération. Ces informations peuvent être utilisées pour comparer différentes stratégies d’indexation et pour le réglage des performances.

Pour vérifier le quota de stockage et l’utilisation d’une collection, exécutez une demande HEAD ou GET sur la ressource de collection et examinez les en-têtes x-ms-request-quota et x-ms-request-usage. Dans le Kit de développement logiciel (SDK) .NET, les propriétés [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) et [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) de [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) contiennent ces valeurs correspondantes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Pour mesurer la surcharge de l’indexation sur chaque opération d’écriture (création, mise à jour ou suppression), inspectez l’en-tête x-ms-request-charge (ou la propriété [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) équivalente dans [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) au sein du Kit de développement logiciel (SDK) .NET) qui permet de mesurer le nombre d’unités de demande consommées par ces opérations.

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
Une modification dans le schéma de la stratégie d'indexation a été introduite le 7 juillet 2015 avec la version 2015-06-03 de l'API REST. Les classes correspondantes dans les versions du Kit de développement logiciel (SDK) ont de nouvelles implémentations pour correspondre au schéma. 

Les modifications suivantes ont été implémentées dans la spécification JSON :

* La stratégie d'indexation prend en charge les index de plage pour les chaînes
* Chaque chemin d'accès peut avoir plusieurs définitions d'index, un pour chaque type de données
* L'indexation de précision prend en charge les nombres de 1 à 8, les chaînes de 1 à 100 et -1 (précision maximale)
* Les segments des chemins d'accès ne nécessitent pas de doubles guillemets pour éviter chaque chemin d'accès. Par exemple, vous pouvez ajouter un chemin d’accès pour /title/? au lieu de /"title"/?
* Le chemin d'accès racine représentant « tous les chemins d'accès » peut être représenté comme /* (en plus de /)

Si votre code approvisionne des collections avec une stratégie d'indexation personnalisée écrite avec la version 1.1.0 du Kit de développement logiciel (SDK) .NET ou une version antérieure, vous devrez modifier le code de votre application pour gérer ces modifications afin de les déplacer vers la version 1.2.0 du Kit de développement logiciel (SDK). Si vous n’avez pas le code qui configure la stratégie d'indexation, ou si vous envisagez de continuer à l'aide d'une version du Kit de développement logiciel (SDK) plus ancienne, aucune modification n'est requise.

À titre de comparaison pratique, voici un exemple de stratégie d’indexation personnalisée écrite à l’aide de l’API REST version 2015-06-03 et de la version précédente 2015-04-08.

**Stratégie d’indexation JSON précédente**

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

**Stratégie d’indexation JSON actuelle**

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

## <a name="next-steps"></a>Étapes suivantes
Suivez les liens ci-dessous pour accéder à des exemples de gestion de stratégie d’index et en savoir plus sur le langage de requête d’Azure Cosmos DB.

1. [Exemples de code de gestion d’index .NET de l’API DocumentDB](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2. [Opérations de collecte de l’API REST DocumentDB](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3. [Requête avec SQL](documentdb-sql-query.md)

