---
title: Activité Web dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser Activité Web, l’une des activités de flux de contrôle prises en charge par Azure Data Factory, pour appeler un point de terminaison REST à partir d’un pipeline.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: jhubbard
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 510f9ac95245580cb7f2f51487b5aeacc2a4825c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="web-activity-in-azure-data-factory"></a>Activité Web dans Azure Data Factory
Une activité web peut être utilisée pour appeler un point de terminaison REST personnalisé à partir d’un pipeline Data Factory. Vous pouvez transmettre des jeux de données et des services liés que l’activité peut utiliser et auxquels elle peut accéder. 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Syntaxe

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
Nom | Nom de l’activité web | Chaîne | OUI
Type | Doit avoir la valeur **WebActivity**. | Chaîne | OUI
method | Méthode d’API REST pour le point de terminaison cible. | Chaîne. <br/><br/>Types pris en charge : « GET », « POST », « PUT » | OUI
url | Point de terminaison cible et chemin d’accès | Chaîne (ou expression avec resultType de chaîne). Si elle ne reçoit pas de réponse du point de terminaison, l’activité expire à 1 minute avec une erreur. | OUI
headers | En-têtes envoyés à la demande. Par exemple, pour définir la langue et le type sur une requête : `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Chaîne (ou expression avec resultType de chaîne) | Oui, l’en-tête Content-type est obligatoire. `"headers":{ "Content-Type":"application/json"}`
body | Représente la charge utile envoyée au point de terminaison. Obligatoire pour les méthodes POST/PUT.  | Chaîne (ou expression avec resultType de chaîne). <br/><br/>Voir le schéma de la charge utile de demande dans la section [Schéma de la charge utile](#request-payload-schema). | Non 
Authentification | Méthode d’authentification utilisée pour appeler le point de terminaison. Les types pris en charge sont « De base » ou « ClientCertificate ». Pour en savoir plus, voir la section [Authentification](#authentication). Si l’authentification n’est pas obligatoire, excluez cette propriété. | Chaîne (ou expression avec resultType de chaîne) | Non 
jeux de données | Liste des jeux de données transmis au point de terminaison. | Tableau de références de jeu de données. Peut être un tableau vide. | OUI
linkedServices | Liste des services liés transmise au point de terminaison. | Tableau des références de service lié. Peut être un tableau vide. | OUI

> [!NOTE]
> Les points de terminaison REST que l’activité web appelle doivent retourner une réponse de type JSON. Si elle ne reçoit pas de réponse du point de terminaison, l’activité expire à 1 minute avec une erreur.

## <a name="authentication"></a>Authentification

### <a name="none"></a>Aucun
Si l’authentification n’est pas requise, n’incluez pas la propriété « authentication ».

### <a name="basic"></a>De base
Spécifiez le nom d’utilisateur et le mot de passe à utiliser avec l’authentification de base. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificat client
Spécifiez le contenu encodé en Base64 d’un fichier PFX et le mot de passe. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Schéma de charge utile de demande
Lorsque vous utilisez la méthode POST/PUT, la propriété body représente la charge utile envoyée au point de terminaison. Vous pouvez transmettre des services liés et des jeux de données dans la charge utile. Voici le schéma de la charge utile : 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>Exemples
Dans cet exemple, l’activité web dans le pipeline appelle un point de terminaison REST. Elle transmet un service lié Azure SQL et un jeu de données Azure SQL au point de terminaison. Le point de terminaison REST utilise la chaîne de connexion Azure SQL pour se connecter au serveur Azure SQL et retourne le nom de l’instance du serveur SQL. 

### <a name="pipeline-definition"></a>Définition de pipeline

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Valeurs de paramètre de pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Code de point de terminaison de service web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
