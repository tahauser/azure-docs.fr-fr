---
title: "Résolution de problèmes YARN à l’aide d’Azure HDInsight | Microsoft Docs"
description: "Obtenez les réponses aux questions courantes sur l’utilisation d’Apache Hadoop YARN et d’Azure HDInsight."
keywords: "Azure HDInsight, YARN, FAQ, guide de dépannage, questions courantes"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: fbcb4807aa7f6a3d6227cd630c77714c4d2834b3
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>Résoudre les problèmes YARN à l’aide d’Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles Apache Hadoop YARN dans Apache Ambari, et leur résolution.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Comment créer une file d’attente YARN dans un cluster ?


### <a name="resolution-steps"></a>Étapes de résolution 

Effectuez les étapes suivantes via Ambari pour créer une file d’attente YARN, puis équilibrer l’allocation de capacité entre toutes les files d’attente. 

Dans cet exemple, la capacité de deux files d’attente existantes (**default** et **thriftsvr**) est modifiée de 50 % à 25 %, ce qui permet à la nouvelle file d’attente (spark) de bénéficier d’une capacité de 50 %.
| File d'attente | Capacity | Capacité maximale |
| --- | --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Cliquez sur l’icône **Vues Ambari**, puis sur l’icône de grille. Sélectionnez ensuite **YARN Queue Manager** (Gestionnaire de files d’attente YARN).

    ![Cliquer sur l’icône Vues Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Sélectionnez la file d’attente **default** (par défaut).

    ![Sélectionner la file d’attente default](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Modifiez la valeur **Capacity** (Capacité) de 50 % à 25 % pour la file d’attente **default** (par défaut) et faites de **même** pour la file d’attente **thriftsvr** .

    ![Définir la capacité sur 25 % pour les files d’attente default et thriftsvr](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Cliquez sur **Add Queue** (Ajouter une file d’attente) pour créer une file d’attente.

    ![Sélectionner Add Queue](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Nommez la nouvelle file d’attente.

    ![Indiquer Spark comme nom de file d’attente](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Conservez la valeur de **capacité** de 50 %, puis cliquez sur le bouton **Actions**.

    ![Cliquer sur le bouton Actions](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Sélectionnez **Save and Refresh Queues** (Enregistrer et actualiser les files d’attente).

    ![Sélectionner Save and Refresh Queues](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Ces modifications se répercutent immédiatement sur l’interface utilisateur du planificateur YARN.

### <a name="additional-reading"></a>Documentation supplémentaire

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Comment télécharger les journaux YARN à partir d’un cluster ?


### <a name="resolution-steps"></a>Étapes de résolution 

1. Connectez-vous au cluster HDInsight à l’aide d’un client Secure Shell (SSH). Pour plus d’informations, consultez la section [Documentation supplémentaire](#additional-reading-2).

2. Pour répertorier les ID de toutes les applications YARN en cours d’exécution, exécutez la commande suivante :

    ```apache
    yarn top
    ```
    Les ID sont répertoriés dans la colonne **APPLICATIONID**. Vous pouvez télécharger les journaux depuis la colonne **APPLICATIONID**.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

3. Pour télécharger les journaux de conteneurs YARN pour tous les processus maîtres d’application, utilisez la commande suivante :
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Cette commande crée un fichier journal nommé amlogs.txt. 

4. Pour télécharger les journaux de conteneurs YARN uniquement pour les processus maîtres d’application les plus récents, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Cette commande crée un fichier journal nommé latestamlogs.txt. 

4. Pour télécharger les journaux de conteneurs YARN pour les deux premiers processus maîtres d’application, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Cette commande crée un fichier journal nommé first2amlogs.txt. 

5. Pour télécharger tous les journaux de conteneurs YARN, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Cette commande crée un fichier journal nommé logs.txt. 

6. Pour télécharger le journal de conteneur YARN pour un conteneur spécifique, utilisez la commande suivante :

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Cette commande crée un fichier journal nommé containerlogs.txt.

### <a name="additional-reading-2"></a>Documentation supplémentaire

- [Se connecter à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN concepts and applications](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/) (Concepts et applications Apache Hadoop Yarn, en anglais)


### <a name="see-also"></a>Voir aussi
[Résoudre des problèmes à l’aide d’Azure HDInsight](hdinsight-troubleshoot-guide.md)







