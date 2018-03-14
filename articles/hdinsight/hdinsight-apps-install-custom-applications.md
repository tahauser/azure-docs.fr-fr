---
title: "Installer vos applications Hadoop personnalisées sur Azure HDInsight | Microsoft Docs"
description: "Découvrez comment installer des applications Hadoop sur des applications HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e556b29c-8176-4bc5-a90b-aa01abfd3aee
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 4065c08e647ff708e1d8c4fa52563da7f83ef0c1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="install-custom-hadoop-applications-on-azure-hdinsight"></a>Installer des applications personnalisées Hadoop sur Azure HDInsight

Dans cet article, vous découvrirez comment installer une application Hadoop sur Azure HDInsight n’ayant pas été publiée sur le portail Azure. Dans cet article, vous allez installer l’application [Hue](http://gethue.com/).

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight sous Linux.  Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même.  

Autres articles associés :

* [Installer des applications HDInsight](hdinsight-apps-install-applications.md): découvrez comment installer une application HDInsight sur vos clusters.
* [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md): découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.

## <a name="prerequisites"></a>Prérequis
Pour installer des applications HDInsight sur un cluster HDInsight existant, vous devez disposer d’un cluster HDInsight. Pour en créer un, consultez [Créer des clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Vous pouvez également installer des applications HDInsight lorsque vous créez un cluster HDInsight.

## <a name="install-hdinsight-applications"></a>Install custom HDInsight applications
Les applications HDInsight peuvent être installées sur un cluster HDInsight existant ou lorsque vous créez un cluster. Pour définir des modèles Azure Resource Manager, consultez [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight).

Fichiers nécessaires pour le déploiement de cette application (Hue) :

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): modèle Azure Resource Manager pour installer l’application HDInsight. Consultez la page [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN : installer une application HDInsight) pour développer votre propre modèle Resource Manager.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh) : action de script appelée par le modèle Resource Manager pour la configuration du nœud de périmètre.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz) : fichier binaire hue appelé à partir de hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz) : fichier binaire hue appelé à partir de hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz) : exemple d’application web (Tomcat) appelée à partir de hui-install_v0.sh.

**Pour installer Hue sur un cluster HDInsight existant**

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Ce bouton ouvre un modèle Resource Manager sur le portail Azure.  Le modèle Resource Manager est situé dans [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Pour savoir comment écrire ce modèle Resource Manager, consultez [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight).
2. À partir du panneau **Paramètres** , saisissez les informations suivantes :

   * **ClusterName**: entrez le nom du cluster où vous souhaitez installer l’application. Ce cluster doit être un cluster existant.
3. Cliquez sur **OK** pour enregistrer les paramètres.
4. Dans le panneau **Déploiement personnalisé**, entrez **Groupe de ressources**.  Le groupe de ressources est un conteneur qui regroupe le cluster, le compte de stockage dépendant et d’autres ressources. Vous devez utiliser le même groupe de ressources que le cluster.
5. Cliquez sur **Conditions juridiques**, puis cliquez sur **Créer**.
6. Vérifiez que la case **Épingler au tableau de bord** est cochée, puis cliquez sur **Créer**. Vous pouvez voir l’état de l’installation dans la vignette épinglée sur le tableau de bord du portail ainsi que la notification du portail (cliquez sur l’icône en forme de cloche en haut du portail).  L’installation de l’application prend environ 10 minutes.

**Pour installer Hue lors de la création d’un cluster**

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Ce bouton ouvre un modèle Resource Manager sur le portail Azure.  Le modèle Resource Manager se trouve à l’adresse [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Pour savoir comment écrire ce modèle Resource Manager, consultez [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight).
2. Suivez les instructions pour créer le cluster et installer Hue. Pour plus d’informations sur la création de clusters HDInsight, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Outre le portail Azure, vous pouvez également utiliser [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) et [l’interface de ligne de commande Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-cli) pour appeler des modèles Resource Manager.

## <a name="validate-the-installation"></a>validation de l'installation
Vous pouvez vérifier l’état de l’application sur le portail Azure pour valider l’installation de l’application. Vous pouvez également valider la création appropriée de tous les points de terminaison HTTP et du site web, le cas échéant :

**Pour accéder au portail Hue**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche.  Si vous ne le voyez pas, cliquez sur **Parcourir**, puis sur **Clusters HDInsight**.
3. Cliquez sur le cluster dans lequel vous avez installé l’application.
4. Dans le panneau **Paramètres**, cliquez sur **Applications** sous la catégorie **Général**. Vous devriez voir **hue** dans le panneau **Applications installées**.
5. Cliquez sur **hue** dans la liste pour en afficher les propriétés.  
6. Cliquez sur le lien de la page web pour valider le site web ; ouvrez le point de terminaison HTTP dans un navigateur pour valider l’interface utilisateur web Hue, puis ouvrez le point de terminaison SSH à l’aide de SSH. Pour en savoir plus, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="troubleshoot-the-installation"></a>Dépanner l’installation
Vous pouvez vérifier l’état de l’installation de l’application dans la notification du portail (cliquez sur l’icône en forme de cloche en haut du portail).

En cas d’échec de l’installation d’une application, des messages d’erreur et des informations de débogage s’affichent à 3 emplacements différents :

* Applications HDInsight : informations générales relatives à l’erreur.

    Ouvrez le cluster à partir du portail, puis cliquez sur Applications dans le panneau Paramètres :

    ![Erreur d’installation des applications hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)
* Action de script HDInsight : si le message d’erreur des applications HDInsight indique un échec d’action de script, le volet Actions de script affichera davantage de détails sur l’échec de script.

    Dans le panneau Paramètres, cliquez sur Actions de script. L’historique des actions de script affiche les messages d’erreur

    ![Erreur de l’action de script des applications hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
* Interface utilisateur web d’Ambari : si le script d’installation était à l’origine de l’échec, utilisez l’interface utilisateur web d’Ambari pour consulter les journaux complets des scripts d’installation.

    Pour plus d’informations, consultez la page [Dépannage](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Supprimer des applications HDInsight
Il existe plusieurs façons de supprimer des applications HDInsight.

### <a name="use-portal"></a>Utilisation du portail
**Pour supprimer une application à l’aide du portail**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche.  Si vous ne le voyez pas, cliquez sur **Parcourir**, puis sur **Clusters HDInsight**.
3. Cliquez sur le cluster dans lequel vous avez installé l’application.
4. Dans le panneau **Paramètres**, cliquez sur **Applications** sous la catégorie **Général**. Vous devriez voir une liste des applications installées. Dans ce didacticiel, **hue** apparaît dans le panneau **Applications installées**.
5. Cliquez avec le bouton droit sur l’application à supprimer, puis sur **Supprimer**.
6. Cliquez sur **Oui** pour confirmer.

À partir du portail, vous pouvez également supprimer le cluster ou supprimer le groupe de ressources contenant l’application.

### <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Vous pouvez utiliser Azure PowerShell pour supprimer le cluster ou le groupe de ressources. Consultez la section [Supprimer des clusters à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Vous pouvez utiliser l’interface de ligne de commande Azure pour supprimer le cluster ou le groupe de ressources. Consultez la section [Supprimer des clusters à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md#delete-clusters).

## <a name="next-steps"></a>étapes suivantes
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment développer des applications Resource Manager pour le déploiement d’applications HDInsight.
* [Install custom HDInsight applications](hdinsight-apps-install-applications.md)(Installer des applications HDInsight personnalisées) : découvrez comment installer une application HDInsight sur vos clusters.
* [Publish HDInsight applications](hdinsight-apps-publish-applications.md)(Publier des applications HDInsight) : découvrez comment publier vos applications HDInsight personnalisées sur Azure Marketplace.
* [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md): apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Créer des clusters Hadoop sous Linux dans HDInsight à l’aide de modèles Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): apprenez à appeler des modèles Resource Manager pour la création de clusters HDInsight.
* [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md): apprenez à utiliser un nœud de périmètre vide pour accéder au cluster HDInsight, tester des applications HDInsight et héberger des applications HDInsight.
