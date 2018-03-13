---
title: "Sécuriser des connecteurs personnalisés avec Azure AD - Azure Logic Apps | Microsoft Docs"
description: "Ajoutez un dispositif de sécurité et d’authentification à une API et à un connecteur avec Azure Active Directory (Azure AD)"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: dae76a4230a1cfbe8970bbb1015041fde7765f49
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2018
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Ajoutez un dispositif de sécurité à une API et à un connecteur avec Azure Active Directory (Azure AD)

Pour sécuriser les appels entre votre API Web et votre connecteur personnalisé, ajoutez-leur l’authentification Azure AD. Dans ce scénario, vous allez créer deux applications Azure Active Directory (Azure AD) : l’une sécurise votre API Web, l’autre sécurise l’inscription de votre connecteur et ajoute l’accès délégué. 

Ce didacticiel prend l’API Azure Resource Manager comme exemple. Azure Resource Manager permet de gérer les composants d’une solution créée dans Azure, par exemple des bases de données, des machines virtuelles et des applications web. Un connecteur personnalisé pour Azure Resource Manager peut être utile pour pouvoir gérer les ressources Azure à partir des workflows. Pour plus d'informations, consultez [Présentation d'Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="prerequisites"></a>configuration requise

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez commencer avec un [compte Azure gratuit](https://azure.microsoft.com/free/). Sinon, souscrivez un [abonnement avec paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* Dans ce didacticiel, [l’exemple de fichier OpenAPI pour Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

  > [!NOTE] 
  > L’exemple de fichier OpenAPI ne définit pas toutes les opérations Azure Resource Manager ; actuellement, il ne contient que l’opération permettant de [Lister tous les abonnements](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). Vous pouvez modifier ce fichier OpenAPI ou en créer un autre à l’aide de [l’éditeur OpenAPI en ligne](http://editor.swagger.io/).
  >
  > Vous pouvez appliquer ce didacticiel à toutes les API RESTful pour lesquelles vous souhaitez utiliser l’authentification Azure AD.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Créer une première application Azure AD pour sécuriser une API Web

La première application Azure AD effectue une authentification lorsque le connecteur personnalisé appelle l’API, Azure Resource Manager dans cet exemple.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Si vous avez plusieurs clients Azure Active Directory, vérifiez que la connexion est établie avec le bon répertoire sous votre nom d’utilisateur. 

   ![Confirmer votre répertoire](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Pour changer de répertoire, cliquez sur votre nom d’utilisateur, puis sélectionnez le répertoire que vous souhaitez.

2. Dans le menu principal Azure, cliquez sur **Azure Active Directory** pour pouvoir consulter votre répertoire actuel.

   ![Sélectionner « Azure Active Directory »](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Si le menu principal Azure n’affiche pas **Azure Active Directory**, choisissez **Tous les services**. Dans la zone **Filtre**, tapez « Azure Active Directory », puis cliquez sur **Azure Active Directory**.

3. Dans le menu du répertoire, sous **Gérer**, cliquez sur **Inscription d’applications**. Dans la liste des applications inscrites, cliquez sur **+ Nouvelle inscription d’application**.

   ![Sélectionner « Inscription d’applications », « + Nouvelle inscription d’application »](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. Sous **Créer**, indiquez les détails de votre application Azure AD, comme le décrit le tableau, puis cliquez sur **Créer**. 

   ![Créez une application Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **Name** | *nom-application-api-web* | Nom de l’application Azure AD de votre API Web | 
   | **Type d’application** | **Application web / API** | Type de votre application | 
   | **URL d’authentification** | `https://login.windows.net` | | 
   |||| 

5. Revenez à la liste **Inscriptions d’applications** de votre répertoire, puis sélectionnez votre application Azure AD.

   ![Sélectionnez l’application Azure AD dans la liste](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. Lorsque la page des détails de l’application s’affiche, veillez à **copier et à enregistrer *l’ID de l’application* dans un endroit sûr**. Vous en aurez besoin par la suite.

   ![Enregistrez l’« ID d’application » en vue d’une utilisation ultérieure](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Maintenant, indiquez une URL de réponse pour votre application Azure AD. Dans le menu **Paramètres** de votre application, cliquez sur **URL de réponse**. Entrez cette URL, puis cliquez sur **Enregistrer**.

   ![URL de réponse](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Paramètre | Valeur suggérée | DESCRIPTION | 
   | ------- | --------------- | ----------- | 
   | **URL de réponse** | Pour votre propre API, entrez cette URL : </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Autorisations déléguées** | {non nécessaire} | | 
   | **Clé client** | {non nécessaire} | | 
   |||| 

   > [!TIP]
   > Si le menu **Paramètres** n’est pas encore apparu, cliquez sur **Paramètres** ici :
   >
   > ![Cliquez sur « Paramètres »](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Créer une deuxième application Azure AD pour le connecteur personnalisé

Votre deuxième application Azure AD sécurise l’inscription de votre connecteur personnalisé et ajoute un accès délégué à l’API Web protégée par la première application Azure AD. 

> [!IMPORTANT]
> Vérifiez que les deux applications Azure AD se trouvent dans le même répertoire.

1. Revenez à la liste **Inscriptions d’applications** et cliquez sur **Nouvelle inscription d’application**.

   ![Sélectionner « Inscription d’applications », « + Nouvelle inscription d’application »](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. Sous **Créer**, indiquez les détails de votre deuxième application Azure AD, comme le décrit le tableau, puis cliquez sur **Créer**. 

   ![Créez une application Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **Name** | *nom-de-votre-connecteur* | Nom de l’application Azure AD de votre connecteur | 
   | **Type d’application** | **Application web / API** | Type de votre application | 
   | **URL d’authentification** | `https://login.windows.net` | | 
   |||| 

3. Revenez à la liste **Inscriptions d’applications** de votre répertoire, puis sélectionnez votre deuxième application Azure AD.

   ![Sélectionnez la deuxième application Azure AD dans la liste](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. Lorsque la page des détails de l’application s’affiche, veillez à **copier et à enregistrer *l’ID de cette application* dans un endroit sûr** également. Vous en aurez besoin par la suite.

   ![Enregistrez l’« ID d’application » en vue d’une utilisation ultérieure](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Maintenant, indiquez une URL de réponse pour votre application Azure AD. Dans le menu **Paramètres** de votre application, cliquez sur **URL de réponse**. Entrez cette URL, puis cliquez sur **Enregistrer**.

   | Paramètre | Valeur suggérée | 
   | ------- | --------------- | 
   | **URL de réponse** | Pour le connecteur personnalisé Azure Resource Manager, entrez cette URL : `https://msmanaged-na.consent.azure-apim.net/redirect`. | 
   ||| 

   > [!TIP]
   > Si le menu **Paramètres** n’est pas encore apparu, cliquez sur **Paramètres** ici :
   >
   > ![Cliquez sur « Paramètres »](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. Revenez dans le menu **Paramètres** et cliquez sur **Autorisations requises** > **Ajouter**.

   ![Autorisations requises > Ajouter](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. À l’ouverture du menu **Ajouter un accès à l’API**, cliquez sur **Sélectionner une API** > 
**API Gestion des services Windows Azure** > **Sélectionner **.

   ![Sélectionnez une API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. Dans le menu **Ajouter un accès à l’API**, cliquez sur **Sélectionner les autorisations**. Sous **Autorisations déléguées**, sélectionnez **Accéder à la Gestion des services Azure en tant qu’utilisateurs de l’organisation** > **Sélectionner**.

   ![Cliquez sur « Autorisations déléguées » > « Accéder à la Gestion des services Azure en tant qu’utilisateurs de l’organisation »](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   Sinon, pour les autres options :

   | Option | Valeur suggérée | DESCRIPTION | 
   | ------ | --------------- | ----------- | 
   | **Autorisations déléguées** | | Sélectionnez les autorisations d’accès délégué à votre API Web | 
   |||| 

9. Maintenant, dans le menu **Ajouter un accès à l’API**, cliquez sur **Terminé**.

   ![Menu « Ajouter un accès aux API » > « Terminé »](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Maintenant, générez une *clé client*, ou « secret », pour l’application Azure AD de votre connecteur. 

    1. Revenez au menu **Paramètres** et cliquez sur **Clés**. 
    Donnez un nom de 16 caractères maximum à votre clé, sélectionnez une période d’expiration et cliquez sur **Enregistrer**.

       ![Créez une clé client](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. Lorsque votre clé générée s’affiche, **veillez à la copier et à l’enregistrer dans un endroit sûr** avant de quitter la page **Clés**.
    
       ![Copiez et enregistrez votre clé manuellement](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. Après avoir enregistré votre clé, vous pouvez fermer le menu **Paramètres** en toute sécurité.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Ajouter un dispositif d’authentification Azure AD à l’application API Web

Maintenant, activez l’authentification de votre API Web avec votre première application Azure AD. Pour plus d’informations, consultez la page [Guide pratique pour configurer votre application App Service de façon à utiliser la connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. Sur le [Portail Azure](https://portal.azure.com), trouvez l’application API Web que vous avez précédemment publiée dans [Créer des connecteurs personnalisés à partir d’API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. Sous **Paramètres**, cliquez sur **Authentification / Autorisation**. 

   1. Sous **Authentification App Service**, cliquez sur **Activée**.
   2. Pour **Mesure à prendre quand une demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.
   3. Pour **Fournisseurs d’authentification**, sélectionnez **Azure Active Directory**. 

   ![Cliquez sur « Authentification / autorisation »](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. Sur la page **Paramètres Azure Active Directory** :

   1. Sous **Mode de gestion**, cliquez sur **Express**.

   2. Maintenant, sélectionnez l’application Azure AD que votre application API Web utilise pour l’authentification. 
   Cliquez sur **Sélectionner une application AD existante** > **Application Azure AD**.

   3. Sous **Applications Azure AD**, sélectionnez l’application Azure AD que vous avez créée précédemment pour votre application API Web. 
   
   4. Cliquez sur **OK** jusqu’à revenir à la page **Authentification / autorisation**.

   Par exemple : 

   ![Sélectionnez l’application Azure AD qui représente votre application API Web](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. Sur la page **Authentification / Autorisation**, cliquez sur **Enregistrer**.

   ![Enregistrez les paramètres d’authentification](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   Votre application API Web peut à présent utiliser Azure AD pour l’authentification.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Configurer l’authentification Azure AD dans le fichier OpenAPI de l’API Web

Ouvrez le fichier OpenAPI de votre application API Web afin d’ajouter l’objet `securityDefintions` et l’authentification Azure AD sous la propriété `host`. Voici un exemple qui montre la propriété `host` et l’objet `securityDefinitions` :

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
Vous pouvez maintenant créer et inscrire votre connecteur personnalisé.

## <a name="next-steps"></a>Étapes suivantes

* [Inscrire votre connecteur](../logic-apps/logic-apps-custom-connector-register.md)
* [FAQ sur les connecteurs personnalisés](../logic-apps/custom-connector-faq.md)
