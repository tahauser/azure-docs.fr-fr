---
title: "Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure"
description: "Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/07/2017
ms.author: eugenesh
ms.openlocfilehash: 2dac2c5980970946a6b9c26ee6ee8ac0f0344144
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure
Cet article explique comment configurer un indexeur d’objets blob Recherche Azure pour extraire le contenu structuré d’objets blob JSON dans Stockage Blob Azure.

Les objets blob JSON dans Stockage Blob Azure se composent généralement d’un seul document JSON ou d’un tableau JSON. L’indexeur d’objets blob dans Recherche Azure peut analyser l’une ou l’autre de ces constructions selon la définition du paramètre **parsingMode** sur la requête.

| Document JSON | parsingMode | Description | Disponibilité |
|--------------|-------------|--------------|--------------|
| Un seul par objet blob | `json` | Analyse les objets blob JSON comme un bloc de texte unique. Chaque objet blob JSON devient un document Recherche Azure unique. | Généralement disponible dans les API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) et [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer). |
| Plusieurs par objet blob | `jsonArray` | Analyse un tableau JSON dans l’objet blob, où chaque élément du tableau devient un document Recherche Azure distinct.  | En préversion, dans [REST api-version=`2016-09-01-Preview`](search-api-2016-09-01-preview.md) et [.NET SDK Preview](https://aka.ms/search-sdk-preview). |

> [!Note]
> Les API en préversion sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.
>

## <a name="setting-up-json-indexing"></a>Configuration de l’indexation JSON
L’indexation d’objets blob JSON s’apparente à l’extraction de documents standard dans un flux de travail en trois parties commun à tous les indexeurs dans Recherche Azure.

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d’une source de données

La première étape consiste à fournir les informations de connexion à la source de données utilisées par l’indexeur. Le type de source de données, spécifié ici sous la forme `azureblob`, détermine les comportements d’extraction de données appelés par l’indexeur. Pour l’indexation d’objets blob JSON, la définition de la source de données est identique pour les documents et les tableaux JSON. 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Étape 2 : Créer un index de recherche cible 

Les indexeurs sont couplés à un schéma d’index. Si vous utilisez l’API (à la place du portail), préparez un index à l’avance pour pouvoir le spécifier sur l’opération de l’indexeur. 

> [!Note]
> Les indexeurs sont exposés dans le portail par le biais de l’action **Importer** pour un nombre limité d’indexeurs généralement disponibles. Le flux de travail d’importation peut souvent construire un index préliminaire en fonction des métadonnées contenues dans la source. Pour plus d’informations, consultez [Importer des données dans Recherche Azure dans le portail](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Étape 3 : Configurer et exécuter l’indexeur

Jusqu’à présent, la source de données et l’index ont été définis indépendamment du paramètre parsingMode. Toutefois, dans cette étape de configuration de l’indexeur, le processus varie selon la façon dont vous souhaitez analyser et structurer le contenu des objets blob JSON dans un index Recherche Azure.

Quand vous appelez l’indexeur, effectuez les étapes suivantes :

+ Définissez le paramètre **parsingMode** avec la valeur `json` (pour indexer chaque objet blob en tant que document unique) ou `jsonArray` (si vos objets blob contiennent des tableaux JSON et que vous souhaitez traiter chaque élément de tableau comme un document distinct).

+ Si nécessaire, utilisez les **mappages de champs** pour choisir les propriétés du document JSON source à utiliser pour remplir votre index de recherche cible. Pour les tableaux JSON, si le tableau existe sous la forme d’une propriété de niveau inférieur, vous pouvez définir une racine de document qui indique l’emplacement du tableau dans l’objet blob.

> [!IMPORTANT]
> Lorsque vous utilisez le mode d’analyse `json` ou `jsonArray`, Recherche Azure suppose que tous les objets blob dans votre source de données contiennent JSON. Si vous devez prendre en charge une combinaison d’objets blob JSON et autres dans la même source de données, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Comment analyser des objets blob JSON uniques

Par défaut, [l’indexeur d’objets blob Recherche Azure](search-howto-indexing-azure-blob-storage.md) analyse les objets blob JSON comme un bloc de texte unique. Vous souhaitez généralement conserver la structure de vos documents JSON. En guise d’exemple, prenons le document JSON suivant dans Stockage Blob Azure :

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Définition de l’indexeur pour les objets blob JSON uniques

À l’aide de l’indexeur d’objets blob Recherche Azure, un document JSON semblable à l’exemple précédent est analysé en un document Recherche Azure unique. L’indexeur charge un index en mettant en correspondance les champs « text », « datePublished » et « tags » de la source avec les champs cibles ayant les mêmes nom et type.

La configuration est fournie dans le corps d’une opération de l’indexeur. Rappelez-vous que l’objet source de données, défini précédemment, spécifie le type de source de donnée et les informations de connexion. En outre, l’index cible doit également exister comme conteneur vide dans votre service. La planification et les paramètres sont facultatifs. Toutefois, si vous les omettez, l’indexeur s’exécute immédiatement en mode d’analyse `json`.

Une requête complète peut se présenter comme suit :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Comme indiqué, les mappages de champs ne sont pas nécessaires. Étant donné un index avec les champs « text », « datePublished » et « tags », l’indexeur d’objets blob peut déduire le mappage correct sans qu’un mappage de champs ne soit présent dans la requête.

## <a name="how-to-parse-json-arrays-preview"></a>Comment analyser les tableaux JSON (préversion)

Vous pouvez également opter pour la fonctionnalité en préversion des tableaux JSON. Cette fonctionnalité est utile quand les objets blob contiennent un *tableau d’objets JSON* et que vous souhaitez que chaque élément devienne un document Recherche Azure distinct. Prenons par exemple l’objet blob JSON suivant. Vous pouvez remplir l’index Recherche Azure avec trois documents distincts contenant chacun les champs « id » et « text ».  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Définition de l’indexeur pour un tableau JSON

Pour un tableau JSON, la requête de l’indexeur utilise l’API en préversion et l’analyseur `jsonArray`. Il s’agit des deux seules exigences spécifiques aux tableaux pour l’indexation d’objets blob JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Là encore, notez que les mappages de champs ne sont pas nécessaires. Étant donné un index avec les champs « id » et « text », l’indexeur d’objets blob peut déduire le mappage correct sans une liste de mappage de champs.

### <a name="nested-json-arrays"></a>Tableaux JSON imbriqués
Que se passe-t-il si vous souhaitez indexer un tableau d’objets JSON, mais que ce tableau est imbriqué quelque part dans le document ? Vous pouvez choisir la propriété qui contient le tableau à l’aide de la propriété de configuration `documentRoot` . Par exemple, si vos objets blob ressemblent à ceci :

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Utilisez cette configuration pour indexer le tableau contenu dans la propriété `level2` :

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Utilisation des mappages de champ pour créer des documents de recherche

Quand les champs sources et cibles ne sont pas parfaitement alignés, vous pouvez définir une section de mappage de champs dans le corps de la requête pour des associations champ à champ explicites.

Actuellement, Azure Search ne peut pas indexer des documents JSON arbitraires directement, car il ne prend en charge que les types de données primitifs, les tableaux de chaînes et les points GeoJSON. Toutefois, vous pouvez utiliser les **mappages de champ** pour sélectionner des parties de votre document JSON et les intégrer dans les champs de niveau supérieur du document de recherche. Pour en savoir plus sur les principes de base des mappages de champs, consultez [Mappages de champs dans les indexeurs Recherche Azure](search-indexer-field-mappings.md).

Revenons à notre exemple de document JSON :

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Prenons un index de recherche avec les champs suivants : `text` de type `Edm.String`, `date` de type `Edm.DateTimeOffset` et `tags` de type `Collection(Edm.String)`. Notez la différence entre « datePublished » dans la source et le champ `date` dans l’index. Pour mapper votre document JSON à la forme souhaitée, utilisez les mappages de champ suivants :

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Les noms de champ source dans les mappages sont spécifiés selon la notation de [pointeur JSON](http://tools.ietf.org/html/rfc6901) . Vous débutez par une barre oblique pour faire référence à la racine de votre document JSON, puis sélectionnez la propriété souhaitée (au niveau arbitraire de l’imbrication) en utilisant un chemin d’accès séparé par des barres obliques avant.

Vous pouvez également faire référence à des éléments de tableau en utilisant un index de base zéro. Par exemple, pour sélectionner le premier élément du tableau « tags » dans l’exemple ci-dessus, utilisez un mappage de champ similaire au suivant :

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Si un nom de champ source dans un chemin de mappage de champ fait référence à une propriété qui n’existe pas dans JSON, ce mappage est ignoré sans erreur. Cela nous permet de prendre en charge les documents avec un schéma différent (cas fréquent). Comme il n’y a aucune validation, vous devez veiller à éviter les fautes de frappe dans la spécification du mappage de champ.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Exemple : Requête d’indexeur avec mappages de champs

L’exemple suivant est une charge utile d’indexeur complète, avec notamment des mappages de champs :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Azure Search
Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas à nous contacter sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Voir aussi

+ [Indexeurs dans Recherche Azure](search-indexer-overview.md)
+ [Indexation de Stockage Blob Azure avec Recherche Azure](search-howto-index-json-blobs.md)
+ [Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche Azure](search-howto-index-csv-blobs.md)
+ [Didacticiel : Rechercher des données semi-structurées à partir de Stockage Blob Azure](search-semi-structured-data.md)