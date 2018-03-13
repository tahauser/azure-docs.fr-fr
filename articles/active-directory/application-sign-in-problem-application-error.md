---
title: "Erreur dans la page d’une application après la connexion | Microsoft Docs"
description: "Comment résoudre les problèmes de connexion à Azure AD quand l’application émet une erreur"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 02236e7f7ec6be0df5082d2cde3d616e628c3927
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Erreur dans la page d’une application après la connexion

Dans ce scénario, Azure AD connecte l’utilisateur, mais l’application affiche une erreur qui empêche l’utilisateur d’achever le flux de connexion. Dans ce scénario, l’application n’accepte pas la réponse émise par Azure AD.

Plusieurs raisons peuvent expliquer pourquoi l’application n’a pas accepté la réponse d’Azure AD. Si l’erreur dans l’application n’est pas suffisamment détaillée pour savoir ce qui manque dans la réponse :

-   Si l’application est la galerie Azure AD, vérifiez que vous avez suivi toutes les étapes de l’article [Débogage d’une authentification unique basée sur SAML aux applications dans Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Utilisez un outil tel que [Fiddler](http://www.telerik.com/fiddler) pour capturer la demande, la réponse et le jeton SAML.

-   Partagez la réponse SAML avec le fournisseur de l’application pour savoir ce qui manque.

## <a name="missing-attributes-in-the-saml-response"></a>Attributs manquants dans la réponse SAML

Pour ajouter un attribut dans la configuration Azure AD à envoyer dans la réponse Azure AD, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur **Afficher et modifier tous les autres attributs utilisateur** sous **Attributs utilisateur** pour modifier les attributs à envoyer à l’application dans le jeton SAML lorsque les utilisateurs se connectent.

   Pour ajouter un attribut :

   * Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   * Cliquez sur **Enregistrer.** Le nouvel attribut s’affiche dans le tableau.

9.  Enregistrez la configuration.

La prochaine fois que l’utilisateur se connectera à l’application, Azure AD enverra le nouvel attribut dans la réponse SAML.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>L’application attend une valeur ou un format d’identificateur d’utilisateur différent

La connexion à l’application échoue parce que la réponse SAML ne comprend pas certains attributs tels que des rôles ou parce que l’application attend un format différent pour l’attribut EntityID.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Ajoutez un attribut dans la configuration de l’application Azure AD :

Pour modifier la valeur de l’identificateur d’utilisateur, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sous **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**.

## <a name="change-entityid-user-identifier-format"></a>Changer le format d’EntityID (identificateur d’utilisateur)

Si l’application attend un autre format pour l’attribut EntityID, vous ne pouvez pas sélectionner le format d’EntityID (identificateur d’utilisateur) qu’Azure AD envoie à l’application dans la réponse après l’authentification de l’utilisateur.

Azure AD sélectionne le format de l’attribut NameID (identificateur d’utilisateur) en fonction de la valeur sélectionnée ou du format demandé par l’application dans la demande d’authentification SAML. Pour plus d’informations, consultez l’article [Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) dans la section NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>L’application attend une méthode de signature différente pour la réponse SAML

Pour modifier les parties du jeton SAML qui sont signées numériquement par Azure Active Directory. Procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur **Afficher les paramètres avancés de signature de certificat** dans la section **Certificat de signature SAML**.

9.  Sélectionnez l’**Option de signature** appropriée attendue par l’application :

  * Signer la réponse SAML

  * Signer la réponse et l’assertion SAML

  * Signer l’assertion SAML

La prochaine fois que l’utilisateur se connectera à l’application, Azure AD signera la partie de la réponse SAML sélectionnée.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>L’application attend l’algorithme de signature SHA-1

Par défaut, Azure AD signe le jeton SAML à l’aide de l’algorithme le plus sécurisé. Il n’est pas recommandé de remplacer l’algorithme de connexion par SHA-1, sauf si l’application l’exige.

Pour modifier l’algorithme de signature, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur **Afficher les paramètres avancés de signature de certificat** dans la section **Certificat de signature SAML**.

9.  Sélectionnez SHA-1 dans **Algorithme de signature**.

La prochaine fois que l’utilisateur se connectera à l’application, Azure AD signera le jeton SAML à l’aide de l’algorithme SHA-1.

## <a name="next-steps"></a>Étapes suivantes
[Débogage d’une authentification unique basée sur SAML aux applications dans Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
