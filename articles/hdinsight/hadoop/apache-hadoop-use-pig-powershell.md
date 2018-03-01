---
title: "Utiliser Hadoop Pig avec PowerShell dans HDInsight - Azure | Documents Microsoft"
description: "Découvrez comment soumettre des tâches Pig vers un cluster Hadoop sur HDInsight à l’aide d’Azure Powershell."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/16/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 7fa50e206c86b307f7e2a90ff0938571b64f41bb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Utilisation d’Azure PowerShell pour exécuter des tâches Pig avec HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell pour soumettre des tâches Pig à un Hadoop sur un cluster HDInsight. Pig vous permet d’écrire des tâches MapReduce en utilisant un langage (Pig Latin) qui modélise les transformations de données, plutôt que de mapper et de réduire les fonctions.

> [!NOTE]
> Ce document ne fournit pas une description détaillée de ce que font les instructions Pig Latin utilisées dans les exemples. Pour plus d’informations sur le langage Pig Latin utilisé dans cet exemple, consultez la rubrique [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Configuration requise

* **Un cluster Azure HDInsight**

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Un poste de travail sur lequel est installé Azure PowerShell**.

## <a id="powershell"></a>Exécuter une tâche Pig

Azure PowerShell propose des *applets de commande* qui vous permettent d'exécuter à distance des tâches Pig sur HDInsight. En interne, PowerShell utilise des appels REST vers [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) en cours d’exécution sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des tâches Pig sur un cluster HDInsight à distance :

* **Login-AzureRmAccount** : authentifie Azure PowerShell sur votre abonnement Azure.
* **New-AzureRmHDInsightPigJobDefinition** : crée une *définition de tâche* à l’aide des instructions Pig Latin spécifiées.
* **Start-AzureRmHDInsightJob** : envoie la définition du travail à HDInsight et démarre la tâche. Un objet *job* est retourné.
* **Wait-AzureRmHDInsightJob**: utilise l’objet de la tâche pour vérifier le statut de la tâche. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.
* **Get-AzureRmHDInsightJobOutput** : permet de récupérer la sortie de la tâche.

Les étapes suivantes montrent comment utiliser ces cmdlets pour exécuter une tâche sur votre cluster HDInsight.

1. À l’aide d’un éditeur, enregistrez le code suivant sous **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Ouvrez une invite de commandes Windows PowerShell. Accédez au répertoire du fichier **pigjob.ps1** , puis utilisez la commande suivante pour exécuter le script :

        .\pigjob.ps1

    Vous êtes invité à vous connecter à votre abonnement Azure. Ensuite, vous devez fournir le nom et le mot de passe du compte HTTPs/Admin pour le cluster HDInsight.

2. Une fois la tâche terminée, un texte similaire à celui présenté ci-dessous doit s’afficher :

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est retournée lorsque la tâche est terminée, affichez les journaux d’erreurs. Pour afficher les informations d’erreur pour cette tâche, ajoutez la commande suivante à la fin du fichier **pigjob.ps1** , enregistrez-le et exécutez-le à nouveau.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Cette applet de commande retourne les informations écrites sur STDERR lors de l’exécution de la tâche.

## <a id="summary"></a>Résumé
Comme vous pouvez le voir, Azure PowerShell offre un moyen facile d’exécuter des tâches Pig sur un cluster HDInsight, de surveiller l’état de la tâche et de récupérer la sortie.

## <a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur Pig dans HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
