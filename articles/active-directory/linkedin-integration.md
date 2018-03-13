---
title: "Activer ou désactiver l’intégration LinkedIn pour les applications Office dans Azure Active Directory | Microsoft Docs"
description: "Explique comment activer et désactiver l’intégration LinkedIn aux applications Microsoft dans Azure Active Director"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/30/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 5ebc44d0ef6200baeacf4f1f8c4371e2d1eed9db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Intégration LinkedIn pour les applications Office
Cet article vous indique comment restreindre les utilisateurs qui bénéficient de l’intégration LinkedIn dans Azure Active Directory (Azure AD). L’intégration de LinkedIn est activée par défaut lorsqu’elle est ajoutée à votre locataire, ce qui permet aux utilisateurs d’accéder aux données LinkedIn publiques dans certaines de leurs applications Microsoft. Chaque utilisateur peut choisir de connecter son compte professionnel ou scolaire à son compte LinkedIn.

> [!IMPORTANT]
> L’intégration de LinkedIn n'est pas déployée sur tous les locataires Azure AD en même temps. Une fois transférée vers votre locataire Azure, l’intégration LinkedIn est activée par défaut. L’intégration LinkedIn n’est pas disponible pour les locataires GoLocal, Sovereign et Government. 

## <a name="linkedin-integration-from-the-user-perspective"></a>Intégration LinkedIn du point de vue de l’utilisateur
Lorsque les utilisateurs de votre organisation connectent leur compte LinkedIn à leur compte professionnel ou scolaire, [ils autorisent LinkedIn à fournir des données](https://www.linkedin.com/help/linkedin/answer/84077) qui seront utilisées dans les applications et services Microsoft fournis par votre organisation. [Les utilisateurs peuvent déconnecter des comptes](https://www.linkedin.com/help/linkedin/answer/85097), retirant ainsi à LinkedIn l’autorisation de partager des données avec Microsoft. L’intégration LinkedIn utilise des informations de profil LinkedIn accessibles au public. [Les utilisateurs peuvent contrôler la manière dont leur profil LinkedIn est affiché](https://www.linkedin.com/help/linkedin/answer/83) à l’aide des paramètres de confidentialité LinkedIn, et notamment choisir si leur profil peut apparaître dans les applications Microsoft.

## <a name="privacy-considerations"></a>Considérations relatives à la confidentialité
L’activation de l’intégration LinkedIn dans Azure AD permet aux applications et aux services Microsoft d’accéder à certaines informations LinkedIn de vos utilisateurs. Pour plus d’informations sur la confidentialité lors de l’activation de l’intégration LinkedIn dans Azure AD, lisez la [déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement/). 

## <a name="manage-linkedin-integration"></a>Gérer l’intégration LinkedIn
Pour les entreprises, l’intégration de LinkedIn est activée par défaut dans Azure AD. L’activation de l’intégration LinkedIn permet à tous les utilisateurs de votre entreprise d’utiliser les fonctionnalités LinkedIn dans les services Microsoft, telles que l’affichage des profils LinkedIn dans Outlook. La désactivation de l’intégration LinkedIn supprime les fonctionnalités LinkedIn des services et des applications Microsoft et arrête le partage des données entre LinkedIn et votre organisation via les services Microsoft.

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>Activer ou désactiver l’intégration LinkedIn pour votre organisation dans le portail Azure

1. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com/) en utilisant un compte d’administrateur général pour le locataire Azure AD.
2. Sélectionnez **Utilisateurs et groupes**.
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Paramètres utilisateur**.
4. Sous **Intégration LinkedIn**, sélectionnez **Oui** ou **Non** pour activer ou désactiver l’intégration LinkedIn.
   ![Activation de l’intégration LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>Activer ou désactiver l’intégration LinkedIn pour les applications Office 2016 de votre organisation à l’aide d’une stratégie de groupe

1. Télécharger les [fichiers modèles d’administration Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrayez les fichiers **ADMX** et les copier dans votre **référentiel central**.
3. Ouvrez la gestion des stratégies de groupe.
4. Créez un objet de stratégie de groupe avec le paramètre suivant : **Configuration utilisateur** > **Modèles d’administration** > **Microsoft Office 2016**  >  **Divers** > **Autoriser l’intégration LinkedIn**.
5. Sélectionnez **Activé** ou **Désactivé**.
  * Lorsque la stratégie est **activée**, le paramètre **Afficher les fonctionnalités LinkedIn dans les applications Office** de la boîte de dialogue des options Office 2016 est activé. Cela signifie également que les utilisateurs de votre organisation peuvent utiliser les fonctionnalités LinkedIn dans leurs applications Office.
  * Lorsque la stratégie est **désactivée**, le paramètre **Afficher les fonctionnalités LinkedIn dans les applications Office** de la boîte de dialogue des options Office 2016 est défini sur l’état désactivé, et les utilisateurs finaux ne peuvent pas modifier ce paramètre. Les utilisateurs de votre organisation ne peuvent pas utiliser les fonctionnalités LinkedIn dans leurs applications Office 2016. 

Cette stratégie de groupe affecte uniquement les applications Office 2016 installées sur un ordinateur local. Les utilisateurs peuvent voir les fonctionnalités LinkedIn dans les cartes de profil Office 365 même s’ils désactivent LinkedIn dans leurs applications Office 2016. 

### <a name="learn-more"></a>En savoir plus 
* [Informations et fonctionnalités LinkedIn dans les applications Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centre d’aide LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Étapes suivantes
Utilisez le lien suivant pour afficher vos paramètres d’intégration LinkedIn actuels dans le portail Azure :

[Configurer l’intégration de LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 