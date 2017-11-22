---
title: Authentification Azure Active Directory et Resource Manager | Microsoft Docs
description: "Guide de développement portant sur l’authentification avec l’API Azure Resource Manager et Azure Active Directory pour intégrer une application dans d’autres abonnements Azure."
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2017
ms.author: dugill;tomfitz
ms.openlocfilehash: 0b7ddaa7e8a98cdff0e92c87f8a1f7e24efbd67e
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Utiliser l’API d’authentification de Resource Manager pour accéder aux abonnements
## <a name="introduction"></a>Introduction
Si, en tant que développeur de logiciels, vous souhaitez créer une application pour gérer les ressources Azure d’un client, cet article vous explique comment effectuer l’authentification avec les API Azure Resource Manager et accéder aux ressources dans d’autres abonnements.

Votre application peut accéder à ces API de différentes manières :

1. **Accès de l’utilisateur et de l’application**: pour les applications qui accèdent aux ressources pour le compte d’un utilisateur connecté. Cette approche fonctionne pour les applications, telles que les applications web et les outils en ligne de commande, qui n’assurent que la « gestion interactive » des ressources Azure.
2. **Accès de l’application uniquement**: pour les applications qui exécutent des services démons et des tâches planifiées. L’identité de l’application se voit octroyer un accès direct aux ressources. Cette approche fonctionne pour les applications qui nécessitent un accès sans affichage (sans assistance) prolongé à Azure.

Cet article explique pas à pas comment créer une application qui met en œuvre ces deux méthodes d’autorisation. Elle montre comment effectuer chaque étape avec l’API REST ou C#. L’application MVC ASP.NET complète est disponible à l’adresse [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Ce que fait l’application web
L’application web :

1. Connecte un utilisateur Azure.
2. Demande à l’utilisateur d’autoriser l’application web à accéder à Resource Manager.
3. Se procure le jeton d’accès de l’utilisateur et de l’application pour accéder à Resource Manager.
4. Utilise le jeton (de l’étape 3) pour affecter le principal du service de l’application à un rôle de l’abonnement. Cette étape donne à l’application un accès à long terme à l’abonnement.
5. Obtient le jeton d’accès de l’application uniquement.
6. Utilise le jeton (de l’étape 5) pour gérer les ressources de l’abonnement à l’aide de Resource Manager.

Voici le schéma de l’application web.

![Flux d’authentification de Resource Manager](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

En tant qu’utilisateur, vous indiquez l’ID de l’abonnement que vous souhaitez utiliser :

![indiquer l’ID de l’abonnement](./media/resource-manager-api-authentication/sample-ux-1.png)

Sélectionnez le compte à utiliser pour la connexion.

![sélectionner le compte](./media/resource-manager-api-authentication/sample-ux-2.png)

Indiquez vos informations d’identification.

![Fournir des informations d’identification](./media/resource-manager-api-authentication/sample-ux-3.png)

Autorisez l’application à accéder à vos abonnements Azure :

![Accorder l’accès](./media/resource-manager-api-authentication/sample-ux-4.png)

Gérez vos abonnements connectés :

![Connecter un abonnement](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Inscription de l’application
Avant de commencer l’écriture du code, inscrivez votre application web à Azure Active Directory (AD). L’inscription de l’application entraîne la création d’une identité centrale pour votre application dans Azure AD. Elle contient des informations de base sur votre application, par exemple l’ID du client OAuth, les URL de réponse et les informations d’identification que votre application utilise pour s’authentifier et accéder aux API d’Azure Resource Manager. L’inscription de l’application enregistre également les différentes autorisations dont votre application a besoin pour accéder aux API Microsoft pour le compte de l’utilisateur.

Votre application ayant accès à plusieurs abonnements, vous devez la configurer comme une application mutualisée. Pour passer la validation, indiquez le domaine associé à votre annuaire Azure Active Directory. Pour afficher les domaines associés à votre annuaire Azure Active Directory, connectez-vous au portail.

L’exemple suivant montre comment inscrire l’application à l’aide d’Azure PowerShell. Pour que cette commande fonctionne, vous devez disposer de la version la plus récente (août 2016) d’Azure PowerShell.

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Pour vous connecter en tant qu’application Active Directory, il vous faut l’ID et le mot de passe de l’application. Pour afficher l’ID d’application renvoyé par la commande précédente, utilisez :

    $app.ApplicationId

L’exemple suivant montre comment inscrire l’application à l’aide de l’interface de ligne de commande Azure.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Les résultats incluent l’ID de l’application, dont vous avez besoin pour vous authentifier en tant qu’application.

### <a name="optional-configuration---certificate-credential"></a>Configuration facultative : informations d’identification des certificats
Azure AD prend également en charge les informations d’identification des certificats pour les applications : vous créez un certificat auto-signé, conservez la clé privée et ajoutez la clé publique lors de l’inscription de votre application Azure AD. Dans le cadre de l’authentification, votre application envoie une petite charge utile à Azure AD, signée à l’aide de votre clé privée ; ensuite, Azure AD valide la signature à l’aide de la clé publique que vous avez inscrite.

Pour plus d’informations sur la création d’une application Active Directory avec un certificat, consultez [Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) ou [Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Obtention de l’ID du client à partir de l’ID d’abonnement
Pour demander un jeton permettant d’appeler Resource Manager, votre application doit connaître l’ID du client Azure AD qui héberge l’abonnement Azure. Vos utilisateurs connaissent sûrement leurs ID d’abonnement, mais pas nécessairement leurs ID de client Azure Active Directory. Pour obtenir l’ID de client d’un utilisateur, demandez-lui l’ID d’abonnement. Indiquez cet ID d’abonnement lors de l’envoi d’une demande sur l’abonnement :

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

La demande échoue, car l’utilisateur n’est pas encore connecté, mais vous pouvez récupérer l’ID de client dans la réponse. Dans cette exception, récupérez l’ID de client dans la valeur de l’en-tête de réponse de **WWW-Authenticate**. Cette implémentation est décrite dans la méthode [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obtention du jeton de l’utilisateur et de l’application
Votre application redirige l’utilisateur vers Azure AD avec une requête d’autorisation OAuth 2.0, afin d’authentifier les informations d’identification de l’utilisateur et d’obtenir un code d’autorisation. Votre application utilise ce code d’autorisation pour obtenir un jeton d’accès pour Resource Manager. La méthode [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) crée la demande d’autorisation.

Cet article montre les demandes de l’API REST pour authentifier l’utilisateur. Vous pouvez également utiliser des bibliothèques d’assistance pour effectuer l’authentification dans votre code. Pour plus d’informations sur ces bibliothèques, consultez [Bibliothèques d’authentification d’Azure Active Directory](../active-directory/active-directory-authentication-libraries.md). Pour des conseils sur l’intégration de la gestion de l’identité dans une application, consultez le [Guide du développeur Azure Active Directory](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Demande d’autorisation (OAuth 2.0)
Envoyez une demande d’autorisation Open ID Connect/OAuth2.0 au point de terminaison d’autorisation Azure AD :

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Les paramètres de la chaîne de requête, qui sont disponibles pour cet exemple, sont décrits dans l’article [Demander un code d’autorisation](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code).

L’exemple suivant indique comment demander l’autorisation OAuth2.0 :

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD authentifie l’utilisateur et, si nécessaire, lui demande d’autoriser l’accès à l’application. Il renvoie le code d’autorisation à l’URL de réponse de votre application. Selon le paramètre response_mode demandé, Azure AD renvoie les données dans la chaîne de requête ou sous la forme de données de publication.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Demande d’autorisation (Open ID Connect)
Si vous souhaitez limiter l’accès à Azure Resource Manager pour le compte de l’utilisateur, mais autoriser ce dernier à se connecter à votre application à l’aide de son compte Azure AD, envoyez une demande d’autorisation Open ID Connect. Avec Open ID Connect, votre application reçoit également un paramètre id_token, envoyé par Azure AD, que votre application peut utiliser pour connecter l’utilisateur.

Les paramètres de la chaîne de requête, qui sont disponibles pour cette demande, sont décrits dans l’article [Envoyer la requête de connexion](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request).

Exemple de demande Open ID Connect :

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD authentifie l’utilisateur et, si nécessaire, lui demande d’autoriser l’accès à l’application. Il renvoie le code d’autorisation à l’URL de réponse de votre application. Selon le paramètre response_mode demandé, Azure AD renvoie les données dans la chaîne de requête ou sous la forme de données de publication.

Exemple de réponse Open ID Connect :

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Demande de jeton (flux d’octroi d’un code d’autorisation OAuth2.0)
Maintenant que votre application a reçu le code d’autorisation d’Azure AD, il est temps d’obtenir le jeton d’accès pour Azure Resource Manager.  Publiez une demande de jeton d’octroi de code OAuth2.0 au point de terminaison de jeton Azure AD :

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Les paramètres de la chaîne de requête, qui sont disponibles pour cet exemple, sont décrits dans l’article [Utiliser le code d’autorisation](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

L’exemple suivant illustre une demande de jeton d’octroi de code avec les informations d’identification de mot de passe :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Lorsque vous utilisez des informations d’identification de certificat, créez une clé d’authentification web JSON (JWT) et effectuez la signature (RSA SHA256) à l’aide de la clé privée des informations d’identification du certificat de votre application. Les types de revendication du jeton sont affichés dans [Demandes de jeton JWT](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Consultez également le [code Active Directory Authentication Library (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) pour la signature des jetons JWT d’assertion de client.

Pour en savoir plus sur l’authentification du client, consultez les [spécifications d’Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) .

L’exemple suivant illustre une demande de jeton d’octroi de code avec les informations d’identification de certificat :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Exemple de réponse de jeton d’octroi de code :

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Gérer la réponse de jeton d’octroi de code
Une réponse de jeton correcte contient le jeton d’accès (utilisateur et application) d’Azure Resource Manager. Votre application utilise ce jeton d’accès pour accéder à Resource Manager pour le compte de l’utilisateur. La durée de vie des jetons d’accès émis par Azure AD est d’une heure. Il est peu probable que votre application web doive renouveler le jeton d’accès (utilisateur et application). Si le cas de figure se présente, utilisez le jeton d’actualisation reçu par votre application dans la réponse de jeton. Publiez une demande de jeton OAuth2.0 au point de terminaison du jeton Azure AD :

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Les paramètres à utiliser avec la demande d’actualisation sont décrits dans la rubrique [Actualisation des jetons d’accès](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

L’exemple suivant indique comment utiliser le jeton d’actualisation :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Bien qu’il soit possible d’utiliser les jetons d’actualisation pour obtenir de nouveaux jetons d’accès pour Azure Resource Manager, ils ne sont pas adaptés à un accès hors connexion par votre application. Les jetons d’actualisation ont une durée de vie limitée ; ils sont liés à l’utilisateur. Si ce dernier quitte l’organisation, l’application utilisant le jeton d’actualisation perd son accès. Cette approche n’est pas adaptée aux applications qui sont utilisées par les équipes pour gérer leurs ressources Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Vérifier si utilisateur peut autoriser l’accès à l’abonnement
Votre application bénéficie désormais d’un jeton permettant d’accéder à Microsoft Azure Resource Manager pour le compte de l’utilisateur. L’étape suivante consiste à connecter votre application à l’abonnement. Une fois connectée, votre application peut gérer les abonnements, même lorsque l’utilisateur n’est pas présent (accès hors connexion prolongé).

Pour chaque abonnement à connecter, appelez l’API [Répertorier toutes les autorisations de Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions) afin de déterminer si l’utilisateur bénéficie de droits de gestion des accès concernant l’abonnement.

La méthode [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) de l’exemple d’application MVC ASP.NET implémente cet appel.

L’exemple suivant indique comment demander les autorisations d’un utilisateur sur un abonnement. L’ID de cet abonnement est 83cfe939-2402-4581-b761-4f59b0a041e4.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Voici un exemple de réponse à une demande d’obtention des autorisations d’un utilisateur concernant un abonnement :

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

L’API d’autorisation renvoie plusieurs autorisations. Chaque autorisation est constituée d’actions autorisées (**actions**) et interdites (**notactions**). Si une action figure dans la liste d’actions autorisées d’une autorisation, mais pas dans sa liste d’actions interdites, l’utilisateur est autorisé à effectuer cette action. **microsoft.authorization/roleassignments/write** . Votre application doit analyser le résultat des autorisations, afin de rechercher une correspondance d’expression régulière sur cette chaîne d’action dans les **actions** autorisées et **interdites** de chaque autorisation.

## <a name="get-app-only-access-token"></a>Obtention du jeton d’accès de l’application uniquement
À présent, vous savez si l’utilisateur peut autoriser l’accès à l’abonnement Azure. Les étapes suivantes sont celles-ci :

1. Attribuer le rôle RBAC approprié à l’identité de votre application sur l’abonnement.
2. Valider l’attribution d’accès en demandant l’autorisation de l’application sur l’abonnement ou en accédant à Resource Manager à l’aide du jeton d’application uniquement.
3. Enregistrer la connexion dans la structure de données des abonnements connectés associée à vos applications, en assurant la persistance de l’ID de l’abonnement.

Étudions la première étape plus en détail. Pour attribuer le rôle RBAC approprié à l’identité de l’application, vous devez déterminer :

* l’ID objet de l’identité de votre application dans l’annuaire Azure Active Directory de l’utilisateur ;
* l’identificateur du rôle RBAC requis par votre application sur l’abonnement.

Lorsque votre application authentifie un utilisateur à partir d’une instance d’Azure AD, elle crée un objet principal du service pour votre application dans cette instance. Azure permet d’attribuer des rôles RBAC à des principaux de service, afin d’accorder un accès direct aux applications correspondantes sur des ressources Azure. Cette action correspond exactement à ce que vous souhaitez faire. Interrogez l’API Azure AD Graph pour déterminer l’identificateur du principal du service de votre application dans l’instance Azure AD de l’utilisateur connecté.

Vous disposez d’un jeton d’accès pour Azure Resource Manager uniquement. Il vous faut donc un nouveau jeton d’accès pour appeler l’API Microsoft Azure AD Graph. Chaque application dans Azure AD étant autorisée à interroger son propre objet principal du service, un jeton d’accès d’application uniquement suffit.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obtenir un jeton d’accès d’application uniquement pour l’API Microsoft Azure AD Graph
Pour authentifier votre application et obtenir un jeton pour l’API Microsoft Azure AD Graph, envoyez une demande de jeton de flux OAuth2.0 pour l’octroi d’informations d’identification du client au point de terminaison de jeton Azure AD (**https://login.microsoftonline.com/{nom_domaine_répertoire}/OAuth2/Token**).

La méthode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) de l’exemple d’application MVC ASP.NET permet d’obtenir un jeton d’accès d’application uniquement pour l’API Graph, via la bibliothèque d’authentification Active Directory Authentication Library (ADAL) pour .NET.

Les paramètres de la chaîne de requête, qui sont disponibles pour cette demande, sont décrits dans l’article [Demander un jeton d’accès](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token).

Voici un exemple de demande de jeton d’octroi d’informations d’identification du client :

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Voici un exemple de réponse à une demande de jeton d’octroi d’informations d’identification du client :

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obtenir le principal de service d’application ObjectId dans l’instance Azure AD de l’utilisateur
Maintenant, utilisez le jeton d’accès d’application uniquement pour interroger l’API des [principaux du service Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) et déterminer l’ID d’objet du principal du service de l’application dans l’annuaire.

La méthode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) de l’exemple d’application MVC ASP.NET implémente cet appel.

L’exemple suivant indique comment demander un principal du service d’une application. L’ID client de l’application est le suivant : a0448380-c346-4f9f-b897-c18733de9394.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

L’exemple suivant inclut une réponse à la demande d’obtention du principal de service d’une application.

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obtenir l’identificateur du rôle Azure RBAC
Pour attribuer le rôle RBAC approprié à votre principal de service, vous devez déterminer l’identificateur du rôle Azure RBAC.

Le rôle RBAC approprié pour votre application :

* Si votre application ne surveille que l’abonnement sans apporter de modifications, des autorisations de lecture sur l’abonnement suffisent. Attribuez le rôle **Lecteur** .
* Si votre application gère l’abonnement dans Azure, ainsi que la création, la modification et la suppression d’entités, il lui faut des autorisations de contributeur.
  * Pour gérer un type particulier de ressource, attribuez des rôles de contributeur propres à cette ressource (de type Contributeur de machines virtuelles, Contributeur de réseaux virtuels, Contributeur de comptes de stockage, etc.).
  * Pour gérer un type de ressource, attribuez le rôle **Contributeur** .

Comme tous les utilisateurs peuvent voir le rôle attribué à votre application, sélectionnez le privilège présentant le niveau requis le moins élevé.

Appelez l’ [API de définition de rôle Resource Manager](https://docs.microsoft.com/rest/api/authorization/roledefinitions) pour répertorier tous les rôles RBAC Azure et recherchez la définition de rôle souhaitée par son nom, puis itérez le résultat.

La méthode [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) de l’exemple d’application MVC ASP.NET implémente cet appel.

L’exemple de demande suivant indique comment obtenir l’identificateur de rôle RBAC Azure. L’ID d’abonnement est 09cbd307-aa71-4aca-b346-5f253e6e3ebb.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La réponse présente le format suivant :

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Il est inutile d’appeler cette API en continu. Une fois que vous avez déterminé le GUID réservé de la définition de rôle, vous pouvez construire l’ID de définition de rôle ainsi :

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Voici les identifiants des rôles intégrés couramment utilisés :

| Rôle | GUID |
| --- | --- |
| Lecteur |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Contributeur |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Collaborateur de machine virtuelle |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Collaborateur de réseau virtuel |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Collaborateur de compte de stockage |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Collaborateur de site web |de139f84-1756-47ae-9be6-808fbbe84772 |
| Collaborateur de plan web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Collaborateur SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Collaborateur de base de données SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Attribuer un rôle RBAC à l’application
Vous avez à présent tout le nécessaire pour attribuer le rôle RBAC approprié au principal de service de votre application sur l’abonnement sélectionné, à l’aide de l’API [Créer une attribution de rôle Resource Manager](https://docs.microsoft.com/rest/api/authorization/roleassignments) .

La méthode [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) de l’exemple d’application MVC ASP.NET implémente cet appel.

Voici un exemple de demande d’attribution d’un rôle RBAC à l’application :

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Dans cette demande, les valeurs suivantes sont utilisées :

| Guid | Description |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |ID d’abonnement |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |ID objet du principal du service de l’application |
| acdd72a7-3385-48ef-bd42-f606fba81ae7 |ID du rôle Lecteur |
| 4f87261d-2816-465d-8311-70a27558df4c |Nouveau GUID créé pour l’attribution du nouveau rôle |

La réponse présente le format suivant :

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obtenir un jeton d’accès d’application uniquement pour Azure Resource Manager
Pour vérifier que cette application possède l’accès souhaité sur l’abonnement, effectuez un test sur l’abonnement à l’aide d’un jeton d’application uniquement.

Pour obtenir un jeton d’accès d’application uniquement, suivez la procédure de la section [Obtenir un jeton d’accès d’application uniquement pour l’API Microsoft Azure AD Graph](#app-azure-ad-graph), en utilisant une valeur différente pour le paramètre de ressource :

    https://management.core.windows.net/

La méthode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de l’exemple d’application MVC ASP.NET permet d’obtenir un jeton d’accès d’application uniquement pour Azure Resource Manager, via la bibliothèque d’authentification ADAL pour .NET.

#### <a name="get-applications-permissions-on-subscription"></a>Obtention des autorisations de l’application sur l’abonnement
Pour vérifier que votre application possède l’accès désiré sur un abonnement Azure, vous pouvez également appeler l’API [Répertorier toutes les autorisations](https://docs.microsoft.com/rest/api/authorization/permissions). Cette approche est similaire à celle qui vous a permis de déterminer si l’utilisateur dispose des droits de gestion des accès sur l’abonnement. Cette fois cependant, appelez l’API d’autorisations avec le jeton d’accès d’application uniquement que vous avez reçu à l’étape précédente.

La méthode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de l’exemple d’application MVC ASP.NET implémente cet appel.

## <a name="manage-connected-subscriptions"></a>Gestion des abonnements connectés
Lorsque le rôle RBAC approprié est attribué au principal de service de votre application sur l’abonnement, cette dernière peut continuer à le surveiller/le gérer via des jetons d’accès d’application uniquement pour Azure Resource Manager.

Si le propriétaire d’un abonnement supprime le rôle attribué à votre application via des outils de ligne de commande ou le portail, votre application ne peut plus accéder à cet abonnement. Dans ce cas, informez l’utilisateur que la connexion à l’abonnement a été supprimée depuis l’extérieur de l’application et donnez-lui la possibilité de « réparer » cette connexion. Dans ce cas, la « réparation » consiste à recréer l’attribution de rôle supprimée hors connexion.

Tout comme vous avez permis à l’utilisateur de connecter des abonnements à votre application, vous devez l’autoriser à les en déconnecter. Dans le contexte de la gestion des accès, le terme « déconnexion » correspond à la suppression du rôle attribué au principal de service de l’application sur l’abonnement. Si vous le souhaitez, vous pouvez également supprimer n’importe quel état de l’application concernant l’abonnement.
Seuls les utilisateurs dotés d’une autorisation de gestion des accès sur l’abonnement peuvent se déconnecter de l’abonnement.

La méthode [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) de l’exemple d’application MVC ASP.NET implémente cet appel.

Et voilà ! Les utilisateurs peuvent maintenant se connecter et gérer facilement leurs abonnements Azure avec votre application.
