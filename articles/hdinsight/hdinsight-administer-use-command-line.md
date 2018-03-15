---
title: "Gestion des clusters Hadoop à l’aide de l’interface CLI Azure - Azure HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser l’interface de ligne de commande Azure pour gérer des clusters Hadoop dans Azure HDInsight. Azure CLI fonctionne sur Windows, Mac et Linux."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: 491dbd157255dc4fa7f77178f9486959ba4847a1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gestion des clusters Hadoop dans HDInsight à l'aide du portail Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Apprenez à utiliser [l'interface de ligne de commande Azure](../cli-install-nodejs.md) afin de gérer des clusters Hadoop dans Azure HDInsight. L’interface de ligne de commande Azure est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux. HDInsight ne prend pas en charge [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview).

Cet article aborde uniquement l’utilisation de l’interface de ligne de commande Azure dans HDInsight. Pour une aide générale sur l’utilisation de l’interface Azure CLI, consultez la rubrique [Installer et configurer l’interface Azure CLI][azure-command-line-tools].

## <a name="prerequisites"></a>Prérequis
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure CLI** - Consultez la rubrique [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md) pour obtenir des informations sur l’installation et la configuration.
* **Connectez-vous à Azure**en utilisant la commande suivante :

    ```cli
    azure login
    ```
  
    Pour plus d’informations sur l’authentification à l’aide d’un compte professionnel ou scolaire, consultez la section [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure](/cli/azure/authenticate-azure-cli).
* **Passez en mode Azure Resource Manager**en exécutant la commande suivante :
  
    ```cli
    azure config mode arm
    ```

Pour obtenir de l’aide, utilisez le commutateur **-h** .  Par exemple : 

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Créer des clusters avec l’interface de ligne de commande
Voir [Créer des clusters dans HDInsight à l’aide d’Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Énumération et affichage des détails de cluster
Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Vue de ligne de commande de la liste de clusters][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Suppression des clusters
Utilisez la commande suivante pour supprimer un cluster :

```cli
azure hdinsight cluster delete <Cluster Name>
```

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui le contient. Notez que cette opération supprime toutes les ressources dans le groupe, notamment le compte de stockage par défaut.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Mise à l’échelle des clusters
Pour modifier la taille du cluster Hadoop :

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Activer / désactiver l’accès HTTP pour un cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Activer / désactiver l’accès RDP pour un cluster

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment effectuer différentes tâches d'administration sur un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administration de HDInsight à l'aide d'Azure PowerShell][hdinsight-admin-powershell]
* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Utilisation de l’interface de ligne de commande Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Énumération et affichage des clusters"
