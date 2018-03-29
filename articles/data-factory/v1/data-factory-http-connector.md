---
title: Déplacer des données à partir d’une source HTTP - Azure | Microsoft Docs
description: Découvrez comment déplacer des données à partir d’une source HTTP locale ou cloud à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9820ed9b4c0abbb79c6f92e62f294fb7fbd4c87e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Déplacer des données à partir d’une source HTTP à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](data-factory-http-connector.md)
> * [Version 2 - Préversion](../connector-http.md)

> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). Si vous utilisez la version 2 du service de fabrique de données, qui est une version d’évaluation, consultez l’article relatif au [connecteur HTTP dans V2](../connector-http.md).


Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour déplacer des données d’un point de terminaison HTTP local ou cloud vers un magasin de données récepteur pris en charge. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et la liste de magasins de données pris en charge comme sources/récepteurs.

À l’heure actuelle, Data Factory prend en charge le déplacement de données d’une source HTTP vers d’autres magasins de données, mais pas le déplacement de données de ces autres magasins vers une destination HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Scénarios et types d’authentification pris en charge
Vous pouvez utiliser ce connecteur HTTP pour récupérer des données d’un **point de terminaison HTTP/s cloud et local** à l’aide de la méthode HTTP **ET** ou **POST**. Les types d’authentification suivants sont pris en charge : **Anonymous** (Anonyme), **Basic** (De base), **Digest**, **Windows** et **ClientCertificate** (Certificat client). Notez que ce connecteur diffère du [connecteur Table web](data-factory-web-table-connector.md), qui est utilisé pour extraire le contenu d’une table d’une page web HTML.

Pour copier des données à partir d’un point de terminaison HTTP local, vous devez installer une passerelle de gestion des données dans l’environnement local/sur la machine virtuelle Azure. Consultez l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et obtenir des instructions détaillées sur la configuration de la passerelle.

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec une activité de copie qui déplace les données d’une source HTTP à l’aide de différents outils/API.

- Le moyen le plus simple de créer un pipeline consiste à utiliser **l’Assistant Copie**. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

- Vous pouvez également utiliser les outils suivants pour créer un pipeline : le **portail Azure**, **Visual Studio**, **Azure PowerShell**, le **modèle Azure Resource Manager**, l’**API .NET** et l’**API REST**. Pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie, consultez le [didacticiel sur l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Pour accéder à des exemples JSON sur la copie de données d’une source HTTP vers le service Stockage Blob Azure, consultez la section [Exemples JSON](#json-examples) de cet article.

## <a name="linked-service-properties"></a>Propriétés du service lié
Le tableau suivant fournit une description des éléments JSON spécifiques du service lié HTTP.

| Propriété | Description | Obligatoire |
| --- | --- | --- |
| Type | La propriété type doit être définie sur : `Http`. | OUI |
| url | URL de base du serveur web | OUI |
| authenticationType | Spécifie le type d’authentification. Les valeurs autorisées sont : **Anonymous** (Anonyme), **Basic** (De base), **Digest**, **Windows**, **ClientCertificate** (Certificat client). <br><br> Reportez-vous aux sections suivant ce tableau pour accéder à d’autres propriétés et à des exemples JSON sur ces types d’authentification. | OUI |
| enableServerCertificateValidation | Indiquez si la validation des certificats SSL doit être activée lorsque la source est un serveur web HTTPS. | Non, la valeur par défaut est True. |
| gatewayName | Nom de la passerelle de gestion des données pour se connecter à une source HTTP locale. | Oui en cas de copie de données à partir d’une source HTTP locale. |
| Encryptedcredential | Informations d’identification chiffrées pour accéder au point de terminaison. Elles sont générées automatiquement lorsque vous configurez les informations d’authentification dans l’Assistant de copie ou la boîte de dialogue contextuelle ClickOnce. | Non. S’applique uniquement pour la copie de données à partir d’un serveur HTTP local. |

Pour plus d’informations sur la définition des informations d’identification pour une source de données de connecteur HTTP local, consultez [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Utilisation de l’authentification Basic (De base), Digest ou Windows

Définissez `authenticationType` sur `Basic`, `Digest` ou `Windows` et spécifiez les propriétés suivantes en plus des propriétés génériques du connecteur HTTP présentées ci-dessus :

| Propriété | Description | Obligatoire |
| --- | --- | --- |
| username | Nom d’utilisateur pour accéder au point de terminaison HTTP. | OUI |
| password | Mot de passe de l’utilisateur (nom d’utilisateur). | OUI |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemple : utilisation de l’authentification Basic (De base), Digest ou Windows

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Utilisation de l’authentification ClientCertificate (Certificat client)

Pour utiliser l’authentification de base, définissez `authenticationType` sur `ClientCertificate` et spécifiez les propriétés suivantes en plus des propriétés génériques du connecteur HTTP présentées ci-dessus :

| Propriété | Description | Obligatoire |
| --- | --- | --- |
| embeddedCertData | Contenu codé en Base64 des données binaires du fichier Personal Information Exchange (PFX). | Spécifiez soit la propriété `embeddedCertData`, soit la propriété `certThumbprint`. |
| certThumbprint | Empreinte du certificat qui a été installé dans le magasin de certificats de votre ordinateur de passerelle. S’applique uniquement pour la copie de données à partir d’une source HTTP locale. | Spécifiez soit la propriété `embeddedCertData`, soit la propriété `certThumbprint`. |
| password | Mot de passe associé au certificat. | Non  |

Si vous utilisez `certThumbprint` pour l’authentification et le certificat est installé dans le magasin personnel de l’ordinateur local, vous devez accorder l’autorisation de lecture au service de passerelle :

1. Lancez Microsoft Management Console (MMC). Ajouter le composant logiciel enfichable **Certificats**ciblant l’**ordinateur local**.
2. Développez **Certificats**, **Personnel**, puis cliquez sur **Certificats**.
3. Cliquez avec le bouton droit sur le certificat du magasin personnel, puis sélectionnez **Toutes les tâches**->**Gérer les clés privées...**
3. Dans l’onglet **Sécurité**, ajoutez le compte d’utilisateur sous lequel le service hôte de la passerelle de gestion des données s’exécute avec l’accès en lecture au certificat.  

#### <a name="example-using-client-certificate"></a>Exemple : utilisation d’un certificat client
Ce service lié lie votre fabrique de données à un serveur web HTTP local. Il utilise un certificat client installé sur l’ordinateur doté de la passerelle de gestion des données.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exemple : utilisation d’un certificat client dans un fichier
Ce service lié lie votre fabrique de données à un serveur web HTTP local. Il utilise un fichier de certificat client sur l’ordinateur doté de la passerelle de gestion des données.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **Http** présente les propriétés suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | Spécifie le type du jeu de données. Cette propriété doit être définie sur `Http`. | OUI |
| relativeUrl | URL relative de la ressource qui contient les données. Quand le chemin d’accès n’est pas spécifié, seule l’URL spécifiée dans la définition du service lié est utilisée. <br><br> Pour construire une URL dynamique, vous pouvez utiliser [les variables système et les fonctions de Data Factory](data-factory-functions-variables.md), par exemple "relativeUrl": "$$Text.Format(’/my/report?month={0:yyyy}-{0:MM}&fmt=csv’, SliceStart)". | Non  |
| requestMethod | Méthode HTTP. Les valeurs autorisées sont **GET** ou **POST**. | Non. La valeur par défaut est `GET`. |
| additionalHeaders | En-têtes de requête HTTP supplémentaires. | Non  |
| RequestBody | Corps de la requête HTTP. | Non  |
| format | Si vous souhaitez simplement **récupérer les données du point de terminaison HTTP en l’état**, sans les analyser, ignorez ces paramètres de format. <br><br> Si vous souhaitez analyser le contenu de la réponse HTTP pendant la copie, les types de formats suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Pour en savoir plus, consultez les sections relatives à [format Text](data-factory-supported-file-and-compression-formats.md#text-format), [format Json](data-factory-supported-file-and-compression-formats.md#json-format), [format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [format Orc](data-factory-supported-file-and-compression-formats.md#orc-format) et [format Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Non  |
| compression | Spécifiez le type et le niveau de compression pour les données. Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**. Les niveaux pris en charge sont **Optimal** et **Fastest**. Pour plus d’informations, consultez [Formats de fichiers et de compression pris en charge dans Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Non  |

### <a name="example-using-the-get-default-method"></a>Exemple : utilisation de la méthode GET (par défaut)

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

### <a name="example-using-the-post-method"></a>Exemple : utilisation de la méthode POST

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d’entrée et de sortie et la stratégie sont disponibles pour tous les types d’activités.

En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Actuellement, quand la source de l’activité de copie est de type **HttpSource**, les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| httpRequestTimeout | Délai d’expiration (TimeSpan) pour l’obtention d’une réponse par la requête HTTP. Il s’agit du délai d’expiration pour l’obtention d’une réponse, et non du délai d’expiration pour la lecture des données de la réponse. | Non. Valeur par défaut : 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Formats de fichier et de compression pris en charge
Pour plus d’informations, voir [Formats de fichiers et de compression pris en charge dans Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples"></a>Exemples JSON
Les exemples suivants offrent des exemples de définitions JSON que vous pouvez utiliser pour créer un pipeline à l’aide du [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou d’[Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données d’une source HTTP vers le service Stockage Blob Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie de Microsoft Azure Data Factory.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Exemple : copier des données d’une source SFTP vers le service Stockage Blob Azure
La solution Data Factory pour cet exemple contient les entités Data Factory suivantes :

1. Un service lié de type [HTTP](#linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [Http](#dataset-properties).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [ttpSource](#copy-activity-properties) et [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L’exemple copie des données d’une source HTTP vers un objet blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

### <a name="http-linked-service"></a>Service lié HTTP
Cet exemple utilise le service lié HTTP avec l’authentification anonyme. Pour connaître les différents types d’authentification que vous pouvez utiliser, consultez la section [Service lié HTTP](#linked-service-properties).

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Service lié Stockage Azure

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Jeu de données d’entrée HTTP
La définition de **external** sur **true** informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à la Data Factory et non produit par une activité dans la Data Factory.

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Jeu de données de sortie d’objet Blob Azure

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1).

```JSON
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-with-copy-activity"></a>Pipeline avec activité de copie

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition JSON du pipeline, le type **source** est défini sur **ttpSource** et le type **sink** sur **BlobSink**.

Pour obtenir la liste des propriétés prises en charge par le type HttpSource, consultez [HttpSource](#copy-activity-properties).

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Pour savoir comment mapper des colonnes d’un jeu de données source sur des colonnes d’un jeu de données récepteur, consultez [Mappage de colonnes des jeux de données dans Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.
