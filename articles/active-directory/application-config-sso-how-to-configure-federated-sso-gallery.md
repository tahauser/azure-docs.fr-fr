---
title: "Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD | Microsoft Docs"
description: "Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD existante et utiliser les didacticiels pour se lancer rapidement"
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
ms.openlocfilehash: e71480910432c6475ca97d20853a72b864195faa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD

Un didacticiel pas à pas est disponible pour toutes les applications de la galerie Azure AD dans lesquelles est activée la fonctionnalité Enterprise Single Sign-On. Pour une aide pas à pas détaillée, vous pouvez accéder à la [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

## <a name="overview-of-steps-required"></a>Vue d’ensemble des étapes requises
Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurer les valeurs de métadonnées de l’application dans Azure AD (URL de connexion, identificateur, URL de réponse)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Récupérer le certificat et les métadonnées Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurer les valeurs de métadonnées Azure AD dans l’application (URL de connexion, émetteur, URL de déconnexion et certificat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Affecter des utilisateurs à l’application](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Dans la zone de texte **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez la renommer dans la zone de texte **Nom**.

9.  Pour ajouter l’application, cliquez sur le bouton **Ajouter**.

Après un court délai, vous pouvez voir apparaître le volet de configuration de l’application.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurer l’authentification unique pour une application à partir de la galerie Azure AD

Pour configurer l’authentification unique pour une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez **Authentification basée sur SAML** dans la liste déroulante **Mode**.

9.  Entrez les valeurs obligatoires dans **Domaine et URL.** Ces valeurs doivent vous être communiquées par le fournisseur de l’application.

   1. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur de services, l’URL de connexion est obligatoire. Pour certaines applications, l’identificateur est également une valeur obligatoire.

   2. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur d’identité, une URL de réponse est obligatoire. Pour certaines applications, l’identificateur est également une valeur obligatoire.

10. **Facultatif** : pour afficher les valeurs non obligatoires, cliquez sur **Afficher les paramètres d’URL avancés**.

11. Dans **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**.

12. **Facultatif** : pour modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**.

  Pour ajouter un attribut :
   
   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   1. Cliquez sur **Enregistrer.** Le nouvel attribut s’affiche dans le tableau.

13. Cliquez sur **Configurer &lt;nom de l’application&gt;** pour accéder à la documentation expliquant comment configurer l’authentification unique pour l’application. En outre, vous disposez des URL de métadonnées et du certificat nécessaires à la configuration de l’authentification unique avec l’application.

14. Pour enregistrer la configuration, cliquez sur **Enregistrer**.

15. Affectez des utilisateurs à l’application.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application

Pour sélectionner l’identificateur de l’utilisateur ou ajouter des attributs d’utilisateur, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Dans la section **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**. L’option sélectionnée doit correspondre à la valeur attendue dans l’application pour authentifier l’utilisateur.

  >[!NOTE] 
  >Azure AD sélectionne le format de l’attribut NameID (Identificateur d’utilisateur) en fonction de la valeur sélectionnée ou du format demandé par l’application dans la demande d’authentification SAML. Pour plus d’informations, consultez l’article [Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) dans la section NameIDPolicy.
  >
  >

9.  Pour ajouter des attributs utilisateur, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** afin de modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte.

   Pour ajouter un attribut :
  
   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Télécharger les métadonnées ou le certificat Azure AD

Pour télécharger les métadonnées ou le certificat de l’application à partir d’Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  *  Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Accédez à la section **Certificat de signature SAML**, puis cliquez sur la valeur de colonne **Télécharger**. En fonction de ce que l’application nécessite pour configurer l’authentification unique, vous voyez soit l’option de téléchargement des métadonnées XML, soit le certificat.

Azure AD ne fournit pas d’URL permettant d’obtenir les métadonnées. Les métadonnées peuvent uniquement être récupérées sous forme de fichier XML.

## <a name="assign-users-to-the-application"></a>Affecter des utilisateurs à l’application

Pour affecter un ou plusieurs utilisateurs directement à une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7.  Une fois l’application chargée, cliquez sur **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9.  Cliquez sur le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

10. Tapez **le nom complet** ou **l’adresse de messagerie** de l’utilisateur souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur **l’utilisateur** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif :** si vous souhaitez **ajouter plusieurs utilisateurs**, entrez un autre **nom complet** ou une autre **adresse de messagerie** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Après avoir sélectionné les utilisateurs, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Après quelques instants, les utilisateurs que vous avez sélectionnés seront en mesure de démarrer ces applications à l’aide des méthodes décrites dans la section de description des solutions.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personnaliser des revendications SAML envoyées à une application

Pour savoir comment personnaliser les revendications d’attribut SAML envoyées à votre application, consultez l’article [Mappage des revendications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)



