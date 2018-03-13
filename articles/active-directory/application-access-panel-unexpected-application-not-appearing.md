---
title: "Une application affectée n’apparaît pas sur le volet d’accès | Microsoft Docs"
description: "Identifier pourquoi une application n’apparaît pas sur le volet d’accès"
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
ms.reviwer: japere
ms.openlocfilehash: 5cb8b600f77c5b7dae91204126e64ec7b9a861ae
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Une application affectée n’apparaît pas sur le volet d’accès

Le volet d’accès est un portail web qui permet à un utilisateur disposant d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) d’afficher et de démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Ces applications sont configurées pour le compte de l’utilisateur dans le portail Azure AD. Pour que l’application soit visible dans le volet d’accès, elle doit être correctement configurée et affectée à l’utilisateur ou à un groupe dont est membre l’utilisateur.

Les types d’applications que peut voir l’utilisateur tombent dans les catégories suivantes :

-   Applications Office 365

-   Applications Microsoft et tierces configurées avec l’authentification unique (SSO) basée sur la fédération

-   Applications à authentification unique (SSO) par mot de passe

-   Applications avec solutions d’authentification unique (SSO) existantes

## <a name="general-issues-to-check-first"></a>Problèmes d’ordre général à vérifier en premier

-   Si une application vient d’être ajoutée pour un utilisateur, essayez de vous connecter / déconnecter de nouveau sur le volet d’accès de l’utilisateur après quelques minutes pour voir si l’application a été ajoutée.

-   Si une licence vient d’être supprimée pour un utilisateur ou un groupe dont l’utilisateur est membre, la prise en compte des modifications peut prendre du temps, en fonction de la taille et de la complexité du groupe. Prévoyez du temps supplémentaire avant de vous connecter au volet d’accès.

## <a name="problems-related-to-application-configuration"></a>Problèmes liés à la configuration d’applications

Une application n’apparaît peut-être pas dans le volet d’accès d’un utilisateur en raison d’une mauvaise configuration. Voici quelques méthodes pour résoudre les problèmes liés à la configuration d’applications :

-   [Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Comment configurer l’authentification unique fédérée pour une application non issue de la galerie](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Comment configurer l’authentification unique avec mot de passe pour une application de la galerie Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Comment configurer l’authentification unique avec mot de passe pour une application non issue de la galerie Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD

Un didacticiel pas à pas est disponible pour toutes les applications de la galerie Azure AD dans lesquelles est activée la fonctionnalité Enterprise Single Sign-On. Pour une aide pas à pas détaillée, vous pouvez accéder à la [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurer les valeurs de métadonnées de l’application dans Azure AD (URL de connexion, identificateur, URL de réponse)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Récupérer le certificat et les métadonnées Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurer les valeurs de métadonnées Azure AD dans l’application (URL de connexion, émetteur, URL de déconnexion et certificat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Dans la zone de texte **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez la renommer dans la zone de texte **Nom**.

9.  Pour ajouter l’application, cliquez sur le bouton **Ajouter**.

Après une courte période, vous pourrez voir le volet de configuration de l’application.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurer l’authentification unique pour une application à partir de la galerie Azure AD

Pour configurer l’authentification unique pour une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez **Authentification basée sur SAML** dans la liste déroulante **Mode**.

9.  Entrez les valeurs obligatoires dans **Domaine et URL.** Ces valeurs doivent vous être communiquées par le fournisseur de l’application.

   1. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur de services, une URL de connexion est obligatoire. Pour certaines applications, l’identificateur est également une valeur obligatoire.

   2. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur d’identité, une URL de réponse est obligatoire. Pour certaines applications, l’identificateur est également une valeur obligatoire.

10. **Facultatif** : pour afficher les valeurs non obligatoires, cliquez sur **Afficher les paramètres d’URL avancés**.

11. Dans **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**.

12. **Facultatif :** pour modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**.

   Pour ajouter un attribut :

   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

13. Cliquez sur **Configurer &lt;nom de l’application&gt;** pour accéder à la documentation expliquant comment configurer l’authentification unique pour l’application. En outre, vous disposez des URL de métadonnées et du certificat nécessaires à la configuration de l’authentification unique pour l’application.

14. Cliquez sur **Enregistrer** pour enregistrer la configuration.

15. Affectez des utilisateurs à l’application.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application

Pour sélectionner l’identificateur de l’utilisateur ou ajouter des attributs d’utilisateur, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

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

9.  Pour ajouter des attributs d’utilisateur, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**, afin de modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte.

   Pour ajouter un attribut :

   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Télécharger les métadonnées ou le certificat Azure AD

Pour télécharger les métadonnées ou le certificat de l’application à partir d’Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Accédez à la section **Certificat de signature SAML**, puis cliquez sur la valeur de colonne **Télécharger**. En fonction de ce que l’application nécessite pour configurer l’authentification unique, vous voyez soit l’option de téléchargement des métadonnées XML, soit le certificat.

    Azure AD ne fournit pas d’URL permettant d’obtenir les métadonnées. Les métadonnées peuvent uniquement être récupérées sous forme de fichier XML.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Comment configurer l’authentification unique fédérée pour une application non issue de la galerie

Pour configurer une application non issue de la galerie, vous devez disposer d’Azure AD Premium, et l’application doit prendre en charge SAML 2.0. Pour plus d’informations sur les versions d’Azure AD, consultez [Tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Configurer les valeurs de métadonnées de l’application dans Azure AD (URL de connexion, identificateur, URL de réponse)](#configuring-single-sign-on)

-   [Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Récupérer le certificat et les métadonnées Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurer les valeurs de métadonnées Azure AD dans l’application (URL de connexion, émetteur, URL de déconnexion et certificat)](#configuring-single-sign-on)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configurer les valeurs de métadonnées de l’application dans Azure AD (URL de connexion, identificateur, URL de réponse)

Pour configurer l’authentification unique pour une application qui n’est pas issue de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Cliquez sur **Application ne figurant pas dans la galerie** dans la section **Ajouter votre propre application**.

7.  Entrez le nom de votre application dans la zone de texte **Nom**.

8.  Pour ajouter l’application, cliquez sur le bouton **Ajouter**.

9.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

10. Sélectionnez **Authentification basée sur SAML** dans la liste déroulante **Mode**.

11. Entrez les valeurs obligatoires dans **Domaine et URL**. Ces valeurs doivent vous être communiquées par le fournisseur de l’application.

   1. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur d’identité, entrez l’URL de réponse et l’identificateur.

   2.  **Facultatif** : pour configurer l’application comme une application à authentification unique initiée par le fournisseur de services, une URL de connexion est obligatoire.

12. Dans **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**.

13. **Facultatif :** pour modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**.

   Pour ajouter un attribut :

   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer.** Le nouvel attribut s’affiche dans le tableau.

14. Cliquez sur **Configurer &lt;nom de l’application&gt;** pour accéder à la documentation expliquant comment configurer l’authentification unique pour l’application. En outre, vous disposez des URL et du certificat Azure AD nécessaires pour l’application.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application

Pour sélectionner l’identificateur de l’utilisateur ou ajouter des attributs d’utilisateur, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

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

9.  Pour ajouter des attributs d’utilisateur, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**, afin de modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte.

   Pour ajouter un attribut :

   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer.** Le nouvel attribut s’affiche dans le tableau.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Télécharger les métadonnées ou le certificat Azure AD

Pour télécharger les métadonnées ou le certificat de l’application à partir d’Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Accédez à la section **Certificat de signature SAML**, puis cliquez sur la valeur de colonne **Télécharger**. En fonction de ce que l’application nécessite pour configurer l’authentification unique, vous voyez soit l’option de téléchargement des métadonnées XML, soit le certificat.

Azure AD ne fournit pas d’URL permettant d’obtenir les métadonnées. Les métadonnées peuvent uniquement être récupérées sous forme de fichier XML.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Comment configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurer l’application pour l’authentification unique basée sur un mot de passe](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Dans la zone de texte **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez la renommer dans la zone de texte **Nom**.

9.  Pour ajouter l’application, cliquez sur le bouton **Ajouter**.

Après une courte période, vous pouvez voir le volet de configuration de l’application.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe.**

9.  [Affectez des utilisateurs à l’application](#how-to-assign-a-user-to-an-application-directly).

10. En outre, vous pouvez également fournir des informations d’identification pour le compte de l’utilisateur en sélectionnant les lignes des utilisateurs, en cliquant sur **Mettre à jour les informations d’identification** et en entrant le nom d’utilisateur et le mot de passe à la place des utilisateurs. Autrement, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Comment configurer l’authentification unique par mot de passe pour une application ne figurant pas dans la galerie

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application ne figurant pas dans la galerie](#add-a-non-gallery-application)

-   [Configurer l’application pour l’authentification unique basée sur un mot de passe](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Ajouter une application ne figurant pas dans la galerie

Pour ajouter une application à partir de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Cliquez sur **Application ne figurant pas dans la galerie**.

7.  Entrez le nom de votre application dans la zone de texte **Nom**. Sélectionnez **Ajouter.**

Après une courte période, vous pourrez voir le volet de configuration de l’application.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

    1.  Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe.**

9.  Entrez **l’URL de connexion**. Il s’agit de l’URL où les utilisateurs entrent leurs nom d’utilisateur et mot de passe pour se connecter. Vérifiez que les champs de connexion sont visibles dans l’URL.

10. [Affectez des utilisateurs à l’application](#how-to-assign-a-user-to-an-application-directly).

11. En outre, vous pouvez également fournir des informations d’identification pour le compte de l’utilisateur en sélectionnant les lignes des utilisateurs, en cliquant sur **Mettre à jour les informations d’identification** et en entrant le nom d’utilisateur et le mot de passe à la place des utilisateurs. Autrement, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problèmes liés à l’affectation des applications aux utilisateurs

Un utilisateur peut ne pas voir une application sur son volet d’accès, car il n’y est pas affecté. Voici plusieurs méthodes pour vérifier :

-   [Vérifier si un utilisateur est affecté à l’application](#check-if-a-user-is-assigned-to-the-application)

-   [Comment affecter un utilisateur directement à une application](#how-to-assign-a-user-to-an-application-directly)

-   [Vérifier si un utilisateur est affecté à une licence liée à l’application](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Comment attribuer une licence à un utilisateur](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Vérifier si un utilisateur est affecté à l’application

Pour vérifier si un utilisateur est affecté à l’application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

6.  **Recherchez** le nom de l’application en question.

7.  Sélectionnez **Utilisateurs et groupes**.

8.  Vérifiez si votre utilisateur est affecté à l’application.

   * Si ce n’est pas le cas, suivez les étapes décrites dans « Comment affecter un utilisateur directement à une application ».

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Comment affecter un utilisateur directement à une application

Pour affecter un ou plusieurs utilisateurs directement à une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

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

Après une courte période, les utilisateurs que vous avez sélectionnés sont en mesure de lancer ces applications dans le volet d’accès.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Vérifier si un utilisateur est affecté à une licence liée à l’application

Pour vérifier les licences affectées à un utilisateur, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** pour le sélectionner.

7.  Cliquez sur **Licences** pour voir quelles licences sont actuellement affectées à l’utilisateur.

  * Si l’utilisateur est affecté à une licence Office, les applications Office internes apparaîtront dans le volet d’accès.

### <a name="how-to-assign-a-user-a-license"></a>Comment affecter une licence à un utilisateur 

Pour affecter une licence à un utilisateur, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** pour le sélectionner.

7.  Cliquez sur **Licences** pour voir quelles licences sont actuellement affectées à l’utilisateur.

8.  Cliquez sur le bouton **Attribuer**.

9.  Sélectionnez **un ou plusieurs produits** dans la liste des produits disponibles.

10. **Facultatif** Cliquez sur l’élément **Options d’affectation** pour affecter les produits de façon granulaire. Cliquez sur **OK** lorsque l’opération est terminée.

11. Cliquez sur le bouton **Attribuer** pour affecter ces licences à cet utilisateur.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problèmes liés à l’affectation des applications aux groupes

Un utilisateur peut ne pas voir une application sur son volet d’accès, car il ne fait pas partie d’un groupe affecté à l’application. Voici plusieurs méthodes pour vérifier :

-   [Vérifier les appartenances d’un utilisateur à des groupes](#check-a-users-group-memberships)

-   [Comment affecter une application directement à un groupe](#how-to-assign-an-application-to-a-group-directly)

-   [Vérifier si un utilisateur fait partie d’un groupe affecté à une licence](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Comment attribuer une licence à un groupe](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Vérifier les appartenances d’un utilisateur à des groupes

Pour vérifier l’appartenance d’un utilisateur à un groupe, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** pour le sélectionner.

7.  Cliquez sur **Groupes**.

8.  Vérifiez si votre utilisateur fait partie d’un groupe affecté à l’application.

  * Si vous souhaitez supprimer l’utilisateur du groupe, **cliquez sur la ligne** du groupe et sélectionnez Supprimer.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Comment affecter une application directement à un groupe

Pour affecter un ou plusieurs groupes directement à une application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7.  Une fois l’application chargée, cliquez sur **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9.  Cliquez sur le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

10. Tapez le **nom de groupe complet** du groupe souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur le **groupe** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo du groupe pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif :** si vous souhaitez **ajouter plusieurs groupes**, entrez un autre **nom de groupe complet** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter ce groupe à la liste **Sélectionné**.

13. Lorsque vous avez fini de sélectionner les groupes, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux groupes que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux groupes sélectionnés.

Après une courte période, les utilisateurs que vous avez sélectionnés seront en mesure de lancer ces applications dans le volet d’accès.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Vérifier si un utilisateur fait partie d’un groupe affecté à une licence

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** pour le sélectionner.

7.  Cliquez sur **Groupes**.

8.  Cliquez sur la ligne d’un groupe spécifique.

9.  Cliquez sur **Licences** pour voir quelles licences sont affectées au groupe.

   * Si le groupe est affecté à une licence Office, certaines applications Office internes pourront apparaître dans le volet d’accès de l’utilisateur.

### <a name="how-to-assign-a-license-to-a-group"></a>Comment attribuer une licence à un groupe

Pour affecter une licence à un groupe, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les groupes**.

6.  **Recherchez** le groupe qui vous intéresse et **cliquez sur la ligne** pour le sélectionner.

7.  Cliquez sur **Licences** pour voir quelles licences sont actuellement affectées au groupe.

8.  Cliquez sur le bouton **Attribuer**.

9.  Sélectionnez **un ou plusieurs produits** dans la liste des produits disponibles.

10. **Facultatif** Cliquez sur l’élément **Options d’affectation** pour affecter les produits de façon granulaire. Cliquez sur **OK** lorsque l’opération est terminée.

11. Cliquez sur le bouton **Attribuer** pour affecter ces licences à ce groupe. Cette opération peut être très longue, selon la taille et la complexité du groupe.

>[!NOTE]
>Pour accélérer ce processus, vous pouvez affecter temporairement une licence directement à l’utilisateur. 
>
>

## <a name="next-steps"></a>Étapes suivantes
[Ajout de nouveaux utilisateurs à Azure Active Directory](active-directory-users-create-azure-portal.md)

