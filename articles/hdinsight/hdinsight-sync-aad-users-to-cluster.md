---
title: Synchroniser des utilisateurs Azure Active Directory vers un cluster - Azure HDInsight | Microsoft Docs
description: "Synchronisez des utilisateurs authentifiés d’Azure Active Directory vers un cluster."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: ad1586a6e358dfb1ca2391474ecdd9bee2f6226d
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchroniser des utilisateurs Azure Active Directory vers un cluster HDInsight

Les [clusters HDInsight joints à un domaine](hdinsight-domain-joined-introduction.md) peuvent utiliser l’authentification forte avec les utilisateurs Azure Active Directory (Azure AD), ainsi que des stratégies de *contrôle d’accès en fonction du rôle* (RBAC). À mesure que vous ajoutez des utilisateurs et des groupes à Azure AD, vous pouvez synchroniser les utilisateurs qui ont besoin d’accéder à votre cluster.

## <a name="prerequisites"></a>Prérequis

Si ce n’est déjà fait, [créez un cluster HDInsight joint au domaine](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Ajouter de nouveaux utilisateurs Azure AD

Pour afficher vos hôtes, ouvrez l’interface utilisateur web d’Ambari. Chaque nœud est actualisé avec de nouveaux paramètres de mise à niveau sans assistance.

1. Dans le [portail Azure](https://portal.azure.com), accédez au répertoire Azure AD associé à votre cluster joint au domaine.

2. Sélectionnez **Tous les utilisateurs** dans le menu de gauche, puis sélectionnez **Nouvel utilisateur**.

    ![Volet Tous les utilisateurs](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Remplissez le formulaire du nouvel utilisateur. Sélectionnez les groupes que vous avez créés pour l’attribution d’autorisations basées sur le cluster. Dans cet exemple, créez un groupe nommé « HiveUsers », auquel vous pouvez attribuer de nouveaux utilisateurs. Les [exemples d’instructions](hdinsight-domain-joined-configure.md) pour la création d’un cluster joint à un domaine comprennent l’ajout de deux groupes, `HiveUsers` et `AAD DC Administrators`.

    ![Volet Nouvel utilisateur](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Sélectionnez **Créer**.

## <a name="use-the-ambari-rest-api-to-synchronize-users"></a>Utiliser l’API REST Ambari pour synchroniser les utilisateurs

Les groupes d’utilisateurs spécifiés pendant le processus de création de cluster sont synchronisés à ce moment-là. La synchronisation des utilisateurs a lieu automatiquement toutes les heures. Pour synchroniser immédiatement les utilisateurs, ou pour synchroniser un groupe n’ayant pas été spécifié lors de la création du cluster, utilisez l’API REST Ambari.

La méthode suivante utilise POST avec l’API REST Ambari. Pour plus d’informations, consultez [Gérer des clusters HDInsight à l’aide de l’API REST d’Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Connectez-vous à votre cluster avec SSH](hdinsight-hadoop-linux-use-ssh-unix.md). Dans le volet de vue d’ensemble de votre cluster dans le portail Azure, sélectionnez le bouton **Secure Shell (SSH)**.

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Copiez la commande `ssh` affichée et collez-la dans votre client SSH. À l’invite, entrez le mot de passe d’utilisateur ssh.

3. Après l’authentification, entrez la commande suivante :

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    La réponse est de ce type :

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Pour afficher l’état de la synchronisation, exécutez une nouvelle commande `curl` à l’aide de la valeur `href` retournée par la commande précédente :

    ```bash
    curl -u admin:<YOUR PASSWORD> http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1
    ```
    
    La réponse est de ce type :
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

5. Ce résultat montre que l’état est **COMPLETE**, qu’un nouvel utilisateur a été créé et qu’une appartenance lui a été attribuée. Dans cet exemple, l’utilisateur est affecté au groupe LDAP synchronisé « HiveUsers », car il a été ajouté à ce même groupe dans Azure AD.

> [!NOTE]
> La méthode précédente synchronise uniquement les groupes Azure AD spécifiés dans la propriété **Accéder au groupe d’utilisateurs** des paramètres de domaine durant la création du cluster. Pour plus d’informations, consultez [Créer un cluster HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Vérifier l’utilisateur Azure AD récemment ajouté

Ouvrez [l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md) pour vérifier que le nouvel utilisateur Azure AD a été ajouté. Accédez à l’interface utilisateur web d’Ambari en naviguant jusqu’à **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de cluster.

1. Dans le tableau de bord Ambari, sélectionnez **Manage Ambari** (Gérer Ambari) sous le menu **admin**.

    ![Gérer Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Sélectionnez **Users** (Utilisateurs) dans le groupe de menus **User + Group Management** (Gestion des utilisateurs et des groupes) sur le côté gauche de la page.

    ![Élément de menu Utilisateurs](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Le nouvel utilisateur doit être listé dans le tableau des utilisateurs. Le type est défini sur `LDAP` plutôt que `Local`.

    ![Page Utilisateurs](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Se connecter à Ambari en tant que nouvel utilisateur

Quand le nouvel utilisateur (ou tout autre utilisateur du domaine) se connecte à Ambari, il utilise son nom d’utilisateur Azure AD et ses informations d’identification de domaine complètes.  Ambari affiche un alias d’utilisateur, qui est le nom complet de l’utilisateur dans Azure AD. Le nouvel exemple d’utilisateur a `hiveuser3@contoso.com` comme nom d’utilisateur. Dans Ambari, ce nouvel utilisateur apparaît en tant que `hiveuser3`, mais il se connecte à Ambari en tant que `hiveuser3@contoso.com`.

## <a name="see-also"></a>Voir aussi

* [Configurer les stratégies Hive dans HDInsight joint à un domaine](hdinsight-domain-joined-run-hive.md)
* [Gérer des clusters HDInsight joints à un domaine](hdinsight-domain-joined-manage.md)
* [Autoriser les utilisateurs à accéder à Ambari](hdinsight-authorize-users-to-ambari.md)
