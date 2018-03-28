---
title: Activer les connexions LinkedIn pour les applications et les services Microsoft dans Azure Active Directory | Microsoft Docs
description: Explique comment activer et désactiver les connexions de comptes LinkedIn pour les applications Microsoft dans Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/15/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 3bf224edea9e6da0d0eadb6fb6a409248de3d0e3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Connexions de comptes LinkedIn pour les applications et services Microsoft
Dans cet article, vous pouvez découvrir comment gérer les connexions de comptes LinkedIn pour votre locataire dans le centre d’administration Azure Active Directory (Azure AD). 

> [!IMPORTANT]
> La fonctionnalité de connexions de comptes LinkedIn est en cours de déploiement sur les locataires Azure AD. Une fois déployée sur votre locataire, elle est activée par défaut. Elle n’est pas disponible pour les clients du gouvernement des États-Unis et les organisations avec des boîtes aux lettres Exchange Online hébergés en Australie, Canada, Chine, France, Allemagne, Inde, Corée du Sud, Royaume-Uni, Japon et Afrique du Sud. La prise en charge de ces emplacements de boîtes aux lettres sera bientôt disponible.  Pour obtenir une vue à jour des informations de déploiement, consultez la page [Feuille de route de Office 365](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc).

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Apparence des connexions de comptes LinkedIn pour l’utilisateur
Les connexions de comptes LinkedIn permettent aux utilisateurs d’afficher des informations de profils LinkedIn publics dans certaines de leurs applications Microsoft. Les utilisateurs de votre locataire peuvent choisir de se connecter à leurs comptes LinkedIn et Microsoft scolaire ou professionnel pour afficher des informations de profils LinkedIn supplémentaires. Pour plus d’informations, consultez [LinkedIn information and features in Microsoft apps and services (Informations et fonctionnalités LinkedIn dans les services et applications Microsoft)](https://go.microsoft.com/fwlink/?linkid=850740).

Quand des utilisateurs de votre organisation se connectent à leurs comptes LinkedIn et Microsoft scolaire ou professionnel, ils ont deux options : 
* Accorder l’autorisation de partager des données entre les deux comptes. Cela signifie qu’ils donnent l’autorisation à leur compte LinkedIn de partager des données avec leur compte Microsoft scolaire ou professionnel, ainsi que l’autorisation à leur compte Microsoft scolaire ou professionnel de partager des données avec leur compte LinkedIn. Les données partagées avec LinkedIn quittent les services en ligne. 
* Accorder l’autorisation de partager des données uniquement à partir de leur compte LinkedIn vers leur compte Microsoft scolaire ou professionnel

Pour plus d’informations sur les données qui sont partagées entre les comptes LinkedIn et les comptes Microsoft scolaires ou professionnels des utilisateurs, consultez [LinkedIn dans les applications Microsoft dans votre entreprise ou établissement scolaire](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Les utilisateurs peuvent se déconnecter des comptes](https://www.linkedin.com/help/linkedin/answer/85097) et supprimer les autorisations de partage des données à tout moment. 
* [Les utilisateurs peuvent contrôler la manière dont leur profil LinkedIn est affiché](https://www.linkedin.com/help/linkedin/answer/83), notamment choisir si leur profil peut apparaître dans les applications Microsoft.

## <a name="privacy-considerations"></a>Considérations relatives à la confidentialité
L’activation des connexions de comptes LinkedIn permet aux applications et aux services Microsoft d’accéder à certaines informations LinkedIn de vos utilisateurs. Pour plus d’informations sur la confidentialité lors de l’activation des connexions de comptes LinkedIn, lisez la [déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement/). 

## <a name="manage-linkedin-account-connections"></a>Gérer les connexions de comptes LinkedIn
La fonctionnalité de connexions de comptes LinkedIn est activée par défaut pour votre locataire entier. Vous pouvez choisir de désactiver les connexions de comptes LinkedIn pour votre locataire entier, ou d’activer les connexions de comptes LinkedIn pour des utilisateurs sélectionnés dans votre locataire. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Activer ou désactiver les connexions de comptes LinkedIn pour votre locataire dans le portail Azure

1. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com/) en utilisant un compte d’administrateur général pour le locataire Azure AD.
2. Sélectionnez **Utilisateurs**.
3. Dans le panneau **Utilisateurs**, sélectionnez **Paramètres utilisateur**.
4. Sous **LinkedIn account connections (Connexions de comptes LinkedIn)** :
  * Sélectionnez **Oui** pour activer les connexions de comptes LinkedIn pour tous les utilisateurs dans votre locataire.
  * Sélectionnez **Sélectionnés** pour activer les connexions de comptes LinkedIn uniquement pour les utilisateurs de locataire sélectionnés.
  * Sélectionnez **Non** pour désactiver les connexions de comptes LinkedIn pour tous les utilisateurs. ![Activation des connexions de comptes LinkedIn](./media/linkedin-integration/LinkedIn-integration.png)
5. Enregistrer vos paramètres lorsque vous avez terminé en sélectionnant **Enregistrer**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Activer ou désactiver les connexions de comptes LinkedIn pour les applications Office 2016 de votre organisation à l’aide d’une stratégie de groupe

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
Utilisez le lien suivant pour afficher vos paramètres de connexions de comptes LinkedIn actuels dans le portail Azure :

[Configurer les connexions de comptes LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 