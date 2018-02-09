---
title: "Didacticiel : configurer Workplace by Facebook pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 70686a48585d83ca5de78fdded99ae46e90cc20c
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Didactiel : configurer Workplace by Facebook pour l’approvisionnement automatique d’utilisateurs

Ce didacticiel vous indique les étapes nécessaires pour approvisionner et annuler automatiquement l’approvisionnement des comptes utilisateur depuis Azure Active Directory (Azure AD) vers Workplace by Facebook.

## <a name="prerequisites"></a>configuration requise

Pour configurer l’intégration d’Azure AD avec Workplace by Facebook, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Workplace by Facebook pour lequel l’authentification unique est activée

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir une [offre d’essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-workplace-by-facebook"></a>Affecter des utilisateurs à Workplace by Facebook

Azure AD utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes utilisateur, seuls les utilisateurs et les groupes qui ont été assignés à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et groupes dans Azure AD représentent les utilisateurs qui nécessitent l’accès à votre application Workplace by Facebook. Vous pouvez alors assigner ces utilisateurs à votre application Workplace by Facebook en suivant les instructions fournies dans la rubrique [Assigner un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

>[!IMPORTANT]
>*   Testez la configuration de l’approvisionnement en affectant un utilisateur Azure AD unique à Workplace by Facebook. Affectez des groupes et des utilisateurs supplémentaires ultérieurement.
>*   Quand vous assignez un utilisateur à Workplace by Facebook, vous devez sélectionner un rôle d’utilisateur valide. Le rôle Accès par défaut ne fonctionne pas pour l’approvisionnement.

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section vous guide tout au long de la connexion d’Azure AD à l’API d’approvisionnement des comptes utilisateur de Workspace by Facebook. Vous découvrez également comment configurer le service d’approvisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans Workspace by Facebook. Cela est fondé sur l’affectation d’utilisateurs et de groupes dans Azure AD.

>[!Tip]
>Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Workplace by Facebook, grâce aux instructions disponibles sur le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que ces deux fonctionnalités se complètent.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Configurer l’approvisionnement de comptes d’utilisateur vers Workplace by Facebook dans Azure AD

Azure AD prend en charge la possibilité de synchroniser automatiquement les informations de compte des utilisateurs affectés à Workplace by Facebook. La synchronisation automatique permet à Workplace by Facebook d’obtenir les données nécessaires pour autoriser l’accès des utilisateurs, avant leur première tentative de connexion. Elle annule également l’approvisionnement des utilisateurs à Workplace by Facebook lorsque l’accès a été révoqué dans Azure AD.

1. Sur le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**.

2. Si vous avez déjà configuré Workplace by Facebook pour l’authentification unique, recherchez votre instance de Workplace by Facebook à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Workplace by Facebook** dans la galerie d’applications. Sélectionnez **Workplace by Facebook** dans les résultats de recherche et ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de Workplace by Facebook, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![Capture instantanée des options d’approvisionnement de Workspace by Facebook](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Jeton secret** et **l’URL client** de votre administrateur Workplace by Facebook.

6. Sur le portail Azure, sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application Workplace by Facebook. Si la connexion échoue, vérifiez que votre compte Workplace by Facebook dispose des autorisations Administrateur d’équipe.

7. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification** et cochez la case.

8. Sélectionnez **Enregistrer**.

9. Dans la section Mappages, sélectionnez **Synchroniser des utilisateurs Azure Active Directory avec Workplace by Facebook**.

10. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD vers Workplace by Facebook. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes d’utilisateur dans Workplace by Facebook, en vue d’opérations de mise à jour. Pour valider les modifications, sélectionnez **Enregistrer**.

11. Pour activer le service d’approvisionnement Azure AD pour Workplace by Facebook, dans la section **Paramètres** définissez **l’État de l’approvisionnement** sur **Activé**.

12. Sélectionnez **Enregistrer**.

Pour plus d’informations sur la façon de configurer l’approvisionnement automatique, consultez [la documentation de Facebook](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Vous pouvez à présent créer un compte de test. Patientez jusqu’à 20 minutes avant de vérifier que le compte a bien été synchronisé avec Workplace by Facebook.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-facebook-at-work-tutorial.md)

