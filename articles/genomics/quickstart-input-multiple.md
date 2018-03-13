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
ms.date: 02/05/2018
ms.openlocfilehash: 7aeb4d5ad939cefcf8300b78b4afcc9d91ca0624
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Soumettre un workflow à l’aide d’entrées multiples d’un seul exemple

Ce démarrage rapide vous montre comment soumettre un workflow dans le service Microsoft Genomics si votre fichier d’entrée est constitué de plusieurs fichiers FASTQ ou BAM **provenant du même échantillon**. Par exemple, si vous avez exécuté le **même échantillon** dans plusieurs couloirs sur le séquenceur, celui-ci peut sortir une paire de fichiers FASTQ pour chaque couloir. Au lieu d’effectuer une concaténation de ces fichiers FASTQ avant l’alignement et l’appel des variants, vous pouvez directement soumettre toutes ces entrées au client `msgen`. La sortie depuis le client `msgen` serait un **ensemble unique** de fichiers, y compris des fichiers .bam, .bai et .vcf. 

Sachez toutefois qu’il est **impossible** de combiner les fichiers FASTQ et BAM dans la même soumission. En outre, vous **ne pouvez pas**  envoyer plusieurs fichiers FASTQ ou BAM par plusieurs personnes. 

Cette article suppose que vous avez déjà installé et exécuté le client `msgen`, et que vous savez comment utiliser Stockage Azure. Si vous avez soumis un workflow à l’aide de l’exemple de données fourni, vous êtes prêt à exécuter ce démarrage rapide. 


## <a name="multiple-bam-files"></a>Fichiers BAM multiples

### <a name="upload-your-input-files-to-azure-storage"></a>Télécharger vos fichiers d’entrée dans Stockage Azure
Supposons que votre entrée soit constituée de plusieurs fichiers BAM, *reads.bam*, *additional_reads.bam* et *yet_more_reads.bam*, et que vous les ayez téléchargé dans votre compte de stockage *myaccount* dans Azure. Vous disposez de l’URL d’API et de votre clé d’accès. Vos sorties doivent être hébergées sous **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Envoyer votre tâche au client `msgen` 

Vous pouvez envoyer plusieurs fichiers BAM en passant l’ensemble des noms à l’argument --input-blob-name-1. Notez que l’ensemble des fichiers doivent provenir d’un même échantillon ; l’ordre n’importe pas. La section ci-dessous donne des exemples d’envois d’une ligne de commande dans Windows, Unix et à l’aide d’un fichier de configuration. Pour plus de clarté, des sauts de ligne ont été ajoutés :


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

La section ci-dessous donne des exemples d’envois d’une ligne de commande dans Windows, Unix et à l’aide d’un fichier de configuration. Pour plus de clarté, des sauts de ligne ont été ajoutés :


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
Dans cet article, vous avez chargé plusieurs fichiers BAM ou des fichiers FASTQ appariés dans Stockage Azure et avez soumis un workflow au service Microsoft Genomics via le client Python `msgen`. Pour plus d’informations sur la soumission du workflow et les autres commandes pouvant être utilisées avec le service Microsoft Genomics, consultez la [FAQ](frequently-asked-questions-genomics.md). 