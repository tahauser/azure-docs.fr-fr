---
title: "Configurer les clusters HDInsight joints à un domaine - Azure | Documents Microsoft"
description: "Découvrez comment configurer les clusters HDInsight joints à un domaine"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: cgronlun
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/15/2018
ms.author: saurinsh
ms.openlocfilehash: b4d71eeb0aab75e67e851f867f194ed7578d0d1c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>Configurer un environnement de bac à sable HDInsight joint à un domaine

Découvrez comment configurer un cluster Azure HDInsight avec un annuaire Active Directory autonome et [Apache Ranger](http://hortonworks.com/apache/ranger/) pour valoriser les stratégies d’authentification forte et de contrôle de l’accès enrichi basé sur les rôles. Pour plus d’informations, consultez [Introduire des clusters HDInsight joints à un domaine](apache-domain-joined-introduction.md). 

> [!IMPORTANT]
> Par défaut, cette configuration peut uniquement servir pour l’utilisation de comptes de stockage Azure. Pour l’utiliser avec Azure Data Lake Store, synchronisez Active Directory avec une nouvelle instance d’Azure Active Directory.

Sans cluster HDInsight joint à un domaine, chaque cluster peut uniquement avoir un compte d’utilisateur HTTP Hadoop et un compte d’utilisateur SSH.  L’authentification de plusieurs utilisateurs peut être effectuée à l’aide des éléments suivants :

-   Un annuaire Active Directory autonome s’exécutant sur Azure IaaS
-   Azure Active Directory

Cet article couvre l’utilisation d’un annuaire Active Directory autonome s’exécutant sur Azure IaaS. Il s’agit de l’architecture la plus simple qu’un client peut suivre pour la prise en charge de plusieurs utilisateurs sur HDInsight. Cet article aborde deux approches pour cette configuration :

- Option 1 : Utiliser un modèle de gestion de ressources Azure pour créer l’annuaire Active Directory autonome et le cluster HDInsight.
- Option 2 : L’ensemble du processus comprend les étapes suivantes :
    - Créer un annuaire Active Directory à l’aide d’un modèle
    - Configurer LDAPS
    - Créer des utilisateurs et des groupes AD
    - Créer un cluster HDInsight

> [!IMPORTANT]
> 
> Oozie n’est pas activé sur HDInsight joint à un domaine.

## <a name="prerequisite"></a>Configuration requise
* Abonnement Azure

## <a name="option-1-one-step-approach"></a>Option 1 : Approche en une étape
Dans cette section, vous ouvrez un modèle de gestion de ressources Azure à partir du portail Azure. Le modèle est utilisé pour créer un annuaire Active Directory autonome et un cluster HDInsight. Vous pouvez créer un cluster Hadoop joint à un domaine, un cluster Spark et un cluster Interactive Query.

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure. Ce modèle se trouve dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/).
   
    Pour créer un cluster Spark :

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Pour créer un cluster Interactive Query :

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Pour créer un cluster Hadoop :

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Entrez les valeurs, sélectionnez **J’accepte les termes et conditions mentionnés ci-dessus** et **Épingler au tableau de bord**, puis cliquez sur **Acheter**. Placez le curseur de votre souris sur le signe d’information en regard des champs dont vous souhaitez lire une description. La plupart des valeurs ont été remplies. Vous pouvez utiliser les valeurs par défaut ou vos propres valeurs.

    - **Groupe de ressources** : entrez un nom pour le groupe de ressources Azure.
    - **Emplacement** : sélectionnez un emplacement proche.
    - **Nom du nouveau compte de stockage** : entrez un nom de compte de stockage Azure. Ce nouveau compte de stockage est utilisé par le contrôleur de domaine principal, le catalogue de données métiers et le cluster HDInsight comme compte de stockage par défaut.
    - **Nom d’utilisateur de l’administrateur** : entrez le nom d’utilisateur de l’administrateur du domaine.
    - **Mot de passe d’administrateur** : entrez le mot de passe de l’administrateur du domaine.
    - **Nom de domaine** : le nom par défaut est *contoso.com*.  Si vous changez le nom de domaine, vous devez également mettre à jour les champs **Certificat LDAP sécurisé** et **Nom unique de l’unité d’organisation**.
    - **Préfixe DNS** : entrez le préfixe DNS de l’adresse IP publique utilisée par l’équilibreur de charge.
    - **Nom du cluster** : entrez le nom du cluster HDInsight.
    - **Type de cluster** : ne changez pas cette valeur. Si vous souhaitez changer le type de cluster, utilisez le modèle prévu à cet effet à la dernière étape.
    - **Mot de passe du certificat LDAP sécurisé** : utilisez la valeur par défaut, sauf si vous modifiez le champ du certificat LDAP sécurisé.
    Certaines valeurs sont codées en dur dans le modèle, par exemple, le nombre d’instances de nœud Worker est deux.  Pour changer les valeurs codées en dur, cliquez sur **Modifier le modèle**.

    ![Cluster joint à un domaine HDInsight, modifier le modèle](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

Une fois le modèle correctement terminé, 23 ressources sont créées dans le groupe de ressources.

## <a name="option-2-multi-step-approach"></a>Option 2 : Approche en plusieurs étapes

Cette section comprend quatre étapes :

1. Créer un annuaire Active Directory à l’aide d’un modèle
2. Configurer LDAPS
3. Créer des utilisateurs et des groupes AD
4. Créer un cluster HDInsight

### <a name="create-an-active-directory"></a>Créer un annuaire Active Directory

Le modèle Azure Resource Manager facilite la création de ressources Azure. Dans cette section, vous utilisez un [modèle de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/) pour créer une forêt et un domaine avec deux machines virtuelles. Les deux machines virtuelles servent de contrôleur de domaine principal et de contrôleur secondaire de domaine.

**Pour créer un domaine avec deux contrôleurs de domaine**

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Le modèle ressemble à ceci :

    ![Cluster HDInsight joint à un domaine, créer un domaine et une forêt avec des machines virtuelles](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. Saisissez les valeurs suivantes :

    - **Abonnement**: sélectionnez un abonnement Azure.
    - **Nom du groupe de ressources** : tapez un nom de groupe de ressources.  Un groupe de ressources est utilisé pour gérer vos ressources Azure qui sont associées à un projet.
    - **Emplacement** : choisissez un emplacement Azure proche.
    - **Nom d’utilisateur de l’administrateur** : il s’agit du nom d’utilisateur de l’administrateur du domaine. Cet utilisateur n’est pas le compte d’utilisateur HTTP de votre cluster HDInsight. Il s’agit du compte que vous utilisez tout au long du didacticiel.
    - **Mot de passe d’administrateur** : entrez le mot de passe de l’administrateur du domaine.
    - **Nom de domaine** : le nom de domaine doit être un nom en deux parties. Par exemple : contoso.com, contoso.local ou hdinsight.test.
    - **Préfixe DNS** : tapez un préfixe DNS.
    - **Port RDP CDP** : utilisez la valeur par défaut pour ce didacticiel.
    - **Port RDP CDM** : utilisez la valeur par défaut pour ce didacticiel.
    - **Emplacement des artefacts** : utilisez la valeur par défaut pour ce didacticiel.
    - **Jeton SAP d’emplacement des artefacts** : laissez ce champ vide pour ce didacticiel.

La création des ressources prend environ 20 minutes.

### <a name="setup-ldaps"></a>Configurer LDAPS

Le protocole LDAP (Lightweight Directory Access Protocol) est utilisé pour la lecture et l’écriture sur AD.

**Pour vous connecter au contrôleur de domaine principal en utilisant le Bureau à distance**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez le groupe de ressources, puis ouvrez la machine virtuelle faisant office de contrôleur de domaine principal. Le nom par défaut du contrôleur de domaine principal est adPDC. 
3. Cliquez sur **Se connecter** pour vous connecter au contrôleur de domaine principal en utilisant le Bureau à distance.

    ![Cluster joint à un domaine HDInsight, se connecter au contrôleur de domaine principal en utilisant le Bureau à distance](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Pour ajouter les services de certificats Active Directory**

4. Si ce n’est pas déjà fait, ouvrez le **Gestionnaire de serveur**.
5. Cliquez sur **Gérer**, puis sur **Ajoutez des rôles et des fonctionnalités**.

    ![Cluster joint à un domaine HDInsight, ajouter des rôles et des fonctionnalités](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. Dans « Avant de commencer », cliquez sur Suivant.
6. Sélectionnez **Installation basée sur un rôle ou une fonctionnalité**, puis cliquez sur **Suivant**.
7. Sélectionnez le contrôleur de domaine principal, puis cliquez sur **Suivant**.  Le nom par défaut du contrôleur de domaine principal est adPDC.
8. Sélectionnez **Services de certificats Active Directory**.
9. Cliquez sur **Ajouter des fonctionnalités** à partir de la boîte de dialogue contextuelle.
10. Suivez l’Assistant, en utilisant les paramètres par défaut pour le reste de la procédure.
11. Cliquez sur **Fermer** pour fermer l’assistant.

**Pour configurer un certificat AD**

1. Dans le Gestionnaire de serveur, cliquez sur l’icône de notification jaune, puis cliquez sur **Configurer les services de certificats Active Directory**.

    ![Cluster joint à un domaine HDInsight, configurer un certificat AD](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. Cliquez sur **Services de rôle** sur la gauche, sélectionnez **Autorité de certification**, puis cliquez sur **Suivant**.
3. Suivez l’Assistant, en utilisant les paramètres par défaut pour le reste de la procédure (cliquez sur **Configurer** à la dernière étape).
4. Cliquez sur **Fermer** pour fermer l’assistant.

### <a name="optional-create-ad-users-and-groups"></a>(Facultatif) Créer des utilisateurs et des groupes AD

**Pour créer des utilisateurs et des groupes dans l’annuaire AD**
1. Connectez-vous au contrôleur de domaine principal en utilisant le Bureau à distance.
1. Ouvrez **Utilisateurs et ordinateurs Active Directory**.
2. Sélectionnez votre nom de domaine dans le volet de gauche.
3. Cliquez sur l’icône **Créer un nouvel utilisateur dans le conteneur actuel** dans le menu supérieur.

    ![Cluster joint à un domaine HDInsight, créer des utilisateurs](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. Suivez les instructions pour créer quelques utilisateurs. Par exemple, hiveuser1 et hiveuser2.
5. Cliquez sur l’icône **Créer un nouveau groupe dans le conteneur actuel** dans le menu supérieur.
6. Suivez les instructions pour créer un groupe nommé **HDInsightUsers**.  Ce groupe sera utilisé quand vous créerez un cluster HDInsight plus loin dans ce didacticiel.

> [!IMPORTANT]
> Vous devez redémarrer la machine virtuelle faisant office de contrôleur de domaine principal avant de créer un cluster HDInsight joint à un domaine.

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>Créer un cluster HDInsight dans le réseau virtuel

Dans cette section, vous utilisez le portail Azure pour ajouter un cluster HDInsight dans le réseau virtuel que vous avez créé à l’aide du modèle Resource Manager dans le didacticiel. Cet article couvre uniquement les informations spécifiques à la configuration d’un cluster joint à un domaine.  Pour plus d’informations générales, consultez [Création de clusters Linux dans HDInsight à l’aide du portail Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).  

**Pour créer un cluster HDInsight joint à un domaine**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez le groupe de ressources que vous avez créé à l’aide du modèle Resource Manager dans le didacticiel.
3. Ajoutez un cluster HDInsight au groupe de ressources.
4. Sélectionnez l’option **Personnalisé** :

    ![Cluster joint à un domaine HDInsight, créer, option de création personnalisée](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    L’option de configuration personnalisée comprend six sections : Bases, Stockage, Application, Taille du cluster, Paramètres avancés et Résumé.
5. Dans la section **Bases** :

    - Type de cluster : sélectionnez **Package de sécurité d’entreprise**. Actuellement le package de sécurité d’entreprise ne peut être activé que pour les types de cluster suivants : Hadoop, Interactive Query et Spark.

        ![Cluster joint à un domaine HDInsight, package de sécurité d’entreprise](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - Nom d’utilisateur de connexion du cluster : il s’agit de l’utilisateur Hadoop HTTP. Ce compte est différent du compte d’administrateur de domaine.
    - Groupe de ressources : sélectionnez le groupe de ressources que vous avez créé à l’aide du modèle Resource Manager.
    - Emplacement : l’emplacement doit être le même que celui que vous avez utilisé quand vous avez créé le réseau virtuel et les contrôleurs de domaine à l’aide du modèle Resource Manager.

6. Dans la section **Paramètres avancés** :

    - Paramètres de domaine :

        ![Cluster joint à un domaine HDInsight, paramètres avancés, domaine](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - Nom de domaine : entrez le nom de domaine que vous avez utilisé dans [Créer un annuaire Active Directory](#create-an-active-directory).
        - Nom d’utilisateur de domaine : entrez le nom d’utilisateur de l’administrateur AD que vous avez utilisé dans [Créer un annuaire Active Directory](#create-an-active-directory).
        - Unité d’organisation : consultez la capture d’écran pour obtenir un exemple.
        - URL LDAPS : consultez la capture d’écran pour obtenir un exemple.
        - Accéder au groupe d’utilisateurs : entrez le nom de groupe d’utilisateurs que vous avez créé dans [Créer des utilisateurs et des groupes AD](#optionally-createad-users-and-groups).
    - Réseau virtuel : sélectionnez le réseau virtuel que vous avez créé dans [Créer un annuaire Active Directory](#create-an-active-directory). Le nom par défaut utilisé dans le modèle est **adVNET**.
    - Sous-réseau : le nom par défaut utilisé dans le modèle est **adSubnet**.



Après avoir terminé ce didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Pour obtenir des instructions sur la suppression d’un cluster, consultez [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configuration de stratégies Hive pour les clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md).
* Pour utiliser des clusters HDInsight joints à un domaine, consultez [Utilisation de SSH avec Hadoop sous Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

