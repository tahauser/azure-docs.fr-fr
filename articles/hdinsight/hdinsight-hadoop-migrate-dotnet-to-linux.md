---
title: "Utilisation de .NET avec Hadoop MapReduce sur HDInsight sous Linux – Azure | Microsoft Docs"
description: "Découvrez comment utiliser des applications .NET pour la diffusion MapReduce sur HDInsight sous Linux."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: bff53af8f5c0b74cf0f69ba474d62ecdb7e20ce1
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migration de solutions .NET pour HDInsight sous Windows vers HDInsight sous Linux

Les clusters HDInsight sous Linux utilisent [Mono (https://mono-project.com)](https://mono-project.com) pour exécuter des applications .NET. Mono vous permet d’utiliser des composants .NET tels que les applications MapReduce avec HDInsight sous Linux. Dans ce document, découvrez comment migrer des solutions .NET créées pour les clusters HDInsight sous Windows de manière à ce qu’elles soient compatibles avec Mono sur HDInsight sous Linux.

## <a name="mono-compatibility-with-net"></a>Compatibilité de Mono avec .NET

La version 4.2.1 de Mono est incluse dans la version 3.6 de HDInsight. Pour plus d’informations sur la version de Mono fournie avec HDInsight, consultez [Versions des composants HDInsight](hdinsight-component-versioning.md). Pour installer une version particulière de Mono, consultez le document [Installation ou mise à jour de Mono](hdinsight-hadoop-install-mono.md).

Pour plus d’informations sur la compatibilité entre Mono et .NET, consultez le document [Compatibilité avec Mono (http://www.mono-project.com/docs/about-mono/compatibility/)](http://www.mono-project.com/docs/about-mono/compatibility/).

> [!IMPORTANT]
> L’infrastructure SCP.NET est compatible avec Mono. Pour plus d’informations sur l’utilisation de SCP.NET avec Mono, consultez la rubrique [Utiliser Visual Studio pour développer des topologies C# pour Apache Storm sur HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Analyse de portabilité automatisée

L’[Analyseur de portabilité .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) peut servir à générer un rapport des incompatibilités entre votre application et Mono. Utilisez la procédure suivante pour configurer l’analyseur pour vérifier si votre application est adaptée à la portabilité Mono :

1. Installez l’[Analyseur de portabilité .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Pendant l’installation, sélectionnez la version de Visual Studio à utiliser.

2. Dans Visual Studio 2015, sélectionnez __Analyser__ > __Paramètres d’analyseur de portabilité__et vérifiez que l’option __4.5__ est cochée dans la section __Mono__.

    ![Option 4.5 cochée dans la section Mono des paramètres de l’analyseur](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Cliquez sur __OK__ pour enregistrer la configuration.

3. Sélectionnez __Analyser__ > __Analyser la portabilité d’Assembly__. Sélectionnez l’assembly qui contient votre solution, puis sélectionnez __Ouvrir__ pour commencer l’analyse.

4. Une fois l’analyse terminée, sélectionnez __Analyser__ > __Afficher les rapports d’analyse__. Dans __Résultats de l’analyse de portabilité__, sélectionnez __Ouvrir le rapport__ pour ouvrir un rapport.

    ![Boîte de dialogue des résultats de l’analyseur de portabilité](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> L’analyseur ne peut pas détecter tous les problèmes liés à votre solution. Par exemple, le chemin d’accès `c:\temp\file.txt` est considéré comme correct si Mono fonctionne sous Windows. Il n’est pas valide sur une plateforme Linux.

## <a name="manual-portability-analysis"></a>Analyse de portabilité manuelle

Procédez à une vérification manuelle de votre code en utilisant les informations contenues dans le document [Portabilité des applications (http://www.mono-project.com/docs/getting-started/application-portability/)](http://www.mono-project.com/docs/getting-started/application-portability/).

## <a name="modify-and-build"></a>Modifier et générer

Vous pouvez continuer à utiliser Visual Studio pour générer vos solutions .NET pour HDInsight. Toutefois, vous devez vous assurer que le projet est configuré pour l’utilisation de .NET Framework 4.5.

## <a name="deploy-and-test"></a>Déploiement et test

Après avoir modifié votre solution avec les recommandations de l’analyseur de portabilité .NET ou à partir d’une analyse manuelle, vous devez la tester avec HDInsight. Le test de la solution sur un cluster HDInsight sous Linux peut révéler des problèmes subtils qui doivent être corrigés. Nous vous recommandons d’activer la fonction de journalisation supplémentaire dans votre application lors de son test.

Pour plus d’informations sur l’accès aux journaux, consultez les documents suivants :

* [Accéder aux journaux des applications YARN dans HDInsight sous Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de C# avec MapReduce dans HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Utilisation des fonctions C# définies par l’utilisateur avec Hive et Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Développement de topologies C# pour Storm sur HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)