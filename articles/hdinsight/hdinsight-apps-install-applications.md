---
title: Installer des applications Hadoop tierces sur Azure HDInsight | Microsoft Docs
description: "Découvrez comment installer des applications Hadoop tierces sur Azure HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: cfaad24e7bf1c38f3be1e13c88fc932be0bd502c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Installer des applications Hadoop tierces sur Azure HDInsight

Découvrez comment installer une application Hadoop tierce sur Azure HDInsight. Pour des instructions d’installation de votre propre application, consultez la page [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même.  

La liste suivante affiche les applications publiées :

* **AtScale Intelligence Platform** fait de votre cluster HDInsight un serveur OLAP de montée en puissance parallèle. Cette application vous permet d’interroger des milliards de lignes de données de manière interactive l’aide d’outils décisionnels de Microsoft Excel, Power BI, Tableau ou QlikView.
* **Cask CDAP for HDInsight** fournit la première plateforme intégrée d’intégration dédiée au Big Data, qui réduit de 80 % le délai de production des applications et des Data Lakes. Cette application prend uniquement en charge les clusters Standard HBase 3.4.
* **DATAIKU DDS on HDInsight** permet aux professionnels des données de concevoir des prototypes, de créer et de déployer des services très spécifiques qui transforment des données brutes en prévisions métiers pertinentes.
* **H2O Artificial Intelligence for HDInsight (Beta)** H2O Sparkling Water prend en charge les algorithmes distribués suivants : GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection et Autoencoders.
* **Kyligence Analytics Platform** Kyligence Analytics Platform (KAP) est un entrepôt de données d’entreprise s’appuyant sur Apache Kylin et Apache Hadoop. Il permet d’obtenir une latence de requête inférieure à une seconde sur des jeux de données extrêmement volumineux, et il simplifie l’analyse des données pour les utilisateurs professionnels et les analystes. 
* **Paxata Self-service Data Preparation**
* **Spark Job Server for KNIME Spark Executor** Spark Job Server for KNIME Spark Executor permet de connecter la plateforme d’analyse KNIME aux clusters HDInsight.
* **Streamsets Data Collector for HDnsight** fournit un environnement de développement intégré complet qui prend en charge la conception, le test, le déploiement et la gestion des pipelines d’ingestion universels qui maillent les flux et groupent les données, tout en incluant diverses transformations intégrées au flux, sans aucune écriture de code personnalisé. 
* **[Trifacta](http://www.trifacta.com/)** permet aux ingénieurs et aux analystes de données d’explorer plus efficacement et de préparer les différents types de données d’aujourd’hui en utilisant l’apprentissage automatique pour fournir une expérience utilisateur, un flux de travail et une architecture exceptionnels.
* **WANdisco Fusion HDI App** permet une connectivité continue homogène avec les données, car elles changent en fonction de votre emplacement. Cette application vous permet d’accéder à vos données à tout moment et partout sans temps d’arrêt ni interruption.

Les instructions fournies dans cet article utilisent le portail Azure. Vous pouvez également exporter le modèle Azure Resource Manager à partir du portail ou obtenir une copie de ce modèle auprès des fournisseurs et utiliser Azure PowerShell et l’interface de ligne de commande Azure pour déployer le modèle.  Consultez [Créer des clusters Hadoop dans HDInsight avec des modèles Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Prérequis
Pour installer des applications HDInsight sur un cluster HDInsight existant, vous devez disposer d’un cluster HDInsight. Pour en créer un, consultez [Créer des clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Vous pouvez également installer des applications HDInsight lorsque vous créez un cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Installer des applications dans des clusters existants
La procédure suivante montre comment installer des applications HDInsight dans un cluster HDInsight existant.

**Installer une application HDInsight**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche.
3. Cliquez sur un cluster HDInsight.  Si vous ne disposez pas d’un tel cluster, vous devez d’abord en créer un.  Consultez la page [Créer des clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Cliquez sur **Applications**, sous la catégorie **Configurations**. Une liste des applications installées s’affiche. Si vous ne trouvez aucune application, cela signifie qu'il n’existe aucune application pour cette version du cluster HDInsight.
   
    ![Applications HDInsight - menu du portail](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Dans le menu, cliquez sur **Ajouter**. Une liste des applications HDInsight existantes s’affiche.
   
    ![Applications HDInsight - applications disponibles](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Cliquez sur l’une des applications disponibles, puis suivez les instructions pour accepter les conditions légales.

Vous pouvez vérifier l’état de l’installation de l’application dans les notifications du portail (cliquez sur l’icône en forme de cloche en haut du portail). L’application installée s’affiche dans la liste Applications installées.

## <a name="install-applications-during-cluster-creation"></a>Installer des applications lors de la création du cluster
Vous pouvez également installer des applications HDInsight lorsque vous créez un cluster. Au cours du processus, les applications HDInsight sont installées une fois le cluster créé et en cours d’exécution. Pour installer les applications pendant la création d’un cluster à l’aide du portail Azure, utilisez l’option Personnalisé et non l’option par défaut Création rapide.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Répertorier les applications HDInsight installées et les propriétés
Le portail affiche une liste des applications HDInsight installées pour un cluster et les propriétés de chaque application installée.

**Répertorier une application HDInsight et afficher des propriétés**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche. 
3. Cliquez sur un cluster HDInsight.
4. Dans **Paramètres**, cliquez sur **Applications** sous la catégorie **Configuration**. Les applications installées sont répertoriées à droite. 
   
    ![Applications HDInsight - applications installées](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Cliquez sur une des applications installées pour en afficher la propriété. Listes des propriétés :
   
   * Nom de l’application : nom de l’application.
   * État : état de l’application. 
   * Page web : l’URL de l’application web que vous avez déployée sur le nœud de périmètre. Les informations d’identification sont les mêmes que celles de l’utilisateur HTTP configurées pour le cluster.
   * Point de terminaison HTTP : les informations d’identification sont les mêmes que celles de l’utilisateur HTTP configuré pour le cluster. 
   * Point de terminaison SSH : vous pouvez utiliser SSH pour la connexion au nœud de périmètre. Les informations d’identification sont les mêmes que celles de l’utilisateur SSH configurées pour le cluster. Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Pour supprimer une application, cliquez avec le bouton droit sur l’application, puis sur **Supprimer** dans le menu contextuel.

## <a name="connect-to-the-edge-node"></a>Connexion au nœud de périmètre
Vous pouvez vous connecter au nœud de périmètre à l’aide de HTTP et SSH. Vous trouverez les informations de point de terminaison sur le [portail](#list-installed-hdinsight-apps-and-properties). Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Les informations d’identification du point de terminaison HTTP sont les informations d’identification de l’utilisateur HTTP configurées pour le cluster HDInsight ; les informations d’identification du point de terminaison SSH sont les informations d’identification SSH configurées pour le cluster HDInsight.

## <a name="troubleshoot"></a>Résolution des problèmes
Consultez [Dépanner l’installation](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Étapes suivantes
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md): découvrez comment déployer des applications HDInsight inédites vers HDInsight.
* [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md): découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.
* [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md): apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Créer des clusters Hadoop sous Linux dans HDInsight à l’aide de modèles Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): apprenez à appeler des modèles Resource Manager pour la création de clusters HDInsight.
* [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md): apprenez à utiliser un nœud de périmètre vide pour accéder au cluster HDInsight, tester des applications HDInsight et héberger des applications HDInsight.

