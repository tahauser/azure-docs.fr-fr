---
title: "Rechercher des données semi-structurées dans le stockage cloud Azure"
description: "Recherche de données blob semi-structurées à l’aide de Recherche Azure."
author: roygara
manager: timlt
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: a80ae99c2ada00885019ee93e4ef36821340d3a5
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="part-2-search-semi-structured-data-in-cloud-storage"></a>Partie 2 : Rechercher des données semi-structurées dans le stockage cloud

Dans cette série de didacticiels en deux parties, vous allez apprendre à rechercher des données semi-structurées et non structurées à l’aide de la fonction Recherche Azure. La [Partie 1](../storage/blobs/storage-unstructured-search.md) vous a expliqué comment rechercher des données non structurées, mais contenait également des explications indispensables pour pouvoir suivre ce didacticiel, notamment sur la création du compte de stockage. 

Dans la Partie 2, nous allons nous intéresser aux données semi-structurées, telles que des données au format JSON, stockées dans des objets blob Azure. Les données semi-structurées contiennent des balises ou des marquages qui séparent le contenu au sein des données. Elles séparent les données non structurées qui doivent être indexées de façon globale des données formellement structurées, qui respectent un modèle de données, tel qu’un schéma de base de données relationnelle, pouvant être analysé par champ.

Dans la partie 2, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer une source de données de Recherche Azure pour un conteneur d’objets blob Azure
> * Créer et remplir un index de Recherche Azure et un indexeur pour analyser le conteneur et extraire le contenu de recherche
> * Effectuer une recherche dans l’index que vous venez de créer

> [!NOTE]
> Ce didacticiel implique la prise en charge des tableaux JSON, qui est une fonctionnalité en version préliminaire du service Recherche Azure. Elle n’est pas disponible dans le portail. Pour cette raison, nous utilisons l’API REST d’évaluation, qui fournit cette fonctionnalité, ainsi qu’un outil client REST pour appeler l’API.

## <a name="prerequisites"></a>Conditions préalables

* Vous devez avoir effectué le [précédent didacticiel](../storage/blobs/storage-unstructured-search.md) pour obtenir le compte de stockage et le service de recherche créés dans le didacticiel précédent.

* Vous devez avoir installé un client REST et savoir comment créer une requête HTTP. Dans le cadre de ce didacticiel, nous utilisons le client [Postman](https://www.getpostman.com/). N’hésitez pas à utiliser un autre client REST avec lequel vous êtes déjà familiarisé.

## <a name="set-up-postman"></a>Configurer Postman

Démarrez Postman et paramétrez une requête HTTP. Si vous n’êtes pas familiarisé avec cet outil, consultez [Explorer les API REST de la Recherche Azure à l’aide de Fiddler ou Postman](search-fiddler.md) pour plus d’informations.

La méthode de requête « POST » est utilisée pour chaque appel de ce didacticiel. Les clés d’en-tête sont « Content-type » et « api-key ». Les valeurs des clés d’en-tête sont, respectivement, « application/json » et votre « clé d’administration » (la clé d’administration est un espace réservé pour votre clé de recherche primaire). Le corps est l’endroit où vous ajoutez le contenu réel de votre appel. Selon le client que vous utilisez, il existe différentes façons de créer votre requête. Nous vous présentons ici les étapes de création de base.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/postmanoverview.png)

Pour les appels REST traités dans ce didacticiel, vous aurez besoin de votre clé d’API de recherche. Vous la trouverez dans la section **Clés** de votre service de recherche. Cette clé d’API doit figurer dans l’en-tête de chaque appel d’API (remplacez la « clé d’administration » dans la capture d’écran précédente avec cette clé) que vous devrez effectuer dans le cadre de ce didacticiel. Veillez à bien conserver la clé, car vous en aurez besoin pour chaque appel.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Télécharger les exemples de données

Un exemple de jeu de données a été préparé pour vous. **Téléchargez le fichier [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** et décompressez-le dans son propre dossier.

L’exemple de jeu de données contient des exemples de fichiers JSON, qui étaient à l’origine des fichiers texte obtenus à partir de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Pour vous simplifier la tâche, nous avons converti ces fichiers au format JSON.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Charger les exemples de données

Dans le portail Azure, accédez au compte de stockage créé dans le [didacticiel précédent](../storage/blobs/storage-unstructured-search.md). Ouvrez ensuite le conteneur de **données**, puis cliquez sur **Charger**.

Cliquez sur **Avancé**, saisissez « clinical-trials-json », puis chargez tous les fichiers JSON que vous avez téléchargés.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/clinicalupload.png)

Une fois le chargement terminé, les fichiers doivent apparaître dans leur propre sous-dossier au sein du conteneur de données.

## <a name="connect-your-search-service-to-your-container"></a>Connecter votre service de recherche à votre conteneur

Nous utilisons le client Postman pour effectuer trois appels d’API vers votre service de recherche afin de créer une source de données, un index et un indexeur. La source de données inclut un pointeur vers votre compte de stockage et vos données JSON. Votre service de recherche établit la connexion lors du chargement des données.

La chaîne de requête doit contenir **api-version=2016-09-01-Preview** et chaque appel doit renvoyer une réponse **201 Créé**. La version de l’API en disponibilité générale ne permet pas encore de traiter les fichiers JSON en tant que tableau JSON. Pour l’instant, ceci est possible uniquement dans la version préliminaire de l’API.

Exécutez les trois appels d’API suivants à partir de votre client REST.

### <a name="create-a-datasource"></a>Créer une source de données

Une source de données spécifie les données à indexer.

Le point de terminaison de cet appel est `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Remplacez `[service name]` par le nom de votre service de recherche.

Pour cet appel, vous avez besoin du nom de votre compte de stockage et de la clé de votre compte de stockage. La clé du compte de stockage est disponible dans le portail Azure, dans la section **Clés d’accès** de votre compte de stockage. L’emplacement est indiqué dans l’image suivante :

  ![Recherche de données semi-structurées](media/search-semi-structured-data/storagekeys.png)

Veillez à remplacer les éléments `[storage account name]` et `[storage account key]` dans le corps de votre appel avant d’exécuter ce dernier.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

La réponse doit ressembler à ce qui suit :

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

### <a name="create-an-index"></a>Création d'un index
    
Le deuxième appel d’API crée un index. Un index spécifie tous les paramètres et leurs attributs.

L’URL pour cet appel est `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Remplacez `[service name]` par le nom de votre service de recherche.

Tout d’abord, remplacez l’URL. Ensuite, copiez et collez le code suivant dans le corps de votre appel et exécutez la requête.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

La réponse doit ressembler à ce qui suit :

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

### <a name="create-an-indexer"></a>Créer un indexeur

Un indexeur connecte la source de données à l’index de recherche cible et peut fournir une planification afin d’automatiser l’actualisation des données.

L’URL pour cet appel est `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Remplacez `[service name]` par le nom de votre service de recherche.

Tout d’abord, remplacez l’URL. Ensuite, copiez et collez le code suivant dans le corps de votre appel et exécutez la requête.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

La réponse doit ressembler à ce qui suit :

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Rechercher vos fichiers JSON

Maintenant que votre service de recherche est connecté à votre conteneur de données, vous pouvez commencer à rechercher vos fichiers.

Ouvrez le portail Azure et accédez à votre service de recherche. Comme vous l’avez fait dans le didacticiel précédent.

  ![Recherche de données non structurées](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Recherche de métadonnées définies par l’utilisateur

Comme auparavant, les données peuvent être interrogées de plusieurs façons : recherche en texte intégral, propriétés système ou métadonnées définies par l’utilisateur. Les propriétés système et les métadonnées définies par l’utilisateur peuvent uniquement être recherchées avec le paramètre `$select` si elles ont été marquées comme **récupérables** lors de la création de l’index cible. Les paramètres de l’index ne peuvent pas être modifiés après leur création. Toutefois, des paramètres supplémentaires peuvent être ajoutés.

`$select=Gender,metadata_storage_size` est un exemple de requête de base qui limite le renvoi à ces deux paramètres.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/lastquery.png)

`$filter=MinimumAge ge 30 and MaximumAge lt 75` est un exemple de requête plus complexe qui renvoie uniquement les résultats pour lesquels le paramètre MinimumAge est supérieur ou égal à 30 et le paramètre MaximumAge inférieur à 75.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/metadatashort.png)

N’hésitez pas à mettre en pratique ce que vous avez appris et à essayer vous-même d’exécuter d’autres requêtes. Notez que vous pouvez utiliser des opérateurs logiques (AND, OR, NOT) et des opérateurs de comparaison (eq, ne, gt, lt, ge, le). Les comparaisons de chaînes sont sensibles à la casse.

Le paramètre `$filter` fonctionne uniquement avec des métadonnées qui ont été marquées comme « filtrables » lors de la création de l’index.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à rechercher des données semi-structurées en utilisant Recherche Azure, à savoir comment effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un service Azure Search Service à l’aide de l’API REST
> * Utiliser le service Azure Search Service pour rechercher votre conteneur

Suivez ce lien pour en savoir plus sur la fonctionnalité de recherche.

> [!div class="nextstepaction"]
> [Indexation de documents dans Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)