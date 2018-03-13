---
title: "Prise en main de Hadoop et Hive dans Azure HDInsight | Microsoft Docs"
description: "Découvrez comment créer des clusters HDInsight et interroger des données avec Hive."
keywords: "prise en main de hadoop,hadoop sur linux,démarrage rapide de hadoop,prise en main de hive,démarrage rapide de hive"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 11c0a583ab671b27a5c5a65c4bb36032a8582466
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Didacticiel Hadoop : prise en main de Hadoop dans HDInsight

Apprenez à créer des clusters [Hadoop](http://hadoop.apache.org/) et exécuter des tâches Hive dans HDInsight. [Apache Hive](https://hive.apache.org/) est le composant le plus populaire de l’écosystème Hadoop. HDInsight est actuellement fournie avec [sept types de cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Chaque type de cluster prend en charge un ensemble de composants bien spécifiques. Tous les types de cluster prennent en charge Hive. Pour obtenir la liste des composants pris en charge dans HDInsight, consultez [Nouveautés des versions de cluster Hadoop fournies par HDInsight](../hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>configuration requise
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Abonnement Azure**: pour créer un compte d’essai gratuit d’une durée d’un mois, accédez à [azure.microsoft.com/free](https://azure.microsoft.com/free).

## <a name="create-cluster"></a>Créer un cluster

La plupart des tâches Hadoop sont des tâches de traitements par lots. Vous créez un cluster, exécutez certaines tâches, puis supprimez le cluster. Cette section vous permet de créer un cluster Hadoop dans HDInsight à l’aide d’un [modèle Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Aucune expérience avec le modèle Resource Manager n’est requise pour ce didacticiel. Pour obtenir d’autres méthodes de création de cluster et comprendre les propriétés utilisées dans ce didacticiel, consultez [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

Le modèle Resource Manager utilisé dans ce didacticiel se trouve dans [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). 

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Entrez ou sélectionnez les valeurs suivantes :

    * **Abonnement** : sélectionnez votre abonnement Azure.
    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez-en un.  Un groupe de ressources est un conteneur de composants Azure.  Dans ce cas, le groupe de ressources contient le cluster HDInsight et le compte de stockage Azure dépendant. 
    * **Emplacement** : sélectionnez l’emplacement Azure où vous souhaitez créer votre cluster.  Choisissez un emplacement proche de vous pour obtenir des performances optimales. 
    * **Type du cluster** : pour les besoins de ce didacticiel, sélectionnez **hadoop**.
    * **Nom de cluster** : saisissez le nom du cluster Hadoop. Étant donné que tous les clusters HDInsight partagent le même espace de noms DNS, ce nom doit être unique. Le nom peut comporter jusqu’à 59 caractères, dont des lettres, des chiffres et des traits d’union. Notez que le premier caractère et le dernier caractère du nom ne peuvent pas être des traits d’union.
    * **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est **admin**. Le mot de passe doit comporter au moins 10 caractères et inclure au moins un chiffre, une lettre majuscule, une lettre minuscule et un caractère non alphanumérique (à l’exception de ’ " ` \). 
    * **Nom d’utilisateur SSH et mot de passe** : le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez renommer le nom d’utilisateur SSH.  Le mot de passe SSH de l’utilisateur a les mêmes exigences que le mot de passe de connexion du cluster.   
   
    ![Prise en main de HDInsight sous Linux - Utilisation du modèle Resource Manager sur le portail](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Deploy Hadoop cluster in HDInsigut using the Azure portal and a resource group manager template (Déployer un cluster Hadoop dans HDInsight à l’aide du portail Azure et d’un modèle de gestionnaire de groupes de ressources)").
     
    Certaines propriétés ont été codées en dur dans le modèle.  Vous pouvez configurer ces valeurs à partir du modèle.
    
    Chaque cluster possède une dépendance [compte de stockage Azure](../hdinsight-hadoop-use-blob-storage.md) ou [compte Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Elle est désignée comme compte de stockage par défaut. Le cluster HDInsight et son compte de stockage par défaut doivent figurer dans la même région Azure. La suppression de clusters n’a pas pour effet de supprimer le compte de stockage. 
        
    Pour consulter une présentation de ces propriétés, voir [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Sélectionnez **J’accepte les termes et conditions mentionnés ci-dessus** et **Épingler au tableau de bord**, puis cliquez sur **Acheter**. Vous verrez une nouvelle vignette intitulée **Déploiement du déploiement de modèle** sur le tableau de bord du portail. La création d’un cluster prend environ 20 minutes. 

4. Une fois le cluster créé, la vignette change de légende pour afficher le nom du groupe de ressources que vous avez spécifié. Cliquez sur la vignette pour afficher le cluster et le stockage par défaut pour le cluster répertorié.
   
    ![Prise en main de HDInsight sous Linux - Groupe de ressources](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Groupe de ressources de cluster Azure HDInsight")

4. Pour ouvrir le cluster, cliquez sur son nom.

   ![Prise en main de HDInsight sous Linux - Paramètres du cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "Propriétés de cluster HDInsight")


## <a name="run-hive-queries"></a>Exécuter des requêtes Hive

[Apache Hive](hdinsight-use-hive.md) est le composant le plus populaire utilisé dans HDInsight. Il existe de nombreuses façons d’exécuter des tâches Hive dans HDInsight. Dans ce didacticiel, vous allez utiliser l’affichage Ambari Hive à partir du portail. Pour d’autres méthodes d’envoi de tâches Hive, consultez la page [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md).

1. Pour ouvrir Ambari, dans la capture d’écran précédente, cliquez sur **Tableau de bord du cluster**, puis sur **Tableau de bord du cluster HDInsight**.  Vous pouvez également accéder à **https://&lt;ClusterName>.azurehdinsight.net**, où &lt;ClusterName> désigne le cluster que vous avez créé dans la section précédente.

2. Entrez le nom d’utilisateur Hadoop et le mot de passe que vous avez spécifiés lors de la création du cluster. Le nom d’utilisateur par défaut est **admin**.

3. Ouvrez l’ **affichage Hive** comme illustré dans la capture d’écran suivante.
   
    ![Sélection des vues Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu Affichage Hive dans HDInsight")

4. Dans l’onglet **REQUÊTE**, collez les instructions HiveQL suivantes dans la feuille de calcul :
   
        SHOW TABLES;

    ![Affichages Hive dans HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Éditeur de requête de l’affichage Hive dans HDInsight")
   
   > [!NOTE]
   > Hive requiert l’utilisation d’un point-virgule.       
   > 
   > 

5. Cliquez sur **Exécuter**. Un onglet **RÉSULTATS** apparaît sous l’onglet **REQUÊTE** et affiche des informations sur le travail. 
   
    Une fois la requête terminée, l’onglet **REQUÊTE** affiche les résultats de l’opération. Vous devriez voir une table appelée **hivesampletable**. Cet exemple de table Hive est fourni avec les clusters HDInsight.
   
    ![Affichages Hive dans HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Éditeur de requête de l’affichage Hive dans HDInsight")

6. Répétez les étapes 4 et 5 pour exécuter la requête suivante :
   
        SELECT * FROM hivesampletable;
   
7. Vous pouvez également enregistrer les résultats de la requête. Cliquez sur le bouton de menu à droite et spécifiez si vous souhaitez télécharger les résultats sous la forme d’un fichier CSV ou les stocker dans le compte de stockage associé au cluster.

    ![Enregistrer le résultat de la requête Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Enregistrer le résultat de la requête Hive")



Une fois que vous avez terminé une tâche Hive, vous pouvez [exporter les résultats dans une base de données SQL Azure ou SQL Server](apache-hadoop-use-sqoop-mac-linux.md). Vous pouvez également [visualiser les résultats à l’aide d’Excel](apache-hadoop-connect-excel-power-query.md). Pour plus d’informations sur l’utilisation de Hive dans HDInsight, consultez [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Nettoyage du didacticiel
Après avoir terminé ce didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. 

> [!NOTE]
> À l’aide [d’Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md), vous pouvez créer des clusters HDInsight à la demande et configurer un paramètre TimeToLive pour supprimer automatiquement les clusters. 
> 
> 

**Pour supprimer le cluster et/ou le compte de stockage par défaut**

1. Revenez à l’onglet du navigateur dans lequel se trouve le portail Azure. Vous devez être sur la page de vue d’ensemble du cluster. Vous pouvez cliquer sur **Supprimer** si vous souhaitez uniquement supprimer le cluster, mais conserver le compte de stockage par défaut :

    ![HDInsight suppression du cluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Suppression du cluster HDInsight")
1. Sur la droite, cliquez sur le nom de groupe de ressources (en surbrillance dans la capture d’écran précédente) pour ouvrir la page de groupe de ressources.
2. Cliquez sur **Supprimer le groupe de ressources** pour supprimer le groupe de ressources contenant le cluster et le compte de stockage par défaut. Vous pouvez également cliquer sur le nom du cluster dans la vignette **Ressources**, puis sur **Supprimer**. Notez que la suppression du groupe de ressources aura pour effet de supprimer le compte de stockage. Si vous souhaitez conserver le compte de stockage, choisissez de supprimer uniquement le cluster.

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer un cluster HDInsight Linux à l’aide d’un modèle Resource Manager et à effectuer des requêtes Hive de base.

Pour en savoir plus sur l’analyse des données avec HDInsight, consultez les articles suivants :

* Pour en savoir plus sur l’utilisation de Hive avec HDInsight, y compris comment exécuter des requêtes Hive à partir de Visual Studio, consultez la page [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md).
* Pour en savoir plus sur Pig, un langage utilisé pour transformer les données, consultez la page [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md).
* Pour en savoir plus sur MapReduce, un moyen d’écrire des programmes pour traiter les données sur Hadoop, consultez la page [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md).
* Pour en savoir plus sur l’utilisation des outils HDInsight pour Visual Studio pour analyser les données sur HDInsight, consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](apache-hadoop-visual-studio-tools-get-started.md).

Si vous êtes prêt à utiliser vos propres données et que vous avez besoin d’en savoir plus sur la façon dont HDInsight stocke les données ou sur l’ajout de données dans HDInsight, consultez les articles suivants :

* Pour plus d’informations sur la façon dont HDInsight utilise le stockage Azure, consultez la page [Use Azure Storage with HDInsight](../hdinsight-hadoop-use-blob-storage.md) (Utilisation du stockage Azure avec HDInsight).
* Pour plus d’informations sur le téléchargement de données dans HDInsight, consultez la page [Téléchargement de données dans HDInsight](../hdinsight-upload-data.md).

Si vous voulez en savoir plus sur la création ou la gestion d’un cluster HDInsight, consultez les articles suivants :

* Pour en savoir plus sur la gestion de votre cluster HDInsight Linux, consultez la page [Gestion des clusters HDInsight à l’aide d’Ambari](../hdinsight-hadoop-manage-ambari.md).
* Pour en savoir plus sur les options que vous pouvez sélectionner pendant la création d’un cluster HDInsight, consultez la page [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Si vous maîtrisez Linux et Hadoop, mais que vous souhaitez connaître les spécificités de Hadoop sur HDInsight, consultez la page [Utilisation de HDInsight sur Linux](../hdinsight-hadoop-linux-information.md). Cet article vous fournit les informations suivantes :
  
  * les URL correspondant aux services hébergés sur le cluster, tels qu'Ambari et WebHCat
  * l'emplacement des fichiers Hadoop et des exemples sur le système de fichiers local
  * l'utilisation de stockage d'Azure Storage (WASB) au lieu de HDFS, en tant que stockage de données par défaut

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


