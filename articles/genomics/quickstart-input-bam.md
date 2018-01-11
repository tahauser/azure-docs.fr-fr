---
title: "Démarrage rapide : Soumettre un workflow à l’aide d’une entrée de fichier BAM | Microsoft Docs"
titleSuffix: Azure
description: "Le démarrage rapide suppose que le client msgen est installé et que vous avez exécuté l’exemple de données dans le service."
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: 9887121593cad4931c86b55012f1c22686adf25f
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Soumettre un workflow à l’aide d’une entrée de fichier BAM

Ce démarrage rapide vous montre comment soumettre un workflow dans le service Microsoft Genomics si votre fichier d’entrée est un fichier BAM unique. Cette rubrique suppose que vous avez déjà installé et exécuté le client `msgen`, et que vous savez comment utiliser Stockage Azure. Si vous avez soumis un workflow à l’aide de l’exemple de données fourni, vous êtes prêt à exécuter ce démarrage rapide. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Configurer : Télécharger votre fichier BAM vers Stockage Azure
Supposons que vous disposiez d’un fichier BAM unique, *reads.bam*, et que vous l’ayez chargé dans votre compte de stockage *myaccount* dans Azure, sous **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span></span>**. Vous disposez de l’URL d’API et de votre clé d’accès. Vos sorties doivent être hébergées sous **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.



## <a name="submit-your-job-to-the-msgen-client"></a>Envoyer votre tâche au client `msgen` 


Voici le jeu minimal d’arguments qu’il vous faudra fournir au client `msgen` ; des sauts de ligne ont été ajoutés pour plus de clarté :

Pour Windows :

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Pour Unix :

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Si vous préférez utiliser un fichier de configuration, voici à quoi il doit ressembler :

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envoyez le fichier `config.txt` avec cet appel : `msgen submit -f config.txt`

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez chargé un fichier BAM dans Stockage Azure et soumis un workflow dans le service Microsoft Genomics, via le client Python `msgen`. Pour plus d’informations sur la soumission du workflow et les autres commandes pouvant être utilisées avec le service Microsoft Genomics, consultez notre [FAQ](frequently-asked-questions-genomics.md). 
