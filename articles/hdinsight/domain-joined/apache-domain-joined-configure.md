---
title: "Configurer les clusters HDInsight joints à un domaine - Azure | Documents Microsoft"
description: "Découvrez comment configurer les clusters HDInsight joints à un domaine"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 649d138a85ca47440e43c00637ee92b86f4eb03e
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="configure-domain-joined-hdinsight-clusters"></a>Configurer des clusters HDInsight joints à un domaine

Découvrez comment configurer un cluster Azure HDInsight avec Azure Active Directory (Azure AD) et [Apache Ranger](http://hortonworks.com/apache/ranger/)pour valoriser les stratégies d’authentification forte et de contrôle de l’accès enrichi basé sur les rôles.  Le cluster HDInsight peut être configuré uniquement sur les clusters Linux. Pour plus d’informations, consultez [Introduire des clusters HDInsight joints à un domaine](apache-domain-joined-introduction.md).

> [!IMPORTANT]
> Oozie n’est pas activé sur HDInsight joint à un domaine.

Cet article est le premier didacticiel d’une série :

* Créez un cluster HDInsight connecté à Azure AD (via la fonctionnalité des services de domaines Azure Directory) avec Apache Ranger activé.
* Créez et appliquez les stratégies Hive via Apache Ranger et autorisez les utilisateurs (par exemple, les scientifiques de données) à se connecter à Hive à l’aide des outils ODBC, par exemple Excel, tableau, etc. Microsoft travaille à l’ajout d’autres charges de travail, comme HBase et Storm, sur le cluster HDInsight joint à un domaine.

Les noms de service Azure doivent être globalement uniques. Les noms suivants sont utilisés dans ce didacticiel. Contoso est un nom fictif. Vous devez remplacer *contoso* par un nom différent quand vous parcourez le didacticiel. 

**Noms :**

| Propriété | Valeur |
| --- | --- |
| Annuaire Azure AD |contosoaaddirectory |
| Nom de domaine Azure AD |contoso (contoso.onmicrosoft.com) |
| Réseau virtuel HDInsight |contosohdivnet |
| Groupe de ressources du réseau virtuel HDInsight |contosohdirg |
| Cluster HDInsight |contosohdicluster |

Ce didacticiel vous indique la procédure de configuration d’un cluster HDInsight joint à un domaine. Chaque section comporte des liens vers d’autres articles avec des informations contextuelles supplémentaires.

## <a name="prerequisite"></a>Configuration requise :
* Familiarisez-vous avec les [services de domaine Azure AD](https://azure.microsoft.com/services/active-directory-ds/), et la structure de [tarification](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Assurez-vous que votre abonnement est dans la liste approuvée de cette version préliminaire publique. Pour ce faire, envoyez un e-mail à hdipreview@microsoft.com, avec votre ID d’abonnement.
* Certificat SSL signé par une autorité signataire ou certificat auto-signé pour votre domaine. Le certificat est nécessaire à la configuration du LDAP sécurisé.

## <a name="procedures"></a>Procédures
1. Créez un réseau virtuel HDInsight dans le mode Resource Management Azure.
2. Créez et configurez Azure AD et Azure AD AS.
3. Créez un cluster HDInsight.

> [!NOTE]
> Ce didacticiel part du principe que vous ne possédez pas d’instance Azure AD. Si vous en avez un, vous pouvez sauter cette étape.
> 
> 

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Créer un réseau virtuel Resource Manager pour le cluster HDInsight
Dans cette section, vous allez créer un réseau virtuel Azure Resource Manager qui sera utilisé pour le cluster HDInsight. Pour plus d’informations sur la création d’un réseau virtuel Azure à l’aide d’autres méthodes, consultez la section [Créez un réseau virtuel](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

Une fois le réseau virtuel créé, vous allez configurer Azure AD DS pour qu’il utilise ce réseau.

**Pour créer un réseau virtuel Resource Manager**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**, **Mise en réseau**, puis sur **Réseau virtuel**. 
3. Sous **Sélectionnez un modèle de déploiement**, sélectionnez **Resource Manager**, puis cliquez sur **Créer**.
4. Tapez ou sélectionnez les valeurs suivantes :
   
   * **Nom** : contosohdivnet
   * **Espace d’adressage** : 10.0.0.0/16
   * **Nom du sous-réseau** : Subnet1
   * **Plage d’adresses de sous-réseau** : 10.0.0.0/24
   * **Abonnement** : (sélectionnez votre abonnement Azure.)
   * **Groupe de ressources** : contosohdirg
   * **Emplacement** : (sélectionnez le même emplacement que le réseau virtuel Azure AD. Par exemple, contosoaadvnet.)
5. Cliquez sur **Créer**.

**Pour configurer DNS pour le réseau virtuel Resource Manager**

1. À partir du [portail Azure](https://portal.azure.com), cliquez sur **Plus de services** > **Réseaux virtuels**. Assurez-vous de ne pas cliquer sur **Réseaux virtuels (classiques)**.
2. Cliquez sur **contosohdivnet**.
3. Cliquez sur **Serveurs DNS** sur le côté gauche du nouveau panneau.
4. Cliquez sur **Personnaliser**, puis saisissez les valeurs suivantes :
   
   * 10.0.0.4
   * 10.0.0.5     
     
5. Cliquez sur **Enregistrer**.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Créez et configurer Azure AD AS pour votre instance Azure AD
Dans cette section, vous allez :

1. Créer une application Azure AD.
2. Créer des utilisateurs Azure AD. Ces utilisateurs sont des utilisateurs du domaine. Vous utilisez le premier utilisateur pour la configuration du cluster HDInsight avec l’application Azure AD.  Les deux autres utilisateurs sont facultatifs pour ce didacticiel. Ils seront utilisés dans la section [Configuration de stratégies Hive pour les clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md), au moment de la configuration des stratégies Apach Ranger.
3. Créez le groupe AAD DC Administrator, puis ajoutez l’utilisateur Azure AD au groupe. Cet utilisateur vous permet de créer l’unité d’organisation.
4. Activez les services de domaine Azure AD (Azure AD DS) pour l’instance Azure AD.
5. Configurez LDAPS pour l’instance Azure AD. Le protocole LDAP (Lightweight Directory Access Protocol) est utilisé pour la lecture et l’écriture sur Azure AD.

Si vous préférez utiliser une instance Azure AD existante, vous pouvez passer les étapes 1 et 2.

**Pour créer une instance Azure AD**

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Nouveau** > **App Services** > **Active Directory** > **Répertoire** > **Création personnalisée**. 
2. Entrez ou sélectionnez les valeurs suivantes :
   
   * **Nom** : contosoaaddirectory
   * **Nom de domaine** : contoso.  Ce nom doit être globalement unique.
   * **Pays ou région** : sélectionnez votre pays ou région.
3. Cliquez sur **Terminé**.

**Créer un utilisateur Azure AD**

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory** > **contosoaaddirectory** > **Utilisateurs et groupes**. 
2. Dans le menu, cliquez sur **Tous les utilisateurs**.
3. Cliquez sur **New User**.
4. Entrez le **Nom** et le **Nom d’utilisateur**, puis cliquez sur **Suivant**. 
5. Configurez le profil utilisateur ; dans **Rôle**, sélectionnez **Administrateur global**, puis cliquez sur **Suivant**.  Le rôle d’administrateur global est requis pour la création des unités d’organisation.
6. Notez le mot de passe temporaire.
7. Cliquez sur **Créer**. Plus loin dans ce didacticiel, vous utiliserez cet utilisateur administrateur général pour créer le cluster HDInsight.

Suivez la même procédure pour créer deux utilisateurs supplémentaires avec le rôle **Utilisateur**, hiveuser1 et hiveuser2. Les utilisateurs suivants seront utilisés dans [Configuration de stratégies Hive pour les clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md).

**Pour créer le groupe AAD DC Administrators et ajouter un utilisateur Azure AD**

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory** > **contosoaaddirectory** > **Utilisateurs et groupes**. 
2. Dans le menu supérieur, cliquez sur **Tous les groupes**.
3. Cliquez sur **Nouveau groupe**.
4. Entrez ou sélectionnez les valeurs suivantes :
   
   * **Nom** : AAD DC Administrators.  Ne modifiez pas le nom du groupe.
   * **Type d’appartenance** : Affecté.
5. Cliquez sur **Sélectionner**.
6. Cliquez sur **Membres**.
7. Sélectionnez le premier utilisateur que vous avez créé à l’étape précédente, puis cliquez sur **Sélectionner**.
8. Répétez la procédure afin de créer un autre groupe appelé **HiveUsers**, puis ajoutez les deux utilisateurs Hive au groupe.

Pour plus d’informations, consultez [Services de domaine Azure AD (version préliminaire) - Créer le groupe « AAD DC Administrators »](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Pour activer Azure AD DS pour votre instance Azure AD**

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Sécurité + Identité** > **Azure AD Domain Services** > **Ajouter**. 
2. Entrez ou sélectionnez les valeurs suivantes :
   * **Nom de l’annuaire** : contosoaaddirectory
   * **Nom du domaine DNS** : affiche le nom DNS par défaut de l’annuaire Azure. Par exemple, contoso.onmicrosoft.com.
   * **Emplacement** : sélectionnez votre région.
   * **Réseau** : sélectionnez le réseau virtuel et le sous-réseau que vous avez créés précédemment. Par exemple, **contosohdivnet**.
3. Cliquez sur **OK** dans la page du récapitulatif. Le message **Déploiement en cours...** s’affiche alors sous les notifications.
4. Attendez que le message **Déploiement en cours...** disparaisse et que le champ **Adresse IP** soit renseigné. Deux adresses IP sont renseignées. Il s’agit des adresses IP des contrôleurs de domaine configurés par les services de domaine. Chaque adresse IP est visible une fois que le contrôleur de domaine correspondant est configuré et prêt. Notez les deux adresses IP. Vous en aurez besoin ultérieurement.

Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Pour synchroniser le mot de passe**

Si vous utilisez votre propre domaine, vous devez synchroniser le mot de passe. Consultez [Enable password synchronization to Azure AD domain services for a cloud-only Azure AD directory (Activer la synchronisation des mots de passe sur les services de domaine Azure AD pour un répertoire Azure AD uniquement dans le cloud)](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

**Pour configurer LDAPS pour l’instance Azure AD**

1. Récupérez un certificat SSL signé par une autorité signataire pour votre domaine.
2. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Azure AD Domain Services** > **contoso.onmicrosoft.com**. 
3. Activez **LDAP sécurisé**.
6. Suivez les instructions de configuration du fichier de certificat et du mot de passe.  
7. Attendez que le champ **Certificat LDAP sécurisé** soit renseigné. Cela peut prendre jusqu’à 10 minutes ou plus.

> [!NOTE]
> Si certaines tâches d’arrière-plan sont exécutées sur l’instance Azure AD DS, vous pouvez rencontrer une erreur lors du chargement du certificat - <i>Une opération est exécutée pour ce client. Veuillez réessayer plus tard</i>.  Si vous rencontrez cette erreur, veuillez réessayer plus tard. La configuration du second contrôleur de domaine peut prendre jusqu’à 3 heures.
> 
> 

Pour plus d’informations, consultez [Configurer le protocole LDAPS (LDAP sécurisé) pour un domaine géré par les services de domaine Azure AD](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-hdinsight-cluster"></a>Créer un cluster HDInsight
Dans cette section, vous créez un cluster Hadoop Linux dans HDInsight à l’aide du portail Azure ou du [modèle Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Pour d'autres méthodes de création de cluster et comprendre les paramètres, consultez [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Pour plus d’informations sur l’utilisation d’un modèle Resource Manager pour créer des clusters Hadoop dans HDInsight, consultez [Création de clusters Hadoop dans HDInsight à l’aide de modèles Azure Resource Manager](../hdinsight-hadoop-create-windows-clusters-arm-templates.md).

**Pour créer un cluster HDInsight joint à un domaine à l’aide du portail Azure**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**, **Intelligence et analyse**, puis sur **HDInsight**.
3. Dans le panneau **Nouveau cluster HDInsight**, entrez ou sélectionnez les valeurs suivantes :
   
   * **Nom du cluster** : saisissez un nom pour le cluster HDInsight joint au domaine.
   * **Abonnement** : sélectionnez l’abonnement Azure utilisé pour créer ce cluster.
   * **Configuration du cluster** :
     
     * **Type de cluster** : Hadoop Le cluster HDInsight joint au domaine est uniquement pris en charge sur les clusters Hadoop, Spark et Interactive Query.
     * **Système d’exploitation** : Linux  Le cluster HDInsight joint au domaine est pris en charge uniquement sur les clusters HDInsight Linux.
     * **Version** : HDI 3.6. HDInsight joint au domaine est pris en charge uniquement sur le cluster HDInsight version 3.6.
     * **Type de cluster** : PREMIUM
       
       Pour enregistrer les modifications, cliquez sur **Sélectionner**.
   * **Informations d’identification** : configurez les informations d’identification pour les utilisateurs du cluster et SSH.
   * **Source de données** : créez un compte de stockage ou utilisez un compte de stockage existant en tant que compte de stockage par défaut pour le cluster HDInsight. L’emplacement doit être celui des deux réseaux virtuels.  Il s’agit également de l’emplacement du cluster HDInsight.
   * **Tarification** : sélectionnez le nombre de nœuds de travail de votre cluster.
   * **Configurations avancées** : 
     
     * **Jonction de domaine et réseau virtuel/sous-réseau** : 
       
       * **Paramètres du domaine** : 
         
         * **Nom du domaine** : contoso.onmicrosoft.com
         * **Mot de passe utilisateur du domaine** :saisissez un nom d’utilisateur du domaine. Ce domaine doit présenter les privilèges suivants : joignez les machines aux domaines et placez-les dans l’unité d’organisation spécifiée durant la création du cluster ; créez les principaux du service au sein de l’unité d’organisation spécifiée durant la création du cluster ; créez les entrées du DNS inversé. Cet utilisateur de domaine devient l’administrateur de ce cluster HDInsight joint à un domaine.
         * **Mot de passe du domaine** : entrez le mot de passe de l’utilisateur du domaine.
         * **Unité d’organisation** : saisissez le nom unique de l’unité d’organisation que vous souhaitez utiliser avec le cluster HDInsight. Par exemple : UO = HDInsightOU, CD = contoso, CD = onmicrosoft, CD = com. Si cette unité d’organisation n’existe pas, le cluster HDInsight tente de la créer. Assurez-vous que l’unité d’organisation est déjà présente ou que le compte de domaine dispose des autorisations nécessaires pour en créer une. Si vous utilisez le compte de domaine qui fait partie des administrateurs AADDC, il dispose déjà des autorisations nécessaires pour créer l’unité d’organisation.
         * **URL LDAPS** : ldaps://contoso.onmicrosoft.com:636
         * **Accéder au groupe d’utilisateurs** : spécifiez le groupe de sécurité dont vous souhaitez synchroniser les utilisateurs avec le cluster. Par exemple, HiveUsers.
           
           Pour enregistrer les modifications, cliquez sur **Sélectionner**.
           
           ![Configurer les paramètres du domaine du portail HDInsight joint](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Réseau virtuel** : contosohdivnet
       * **Sous-réseau** : Subnet1
         
         Pour enregistrer les modifications, cliquez sur **Sélectionner**.        
         Pour enregistrer les modifications, cliquez sur **Sélectionner**.
   * **Groupe de ressources** : sélectionnez le groupe de ressources utilisé pour le réseau virtuel HDInsight (contosohdirg).
4. Cliquez sur **Create**.  

Pour créer le cluster HDInsight joint au domaine, vous pouvez également utiliser le modèle de gestion des ressources Azure. Appliquez la procédure suivante :

**Pour créer un cluster joint à un domaine à l’aide du modèle de gestion des ressources**

1. Cliquez sur l’image suivante pour ouvrir un modèle Resource Manager dans le portail Azure. Le modèle Resource Manager est situé dans un conteneur blob public. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. À partir du panneau **Paramètres**, saisissez les informations suivantes :
   
   * **Abonnement** : (sélectionnez votre abonnement Azure.)
   * **Groupe de ressources** : cliquez sur **Use existing (Utiliser l’existant)**, et spécifiez le groupe de ressources utilisé.  Par exemple, contosohdirg. 
   * **Emplacement** : spécifiez un emplacement de groupe de ressources.
   * **Nom du cluster** : saisissez un nom pour le cluster Hadoop créé. Par exemple, contosohdicluster.
   * **Type de cluster** : sélectionnez un type de cluster.  La valeur par défaut est **hadoop**.
   * **Emplacement** : sélectionnez un emplacement pour le cluster.  Le compte de stockage par défaut utilise le même emplacement.
   * **Cluster Worker Node count (Nombre de nœuds de travail du cluster)** : sélectionnez le nombre de nœuds de travail.
   * **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est **admin**.
   * **Nom d’utilisateur SSH et mot de passe** : le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez le renommer. 
   * **ID réseau virtuel** : /subscriptions/&lt;SubscriptionID&gt;/resourceGroups/&lt;ResourceGroupName&gt;/providers/Microsoft.Network/virtualNetworks/&lt;VNetName&gt;
   * **Sous-réseau du réseau virtuel** : /subscriptions/&lt;SubscriptionID&gt;/resourceGroups/&lt;ResourceGroupName&gt;/providers/Microsoft.Network/virtualNetworks/&lt;VNetName&gt;/subnets/Subnet1
   * **Nom de domaine** : contoso.onmicrosoft.com
   * **Organization Unit DN (Nom de domaine de l’unité d’organisation)** : UO = HDInsightOU,CD = contoso, CD = onmicrosoft, CD = com
   * **Nom de domaine du groupe d’utilisateurs du cluster** : [\"HiveUsers\"]
   * **LDAPUrls** : ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (saisissez le nom d’utilisateur de l’administrateur du domaine)
   * **DomainAdminPassword** : (saisissez le mot de passe utilisateur de l’administrateur du domaine)
   * **J’accepte les termes et conditions mentionnés ci-dessus** : (vérification)
   * **Épingler au tableau de bord** : (vérification)
3. Cliquez sur **Achat**. Vous verrez une nouvelle vignette intitulée **Déploiement du déploiement de modèle**. La création d’un cluster prend environ 20 minutes. Une fois le cluster créé, vous pouvez cliquer sur le panneau du cluster dans le portail pour l’ouvrir.

Après avoir terminé ce didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Pour obtenir des instructions sur la suppression d’un cluster, consultez [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configuration de stratégies Hive pour les clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md).
* Pour utiliser des clusters HDInsight joints à un domaine, consultez [Utilisation de SSH avec Hadoop sous Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

