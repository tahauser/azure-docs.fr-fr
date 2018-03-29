---
title: Configuration d’une chaîne de connexion pour le stockage Azure | Microsoft Docs
description: Configurez une chaîne de connexion pour un compte de stockage Azure. Une chaîne de connexion inclut les informations nécessaires pour authentifier l’accès à un compte de stockage à partir de votre application, pendant l’exécution.
services: storage
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: cshoe
ms.openlocfilehash: dee0fd68ccaf80ff6e44ee10fa24bb19e220d0d5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-azure-storage-connection-strings"></a>Configuration des chaînes de connexion Stockage Azure

Une chaîne de connexion inclut les informations d’authentification nécessaires pour que votre application accède aux données dans un compte de stockage Azure pendant l’exécution. Vous pouvez configurer les chaînes de connexion pour effectuer les opérations suivantes :

* Connexion à l’émulateur de stockage Azure
* Accès à un compte de stockage dans Azure
* Accès aux ressources spécifiées dans Azure via une signature d’accès partagé (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Récupération de votre chaîne de connexion
Votre application doit accéder à la chaîne de connexion pendant l’exécution pour authentifier les requêtes transmises au stockage Azure. Plusieurs options vous permettant de stocker votre chaîne de connexion s’offrent à vous :

* Une application s’exécutant sur le bureau ou sur un appareil peut stocker la chaîne de connexion dans un fichier **app.config** ou **web.config**. Ajoutez la chaîne de connexion dans la section **AppSettings** de ces fichiers.
* Une application s’exécutant dans un service cloud Azure peut stocker la chaîne de connexion dans le [schéma de configuration du service Azure (fichier .cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Ajoutez la chaîne de connexion à la section **ConfigurationSettings** du fichier de configuration du service.
* Vous pouvez utiliser votre chaîne de connexion directement dans votre code. Cependant, le plus souvent, nous vous recommandons de stocker votre chaîne de connexion dans un fichier de configuration.

Le stockage de votre chaîne de connexion dans un fichier de configuration facilite la mise à jour de la chaîne de connexion qui vous permet de basculer entre l’émulateur de stockage et un compte de stockage Azure dans le cloud. Il vous suffit de modifier la chaîne de connexion pour la faire pointer vers votre environnement cible.

Vous pouvez utiliser [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) pour accéder à votre chaîne de connexion lors de l’exécution, quel que soit l’environnement d’exécution de votre application.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Création d’une chaîne de connexion pour l’émulateur de stockage
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Pour plus d’informations sur l’émulateur de stockage, consultez [Utilisation de l'émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Création d’une chaîne de connexion pour un compte de stockage Azure
Pour créer une chaîne de connexion pour votre compte de stockage Azure, utilisez le format suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTPS (recommandé) ou HTTP, remplacez `myAccountName` par le nom de votre compte de stockage et remplacez `myAccountKey` par la touche d’accès rapide à votre compte :

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Par exemple, votre chaîne de connexion peut ressembler à ceci :

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Même si le stockage Azure prend en charge HTTP et HTTPS au sein d’une chaîne de connexion, nous vous *conseillons vivement d’utiliser HTTPS*.

> [!TIP]
> Vous trouverez les chaînes de connexion de votre compte de stockage dans le [portail Azure](https://portal.azure.com). Accédez à **PARAMÈTRES** > **Clés d’accès** dans le panneau de menu de votre compte de stockage pour afficher les chaînes de connexion pour les clés d’accès primaire et secondaire.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Création d’une chaîne de connexion à l’aide d’une signature d’accès partagé
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Création d’une chaîne de connexion pour un point de terminaison de stockage explicite
Vous pouvez spécifier les points de terminaison de service explicites dans votre chaîne de connexion au lieu d’utiliser les points de terminaison par défaut. Pour créer une chaîne de connexion spécifiant un point de terminaison explicite, indiquez le point de terminaison complet de chaque service, ainsi que le protocole (HTTPS (recommandé) ou HTTP) au format suivant :

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Si vous avez mappé votre point de terminaison Stockage Blob à un [domaine personnalisé](../blobs/storage-custom-domain-name.md), il vaut mieux spécifier un point de terminaison explicite. Dans ce cas, vous pouvez spécifier votre point de terminaison personnalisé pour le stockage Blob dans votre chaîne de connexion. Vous pouvez éventuellement spécifier les points de terminaison par défaut pour les autres services si votre application les utilise.

Voici un exemple de chaîne de connexion qui spécifie un point de terminaison explicite pour le service Blob :

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Cet exemple spécifie des points de terminaison explicites pour tous les services, notamment un domaine personnalisé pour le service Blob :

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Les valeurs des points de terminaison dans une chaîne de connexion sont utilisées pour construire les URI de demande aux services de stockage. Elles indiquent la forme des URI renvoyés à votre code.

Si vous avez mappé un point de terminaison de stockage à un domaine personnalisé et omettez ce point de terminaison d’une chaîne de connexion, vous ne pourrez pas accéder aux données de ce service avec votre code à l’aide de cette chaîne de connexion.

> [!IMPORTANT]
> Les valeurs des points de terminaison de service dans vos chaînes de connexion doivent être des URI correctement formés, notamment `https://` (recommandé) ou `http://`. Étant donné que le stockage Azure ne prend pas encore en charge HTTPS pour les domaines personnalisés, vous *devez* spécifier `http://` pour n’importe quel URI de point de terminaison qui pointe vers un domaine personnalisé.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Création d’une chaîne de connexion avec un suffixe de point de terminaison
Pour créer une chaîne de connexion pour un service de stockage dans les régions ou les instances avec des suffixes de point de terminaison différents, comme pour Azure China ou Azure Government, utilisez le format de chaîne de connexion suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTPS (recommandé) ou HTTP, remplacez `myAccountName` par le nom de votre compte de stockage, remplacez `myAccountKey` par votre clé d’accès au compte et remplacez `mySuffix` par le suffixe d’URI :

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Voici un exemple de chaîne de connexion pour des services de stockage dans Azure China :

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analyse d’une chaîne de connexion
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Utilisation de l’émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md)
* [Explorateurs du stockage Azure](storage-explorers.md)
* [Utilisation des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md)

