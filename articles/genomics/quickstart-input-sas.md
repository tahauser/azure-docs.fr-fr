---
title: "Envoyer un flux de travail à l’aide d’un SAS plutôt qu’une clé de compte de stockage | Microsoft Docs"
titleSuffix: Azure
description: "Le démarrage rapide suppose que le client msgen est installé et que vous avez exécuté l’échantillon de données dans le service."
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 03/02/2018
ms.openlocfilehash: b6d84428749d8f5f78374efcca22ef913ee96c5e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="submit-a-workflow-using-a-sas-instead-of-a-storage-account-key"></a>Envoyer un flux de travail à l’aide d’un SAS plutôt qu’une clé de compte de stockage

Ce guide de démarrage rapide montre comment envoyer un flux de travail au service Microsoft Genomics à l’aide d’un fichier config.txt contenant des [signatures d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) au lieu de clés de compte de stockage. Cette fonctionnalité peut être utile si avoir la clé de compte de stockage visible dans le fichier config.txt pose des problèmes de sécurité. Cette article suppose que vous avez déjà installé et exécuté le client `msgen`, et que vous savez comment utiliser Stockage Azure. Si vous avez soumis un workflow à l’aide de l’exemple de données fourni, vous êtes prêt à exécuter ce démarrage rapide. 

## <a name="what-is-a-sas"></a>Qu’est ce qu’une SAP ?
Une [signature d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) fournit un accès délégué aux ressources de votre compte de stockage. Avec une signature d’accès partagé, vous pouvez accorder l’accès aux ressources dans votre compte de stockage sans partager les clés de votre compte. C’est tout l’intérêt d’utiliser des signatures d’accès partagé dans vos applications : une SAP est un moyen sécurisé de partager vos ressources de stockage sans compromettre vos clés de compte.

La SAP envoyée à Microsoft Genomics doit être une [SAP Service](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS) qui délègue l’accès uniquement au blob ou au conteneur où sont stockés les fichiers d’entrée et de sortie. 

L’URI d’un jeton de signature d’accès partagé (SAP) au niveau du service se compose de l’URI vers la ressource à laquelle la SAP va déléguer l’accès, suivi du jeton SAP. Le jeton SAP est la chaîne de requête qui inclut toutes les informations nécessaires pour authentifier la SAP, et qui précise également la ressource, les autorisations disponibles pour l’accès, l’intervalle de temps pendant lequel la signature est valide, l’adresse ou la plage d’adresses IP prise en charge à partir de laquelle les requêtes peuvent provenir, le protocole pris en charge permettant d’effectuer une requête, un identificateur de stratégie d’accès facultatif associé à la demande et la signature proprement dite. 

## <a name="sas-needed-for-submitting-a-workflow-to-the-microsoft-genomics-service"></a>SAP nécessaires pour l’envoi d’un flux de travail au service Microsoft Genomics
Deux ou plusieurs jetons SAP sont requis pour chaque flux de travail envoyé au service Microsoft Genomics, un pour chaque fichier d’entrée et un pour le conteneur de sortie.

Les SAP pour les fichiers d’entrée doivent avoir les propriétés suivantes :
1.  Portée (compte, conteneur, blob) : blob
2.  Expiration : dans 48 heures
3.  Autorisations : lecture

Les SAP pour le conteneur de sortie doivent avoir les propriétés suivantes :
1.  Portée (compte, conteneur, blob) : conteneur
2.  Expiration : dans 48 heures
3.  Autorisations : lecture, écriture, suppression


## <a name="create-a-sas-for-the-input-files-and-the-output-container"></a>Créer une SAP pour les fichiers d’entrée et le conteneur de sortie
Il existe deux façons de créer un jeton SAP, à l’aide de l’Explorateur Stockage Azure ou par programme.  Si vous écrivez du code, vous pouvez construire la SAP vous-même, ou utiliser le kit de développement logiciel (SDK) Stockage Azure dans le langage de votre choix.


### <a name="set-up-create-a-sas-using-azure-storage-explorer"></a>Configuration : créer une SAP à l’aide de l’Explorateur Stockage Azure

L’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) est un outil pour gérer les ressources que vous avez stockées dans le stockage Azure.  Vous pouvez obtenir plus d’informations sur l’utilisation de l’Explorateur Stockage Azure [ici](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Les SAP pour les fichiers d’entrée doivent être déterminées en fonction du fichier d’entrée particulier (blob). Pour créer un jeton SAP, suivez [ces instructions](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-storage-explorer#work-with-shared-access-signatures). Une fois que vous avez créé la SAP, l’URL complète avec la chaîne de requête ainsi que la chaîne de requête elle-même sont fournies et peuvent être copiées à partir de l’écran.

 ![Explorateur de stockage SAP Genomics](./media/quickstart-input-sas/genomics-sas-storageexplorer.png "Explorateur de stockage SAP Genomics")


### <a name="set-up-create-a-sas-programattically"></a>Configuration : créer une SAP par programme

Pour créer une SAP à l’aide du kit de développement logiciel (SDK) Stockage Azure, consultez la documentation existante dans plusieurs langages, notamment [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2#generate-a-shared-access-signature-uri-for-a-blob), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-python-how-to-use-blob-storage), and [Node.js](https://docs.microsoft.com/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage#work-with-shared-access-signatures). 

Pour créer une SAP sans kit de développement logiciel (SDK), la chaîne de requête SAP peut être directement construite, y compris toutes les informations requises pour authentifier la SAP. Ces [instructions](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) décrivent en détail les composants de la chaîne de requête SAP et comment la générer. La signature SAP requise est créée en générant un HMAC à l’aide des informations d’authentification du blob/conteneur, comme décrit dans ces [instructions](https://docs.microsoft.com/rest/api/storageservices/service-sas-examples).


## <a name="add-the-sas-to-the-configtxt-file"></a>Ajouter la SAP au fichier config.txt
Pour exécuter un flux de travail via le service Microsoft Genomics à l’aide d’une chaîne de requête SAP, modifiez le fichier config.txt pour supprimer les clés du fichier config.txt. Ensuite, ajoutez la chaîne de requête SAP (qui commence par un `?`) au nom du conteneur de sortie, comme indiqué. 

![Configuration de SAP Genomics](./media/quickstart-input-sas/genomics-sas-config.png "Configuration de SAP Genomics")

Utilisez le client Microsoft Genomics Python pour soumettre votre flux de travail avec la commande suivante, en ajoutant la chaîne de requête SAP correspondante à chacun des noms d’objet blob d’entrée :

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read file, SAS query string appended] -b2 [name of your second paired end read file, SAS query string appended]
```

### <a name="if-adding-the-input-file-names-to-the-configtxt-file"></a>Si vous ajoutez les noms de fichiers d’entrée au fichier config.txt
Vous pouvez également ajouter directement les noms des fichiers de séquences appariées au fichier config.txt, en ajoutant les jetons de requête SAP comme indiqué :

![Configuration des noms de blobs SAP Genomics](./media/quickstart-input-sas/genomics-sas-config-blobnames.png "Configuration des noms de blobs SAP Genomics")

Utilisez le client Microsoft Genomics Python pour soumettre votre flux de travail à l’aide de la commande suivante, en omettant les commandes `-b1` et `-b2` :

```python
msgen submit -f [full path to your config file] 
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez utilisé des jetons SAP au lieu de clés de compte pour soumettre un flux de travail au service Microsoft Genomics via le client Python `msgen`. Pour plus d’informations sur la soumission du flux de travail et les autres commandes pouvant être utilisées avec le service Microsoft Genomics, consultez notre [FAQ](frequently-asked-questions-genomics.md). 
