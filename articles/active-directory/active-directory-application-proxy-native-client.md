---
title: Publier des applications clientes natives - Azure AD | Microsoft Docs
description: "Explique comment activer des applications clientes natives de manière à communiquer avec le connecteur de proxy d'application Azure AD pour fournir un accès à distance sécurisé à vos applications locales."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 0753db5c5accf67411a9968f56aa9ad2158bad89
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Guide pratique pour activer des applications clientes natives de manière à ce qu’elles interagissent avec des applications proxy

En plus des applications web, vous pouvez aussi utiliser le proxy d’application Azure Active Directory pour publier des applications clientes natives configurées avec la bibliothèque d’authentification Azure AD (ADAL). Les applications clientes natives sont différentes des applications web du fait qu’elles sont installées sur un appareil alors que les applications web sont accessibles via un navigateur. 

Le proxy d’application prend en charge les applications clientes natives en acceptant les jetons générés par Azure AD qui sont envoyés dans les en-têtes. Le service de proxy d’application effectue l’authentification pour le compte des utilisateurs. Cette solution n’utilise pas de jetons d’application pour l’authentification. 

![Relation entre les utilisateurs finaux, Azure Active Directory et les applications publiées](./media/active-directory-application-proxy-native-client/richclientflow.png)

Utilisez la bibliothèque Azure AD Authentication, qui prend en charge l’authentification et de nombreux environnements clients, pour publier des applications natives. Le proxy d'application est conforme au [scénario Application Native vers API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). 

Cet article vous guide dans quatre étapes pour publier une application native avec le proxy d’application et la bibliothèque Azure AD Authentication. 

## <a name="step-1-publish-your-application"></a>Étape 1 : publier votre application
Publiez votre application proxy comme vous le feriez pour toute autre application et affectez des utilisateurs pour accéder à votre application. Pour plus d'informations, consultez [Publier des applications avec le proxy d'application](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Étape 2 : configurer votre application
Configurez votre application native comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à **Azure Active Directory** > **Inscriptions d’application**.
3. Sélectionnez **Nouvelle inscription d’application**.
4. Spécifiez un nom pour votre application, sélectionnez **Natif** comme type d’application et indiquez l’URI de redirection de votre application. 

   ![Créer une nouvelle inscription d’application](./media/active-directory-application-proxy-native-client/create.png)
5. Sélectionnez **Créer**.

Pour plus d’informations sur la création d’une nouvelle inscription d’application, consultez [Integrating applications with Azure Active Directory](.//develop/active-directory-integrating-applications.md) (Intégration d’applications à Azure Active Directory).


## <a name="step-3-grant-access-to-other-applications"></a>Étape 3: accorder l’accès à d’autres applications
Activez l'application native à exposer à d'autres applications dans votre répertoire :

1. Toujours dans **Inscriptions d’application**, sélectionnez la nouvelle application native que vous venez de créer.
2. Sélectionnez **Autorisations requises**.
3. Sélectionnez **Ajouter**.
4. Ouvrez la première étape, **Sélectionner une API**.
5. Utilisez la barre de recherche pour retrouver l’application de proxy d’application que vous avez publiée dans la première section. Choisissez cette application, puis cliquez sur **Sélectionner**. 

   ![Rechercher l’application de proxy](./media/active-directory-application-proxy-native-client/select_api.png)
6. Ouvrez la deuxième étape, **Sélectionner les autorisations**.
7. Utilisez la case à cocher pour accorder l’accès de votre application native à votre application de proxy, puis cliquez sur **Sélectionner**.

   ![Accorder l’accès à l’application de proxy](./media/active-directory-application-proxy-native-client/select_perms.png)
8. Sélectionnez **Terminé**.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>Étape 4 : modifier la bibliothèque d’authentification Active Directory
Modifiez le code de l’application native dans le contexte de l’authentification de l’Active Directory Authentication Library (ADAL) de manière à inclure le texte suivant :

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = authContext.AcquireToken("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Les variables dans l’exemple de code doivent être remplacées comme suit :

* L’**ID de locataire** est indiqué dans le portail Azure. Accédez à **Azure Active Directory** > **Propriétés** et copiez l’ID de répertoire. 
* L’**URL externe** est l’URL frontale que vous avez entrée dans l’application de proxy. Pour retrouver cette valeur, accédez à la section **Proxy d’application** de l’application de proxy.
* **L’ID d’application** de l’application native est indiqué sur la page **Propriétés** de l’application native.
* L**URI de redirection de l’application native** est indiqué sur la page **URI de redirection** de l’application native.

Une fois que la bibliothèque ADAL a été modifiée avec ces paramètres, vos utilisateurs doivent pouvoir s’authentifier auprès des applications clientes natives même quand ils ne sont pas sur le réseau d’entreprise. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux d’application native, consultez [Application native vers API web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)

Découvrez plus en détail la configuration de l’[authentification unique pour le proxy d’application](application-proxy-sso-overview.md).
