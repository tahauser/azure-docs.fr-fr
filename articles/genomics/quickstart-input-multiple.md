---
title: "Démarrage rapide : Soumettre un workflow à l’aide d’entrées multiples | Microsoft Docs"
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
ms.openlocfilehash: d410516f807b7914e15bed1fb93ee58d3e340d1e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Soumettre un workflow à l’aide d’entrées multiples d’un seul exemple

Ce démarrage rapide vous montre comment soumettre un workflow dans le service Microsoft Genomics si votre fichier d’entrée est constitué de plusieurs fichiers FASTQ ou BAM **provenant du même échantillon**. Sachez toutefois qu’il est **impossible** de combiner les fichiers FASTQ et BAM dans la même soumission.

Cette rubrique suppose que vous avez déjà installé et exécuté le client `msgen`, et que vous savez comment utiliser Stockage Azure. Si vous avez soumis un workflow à l’aide de l’exemple de données fourni, vous êtes prêt à exécuter ce démarrage rapide. 


## <a name="multiple-bam-files"></a>Fichiers BAM multiples

### <a name="upload-your-input-files-to-azure-storage"></a>Télécharger vos fichiers d’entrée dans Stockage Azure
Supposons que votre entrée soit constituée de plusieurs fichiers BAM, *reads.bam*, *additional_reads.bam* et *yet_more_reads.bam*, et que vous les ayez téléchargé dans votre compte de stockage *myaccount* dans Azure. Vous disposez de l’URL d’API et de votre clé d’accès. Vos sorties doivent être hébergées sous **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Envoyer votre tâche au client `msgen` 

Vous pouvez envoyer plusieurs fichiers BAM en passant l’ensemble des noms à l’argument --input-blob-name-1. Notez que l’ensemble des fichiers doivent provenir d’un même échantillon ; l’ordre n’importe pas. Vous trouverez ci-dessous des exemples d’envois d’une ligne de commande dans Windows, Unix et à l’aide d’un fichier de configuration. Pour plus de clarté, des sauts de ligne ont été ajoutés :


Pour Windows :

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
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
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
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
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envoyez le fichier `config.txt` avec cet appel : `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Plusieurs fichiers FASTQ appariés

### <a name="upload-your-input-files-to-azure-storage"></a>Télécharger vos fichiers d’entrée dans Stockage Azure
Supposons que vous disposiez de plusieurs fichiers FASTQ dans votre entrée, *reads_1.fq.gz* et *reads_2.fq.gz*, *additional_reads_1.fq.gz* et *additional_reads_2.fq.gz*, ainsi que *yet_more_reads_1.fq.gz* et *yet_more_reads_2.fq.gz*. Vous les avez téléchargés dans votre compte de stockage *myaccount*dans Azure, et vous disposez de l’URL d’API et de votre clé d’accès. Vos sorties doivent être hébergées sous **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Envoyer votre tâche au client `msgen` 

Les fichiers FASTQ ne doivent pas uniquement provenir du même échantillon, ils doivent être traités simultanément.  L’ordre des noms de fichiers importe lorsqu’ils sont passés en tant qu’arguments à --input-blob-name-1 et --input-blob-name-2. 

Vous trouverez ci-dessous des exemples d’envois d’une ligne de commande dans Windows, Unix et à l’aide d’un fichier de configuration. Pour plus de clarté, des sauts de ligne ont été ajoutés :


Pour Windows :

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Si vous préférez utiliser un fichier de configuration, voici à quoi il doit ressembler :

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envoyez le fichier `config.txt` avec cet appel : `msgen submit -f config.txt`

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez chargé plusieurs fichiers BAM ou des fichiers FASTQ appariés dans Stockage Azure et avez soumis un workflow au service Microsoft Genomics via le client Python `msgen`. Pour plus d’informations sur la soumission du workflow et les autres commandes pouvant être utilisées avec le service Microsoft Genomics, consultez notre [FAQ](frequently-asked-questions-genomics.md). 