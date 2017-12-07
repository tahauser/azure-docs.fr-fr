---
title: Activer des dumps de tas pour les services Hadoop sur HDInsight - Azure | Microsoft Docs
description: "Activez les services Hadoop à partir des clusters HDInsight sur Linux pour le débogage et l’analyse."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8f151adb-f687-41e4-aca0-82b551953725
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: dcc04e5bba28d0cb32e8633542ab8d3c125003ec
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="enable-heap-dumps-for-hadoop-services-on-linux-based-hdinsight"></a>Activer les dumps de tas pour les services Hadoop sur HDInsight sur Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Les dumps de tas contiennent un instantané de la mémoire de l’application, y compris des valeurs des variables au moment de la création du dump. Ils sont donc utiles pour diagnostiquer les problèmes qui se produisent au moment de l’exécution.

> [!IMPORTANT]
> Les étapes décrites dans ce document fonctionnent uniquement avec les clusters HDInsight sur Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Services

Vous pouvez activer des dumps de tas pour les services suivants :

* **hcatalog** - tempelton
* **hive** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **yarn** - resourcemanager, nodemanager, timelineserver
* **hdfs** - datanode, secondarynamenode, namenode

Vous pouvez également activer les dumps de tas du mappage et réduire les processus exécutés par HDInsight.

## <a name="configuration"></a>Présentation de la configuration du dump du tas

Les dumps de tas sont activés par la transmission d’options (parfois appelées options ou paramètres) à la machine virtuelle Java au démarrage d’un service. Pour la plupart des services Hadoop, vous pouvez modifier le script shell utilisé pour démarrer le service afin d’activer ces options.

Dans chaque script, il existe une exportation pour **\*\_OPTS**, qui contient les options transmises à la machine virtuelle Java. Par exemple, dans le script **hadoop-env.sh**, la ligne qui commence par `export HADOOP_NAMENODE_OPTS=` contient les options du service NameNode.

Les processus de mappage et de réduction sont légèrement différents, car ces opérations sont des processus enfant du service MapReduce. Chaque processus de mappage ou de réduction s’exécute dans un conteneur enfant et deux entrées contiennent les options JVM. Ils sont contenus dans **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]
> Nous recommandons d’utiliser Ambari pour modifier les scripts et les paramètres de mapred-site.xml, car Ambari gère la réplication des modifications sur les nœuds du cluster. Consultez la section [Utilisation d’Ambari](#using-ambari) pour connaître les étapes spécifiques.

### <a name="enable-heap-dumps"></a>Activer les dumps de tas

L’option suivante active les dumps de tas quand OutOfMemoryError se produit :

    -XX:+HeapDumpOnOutOfMemoryError

Le **+** indique que cette option est activée. La valeur par défaut est désactivée.

> [!WARNING]
> Les dumps de tas ne sont pas activés pour les services Hadoop sur HDInsight par défaut, car les fichiers dump peuvent être volumineux. Si vous les activez pour la résolution des problèmes, pensez à les désactiver après avoir reproduit le problème et regroupé les fichiers de vidage.

### <a name="dump-location"></a>Emplacement du dump

L’emplacement par défaut pour le fichier dump est le répertoire de travail actuel. Vous pouvez contrôler l’endroit de stockage du fichier à l’aide de l’option suivante :

    -XX:HeapDumpPath=/path

Par exemple, l’utilisation de `-XX:HeapDumpPath=/tmp` entraîne le stockage des dumps dans le répertoire /tmp.

### <a name="scripts"></a>Scripts

Vous pouvez également déclencher un script quand **OutOfMemoryError** se produit. Par exemple, vous pouvez déclencher une notification pour signaler que l’erreur s’est produite. Utilisez l’option suivante pour déclencher un script quand __OutOfMemoryError__ survient :

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Hadoop étant un système distribué, tout script utilisé doit être placé sur tous les nœuds du cluster sur lequel le service s’exécute.
> 
> Le script doit également être dans un emplacement accessible par le compte sur lequel s’exécute le service et doit fournir des autorisations d’exécution. Par exemple, vous souhaitez stocker les scripts dans `/usr/local/bin` et utiliser `chmod go+rx /usr/local/bin/filename.sh` pour accorder les autorisations de lecture et d’exécution.

## <a name="using-ambari"></a>Utilisation d’Ambari

Pour modifier la configuration d’un service, procédez comme suit :

1. Ouvrez l’interface utilisateur web Ambari de votre cluster. L’URL est https://NOMDEVOTRECLUSTER.azurehdinsight.net.

    Quand vous y êtes invité, authentifiez-vous auprès du site en utilisant le nom du compte HTTP (par défaut : admin) et le mot de passe de votre cluster.

   > [!NOTE]
   > Vous pouvez être invité une deuxième fois par Ambari à entrer le nom d’utilisateur et le mot de passe. Dans ce cas, saisissez simplement les mêmes nom de compte et mot de passe

2. Dans la liste de gauche, sélectionnez la zone de service que vous souhaitez modifier. Par exemple, **HDFS**. Dans la zone centrale, sélectionnez l’onglet **Configurations** .

    ![Image du site web Ambari avec l’onglet des configurations HDFS sélectionné](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. À l’aide de l’entrée **Filtre...**, entrez **opts**. Seuls les éléments contenant ce texte s’affichent.

    ![Liste de filtrage](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Recherchez l’entrée **\*\_OPTS** du service pour lequel vous souhaitez activer les dumps de tas et ajoutez les options que vous souhaitez activer. Dans l’image suivante, j’ai ajouté `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` à l’entrée **HADOOP\_NAMENODE\_OPTS** :

    ![HADOOP_NAMENODE_OPTS with -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > Quand vous activez les dumps de tas du processus enfant de mappage ou de réduction, cherchez les champs intitulés **mapreduce.admin.map.child.java.opts** et **mapreduce.admin.reduce.child.java.opts**.

    Utilisez le bouton **Enregistrer** pour enregistrer les modifications. Vous pouvez entrer une courte note décrivant les modifications.

5. Une fois que ces dernières ont été appliquées, l’icône **Redémarrage requis** s’affiche en regard d’un ou de plusieurs services.

    ![icône de redémarrage requis et bouton redémarrer](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Sélectionnez chaque service nécessitant un redémarrage et utilisez le bouton **Actions de service** pour **Activer le mode de maintenance**. Le mode de maintenance bloque la génération d’alertes au redémarrage de ce service.

    ![menu Activer le mode de maintenance](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Une fois le mode de maintenance activé, utilisez le bouton **Redémarrer** pour que le service puisse **redémarrer tous les éléments affectés**

    ![entrée Redémarrer tous les éléments affectés](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > les entrées du bouton **Redémarrer** peuvent être différentes pour d’autres services.

8. Une fois que les services ont été redémarrés, utilisez le bouton **Actions de service** pour **Désactiver le mode de maintenance**. Ambari reprend la surveillance des alertes du service.

