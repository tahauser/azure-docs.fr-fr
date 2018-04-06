---
title: Surveiller et gérer des clusters Azure HDInsight à l’aide de l’interface utilisateur Web d’Ambari | Microsoft Docs
description: Découvrez comment utiliser Ambari pour gérer et surveiller des clusters HDInsight Linux. Dans ce document, vous apprenez à utiliser l’interface utilisateur web d’Ambari incluse avec les clusters HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4787f3cc-a650-4dc3-9d96-a19a67aad046
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: larryfr
ms.openlocfilehash: 922dfd475f27f182d8958887087fc2f4945cc43a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifie la gestion et la surveillance d'un cluster Hadoop en fournissant une interface utilisateur web et une API REST faciles à utiliser. Ambari est inclus dans les clusters HDInsight sous Linux et sert à surveiller le cluster et à apporter des modifications de configuration.

Dans ce document, vous apprenez à utiliser l’interface utilisateur web d’Ambari avec un cluster HDInsight.

## <a id="whatis"></a>Présentation d'Ambari

[Apache Ambari](http://ambari.apache.org) simplifie la gestion d’Hadoop grâce à une interface utilisateur web facile à utiliser. Vous pouvez utiliser Ambari pour gérer et surveiller les clusters Hadoop. Les développeurs peuvent intégrer ces fonctionnalités dans leurs applications à l’aide des [API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

L’interface utilisateur Web d’Ambari est fournie par défaut avec les clusters HDInsight utilisant le système d’exploitation Linux.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). 

## <a name="connectivity"></a>Connectivité

L’interface utilisateur web d’Ambari est disponible sur votre cluster HDInsight à l’adresse HTTPS://CLUSTERNAME.azurehdinsight.net, où **CLUSTERNAME** correspond au nom de votre cluster.

> [!IMPORTANT]
> Une connexion à Ambari sur HDInsight requiert HTTPS. Lorsque vous êtes invité à vous authentifier, utilisez le nom du compte administrateur et le mot de passe fournis lors de la création du cluster.

## <a name="ssh-tunnel-proxy"></a>Tunnel SSH (proxy)

Bien qu’Ambari pour votre cluster soit accessible directement via Internet, certains liens de l’interface utilisateur web d’Ambari (comme celui vers le JobTracker) ne sont pas exposés sur Internet. Pour accéder à ces services, vous devez créer un tunnel SSH. Pour plus d’informations, consultez [Utilisation du tunneling SSH avec HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interface utilisateur web d'Ambari

> [!WARNING]
> Les fonctionnalités de l’interface utilisateur web Ambari ne sont pas toutes prises en charge dans HDInsight. Pour plus d’informations, consultez la section [Opérations non prises en charge](#unsupported-operations) de ce document.

Pendant la connexion à l’interface utilisateur web d’Ambari, vous devez vous authentifier auprès de la page. Utilisez le nom d’utilisateur et le mot de passe de l’administrateur du cluster (administrateur par défaut) utilisés lors de la création du cluster.

Lorsque la page s'ouvre, vérifiez la barre située en haut de l'écran. Cette barre contient les informations et les commandes suivantes :

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logo d’Ambari** : ouvre le tableau de bord, qui peut être utilisé pour surveiller le cluster.

* **Nom du cluster # ops** : affiche le nombre d'opérations Ambari en cours. En sélectionnant le nom du cluster ou **ops #** , une liste des opérations effectuées en arrière-plan s’affiche.

* **# alertes** : affiche les avertissements ou alertes critiques, le cas échéant, pour le cluster.

* **Tableau de bord** : affiche le tableau de bord.

* **Services** : informations et paramètres de configuration des services sur le cluster

* **Hôtes** : informations et paramètres de configuration des nœuds sur le cluster

* **Alertes** : journal contenant informations, avertissements et alertes critiques.

* **Administrateur** : pile logicielle/services installés sur le cluster, informations sur le compte de service et sécurité Kerberos.

* **Bouton Administrateur** : gestion d'Ambari, paramètres utilisateur et déconnexion.

## <a name="monitoring"></a>Surveillance

### <a name="alerts"></a>Alertes

La liste suivante contient les statuts d’alerte courants utilisés par Ambari :

* **OK**
* **Avertissement**
* **CRITIQUE**
* **INCONNU**

Les alertes autres que **OK** font apparaître l’entrée **# alerts** en haut de la page pour afficher le nombre d’alertes. La sélection de cette entrée fait apparaître les alertes et leur état.

Les alertes sont organisées en plusieurs groupes par défaut, qui peuvent être consultés depuis la page **Alertes** .

![page d’alertes](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Vous pouvez gérer les groupes à l'aide du menu **Actions**, en sélectionnant **Gérer les groupes d'alerte**.

![gérer des groupes d'alertes, boîte de dialogue](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Vous pouvez également gérer les méthodes d’alerte et créer des notifications d’alerte à partir du menu **Actions** en sélectionnant __Gérer les notifications d’alerte__. Les notifications en cours sont affichées. Vous pouvez également créer des notifications à partir d’ici. Les notifications peuvent être envoyées via **EMAIL** ou **SNMP** lorsque des combinaisons spécifiques alerte/gravité se produisent. Par exemple, vous pouvez envoyer un e-mail lorsque l’une des alertes du groupe **YARN par défaut** est définie comme **Critique**.

![créer une alerte, boîte de dialogue](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Enfin, en sélectionnant __Gérer les paramètres d’alerte__ à partir du menu __Actions__, vous pouvez définir le nombre d’occurrences d’une alerte avant l’envoi d’une notification. Ce paramètre peut être utilisé pour empêcher les notifications des erreurs temporaires.

### <a name="cluster"></a>Cluster

L'onglet **Mesures** du tableau de bord contient une série de widgets qui permettent de surveiller facilement l'état de votre cluster d'un seul coup d'œil. Plusieurs widgets, tels que **Utilisation du processeur**, fournissent des informations supplémentaires lorsque vous cliquez dessus.

![tableau de bord avec des mesures](./media/hdinsight-hadoop-manage-ambari/metrics.png)

L'onglet **Cartes thermiques** affiche les paramètres sous forme de cartes thermiques colorées, allant du vert au rouge.

![tableau de bord avec des cartes thermiques](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Pour plus d’informations sur les nœuds du cluster, sélectionnez **Hôtes**. Puis sélectionnez le nœud spécifique qui vous intéresse.

![détails de l'hôte](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Services

La barre latérale du tableau de bord, intitulée **Services** , fournit un aperçu rapide de l'état des services exécutés sur le cluster. Différentes icônes sont utilisées pour indiquer l’état ou les actions à entreprendre. Par exemple, un symbole de recyclage jaune s’affiche si un service doit être recyclé.

![barre latérale des services](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]
> Les services affichés diffèrent selon les versions et les types de cluster HDInsight. Les services affichés ici peuvent être différents de ceux affichés pour votre cluster.

La sélection d’un service fait apparaître des informations détaillées sur le service.

![informations de résumé du service](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Liens rapides

Certains services affichent un lien **Liens rapides** en haut de la page. Ils peuvent être utilisés pour accéder à des interfaces utilisateur web spécifiques des services, tels que :

* **Historique des travaux** : historique des travaux MapReduce.
* **Gestionnaire des ressources** : interface utilisateur du gestionnaire des ressources YARN.
* **NameNode** : interface utilisateur NameNode HDFS.
* **Interface utilisateur web Oozie** : interface utilisateur Oozie.

La sélection de l’un de ces liens ouvre un nouvel onglet dans votre navigateur qui affiche la page sélectionnée.

> [!NOTE]
> Si vous sélectionnez **Liens rapides** pour un service, une erreur « serveur introuvable » peut s’afficher. Si vous rencontrez cette erreur, vous devez utiliser un tunnel SSH lorsque vous utilisez **Liens rapides** pour ce service. Pour plus d’informations, consultez [Utilisation du tunneling SSH avec HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="management"></a>gestion

### <a name="ambari-users-groups-and-permissions"></a>Utilisateurs d'Ambari, groupes et autorisations

Il est possible de travailler avec des utilisateurs, des groupes et des autorisations lorsque vous utilisez un cluster HDInsight [joint au domaine](./domain-joined/apache-domain-joined-introduction.md). Pour plus d’informations sur l’utilisation de l’interface utilisateur de gestion Ambari sur un cluster joint à un domaine, consultez [Gestion des clusters HDInsight joints à un domaine](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]
> Ne modifiez pas le mot de passe pour l’agent de surveillance Ambari (hdinsightwatchdog) sur votre cluster HDInsight basé sur Linux. La modification du mot de passe élimine la possibilité d’utiliser les actions de script ou d’effectuer des opérations de mise à l’échelle sur votre cluster.

### <a name="hosts"></a>Hôtes

La page **Hôtes** répertorie tous les hôtes du cluster. Pour gérer des hôtes, procédez comme suit :

![page d'hôtes](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]
> L’ajout, la désactivation et la remise en service d’un hôte ne doivent pas être utilisés avec des clusters HDInsight.

1. Sélectionnez l’hôte que vous souhaitez gérer.

2. Utilisez le menu **Actions** pour sélectionner l'action que vous souhaitez effectuer :

   * **Démarrer tous les composants** : démarre tous les composants sur l'hôte.

   * **Arrêter tous les composants** : arrête tous les composants sur l'hôte.

   * **Redémarrer tous les composants** : redémarre tous les composants sur l’hôte.

   * **Activer le mode Maintenance** : supprime les alertes de l'hôte. Ce mode doit être activé si vous effectuez des actions qui génèrent des alertes. Par exemple, l’arrêt et le démarrage d’un service.

   * **Désactiver le mode Maintenance** : rétablit les alertes normales de l'hôte.

   * **Arrêter** : arrête DataNode ou NodeManagers sur l'hôte.

   * **Démarrer** : démarre DataNode ou NodeManagers sur l'hôte.

   * **Redémarrer** : arrête et redémarre DataNode ou NodeManagers sur l'hôte.

   * **Désactiver** : supprime un hôte du cluster.

     > [!NOTE]
     > N'utilisez pas cette action sur les clusters HDInsight.

   * **Réactiver** : ajoute un hôte préalablement désactivé au cluster.

     > [!NOTE]
     > N'utilisez pas cette action sur les clusters HDInsight.

### <a id="service"></a>Services

Dans la page **Tableau de bord** ou **Services**, utilisez le bouton **Actions** situé au bas de la liste des services pour arrêter et démarrer tous les services.

![Actions de service](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]
> Bien que l’option **Ajouter un service** soit répertoriée dans ce menu, vous ne devez pas l’utiliser pour ajouter des services au cluster HDInsight. Les nouveaux services doivent être ajoutés à l'aide d'une action de script lors de l’approvisionnement du cluster. Pour plus d’informations sur l’utilisation des actions de script, consultez [Personnaliser des clusters HDInsight à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).

Bien que le bouton **Actions** permette de redémarrer tous les services, vous souhaitez souvent démarrer, arrêter ou redémarrer un service spécifique. Pour effectuer des actions sur un service individuel, procédez comme suit :

1. Depuis la page **Tableau de bord** ou **Services**, sélectionnez un service.

2. En haut de l'onglet **Résumé**, utilisez le bouton **Actions de service** et sélectionnez l'action que vous souhaitez effectuer. Ceci redémarre le service sur tous les nœuds.

    ![action de service](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]
   > Le redémarrage de certains services lorsque le cluster fonctionne peut générer des alertes. Pour éviter ces alertes, vous pouvez utiliser le bouton **Actions de service** afin d’activer le **Mode Maintenance** avant de lancer le redémarrage.

3. Lorsqu’une action est sélectionnée, l’entrée **# op** située en haut de la page s’incrémente pour indiquer qu’une opération s’exécute en arrière-plan. Si leur affichage est configuré, une liste des opérations exécutées en arrière-plan apparaît.

   > [!NOTE]
   > Si vous avez activé **Mode Maintenance** pour le service, n'oubliez pas de le désactiver à l'aide du bouton **Actions de service** une fois l'opération terminée.

Pour configurer un service, procédez comme suit :

1. Depuis la page **Tableau de bord** ou **Services**, sélectionnez un service.

2. Sélectionnez l'onglet **Configurations** . La configuration actuelle apparaît. Une liste des configurations précédentes est également affichée.

    ![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Utilisez les champs affichés pour modifier la configuration, puis sélectionnez **Enregistrer**. Ou sélectionnez une configuration antérieure, puis sélectionnez **Définir comme actuelle** pour rétablir des paramètres utilisés précédemment.

## <a name="ambari-views"></a>Affichages Ambari

Les affichages Ambari permettent aux développeurs d’incorporer des éléments d’interface utilisateur dans l’interface utilisateur web Ambari à l’aide de l’ [infrastructure des affichages Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight propose les affichages suivants avec les types de clusters Hadoop :

* Gestionnaire de files d’attente Yarn : le gestionnaire de files d’attente fournit une interface utilisateur simple pour afficher et modifier les files d’attente Yarn.

* Affichage Hive : l’affichage Hive vous permet d’exécuter des requêtes Hive directement à partir du navigateur web. Vous pouvez enregistrer des requêtes, afficher les résultats, enregistrer les résultats dans le stockage de cluster ou les télécharger sur votre système local. Pour plus d’informations sur l’utilisation des affichages Hive, consultez [Utiliser des affichages Hive avec HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Vue Tez : la vue Tez vous permet de mieux comprendre et optimiser les tâches. Vous pouvez afficher des informations sur l’exécution des tâches Tez et les ressources utilisées.

## <a name="unsupported-operations"></a>Opérations non prises en charge

Les opérations Ambari suivantes ne sont pas prises en charge dans HDInsight :

* __Déplacement du service Collecteur de métriques__. Quand vous affichez des informations sur le service Collecteur de métriques, l’une des actions disponibles à partir du menu Service Actions est __Move Metrics collector__. Cette opération n’est pas prise en charge avec HDInsight.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser [l’API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) avec HDInsight.