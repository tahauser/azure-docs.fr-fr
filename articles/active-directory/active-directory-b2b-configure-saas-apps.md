---
title: Configurer des applications SaaS pour B2B Collaboration dans Azure Active Directory | Microsoft Docs
description: Code et exemples PowerShell pour Azure Active Directory B2B Collaboration
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: e2206ca8ddee867b9ea48911f70793010989ad69
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurer des applications SaaS pour B2B Collaboration

Azure Active Directory (Azure AD) B2B Collaboration fonctionne avec la plupart des applications qui s’intègrent à Azure AD. Dans cette section, nous vous fournissons des instructions sur la configuration de certaines applications SAP populaires à utiliser avec Azure AD B2B.

Avant d’examiner les instructions propres aux applications, voici quelques règles générales :

* Pour la plupart des applications, le paramétrage utilisateur doit être effectué manuellement. Autrement dit, les utilisateurs doivent également être créés manuellement dans l’application.

* Pour les applications qui prennent en charge la configuration automatique, par exemple Dropbox, des invitations distinctes sont créées à partir des applications. Les utilisateurs doivent accepter chaque invitation.

* Dans les attributs d’utilisateur, pour atténuer les problèmes liés aux disques de profil utilisateur altérés dans les utilisateurs invités, définissez toujours **Identificateur d’utilisateur** sur **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Pour permettre aux utilisateurs de se connecter à l’aide de leur compte d’organisation, vous devez configurer manuellement Dropbox Business pour utiliser Azure AD comme fournisseur d’identité SAML (Security Assertion Markup Language). Si Dropbox for Business n’a pas été configuré à cette intention, l’application ne peut pas inviter ni autoriser les utilisateurs à se connecter à l’aide d’Azure AD.

1. Pour ajouter l’application Dropbox Business dans Azure AD, sélectionnez **Applications d’entreprise** dans le volet gauche, puis cliquez sur **Ajouter**.

  ![Bouton « Ajouter » dans la page Applications d’entreprise](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

2. Dans la fenêtre **Ajouter une application**, entrez **dropbox** dans la zone de recherche, puis sélectionnez **Dropbox Business** dans la liste de résultats.

  ![Rechercher « dropbox » dans la page Ajouter une application](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

3. Dans la page **Authentification unique**, sélectionnez **Authentification unique** dans le volet gauche, puis entrez **user.mail** dans la zone **Identificateur d’utilisateur**. (Il est défini comme UPN par défaut).

  ![Configuration de l’authentification unique pour l’application](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

4. Pour télécharger le certificat à utiliser pour la configuration de Dropbox, sélectionnez **Configurer Dropbox**, puis **URL SSO SAML** dans la liste.

  ![Téléchargement du certificat pour la configuration de Dropbox](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Connectez-vous à Dropbox avec l’URL de connexion à partir de la page **Authentification unique**.

  ![Page de connexion Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

6. Dans le menu, sélectionnez **Console d’administration**.

  ![Lien « Console d’administration » dans le menu Dropbox](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

7. Dans la boîte de dialogue **Authentification**, sélectionnez **Plus**, chargez le certificat, puis, dans la zone **URL de connexion**, entrez l’URL SSO SAML.

  ![Lien « Plus » dans la boîte de dialogue Authentification réduite](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![Lien « URL de connexion » dans la boîte de dialogue Authentification développée](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

8. Pour configurer le paramétrage utilisateur automatique dans le portail Azure, sélectionnez **Approvisionnement** dans le volet gauche, puis sélectionnez **Automatique** dans la zone **Mode d’approvisionnement** et **Autoriser**.

  ![Configuration de l’approvisionnement d’utilisateurs automatique dans le portail Azure](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

Une fois que les utilisateurs invités ou membres ont été configurés dans l’application Dropbox, ils reçoivent une invitation distincte de Dropbox. Pour utiliser l’authentification unique de Dropbox, les invités doivent accepter l’invitation en cliquant sur un lien qu’elle contient.

## <a name="box"></a>Box
Vous pouvez autoriser les utilisateurs à authentifier les utilisateurs invités de Box avec leur compte Azure AD à l’aide de la fédération basée sur le protocole SAML. Dans cette procédure, vous chargez les métadonnées vers Box.com.

1. Ajoutez l’application Box à partir des applications d’entreprise.

2. Configurez l’authentification unique dans l’ordre suivant :

  ![Configurer l’authentification unique de Box](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

 a. Dans la zone **URL de connexion**, vérifiez que l’URL de connexion est définie correctement pour Box dans le portail Azure. Cette URL est celle de votre client Box.com. Elle doit suivre la convention d’affectation de noms *https://.box.com*.  
 L’**Identificateur** ne s’applique pas à cette application, mais apparaît toujours comme un champ obligatoire.

 b. Dans la zone **Identificateur d’utilisateur**, entrez **user.mail** (pour l’authentification unique des comptes d’invité).

 c. Sous **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

 d. Pour configurer votre client Box.com pour utiliser Azure AD comme fournisseur d’identité, téléchargez le fichier de métadonnées et enregistrez-le sur votre lecteur local.

 e. Transférez le fichier de métadonnées à l’équipe de support Box, qui configure l’authentification unique pour vous.

3. Pour le paramétrage utilisateur automatique d’Azure AD, dans le volet gauche, sélectionnez **Approvisionnement**, puis **Autoriser**.

  ![Autoriser Azure AD à se connecter à Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

À l’instar des invités Dropbox, les invités Box doivent utiliser leur invitation reçue de l’application Box.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur Azure AD B2B Collaboration :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
* [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
* [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
* [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
* [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)
