---
title: Opérationnaliser R Server sur HDInsight - Azure | Microsoft Docs
description: Découvrez comment opérationnaliser R Server dans Azure HDInsight.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 93957b7ee10527039bf2e96cc5470420cdef0651
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>Opérationnaliser un cluster R Server sur Azure HDInsight

Après avoir utilisé un cluster R Server dans HDInsight pour effectuer votre modélisation de données, vous pouvez opérationnaliser le modèle afin d’élaborer des prédictions. Cet article explique comment accomplir cette tâche.

## <a name="prerequisites"></a>Prérequis


* **Un cluster R Server sur HDInsight** : pour connaître la marche à suivre pour sa création, consultez [Prise en main de R Server sur HDInsight](r-server-get-started.md).

* **Client Secure Shell (SSH)** : un client SSH est utilisé pour se connecter à distance au cluster HDInsight et exécuter des commandes directement sur celui-ci. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>Opérationnaliser le cluster R Server avec une configuration complète

1. Utilisez SSH au sein du nœud de périmètre.  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Pour savoir comment utiliser SSH avec Azure HDInsight, consultez [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Basculez du répertoire vers la version appropriée et exécutez la commande sudo sur la DLL dotnet : 

    - Pour Microsoft R Server 9.1 :

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Pour Microsoft R Server 9.0 :

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Les options disponibles s’affichent. Choisissez la première option, comme illustré dans la capture d’écran suivante, pour **configurer R Server pour l’opérationnalisation**.

    ![opérationnalisation complète](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Vous devez maintenant choisir la configuration souhaitée pour l’opérationnalisation du cluster R Server. Sélectionnez la première option en entrant **A**.

    ![opérationnalisation complète](./media/r-server-operationalize/admin-util-one-box-2.png)

5. Quand vous y êtes invité, saisissez et saisissez à nouveau le mot de passe d’un utilisateur administrateur local.

6. Vous devriez voir des sorties suggérant que l’opération a réussi. Vous êtes également invité à sélectionner une autre option dans le menu. Entrez E pour revenir au menu principal.

    ![opérationnalisation complète](./media/r-server-operationalize/admin-util-one-box-3.png)

7. Vous pouvez éventuellement effectuer des vérifications de diagnostic en exécutant un test de diagnostic, comme suit :

    a. Dans le menu principal, sélectionnez **6** pour exécuter les tests de diagnostic.

    ![opérationnalisation complète](./media/r-server-operationalize/diagnostic-1.png)

    b. Dans le menu Diagnostic Tests (Tests de diagnostic), sélectionnez **A**. Quand vous y êtes invité, entrez le mot de passe que vous avez fourni pour l’utilisateur administrateur local.

    ![opérationnalisation complète](./media/r-server-operationalize/diagnostic-2.png)

    c. Vérifiez que la sortie indique que l’intégrité globale est satisfaisante, comme illustré ci-dessous.

    ![opérationnalisation complète](./media/r-server-operationalize/diagnostic-3.png)

    d. Dans les options de menu présentées, entrez **E** pour revenir au menu principal, puis **8** pour quitter l’utilitaire d’administration.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Retards importants lors de l’utilisation d’un service web sur Spark

Si vous rencontrez d’importants retards lorsque vous utilisez un service web créé avec des fonctions mrsdeploy dans un contexte d’exécution Spark, vous devrez peut-être ajouter des dossiers manquants. L’application Spark appartient à un utilisateur nommé « *rserve2* » à chaque fois qu’elle est appelée depuis un service web à l’aide de fonctions mrsdeploy. Pour contourner ce problème :

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


À ce stade, la configuration de l’opérationnalisation est terminée. Vous pouvez désormais utiliser le package `mrsdeploy` sur votre RClient pour vous connecter à l’opérationnalisation sur le nœud de périphérie et commencer à utiliser ses fonctionnalités, telles que l’[exécution à distance](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) et les [services web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Selon que votre cluster est configuré sur un réseau virtuel ou non, vous devrez peut-être configurer le tunneling de réacheminement du port via une connexion SSH. Les sections suivantes expliquent comment configurer ce tunnel.

### <a name="r-server-cluster-on-virtual-network"></a>Cluster R Server sur un réseau virtuel

Assurez-vous que vous autorisez le trafic via le port 12800 vers le nœud de périmètre. De cette façon, vous pouvez utiliser le nœud de périmètre pour vous connecter à la fonctionnalité d’opérationnalisation.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Si `remoteLogin()` ne peut pas se connecter au nœud de périmètre, mais si vous pouvez exécuter SSH sur ce dernier, vous devez alors vérifier si la règle permettant d’autoriser le trafic sur le port 12800 a été configurée correctement ou non. Si vous continuez à rencontrer ce problème, configurez le tunneling de réacheminement du port via SSH pour le contourner. Pour connaître la marche à suivre, consultez la section suivante :

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>Cluster R Server non configuré sur un réseau virtuel

Si votre cluster n’est pas configuré sur un réseau virtuel ou si vous rencontrez des problèmes de connectivité via un réseau virtuel, vous pouvez utiliser le tunneling de réacheminement du port SSH :

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Une fois votre session SSH active, le trafic à partir du port 12800 de votre machine est transféré vers le port du nœud de périmètre 12800, via une session SSH. Vérifiez que vous utilisez `127.0.0.1:12800` dans votre méthode `remoteLogin()`. Cela vous permet de vous connecter à l’opérationnalisation du nœud de périphérie via le réacheminement de port.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Mise à l’échelle des nœuds de calcul opérationnalisés sur les nœuds Worker HDInsight

Pour mettre à l’échelle les nœuds de calcul, vous devez d’abord désactiver les nœuds Worker, puis configurer les nœuds de calcul sur les nœuds de travail désactivés.

### <a name="step-1-decommission-the-worker-nodes"></a>Étape 1 : Désactiver les nœuds Worker

Le cluster R Server n’est pas managé via YARN. Si les nœuds Worker ne sont pas désactivés, le gestionnaire de ressources YARN ne fonctionne pas comme prévu, car il n’a pas connaissance des ressources prises en charge par le serveur. Afin d’éviter ce problème, nous vous recommandons de désactiver les nœuds Worker avant d’augmenter la taille des nœuds de calcul.

Pour désactiver les nœuds Worker, procédez comme suit :

1. Connectez-vous à la console Ambari du cluster et cliquez sur l’onglet **Hôtes**.

2. Sélectionnez les nœuds Worker (à désactiver).

3. Cliquez sur **Actions** > **Hôtes sélectionnés** > **Hôtes** > **Activer le mode de maintenance**. Par exemple, dans l’image suivante, nous avons sélectionné wn3 et wn4 pour la désactivation.  

   ![Désactiver les nœuds Worker](./media/r-server-operationalize/get-started-operationalization.png)  

* Sélectionnez **Actions** > **Hôtes sélectionnés** > **DataNodes** > cliquez sur **Désactiver**.
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **NodeManagers** > cliquez sur **Désactiver**.
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **DataNodes** > cliquez sur **Arrêter**.
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **NodeManagers** > cliquez sur **Arrêter**.
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **Hôtes** > cliquez sur **Arrêter tous les composants**.
* Désélectionnez les nœuds Worker et sélectionnez les nœuds principaux.
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **Hôtes** > **Redémarrer tous les composants**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Étape 2 : Configurer les nœuds de calcul sur chaque nœud Worker désactivé

1. Utilisez SSH dans chaque nœud Worker désactivé.

2. Exécutez l’utilitaire d’administration en utilisant la DLL pertinente pour votre cluster R Server. Pour R Server 9.1, exécutez la commande suivante :

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Entrez **1** pour **configurer R Server pour l’opérationnalisation**.

4. Entrez **C** pour sélectionner l’option `C. Compute node`. Cette opération permet de configurer un nœud de calcul sur le nœud Worker.

5. Quittez l’utilitaire d’administration.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Étape 3 : Ajouter des détails sur les nœuds de calcul sur le nœud web

Une fois que tous les nœuds Worker désactivés sont configurés pour exécuter les nœuds de calcul, revenez au nœud de périphérie et ajoutez les adresses IP des nœuds Worker désactivés dans la configuration du nœud web R Server :

1. Utilisez SSH au sein du nœud de périmètre.

2. Exécutez `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

3. Recherchez la section « Uris » et ajoutez l’adresse IP du nœud Worker ainsi que les détails du port.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Étapes suivantes

* [Gérer un cluster R Server sur HDInsight](r-server-hdinsight-manage.md)
* [Options de contexte de calcul pour R Server sur HDInsight](r-server-compute-contexts.md)
* [Solutions de stockage Azure pour R Server sur HDInsight](r-server-storage.md)