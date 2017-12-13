---
title: "Gestion des clusters HDInsight joints à un domaine - Azure | Microsoft Docs"
description: "Découvrez comment gérer les clusters HDInsight joints à un domaine"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 0fc32960fc2f1ae69315dbfd6bfb8c34c4adc0fa
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Gérer des clusters HDInsight joints à un domaine
Découvrez les utilisateurs et les rôles dans HDInsight joint à un domaine et apprenez à gérer des clusters HDInsight joints à un domaine.

## <a name="users-of-domain-joined-hdinsight-clusters"></a>Utilisateurs des clusters HDInsight joints à un domaine
Un cluster HDInsight qui n’est pas joint à un domaine comporte deux comptes d’utilisateur créés lors de la création du cluster :

* **Administrateur Ambari** : ce compte est également appelé *utilisateur Hadoop* ou *utilisateur HTTP*. Ce compte peut être utilisé pour la connexion à Ambari via https://&lt;clustername>.azurehdinsight.net. Il peut également être utilisé pour exécuter des requêtes sur des vues Ambari, exécuter des travaux à l’aide d’outils externes (par exemple, PowerShell, Templeton, Visual Studio) et s’authentifier avec le pilote ODBC Hive et les outils décisionnels (Excel, PowerBI ou Tableau).
* **Utilisateur SSH** : ce compte peut être utilisé avec SSH et exécuter des commandes sudo. Il dispose de privilèges root d’accès aux machines virtuelles Linux.

Un cluster HDInsight joint à un domaine a trois nouveaux utilisateurs en plus de l’administrateur Ambari et de l’utilisateur SSH.

* **Administrateur Ranger** : ce compte est le compte administrateur Apache Ranger local. Il ne s’agit pas d’un utilisateur de domaine Active Directory. Ce compte peut être utilisé pour configurer des stratégies et d’autres administrateurs utilisateurs ou administrateurs délégués (pour que ces utilisateurs puissent gérer les stratégies). Par défaut, le nom d’utilisateur est *admin* et le mot de passe est le même que le mot de passe administrateur Ambari. Le mot de passe peut être mis à jour à partir de la page Paramètres dans Ranger.
* **Utilisateur de domaine administrateur de cluster** : ce compte est un utilisateur de domaine Active Directory désigné comme administrateur du cluster Hadoop, y compris Ambari et Ranger. Vous devez fournir les informations d’identification de cet utilisateur lors de la création du cluster. Cet utilisateur possède les privilèges suivants :

  * Joindre des machines au domaine et les placer dans l’unité d’organisation que vous spécifiez lors de la création du cluster.
  * Créer des principaux de service au sein de l’unité d’organisation que vous spécifiez lors de la création du cluster.
  * Créer des entrées DNS inversées.

    Notez que les autres utilisateurs d’Active Directory possèdent également ces privilèges.

    Il existe des points de terminaison au sein du cluster (par exemple, Templeton) qui ne sont pas gérés par Ranger et ne sont donc pas sécurisés. Ces points de terminaison sont verrouillés pour tous les utilisateurs, à l’exception de l’utilisateur de domaine administrateur du cluster.
* **Standard** : pendant la création du cluster, vous pouvez fournir plusieurs groupes Active Directory. Les utilisateurs de ces groupes sont synchronisés avec Ranger et Ambari. Ce sont des utilisateurs de domaine qui ont accès uniquement aux points de terminaison gérés par Ranger (par exemple, Hiveserver2). Les stratégies RBAC et les audits sont tous applicables à ces utilisateurs.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Rôles des clusters HDInsight joints à un domaine
Les rôles de HDInsight joint à un domaine sont les suivants :

* Administrateur de cluster
* Opérateur de cluster
* Administrateur de services
* Opérateur de service
* Utilisateur de cluster

**Pour voir les autorisations de ces rôles.**

1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Rôles**.
3. Cliquez sur le point d’interrogation bleu pour afficher les autorisations :

    ![Autorisations des rôles de HDInsight joint à un domaine](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Ouverture de l’interface utilisateur de gestion Ambari
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Ouvrez votre cluster HDInsight dans un panneau. Voir [Énumération et affichage des clusters](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Cliquez sur **Tableau de bord** dans le menu du haut pour ouvrir Ambari.
4. Connectez-vous à Ambari avec le nom d’utilisateur et le mot de passe du domaine de l’administrateur du cluster.
5. Cliquez sur le menu déroulant **Admin** (Administrateur) dans l’angle supérieur droit, puis sur **Manage Ambari** (Gérer Ambari).

    ![Gérer Ambari HDInsight joint à un domaine](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    L’interface utilisateur ressemble à ce qui suit :

    ![Interface utilisateur de gestion Ambari HDInsight joint à un domaine](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Énumération des utilisateurs du domaine synchronisés à partir d’Active Directory
1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Utilisateurs**. Vous devriez voir tous les utilisateurs synchronisés à partir d’Active Directory sur le cluster HDInsight.

    ![Énumération des utilisateurs interface utilisateur de gestion Ambari HDInsight joint à un domaine](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Énumération des groupes du domaine synchronisés à partir d’Active Directory
1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Groupes**. Vous devriez voir tous les groupes synchronisés à partir d’Active Directory sur le cluster HDInsight.

    ![Énumération des groupes interface utilisateur de gestion Ambari HDInsight joint à un domaine](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configuration des autorisations des affichages Hive
1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Views** (Affichages).
3. Cliquez sur **HIVE** pour afficher les détails.

    ![Affichages Hive interface utilisateur de gestion Ambari HDInsight joint à un domaine](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Cliquez sur le lien **Hive View** (Affichage Hive) pour configurer les affichages Hive.
5. Faites défiler jusqu’à la section **Autorisations**.

    ![Configuration des autorisations affichages Hive interface utilisateur de gestion Ambari HDInsight joint à un domaine](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Cliquez sur **Add User** (Ajouter un utilisateur) ou **Add Group** (Ajouter un groupe), puis spécifiez les utilisateurs ou les groupes que peuvent utiliser les affichages Hive.

## <a name="configure-users-for-the-roles"></a>Configuration des utilisateurs pour les rôles
 Pour afficher une liste des rôles et de leurs autorisations, reportez-vous à [Rôles des clusters HDInsight joints à un domaine](#roles-of-domain---joined-hdinsight-clusters).

1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Rôles**.
3. Cliquez sur **Add User** (Ajouter un utilisateur) ou **Add Group** (Ajouter un groupe) pour affecter des utilisateurs et des groupes aux différents rôles.

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un cluster HDInsight joint à un domaine, consultez [Configuration de clusters HDInsight joints à un domaine](apache-domain-joined-configure.md).
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configuration de stratégies Hive pour les clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md).
* Pour exécuter des requêtes Hive en utilisant SSH sur des clusters HDInsight joints au domaine, voir [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
