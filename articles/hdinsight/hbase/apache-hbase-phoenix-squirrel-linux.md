---
title: Utiliser Apache Phoenix et SQLLine avec HBase dans Azure HDInsight | Microsoft Docs
description: "Découvrez comment utiliser Apache Phoenix dans HDInsight. Découvrez également comment installer et configurer SQLLine sur votre ordinateur pour vous connecter à un cluster HBase dans HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 12791da56f32ffffa4b1131c408829f50f6e9124
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Utilisation d’Apache Phoenix avec les clusters HBase basés sur Linux dans HDinsight
Découvrez comment utiliser [Apache Phoenix](http://phoenix.apache.org/) dans Azure HDInsight et comment utiliser SQLLine. Pour plus d'informations sur Phoenix, consultez [Phoenix en 15 minutes ou moins](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Pour en savoir plus sur la grammaire Phoenix, consultez [Grammaire Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Pour plus d’informations sur la version de Phoenix avec HDInsight, consultez [Nouveautés des versions de cluster Hadoop fournies par HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Utiliser SQLLine
[SQLLine](http://sqlline.sourceforge.net/) est un utilitaire de ligne de commande pour exécuter SQL.

### <a name="prerequisites"></a>configuration requise
Avant de pouvoir utiliser SQLLine, vous devez disposer des éléments suivants :

* **Un cluster HBase dans HDInsight**. Pour en créer un, consultez [Prise en main d’un exemple Apache HBase dans HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Quand vous vous connectez à un cluster HBase, vous devez vous connecter à l’une des machines virtuelles ZooKeeper. Chaque cluster HDInsight a trois machines virtuelles ZooKeeper.

**Pour connaître le nom d’hôte ZooKeeper**

1. Ouvrez Ambari en accédant à la page **https://\<nom du cluster\>.azurehdinsight.net**.
2. Pour vous connecter, entrez le nom d’utilisateur (cluster) HTTP et le mot de passe.
3. Dans le menu de gauche, sélectionnez **ZooKeeper**. Trois instances de **serveur ZooKeeper** apparaissent dans la liste.
4. Sélectionnez l’une de ces instances de **serveur ZooKeeper**. Dans le volet **Résumé**, recherchez le **nom d’hôte**. Il ressemble à *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Pour utiliser SQLLine**

1. Connectez-vous au cluster à l’aide de SSH. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Dans SSH, utilisez les commandes suivantes pour exécuter SQLLine :

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Pour créer une table HBase et insérer des données, exécutez les commandes suivantes :

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Pour plus d’informations, consultez le [manuel SQLLine](http://sqlline.sourceforge.net/#manual) et la [grammaire Phoenix](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>étapes suivantes
Dans cet article, vous avez appris comment utiliser Apache Phoenix dans HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].
  HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées.
* [Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Avec l’intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase.
* [Configuration de la réplication HBase dans HDInsigtht](apache-hbase-replication.md). Découvrez comment configurer la réplication HBase entre deux centres de données Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


