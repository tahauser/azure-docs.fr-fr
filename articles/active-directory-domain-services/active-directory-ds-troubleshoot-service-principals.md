---
title: "Azure Active Directory Domain Services : Dépannage de la configuration du principal de service | Microsoft Docs"
description: "Résolution des problèmes de configuration du principal de service pour les services de domaine Azure AD"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: e6144a4018f7fbe7dbf7b4693e3f41884e4a80a2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-service-principal-configuration"></a>Services de domaine Azure AD - Résolution des problèmes de configuration du principal de service

Pour travailler sur, gérer et mettre à jour votre domaine, Microsoft utilise divers [principaux de service](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects) pour communiquer avec votre annuaire. Si un d’entre eux est mal configuré ou supprimé, cela peut provoquer une interruption de votre service.

## <a name="aadds102-service-principal-not-found"></a>AADDS102 : Principal de service introuvable

**Message d'alerte :**

*Un principal de service requis pour que les services de domaine Azure AD fonctionnent correctement a été supprimé de votre annuaire Azure AD. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine géré.*

Les principaux de service sont des applications que Microsoft utilise pour gérer, mettre à jour et maintenir votre domaine géré. S’ils sont supprimés, ils interrompent la capacité de Microsoft à travailler sur votre domaine. Utilisez les étapes suivantes pour déterminer les services principaux qui doivent être recréés.

1. Accédez à la page [Applications d’entreprise - Toutes les applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) dans le portail Azure.
2. Dans la liste déroulante Afficher, sélectionnez **Toutes les applications** et cliquez sur **Appliquer**.
3. Dans le tableau suivant, recherchez chaque ID d’application en collant l’ID dans la zone de recherche et en appuyant sur Entrée. Si les résultats de recherche sont vides, vous devez recréer le principal du service en suivant les étapes décrites dans la colonne « Résolution ».

| ID de l'application | Résolution : |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recréer un principal de service manquant avec PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Réinscrire l’espace de noms Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Réinscrire l’espace de noms Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Principaux de service avec correction autonome](#service-principals-that-self-correct) |

### <a name="recreate-a-missing-service-principal-with-powershell"></a>Recréer un principal de service manquant avec PowerShell

*Suivez ces étapes pour les ID manquants : 2565bd9d-da50-47d4-8b85-4c97f669dc36*

**Correction :**

Vous avez besoin d’Azure AD PowerShell pour effectuer ces étapes. Pour plus d’informations sur l’installation d’Azure AD PowerShell, consultez [cet article](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Pour résoudre ce problème, saisissez les commandes suivantes dans une fenêtre PowerShell :
1. Install-Module AzureAD
2. Import-Module AzureAD
3. Saisissez la commande PowerShell suivante pour vérifier si le principal du service requis pour Azure AD Domain Services est absent de votre annuaire Azure AD :
      ```PowerShell
      Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
      ```
4. Créez le principal de service en saisissant la commande PowerShell suivante :
     ```PowerShell
     New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
     ```
5. Après avoir créé le principal de service manquant, attendez deux heures et vérifiez l’intégrité de votre domaine.


### <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Réinscrivez l’espace de noms Microsoft AAD via le portail Azure

*Suivez ces étapes pour les ID manquants : 443155a6-77f3-45e3-882b-22b3a8d431fb et abba844e-bc0e-44b0-947a-dc74e5d09022*

**Correction :**

Pour restaurer les services de domaine sur votre annuaire, procédez comme suit :

1. Accédez à la page [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) dans le portail Azure.
2. Choisissez l’abonnement dans la table qui est associée à votre domaine géré
3. À l’aide de la navigation de gauche, choisissez **Fournisseurs de ressources**
4. Recherchez « Microsoft.AAD » dans la table et cliquez sur **Réinscrire**
5. Pour vérifier que votre alerte est résolue, affichez votre page d’alerte d’intégrité deux heures plus tard.


### <a name="service-principals-that-self-correct"></a>Principaux de service avec correction autonome

*Suivez ces étapes pour les ID manquants : d87dcbc6-a371-462e-88e3-28ad15ec4e64*

**Correction :**

Microsoft peut déterminer lorsque des principaux de service spécifiques sont manquants, mal configurés ou supprimés. Pour remettre le service en état rapidement, Microsoft recrée les principaux de service lui-même. Vérifiez l’intégrité de votre domaine au bout de deux heures pour vous assurer que le principal a été recréé.

## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
