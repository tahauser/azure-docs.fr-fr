---
title: Configurer l'authentification Azure Active Directory pour votre application App Services
description: "Découvrez comment configurer l'authentification Azure Active Directory pour votre application App Services."
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 990fab9aeea71b8cf344b9a49a5ed438db6663c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Configurer votre application App Service pour utiliser une connexion Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article montre comment configurer Azure App Services pour utiliser Azure Active Directory comme fournisseur d’authentification.

## <a name="express"></a>Configuration d'Azure Active Directory à l'aide de la configuration rapide
1. Dans le [portail Azure], accédez à votre application App Service. Dans la barre de navigation gauche, sélectionnez **Authentification / Autorisation**.
2. Si l'option **Authentification / Autorisation** n'est pas activée, sélectionnez **Activer**.
3. Sélectionnez **Azure Active Directory**, puis **Rapide** sous **Mode de gestion**.
4. Cliquez sur **OK** pour inscrire l'application App Service auprès d'Azure Active Directory. Une nouvelle inscription d’application est créée. Si vous choisissez une inscription d'app existante à la place, cliquez sur **Sélectionner une application existante** et recherchez le nom d’une inscription d'app précédemment créée au sein de votre client.
   Cliquez sur l'inscription d'app pour la sélectionner, puis sur **OK**. Cliquez ensuite sur **OK** dans la page des paramètres Azure Active Directory.
   Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
5. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Azure Active Directory, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Se connecter avec Azure Active Directory**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour être authentifiées.
6. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application App Service.

## <a name="advanced"></a>(Méthode alternative) Configurer manuellement Azure Active Directory avec des paramètres avancés
Vous pouvez également choisir de fournir des paramètres de configuration manuellement. Il s’agit de la solution préférée si le locataire AAD que vous voulez utiliser diffère de celui avec lequel vous vous connectez à Azure. Pour terminer la configuration, vous devez d’abord créer une inscription dans Azure Active Directory, puis fournir des informations d’inscription à App Service.

### <a name="register"> </a>Inscription de votre application App Service auprès d'Azure Active Directory
1. Connectez-vous au [portail Azure]et accédez à votre application App Service. Copiez l**'URL** de votre application. Elle vous permettra de configurer l'inscription de votre application Azure Active Directory.
2. Accédez à **Active Directory**, sélectionnez **Inscriptions des applications**, puis cliquez sur **Nouvelle inscription d’application** en haut pour démarrer une nouvelle inscription d’application. 
3. Dans la page **Créer**, entrez un **Nom** pour l'inscription de votre application, sélectionnez le type **Application web / API** et, dans la zone **URL de connexion**, collez l’URL de l’application (celle de l’étape 1). Cliquez ensuite sur **Créer**.
4. Au bout de quelques secondes, vous devez voir apparaître la nouvelle inscription d’application que vous venez de créer.
5. Une fois l'inscription de l’application ajoutée, cliquez sur le nom de l’inscription d’application, cliquez sur **Paramètres** en haut, puis cliquez sur **Propriétés** 
6. Dans la zone **URI d’ID d’application**, collez l’URI de l’application (celui de l’étape 1). Dans la zone **URL de la page d’accueil**, collez l’URL de l’application (celle de l’étape 1), puis cliquez sur **Enregistrer**
7. Cliquez maintenant sur **URL de réponse**, éditez **l’URL de réponse** et collez-y l’URL de l’application (à l’étape 1), vérifiez que le protocole est **https://** (et non pas http://), puis ajoutez */.auth/login/aad/callback* à la fin de l’URL (par exemple, `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Cliquez sur **Enregistrer**.   
8.  À ce stade, copiez **l’ID d’application** pour l’application. Gardez-le pour une utilisation ultérieure. Vous en aurez besoin pour configurer votre application App Service.
9. Fermez la page **Application inscrite**. Dans la page **Inscriptions des applications**, cliquez en haut sur le bouton **Points de terminaison**, puis copiez l’URL du **Document de métadonnées de fédération**. 
10. Ouvrez une nouvelle fenêtre de navigateur et accédez à l’URL en la collant, puis accédez à la page XML. En haut du document, vous voyez un élément **EntityDescriptor**, qui doit contenir un attribut **entityID** au format `https://sts.windows.net/`, suivi d’un GUID propre à votre locataire (appelé « ID de locataire »). Copiez cette valeur, elle vous servira d'**URL de l'émetteur**. Vous configurerez l’application pour utiliser cette valeur plus tard.

### <a name="secrets"> </a>Ajout d'informations Azure Active Directory à votre application App Service
1. Retournez dans le [portail Azure], puis accédez à votre application App Service. Cliquez sur **Authentification/autorisation**. Si la fonctionnalité Authentification/Autorisation n’est pas activée, positionnez le commutateur sur **Activé**. Sous Fournisseurs d’authentification, cliquez sur **Azure Active Directory** pour configurer votre application. (Facultatif) Par défaut, App Service fournit une authentification, mais ne restreint pas l’accès autorisé au contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application. Définissez **Mesure à prendre quand une demande n’est pas authentifiée**, sur **Se connecter avec Azure Active Directory**. Cette option implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour être authentifiées.
2. Dans la configuration de l’authentification Active Directory, cliquez sur **Avancé** sous **Mode de gestion**. Collez l’ID d’application dans la zone ID client (celui de l’étape 8) et collez entityId (celui de l’étape 10) comme valeur de URL de l’émetteur. Cliquez ensuite sur **OK**.
3. Dans la page de configuration de l’authentification Active Directory, cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application App Service.

## <a name="optional-configure-a-native-client-application"></a>(Facultatif) Configurer une application cliente native
Azure Active Directory permet également d’inscrire les clients natifs, ce qui offre un contrôle accru du mappage d’autorisations. Cela est utile si vous souhaitez effectuer des connexions à l’aide d’une bibliothèque telle que **Active Directory Authentication Library**.

1. Accédez à **Azure Active Directory** dans le [portail Azure].
2. Dans le volet de navigation de gauche, sélectionnez **Inscriptions des applications**. Cliquez sur **Nouvelle inscription d'application** dans la partie supérieure.
4. Dans la page **Créer**, entrez un **Nom** pour l'inscription de votre application. Sélectionnez **Native** comme **type d'application**.
5. Dans la zone **URI de redirection**, entrez le point de terminaison */.auth/login/done* de votre site à l’aide du modèle HTTPS. Cette valeur doit être semblable à *https://contoso.azurewebsites.net/.auth/login/done*. Si vous créez une application Windows, utilisez plutôt le [SID de package](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) en tant qu’URI.
5. Cliquez sur **Créer**.
6. Une fois l'inscription de l'application ajoutée, sélectionnez-la pour l'ouvrir. Recherchez l’**ID de l'application** et notez-en la valeur.
7. Cliquez sur **Tous les paramètres** > **Autorisations requises** > **Ajouter** > **Sélectionner une API**.
8. Entrez le nom de l'application App Service que vous avez inscrite précédemment pour la rechercher, sélectionnez-la, puis cliquez sur **Sélectionner**. 
9. Sélectionnez **Accéder à \<nom_app**. Puis cliquez sur **Sélectionner**. Cliquez ensuite sur **Terminé**.

Vous avez maintenant configuré une application cliente native qui peut accéder à votre application App Service.

## <a name="related-content"></a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[portail Azure]: https://portal.azure.com/
[alternative method]:#advanced
