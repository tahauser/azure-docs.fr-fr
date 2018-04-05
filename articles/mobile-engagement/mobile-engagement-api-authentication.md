---
title: Authentifier avec l’API REST de Mobile Engagement
description: Décrit comment authentifier avec l’API REST Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 5979ded9afaa31054f835b5f16fe525809f5730d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Authentifier avec l’API REST de Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

## <a name="overview"></a>Vue d'ensemble

Ce document décrit comment obtenir un jeton OAuth Azure AD valide pour s’authentifier auprès des API REST Mobile Engagement.

Il suppose que vous disposez d’un abonnement Azure valide et que vous avez créé une application Mobile Engagement à l’aide d’un des [didacticiels pour les développeurs](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentification

Un jeton OAuth basé sur Microsoft Azure Active Directory est utilisé pour l’authentification. 

Pour authentifier une requête d’API, un en-tête d’autorisation doit être ajouté à chaque requête. L’en-tête d’autorisation se présente sous le format suivant :

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Les jetons Azure Active Directory expirent dans une heure.
> 
> 

Il existe plusieurs façons d’obtenir un jeton. Comme les API sont appelées à partir d’un service cloud, vous allez utiliser une clé API. Dans la terminologie Azure, une clé API est appelée mot de passe principal du service. La procédure suivante décrit comment la configurer manuellement.

### <a name="one-time-setup-using-a-script"></a>Installation unique (à l’aide d’un script)

Pour effectuer l’installation à l’aide d’un script PowerShell, suivez les instructions suivantes. Un script PowerShell nécessite un temps réduit d’installation, mais utilise les valeurs par défaut les plus autorisées. 

Si vous le souhaitez, vous pouvez également suivre les instructions de l’ [installation manuelle](mobile-engagement-api-authentication-manual.md) pour effectuer cette opération directement à partir du portail Azure. Lorsque vous configurez à partir du portail Azure, vous pouvez procéder de manière plus détaillée.

1. Récupérer la dernière version d’Azure PowerShell [ici](http://aka.ms/webpi-azps). Pour plus d’informations sur les instructions de téléchargement, consultez [cette vue d’ensemble](/powershell/azure/overview).

2. Une fois PowerShell installé, utilisez les commandes suivantes pour vérifier que le **module Azure** est installé :

    a. Vérifiez que le module Azure PowerShell est disponible dans la liste des modules disponibles.

        Get-Module –ListAvailable

    ![Modules Azure disponibles][1]

    b. Si vous ne trouvez pas le module Azure PowerShell dans la liste ci-dessus, vous devez exécuter ce qui suit :

        Import-Module Azure
3. Connectez-vous à Azure Resource Manager à partir de PowerShell en exécutant la commande suivante. Fournissez le nom d’utilisateur et le mot de passe associés à votre compte Azure : 

        Login-AzureRmAccount
4. Si vous avez plusieurs abonnements, suivez la procédure suivante :

    a. Récupérez une liste de l’ensemble de vos abonnements. Copiez ensuite l’élément **SubscriptionId** de l’abonnement que vous souhaitez utiliser. Assurez-vous que cet abonnement possède l’application Mobile Engagement. Vous vous apprêtez à utiliser cette application pour interagir avec les API. 

        Get-AzureRmSubscription

    b. Exécutez la commande ci-dessous. Fournissez l’élément **SubscriptionId** afin de configurer l’abonnement que vous allez utiliser :

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Copiez le texte du script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) sur votre ordinateur local. Enregistrez-le ensuite en tant qu’applet de commande PowerShell (par exemple, `APIAuth.ps1`), et exécutez-le.

         `.\APIAuth.ps1`.

6. Le script vous demande de fournir une entrée pour **principalName**. Indiquez un nom approprié à attribuer à votre application Active Directory (par exemple, APIAuth). 

7. Une fois l’exécution du script terminée, il affiche les 4 valeurs suivantes. Veillez à les copier, car vous en avez besoin pour vous authentifier par programmation avec Active Directory : 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **Secret**

   Vous utilisez la valeur TenantId `{TENANT_ID}`, la valeur ApplicationId `{CLIENT_ID}` et la valeur Secret `{CLIENT_SECRET}`.

   > [!NOTE]
   > Votre stratégie de sécurité par défaut peut vous empêcher d’exécuter les scripts PowerShell. Dans ce cas, configurez temporairement votre stratégie d’exécution pour permettre l’exécution du script à l’aide de la commande suivante :
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Voici à quoi ressemble l’ensemble d’applets de commande PowerShell.
    ![Applets de commande PowerShell][3]
9. Dans le portail Azure, accédez à Active Directory, sélectionnez **Inscriptions des applications**, puis recherchez votre application pour vérifier qu’elle existe.
    ![Rechercher votre application][4]

### <a name="steps-to-get-a-valid-token"></a>Procédure d'obtention d'un jeton valide

1. Appelez l’API avec les paramètres suivants. Veillez à remplacer l’**ID\_LOCATAIRE**, l’**ID\_CLIENT**, et la **CLÉ\_SECRÈTE** :
   
   * **URL de requête** en tant que `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **En-tête Content-Type HTTP** en tant que `application/x-www-form-urlencoded`
   
   * **Texte de la requête HTTP** en tant que `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Voici un exemple de requête :
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Voici un exemple de réponse :
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     Cet exemple inclut l’encodage de l’URL et les paramètres POST, dans lesquels la valeur `resource` est en fait `https://management.core.windows.net/`. Veillez également à encoder l’URL `{CLIENT_SECRET}`, car elle peut contenir des caractères spéciaux.

     > [!NOTE]
     > Pour le test, vous pouvez utiliser un outil client HTTP comme [Fiddler](http://www.telerik.com/fiddler) ou l’[extension Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Dans chaque appel d'API, incluez l'en-tête de requête d'autorisation :
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Si votre requête renvoie un code d’état 401, vérifiez le corps de la réponse. Il peut vous indiquer que le jeton est expiré. Dans ce cas, récupérez un nouveau jeton.

## <a name="use-the-apis"></a>Utiliser les API
Maintenant que vous avez un jeton valide, vous êtes prêt à passer les appels d'API.

1. Dans chaque demande d’API, vous devez disposer d’un jeton valide, non expiré. Vous avez récupéré le jeton non expiré dans la section précédente.

2. Spécifiez quelques paramètres dans l’URI de la requête identifiant votre application. L’URI de la requête ressemble au code suivant :
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Pour récupérer les paramètres, sélectionnez le nom de votre application. Sélectionnez ensuite **Tableau de bord**. Vous voyez une page comportant les 3 paramètres, comme suit :
   
   * **1**`{subscription-id}`
   * **2**`{app-collection}`
   * **3**`{app-resource-name}`
   * **4** Le nom de votre groupe de ressources va être **MobileEngagement** sauf si vous en avez créé un autre. 

> [!NOTE]
> Ignorez l’adresse racine de l’API, car elle s’appliquait aux API précédentes.
> 
> Si vous avez créé l’application à l’aide du portail Azure, vous devez utiliser le nom de ressource de l’application, qui est différent du nom de l’application elle-même. Si vous avez créé l’application dans le portail Azure, vous devez utiliser le nom de l’application. (Il n’existe aucune distinction entre le nom des ressources d’application et le nom des applications créées dans le nouveau portail.)
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



