---
title: "Didacticiel : Configurer Asana pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Asana."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 4f3bd11a99f43d6405ea285a7a283179d561f92a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Didacticiel : Configurer Asana pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer les étapes à effectuer dans Asana et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur d’Azure AD vers Asana.

## <a name="prerequisites"></a>configuration requise

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un client Azure Active Directory
*   Un locataire Asana avec le [plan Entreprise](https://www.asana.com/pricing) ou supérieur activé 
*   Un compte utilisateur dans Asana avec des autorisations d’administrateur 

> [!NOTE] 
> L’intégration de l’approvisionnement d’Azure AD repose sur [l’API Asana](https://app.asana.com/api/1.0/scim/Users) qui est disponible pour Asana.

## <a name="assigning-users-to-asana"></a>Affectation d’utilisateurs à Asana

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes utilisateur, seuls les utilisateurs qui ont été « affectés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application Asana. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs à votre application Asana en suivant les instructions fournies ici :

[Affecter un utilisateur à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Conseils importants pour l’affectation d’utilisateurs à Asana

*   Il est recommandé qu’un seul utilisateur Azure AD soit affecté à Asana pour tester la configuration de l’approvisionnement. Les autres utilisateurs peuvent être affectés ultérieurement.

## <a name="configuring-user-provisioning-to-asana"></a>Configuration de l’approvisionnement des utilisateurs sur Asana 

Cette section vous guide lors de la connexion de votre instance Azure AD au compte utilisateur Asana fournissant l’API et la configuration du service d’approvisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans Asana en fonction des affectations d’utilisateurs dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique SAML pour Asana en suivant les instructions fournies dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Pour configurer l’approvisionnement automatique de comptes utilisateur vers Asana dans Azure AD :

1)  Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2) Si vous avez déjà configuré Asana pour l’authentification unique, recherchez votre instance Asana à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Asana** dans la galerie d’applications. Sélectionnez **Asana** dans les résultats de la recherche, puis ajoutez-le à votre liste d’applications.

3)  Sélectionnez votre instance Asana, puis sélectionnez l’onglet **Approvisionnement**.

4)  Définissez le **Mode d’approvisionnement** sur **Automatique**.

![Approvisionnement Asana](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5) Dans la section des informations d’identification d’administrateur, suivez les instructions pour générer le jeton et entrez-le dans la zone de texte **Jeton secret**.

* Connectez-vous à [Asana](https://app.asana.com) à l’aide du compte administrateur.
* Cliquez sur la photo de profil dans la barre supérieure et sélectionnez les paramètres actuels de nom de l’organisation.
* Accédez à l’onglet Comptes de service
* Cliquez sur Add Service Account (Ajouter un compte de service).
* Mettez à jour les champs Nom, À propos, ainsi que la photo de profil si nécessaire. Copiez ensuite le contenu du champ **Jeton**, puis cliquez sur Enregistrer les modifications.

6) Dans le portail Azure, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application Asana. Si la connexion échoue, vérifiez que votre compte Asana dispose des autorisations d’administrateur et recommencez l’étape **« Tester la connexion »**.

7) Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case se trouvant en dessous.

8) Cliquez sur **Enregistrer**. 

9) Dans la section Mappages, sélectionnez **Synchronize Azure Active Directory Users to Asana** (Synchroniser les utilisateurs Azure Active Directory avec Asana).

10) Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui seront synchronisés d’Azure AD vers Asana. Notez que les attributs sélectionnés en tant que propriétés de **Correspondance** seront utilisés pour faire correspondre les comptes utilisateur dans Asana pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications. Pour plus d’informations, consultez [Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory](active-directory-saas-customizing-attribute-mappings.md).

11) Pour activer le service d’approvisionnement Azure AD pour Asana, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

12) Cliquez sur **Enregistrer**. 

Cela démarre la synchronisation initiale des utilisateurs affectés à Asana dans la section Utilisateurs. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent environ toutes les 20 minutes, tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service de configuration dans votre application Asana.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-asana-tutorial.md)
