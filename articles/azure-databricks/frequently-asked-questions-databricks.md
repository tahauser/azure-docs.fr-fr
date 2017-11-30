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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6bb542537ec713be272f7e58e0b247763214ef4a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Version préliminaire d’Azure Databricks : Questions courantes et aide

Cet article répertorie les principales questions relatives à Azure Databricks. Il répertorie également certains problèmes courants que vous pouvez rencontrer lors de l’utilisation d’Azure Databricks. Pour plus d’informations sur Azure Databricks, consultez [Présentation d’Azure Databricks](what-is-azure-databricks.md) 

## <a name="common-questions"></a>Questions courantes

Cette section répertorie les questions courantes relatives à Azure Databricks.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>Puis-je utiliser mes propres clés de chiffrement local ? 
Dans la version actuelle, l’utilisation de vos propres clés à partir d’Azure Keyvault n’est pas prise en charge. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>Puis-je utiliser des réseaux virtuels Azure avec Azure Databricks ?
Un nouveau réseau virtuel est créé dans le cadre de l’approvisionnement d’Azure Databricks. Dans le cadre de cette version, vous ne pouvez pas utiliser votre propre réseau virtuel Azure.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Comment accéder à Azure Data Lake Store à partir d’un ordinateur portable ? 

1. Dans Azure Active Directory, approvisionnez un principal de service et enregistrez sa clé.
2. Attribuez les autorisations nécessaires au principal de service dans Azure Data Lake Store.
3. Pour accéder à un fichier dans Azure Data Lake Store, utilisez les informations d’identification du principal de service du bloc-notes.

Pour plus d’informations, consultez [Utiliser Data Lake Store avec Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store).

## <a name="troubleshooting"></a>Résolution des problèmes

Cette section décrit comment résoudre les problèmes courants lié à Azure Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problème : Cet abonnement n’est pas inscrit pour utiliser l’espace de noms « Microsoft.Databricks »

**Message d’erreur**

Cet abonnement n’est pas inscrit pour utiliser l’espace de noms « Microsoft.Databricks » Pour découvrir comment inscrire des abonnements, consultez https://aka.ms/rps-not-found. (Code : MissingSubscriptionRegistration)

**Solution**

1. Accédez au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Abonnements**, sur l’abonnement que vous utilisez, puis sur **Fournisseurs de ressources**. 
3. Dans la liste des fournisseurs de ressources, en regard de **Microsoft.Databricks**, cliquez sur **Inscrire**. Vous devez disposer du rôle Contributeur ou Propriétaire sur l’abonnement pour inscrire le fournisseur de ressources.


### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problème : votre compte {e-mail} ne dispose pas du rôle de propriétaire ou de collaborateur sur la ressource de l’espace de travail Databricks dans le portail Azure.

**Message d’erreur**

Votre compte {e-mail} ne dispose pas du rôle de propriétaire ou de collaborateur sur la ressource de l’espace de travail Databricks dans le portail Azure. Cette erreur peut également se produire si vous êtes un utilisateur invité dans le client. Demandez à votre administrateur de vous accorder l’accès ou de vous ajouter en tant qu’utilisateur directement dans l’espace de travail Databricks. 

**Solution**

Voici quelques solutions à ce problème :

* Pour initialiser le client, vous devez être connecté en tant qu’utilisateur normal du client, pas comme utilisateur invité. Vous devez également détenir le rôle de collaborateur sur la ressource de l’espace de travail Databricks. Vous pouvez accorder un accès utilisateur à partir de l’onglet **Contrôle d’accès (IAM)** au sein de votre espace de travail Azure Databricks dans le portail Azure.

* Cette erreur peut également se produire si votre nom de domaine de messagerie est attribué à plusieurs annuaires Active Directory. Pour contourner ce problème, créez un utilisateur dans l’annuaire Active Directory contenant l’abonnement avec votre espace de travail Databricks.

    a. Dans le portail Azure, accédez à Azure Active Directory, cliquez sur **Utilisateurs et groupes**, puis sur **Ajouter un utilisateur**.

    b. Ajoutez un utilisateur avec un e-mail `@<tenant_name>.onmicrosoft.com` au lieu de @<votre_domaine>. Vous trouverez le <tenant_name>. onmicrosoft.com associé à votre annuaire Active Directory dans les **Domaines personnalisés** sous Azure Active Directory dans le portail Azure.
    
    c. Accordez à ce nouvel utilisateur le rôle de **Contributeur** sur la ressource de l’espace de travail Databricks.
    
    d. Connectez-vous au portail Azure avec le nouvel utilisateur et recherchez l’espace de travail Databricks.
    
    e. Lancez l’espace de travail Databricks en tant que cet utilisateur.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problème : votre compte {e-mail} n’a pas été enregistré dans Databricks 

**Solution**

Si vous n’avez pas créé l’espace de travail et que vous êtes ajouté en tant qu’utilisateur de celui-ci, contactez la personne qui a créé l’espace de travail afin qu’elle vous ajoute à l’aide de la Console d’administration Azure Databricks. Pour obtenir des instructions, consultez [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Ajout et gestion des utilisateurs). Si vous avez créé l’espace de travail et que vous obtenez encore cette erreur, essayez de cliquer à nouveau sur Initialiser l’espace de travail à partir du portail Azure.

### <a name="issue-cloud-provider-launch-failure-publicipcountlimitreached-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problème : échec du lancement du fournisseur cloud (PublicIPCountLimitReached) : une erreur de fournisseur de cloud s’est produite lors de la configuration du cluster.

**Message d’erreur**

Échec du lancement du fournisseur cloud : une erreur de fournisseur de cloud s’est produite lors de la configuration du cluster. Consultez le guide relatif à Databricks pour plus d’informations. Code d’erreur Azure : PublicIPCountLimitReached. Message d’erreur Azure : Impossible de créer plus de 60 adresses IP publiques pour cet abonnement dans cette région.

**Solution**

Les clusters Azure Databricks utilisent une adresse IP publique par nœud. Si votre abonnement a déjà utilisé toutes ses adresses IP publiques, vous devez [demander à augmenter le quota](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Choisissez **Quota** comme **Type de problème**, **Mise en réseau : ARM** comme **Type de quota**, et demandez une augmentation du quota d’adresses IP publiques dans **Détails**. Par exemple, si votre limite est actuellement de 60 et que vous souhaitez créer un cluster de 100 nœuds, demandez une augmentation de cette limite à 160.

### <a name="issue-cloud-provider-launch-failure-missingsubscriptionregistration-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problème : échec du lancement du fournisseur cloud (MissingSubscriptionRegistration) : une erreur de fournisseur de cloud s’est produite lors de la configuration du cluster.

**Message d’erreur**

Échec du lancement du fournisseur cloud : une erreur de fournisseur de cloud s’est produite lors de la configuration du cluster. Consultez le guide relatif à Databricks pour plus d’informations.
Code d’erreur Azure : MissingSubscriptionRegistration Message d’erreur Azure : L’abonnement n’est pas inscrit pour utiliser l’espace de noms « Microsoft.Compute ». Pour découvrir comment inscrire des abonnements, consultez https://aka.ms/rps-not-found.

**Solution**

1. Accédez au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Abonnements**, sur l’abonnement que vous utilisez, puis sur **Fournisseurs de ressources**. 
3. Dans la liste des fournisseurs de ressources, en regard de **Microsoft.Compute**, cliquez sur **Inscrire**. Vous devez disposer du rôle Contributeur ou Propriétaire sur l’abonnement pour inscrire le fournisseur de ressources.

Pour obtenir des instructions plus détaillées, consultez [Fournisseurs et types de ressources](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des instructions pas à pas pour créer une fabrique de données de version 2, consultez les didacticiels suivants :

- [Démarrage rapide : Prise en main d’Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Présentation d’Azure Databricks](what-is-azure-databricks.md)

