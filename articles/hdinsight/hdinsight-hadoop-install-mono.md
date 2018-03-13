---
title: "Installer ou mettre à jour Mono sur HDInsight - Azure | Microsoft Docs"
description: "Découvrez comment utiliser une version particulière de Mono avec le cluster HDInsight. Mono permet d’exécuter des applications .NET sur des clusters HDInsight sous Linux."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: 555f82ec9351c8c3610ad99a95159cc47d2ee539
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="install-or-update-mono-on-hdinsight"></a>Installation ou mise à jour de Mono sur HDInsight

Apprenez à installer une version spécifique de [Mono](https://www.mono-project.com) sur HDInsight 3.4 ou version ultérieure.

Mono est installé sur HDInsight 3.4 et versions ultérieures et est utilisé pour exécuter des applications .NET. Pour plus d’informations sur la version de Mono fournie avec chaque version d’HDInsight, consultez [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md). Pour installer une autre version sur votre cluster, utilisez l’action de script de ce document. 

## <a name="how-it-works"></a>Fonctionnement

Le script accepte les paramètres suivants :

* __Mono version number__ (Numéro de version de Mono) : la version de Mono à installer. La version doit être disponible à partir de [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

Le script installe les packages Mono suivants :

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>Le script

__Emplacement du script__ : [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Conditions requises__ :

* Le script doit être appliqué sur les __nœuds principaux__ et les __nœuds de travail__.

## <a name="to-use-the-script"></a>Pour utiliser le script

Pour plus d’informations sur l’utilisation de ce script avec HDInsight, consultez le document [Personnalisation de clusters HDInsight basés sur Linux à l'aide d'une action de script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster). Vous pouvez utiliser le script avec le portail Azure, Azure PowerShell ou l’interface de ligne de commande Azure.

Tout en respectant le document d’action de script, utilisez l’URI suivant :

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Pour spécifier la version Mono qui est installée, utilisez le numéro de version dans le champ __Paramètres__. Par exemple, entrez `5.4` pour installer Mono 5.4.

> [!NOTE]
> Lorsque vous configurez HDInsight avec ce script, définissez le script sur le statut __Persisted__ (Persistant). Ce paramètre permet à HDInsight d’appliquer le script aux nœuds de travail ajoutés par les opérations de mise à l’échelle.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à mettre à niveau ou installer une version particulière de Mono sur HDInsight. Pour plus d’informations sur l’utilisation des applications .NET avec Mono sur HDInsight, consultez les documents suivants :

* [Utilisation de .NET pour la diffusion MapReduce sur HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Utilisation de .NET avec Hive et Pig sur HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Développement de solutions C# avec Storm sur HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Migration de solutions .NET vers une version d’HDInsight pour Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Pour plus d’informations sur l’utilisation des actions de script, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md)