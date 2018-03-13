---
title: 'Azure AD Connect : authentification directe - Verrouillage intelligent | Documents Microsoft'
description: "Cet article décrit comment l’authentification directe Azure Active Directory (Azure AD) protège vos comptes locaux contre les attaques de mot de passe par force brute dans le cloud"
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: billmath
ms.openlocfilehash: fc46fe1d68538757ba5a8c5aa1acb4b51f8a171b
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Authentification directe Azure Active Directory : Verrouillage intelligent

## <a name="overview"></a>Vue d'ensemble

Azure Active Directory (Azure AD) protège contre les attaques de mot de passe par force brute et empêche le verrouillage des utilisateurs authentiques sur leurs applications Office 365 et SaaS. Cette fonctionnalité, appelée *Verrouillage intelligent*, est pris en charge lorsque vous utilisez l’authentification directe en tant que méthode de connexion. Le verrouillage intelligent est activé par défaut pour tous les locataires, et il protège en permanence vos comptes d’utilisateur.

Le verrouillage intelligent suit les tentatives de connexion ayant échouées. Après un *seuil de verrouillage* donné, il démarre une *durée de verrouillage*. Le verrouillage intelligent rejette toutes les tentatives de connexion de l’attaquant pendant la durée de verrouillage. Si l’attaque se poursuit, une fois la durée de verrouillage terminée, les durées de verrouillage des tentatives de connexion ayant échoué suivantes sont plus longues.

>[!NOTE]
>Le seuil de verrouillage par défaut est de 10 tentatives ayant échoué. La durée de verrouillage par défaut est de 60 secondes.

Le verrouillage intelligent fait également la distinction entre les connexions d’utilisateurs authentiques et d’attaquants. Dans la plupart des cas, il ne verrouille que les attaquants. Cette fonctionnalité empêche les attaquants de verrouiller à des fins malveillantes des utilisateurs authentiques. Le verrouillage intelligent utilise l’ancien comportement de connexion, les appareils et les navigateurs des utilisateurs et d’autres signaux pour faire la distinction entre les utilisateurs authentiques et les attaquants. Les algorithmes sont constamment améliorés.

L’authentification directe transfère les requêtes de validation de mot de passe à l’Active Directory (AD) local, vous devez donc empêcher les attaquants de verrouiller les comptes de vos utilisateurs Active Directory. Active Directory possède des stratégies de verrouillage de compte propres, notamment les stratégiques [Seuil de verrouillage de compte](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) et [Réinitialiser le compteur de verrouillage de compte après](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx). Configurez les valeurs de seuil de verrouillage et de durée de verrouillage d’Azure AD de manière appropriée afin de filtrer les attaques dans le cloud avant qu’elles atteignent l’Active Directory local.

>[!NOTE]
>>La fonctionnalité Smart Lockout est gratuite et _activée_ par défaut pour tous les clients. Toutefois, la modification du seuil de verrouillage et des valeurs de durée de verrouillage d’Azure AD à l’aide de l’API Graph oblige votre locataire à avoir une licence Azure AD Premium P2. 

Pour vous assurer que les comptes Active Directory locaux de vos utilisateurs sont correctement protégés, vous devez vérifier que :

   * Le seuil de verrouillage d’Azure AD est _inférieur_ au seuil de verrouillage de compte Active Directory. Définissez les valeurs de sorte que le seuil de verrouillage de compte Active Directory soit au moins deux ou trois fois supérieur au seuil de verrouillage d’Azure AD.
   * La durée de verrouillage d’Azure AD (exprimée en secondes) est _plus longue_ que la durée après laquelle réinitialiser le compteur de verrouillage de compte Active Directory (exprimée en minutes).

>[!IMPORTANT]
>Actuellement, un administrateur ne peut pas déverrouiller les comptes cloud des utilisateurs si ceux-ci ont été verrouillés à l’aide de la fonctionnalité Verrouillage intelligent. L’administrateur doit attendre que la durée de verrouillage expire.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Vérifier vos stratégies de verrouillage de compte Active Directory

Utilisez les instructions suivantes pour vérifier vos stratégies de verrouillage de compte Active Directory :

1.  Ouvrez l’outil de gestion de stratégie de groupe.
2.  Modifiez la stratégie de groupe appliquée à tous les utilisateurs, par exemple la **stratégie de domaine par défaut**.
3.  Accédez à **Configuration de l’ordinateur** > **Stratégies** > **Paramètres Windows** > **Paramètres de sécurité** > **Stratégies de compte** > **Stratégie de verrouillage de compte**.
4.  Vérifiez vos valeurs de **Seuil de verrouillage de compte** et **Réinitialiser le compteur de verrouillage de compte après**.

![Stratégies de verrouillage de compte Active Directory](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Utilisez l’API Graph pour gérer les valeurs de verrouillage intelligent de votre locataire (requiert une licence Premium)

>[!IMPORTANT]
>Modifier les valeurs de seuil de verrouillage et de durée de verrouillage d’Azure AD à l’aide de l’API Graph est une fonctionnalité d’Azure AD Premium P2. Vous devez également être un administrateur global sur votre locataire.

Vous pouvez utiliser l’[Explorateur graphique](https://developer.microsoft.com/graph/graph-explorer) pour lire, définir et mettre à jour les valeurs de verrouillage intelligent d’Azure AD. Vous pouvez également effectuer ces opérations par programmation.

### <a name="view-smart-lockout-values"></a>Afficher les valeurs de verrouillage intelligent

Suivez ces étapes pour afficher les valeurs de verrouillage intelligent de votre locataire :

1. Connectez-vous à l’Explorateur graphique en tant qu’administrateur global de votre locataire. Si vous y êtes invité, accordez l’accès pour les autorisations demandées.
2. Sélectionnez **Modifier les autorisations**, puis sélectionnez l’autorisation **Directory.ReadWrite.All**.
3. Configurez la requête d’API Graph comme suit : définissez la version sur **BETA**, le type de requête sur **GET** et l’URL sur `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Sélectionnez **Exécuter la requête** pour consulter les valeurs de verrouillage intelligent de votre locataire. Si vous n’avez pas défini les valeurs de votre client avant, un ensemble vide s’affiche.

### <a name="set-smart-lockout-values"></a>Définir les valeurs de verrouillage intelligent

Suivez comme suit pour définir les valeurs de verrouillage intelligent de votre locataire (requis pour la première fois uniquement) :

1. Connectez-vous à l’Explorateur graphique en tant qu’administrateur global de votre locataire. Si vous y êtes invité, accordez l’accès pour les autorisations demandées.
2. Sélectionnez **Modifier les autorisations**, puis sélectionnez l’autorisation **Directory.ReadWrite.All**.
3. Configurez la requête d’API Graph comme suit : définissez la version sur **BETA**, le type de requête sur **POST** et l’URL sur `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copiez et collez la requête JSON suivante dans le champ **Corps de la requête**.
5. Sélectionnez **Exécuter la requête** pour définir les valeurs de verrouillage intelligent de votre locataire.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Si vous ne les utilisez pas, vous pouvez laisser les valeurs **BannedPasswordList** et **EnableBannedPasswordCheck** vides (**""**) et **false** respectivement.

Vérifiez que vous avez défini les valeurs de verrouillage intelligent de votre locataire correctement à l’aide des étapes dans [Afficher les valeurs de verrouillage intelligent](#view-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Mettre à jour les valeurs de verrouillage intelligent

Suivez ces étapes pour mettre à jour les valeurs de verrouillage intelligent de votre locataire (si vous les avez définies avant) :

1. Connectez-vous à l’Explorateur graphique en tant qu’administrateur global de votre locataire. Si vous y êtes invité, accordez l’accès pour les autorisations demandées.
2. Sélectionnez **Modifier les autorisations**, puis sélectionnez l’autorisation **Directory.ReadWrite.All**.
3. [Suivez ces étapes pour afficher les valeurs de verrouillage intelligent de votre locataire](#view-smart-lockout-values). Copiez la valeur `id` (un GUID) de l’élément avec **displayName** en tant que **PasswordRuleSettings**.
4. Configurez la requête d’API Graph comme suit : définissez la version sur **BETA**, le type de requête sur **PATCH** et l’URL sur `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. Utilisez le GUID de l’étape 3 pour `<id>`.
5. Copiez et collez la requête JSON suivante dans le champ **Corps de la requête**. Modifiez les valeurs de verrouillage intelligent comme il convient.
6. Sélectionnez **Exécuter la requête** pour mettre à jour les valeurs de verrouillage intelligent de votre locataire.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Vérifiez que vous avez mis à jour les valeurs de verrouillage intelligent de votre locataire correctement à l’aide des étapes dans [Afficher les valeurs de verrouillage intelligent](#view-smart-lockout-values).

## <a name="next-steps"></a>Étapes suivantes
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : utilisez le Forum Azure Active Directory pour consigner de nouvelles demandes de fonctionnalités.
