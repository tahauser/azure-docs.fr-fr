---
title: Outils d’ingestion de données de la machine virtuelle DSVM - Azure | Microsoft Docs
description: Outils d’ingestion de données de la machine virtuelle DSVM
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 8526e949aee2935824a03a0972d9e45c71d6601b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Outils d’ingestion de données de la machine virtuelle DSVM

L’une des premières étapes techniques dans un projet de science de données ou d’intelligence artificielle consiste à identifier les jeux de données à utiliser, puis à les importer dans votre environnement analytique. La machine virtuelle DSVM (Data Science Virtual Machine) fournit des outils et des bibliothèques pour importer des données de différentes sources dans un stockage de données analytiques sur la machine DSVM ou dans une plateforme de données sur le cloud ou localement. 

Voici quelques outils de déplacement de données que nous vous avons fournis sur la machine virtuelle DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil permettant de copier des données d’objets blob du stockage Azure vers Azure Data Lake Store. Il peut également copier des données entre deux comptes Azure Data Lake Store.      |
| Versions DSVM prises en charge      | Windows      |
| Utilisations classiques      | Importation de plusieurs objets blob du stockage Azure vers Azure Data Lake Store.      |
|  Comment l’utiliser/l’exécuter ?    |   Ouvrez une invite de commandes et tapez `adlcopy` pour obtenir de l’aide.    |
| Liens vers des exemples      | [Utilisation d’AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Outils connexes sur la machine virtuelle DSVM      | AzCopy, ligne de commande Azure     |

## <a name="azure-command-line"></a>Ligne de commande Azure

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil de gestion pour Azure. Il contient également des verbes de commande pour déplacer des données à partir de plateformes de données Azure, telles que les objets blob de stockage Azure, Azure Data Lake Storage.     |
| Versions DSVM prises en charge      | Windows, Linux     |
| Utilisations classiques      | Importation/exportation de données vers et depuis le stockage Azure, Azure Data Lake Store      |
|  Comment l’utiliser/l’exécuter ?    |   Ouvrez une invite de commandes et tapez `az` pour obtenir de l’aide.    |
| Liens vers des exemples      | [Utilisation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure)     |
| Outils connexes sur la machine virtuelle DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil permettant de copier des données vers et depuis des fichiers locaux, des objets blob, des fichiers et des tables de stockage Azure.      |
| Versions DSVM prises en charge      | Windows      |
| Utilisations classiques      | Copie de fichiers vers le stockage d’objets blob, copie d’objets blob entre plusieurs comptes.      |
|  Comment l’utiliser/l’exécuter ?    |   Ouvrez une invite de commandes et tapez `azcopy` pour obtenir de l’aide.    |
| Liens vers des exemples      | [AzCopy sur Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Outils connexes sur la machine virtuelle DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Outil de migration de données Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil permettant d’importer des données dans Azure Cosmos DB à partir de différentes sources, notamment des fichiers JSON, des fichiers CSV, SQL, MongoDB, Stockage Table Azure, Amazon DynamoDB et les collections d’API SQL Azure Cosmos DB.      |
| Versions DSVM prises en charge      | Windows      |
| Utilisations classiques      | Importation de fichiers à partir d’une machine virtuelle vers CosmosDB, importation de données à partir du stockage Table Azure vers CosmosDB ou importation de données à partir d’une base de données SQL Server vers CosmosDB.     |
|  Comment l’utiliser/l’exécuter ?    |   Pour utiliser la version en ligne de commande, ouvrez une invite de commandes et tapez `dt`. Pour utiliser l’outil GUI, ouvrez une invite de commandes et tapez `dtui`.    |
| Liens vers des exemples      | [Importation de données dans CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Outils connexes sur la machine virtuelle DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil SQL Server permettant de copier des données entre SQL Server et un fichier de données.      |
| Versions DSVM prises en charge      | Windows      |
| Utilisations classiques      | Importation d’un fichier CSV dans une table SQL Server, exportation d’une table SQL Server dans un fichier.      |
|  Comment l’utiliser/l’exécuter ?    |   Ouvrez une invite de commandes et tapez `bcp` pour obtenir de l’aide.    |
| Liens vers des exemples      | [Utilitaire de copie en bloc](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Outils connexes sur la machine virtuelle DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Un outil pour monter un conteneur d’objets blob Azure dans le système de fichiers Linux.      |
| Versions DSVM prises en charge      | Linux      |
| Utilisations classiques      | Lecture et écriture dans des objets blob dans un conteneur      |
|  Comment l’utiliser/l’exécuter ?    |   Exécutez _blobfuse_ sur un terminal.    |
| Liens vers des exemples      | [blobfuse sur GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Outils connexes sur la machine virtuelle DSVM      | Ligne de commande Azure      |


## <a name="microsoft-data-management-gateway"></a>Passerelle de gestion des données de Microsoft

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil permettant de connecter des sources de données locales à des services cloud en vue de leur consommation.      |
| Versions DSVM prises en charge      | Windows      |
| Utilisations classiques      | Connexion d’une machine virtuelle à une source de données locale      |
|  Comment l’utiliser/l’exécuter ?    |   Démarrez « Passerelle de gestion des données Microsoft » à partir du menu Démarrer.    |
| Liens vers des exemples      | [Passerelle de gestion de données](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Outils connexes sur la machine virtuelle DSVM      | AzCopy, AdlCopy, bcp    |
