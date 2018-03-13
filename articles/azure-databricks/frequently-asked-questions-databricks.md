---
title: 'Azure Databricks : Questions courantes et aide | Microsoft Docs'
description: "Obtenez des réponses aux questions courantes et des informations de dépannage sur Azure Databricks."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: nitinme
ms.openlocfilehash: 5da6ffc346cc0e7f0f83bf4a4c33600b668a17ca
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Forum aux Questions sur Azure Databricks

Cet article répertorie les principales questions relatives à Azure Databricks. Il répertorie également certains problèmes éventuellement rencontrés avec l’utilisation de Databricks. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>Puis-je utiliser mes propres clés de chiffrement local ? 
Dans la version actuelle, l’utilisation de vos propres clés à partir d’Azure Key Vault n’est pas prise en charge. 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Puis-je utiliser les réseaux virtuels Azure avec Databricks ?
Un nouveau réseau virtuel est créé dans le cadre de l’approvisionnement de Databricks. Dans cette version, vous ne pouvez pas utiliser votre propre réseau virtuel Azure.

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Comment accéder à Azure Data Lake Store à partir d’un ordinateur portable ? 

Procédez comme suit :
1. Dans Azure Active Directory (Azure AD), approvisionnez un principal de service et enregistrez sa clé.
2. Attribuez les autorisations nécessaires au principal de service dans Data Lake Store.
3. Pour accéder à un fichier dans Data Lake Store, utilisez les informations d’identification du principal de service dans Notebook.

Pour plus d’informations, consultez [Utiliser Data Lake Store avec Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Correction des problèmes courants

Vous trouverez ci-après certains problèmes éventuellement rencontrés avec Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problème : Cet abonnement n’est pas inscrit pour utiliser l’espace de noms « Microsoft.Databricks »

#### <a name="error-message"></a>Message d’erreur

« Cet abonnement n’est pas inscrit pour utiliser l’espace de noms « Microsoft.Databricks ». Pour découvrir comment inscrire des abonnements, consultez https://aka.ms/rps-not-found. (Code : MissingSubscriptionRegistration) »

#### <a name="solution"></a>Solution

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Abonnements**, l’abonnement que vous utilisez, puis **Fournisseurs de ressources**. 
3. Dans la liste des fournisseurs de ressources, en regard de **Microsoft.Databricks**, cliquez sur **S’inscrire**. Vous devez disposer du rôle de Collaborateur ou Propriétaire sur l’abonnement pour inscrire le fournisseur de ressources.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problème : Votre compte {e-mail} ne dispose pas du rôle de propriétaire ou de collaborateur sur la ressource de l’espace de travail Databricks dans le portail Azure

#### <a name="error-message"></a>Message d’erreur

« Votre compte {e-mail} ne dispose pas du rôle Propriétaire ou Collaborateur sur la ressource de l’espace de travail Databricks dans le portail Azure. Cette erreur peut également se produire si vous êtes un utilisateur invité dans le client. Demandez à votre administrateur de vous accorder l’accès ou de vous ajouter en tant qu’utilisateur directement dans l’espace de travail Databricks. » 

#### <a name="solution"></a>Solution

Voici quelques solutions à ce problème :

* Pour initialiser le locataire, vous devez être connecté en tant qu’utilisateur normal du locataire, pas comme utilisateur invité. Vous devez également détenir le rôle de collaborateur sur la ressource de l’espace de travail Databricks. Vous pouvez accorder un accès utilisateur à partir de l’onglet **Contrôle d’accès (IAM)** au sein de votre espace de travail Databricks dans le portail Azure.

* Cette erreur peut également se produire si votre nom de domaine de messagerie est attribué à plusieurs répertoires dans Azure AD. Pour contourner ce problème, créez un nouvel utilisateur dans le répertoire contenant l’abonnement avec votre espace de travail Databricks.

    a. Dans le portail Azure, allez à Azure AD. Sélectionnez **Utilisateurs et Groupes** > **Ajouter un utilisateur**.

    b. Ajoutez un utilisateur avec un e-mail `@<tenant_name>.onmicrosoft.com` au lieu d’un e-mail `@<your_domain>`. Vous trouverez cette option dans **Domaines personnalisés**, sous Azure AD dans le portail Azure.
    
    c. Accordez à ce nouvel utilisateur le rôle de **Collaborateur** sur la ressource de l’espace de travail Databricks.
    
    d. Connectez-vous au portail Azure avec le nouvel utilisateur et recherchez l’espace de travail Databricks.
    
    e. Lancez l’espace de travail Databricks en tant que cet utilisateur.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problème : votre compte {e-mail} n’a pas été enregistré dans Databricks 

#### <a name="solution"></a>Solution

Si vous n’avez pas créé l’espace de travail et que vous êtes ajouté en tant qu’utilisateur, contactez la personne qui a créé l’espace de travail. Demandez à cette personne de vous ajouter à l’aide de la console administrateur Azure Databricks. Pour obtenir des instructions, consultez [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Ajout et gestion des utilisateurs). Si vous avez créé l’espace de travail et que vous rencontrez encore cette erreur, essayez de cliquer à nouveau sur **Initialiser l’espace de travail** à partir du portail Azure.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problème : Échec du lancement du fournisseur de cloud lors de la configuration du cluster (PublicIPCountLimitReached)

#### <a name="error-message"></a>Message d’erreur

« Échec du lancement du fournisseur de cloud : une erreur de fournisseur de cloud s’est produite lors de la configuration du cluster. Pour plus d’informations, consultez le guide relatif à Databricks. Code d’erreur Azure : PublicIPCountLimitReached. Message d’erreur Azure : impossible de créer plus de 60 adresses IP publiques pour cet abonnement dans cette région. »

#### <a name="solution"></a>Solution

Les clusters Databricks utilisent une adresse IP publique par nœud. Si votre abonnement a déjà utilisé toutes ses adresses IP publiques, vous devez [demander à augmenter le quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Sélectionnez **Quota** en tant que **Type de problème** et **Mise en réseau : ARM** en tant que **Type de quota**. Dans **Détails**, demandez une augmentation du quota d’adresses IP publiques. Par exemple, si votre limite est actuellement de 60 et que vous souhaitez créer un cluster de 100 nœuds, demandez une augmentation de cette limite à 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problème : Un second type d’échec du lancement du fournisseur de cloud lors de la configuration du cluster (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Message d’erreur

« Échec du lancement du fournisseur de cloud : une erreur de fournisseur de cloud s’est produite lors de la configuration du cluster. Pour plus d’informations, consultez le guide relatif à Databricks.
Code d’erreur Azure : MissingSubscriptionRegistration Message d’erreur Azure : L’abonnement n’est pas inscrit pour utiliser l’espace de noms « Microsoft.Compute ». Pour découvrir comment inscrire des abonnements, consultez https://aka.ms/rps-not-found. »

#### <a name="solution"></a>Solution

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Abonnements**, l’abonnement que vous utilisez, puis **Fournisseurs de ressources**. 
3. Dans la liste des fournisseurs de ressources, en regard de **Microsoft.Compute**, cliquez sur **S’inscrire**. Vous devez disposer du rôle de Collaborateur ou Propriétaire sur l’abonnement pour inscrire le fournisseur de ressources.

Pour obtenir des instructions plus détaillées, consultez [Fournisseurs et types de ressources](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problème : Pour accéder aux ressources de votre organisation, Azure Databricks a besoin d’autorisations que seul un administrateur peut accorder.

#### <a name="background"></a>Arrière-plan

Azure Databricks est intégré à Azure AD. Cela vous permet de définir des autorisations dans Azure Databricks (par exemple, sur des ordinateurs portables ou des clusters) en spécifiant les utilisateurs d’Azure AD. Pour qu’Azure Databricks soit en mesure de répertorier les noms des utilisateurs à partir d’Azure AD, il doit disposer de l’autorisation de lire ces informations. Cela nécessite votre consentement. Si le consentement n’est pas encore disponible, l’erreur s’affiche.

#### <a name="solution"></a>Solution

Connectez-vous au portail Azure en tant qu’administrateur. Pour Azure Active Directory, accédez à l’onglet **Paramètres utilisateur** et vérifiez que l’option **Les utilisateurs peuvent autoriser les applications à accéder aux données de l’entreprise en leur nom** est définie sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Prise en main d’Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Présentation d’Azure Databricks](what-is-azure-databricks.md)

