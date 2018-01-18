---
title: "Authentifier avec l’API REST de Mobile Engagement"
description: "Décrit comment authentifier avec l’API REST Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 66bcd738b86f846eae3499b289a6629323009a44
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Authentifier avec l’API REST de Mobile Engagement

## <a name="overview"></a>Vue d'ensemble

Ce document décrit comment obtenir un jeton OAuth Azure AD valide pour s’authentifier auprès des API REST Mobile Engagement.

Il suppose que vous disposez d’un abonnement Azure valide et que vous avez créé une application Mobile Engagement à l'aide d'un des [didacticiels pour les développeurs](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentification

Un jeton OAuth basé sur Microsoft Azure Active Directory est utilisé pour l’authentification. 

Pour authentifier une requête API, un en-tête d’autorisation doit être ajouté à chaque requête, qui se présente sous la forme suivante :

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Les jetons Azure Active Directory expirent dans une heure.
> 
> 

Il existe plusieurs façons d’obtenir un jeton. Comme les API sont appelées à partir d’un service cloud, vous voulez utiliser une clé API. Dans la terminologie Azure, une clé d’API est appelée mot de passe principal de du service. La procédure suivante décrit comment la configurer manuellement.

### <a name="one-time-setup-using-script"></a>Installation unique (à l'aide d’un script)

Suivez l’ensemble des instructions ci-dessous pour effectuer l’installation à l’aide d’un script PowerShell qui prend le temps minimal pour l’installation, mais utilise les valeurs par défaut les plus autorisées. Si vous le souhaitez, vous pouvez également suivre les instructions de l’ [installation manuelle](mobile-engagement-api-authentication-manual.md) pour effectuer cette opération directement à partir du portail Azure et affiner la configuration.

1. Récupérez la dernière version d’Azure PowerShell [ici](http://aka.ms/webpi-azps). Consultez ce [lien](/powershell/azure/overview)pour obtenir des instructions de téléchargement.
2. Une fois Azure PowerShell installé, utilisez les commandes suivantes pour vérifier que le **module Azure** est installé :

    a. Vérifiez que le module Azure PowerShell est disponible dans la liste des modules disponibles.

        Get-Module –ListAvailable

    ![Modules Azure disponibles][1]

    b. Si vous ne trouvez pas le module Azure PowerShell dans la liste ci-dessus, vous devez exécuter ce qui suit :

        Import-Module Azure
3. Connectez-vous à Azure Resource Manager à partir de PowerShell en exécutant la commande suivante et en fournissant vos nom d’utilisateur et mot de passe pour votre compte Azure : 

        Login-AzureRmAccount
4. Si vous avez plusieurs abonnements, vous devez exécuter ce qui suit :

    a. Obtenez une liste de tous vos abonnements et copiez le SubscriptionId de l’abonnement que vous souhaitez utiliser. Vérifiez que cet abonnement est le même que celui qui possède l’application Mobile Engagement avec laquelle vous allez interagir à l’aide des API. 

        Get-AzureRmSubscription

    b. Exécutez la commande suivante pour spécifier le SubscriptionId qui servira à configurer l’abonnement à utiliser.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Copiez le texte du script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) sur votre ordinateur local puis enregistrez-le en tant qu’applet de commande PowerShell (par exemple, `APIAuth.ps1`) et exécutez-le `.\APIAuth.ps1`.
6. Le script vous demande de fournir une entrée pour **nom_principal**. Indiquez ici un nom approprié que vous souhaitez utiliser pour créer votre application Active Directory (par exemple, APIAuth). 
7. Une fois le script terminé, il affiche les quatre valeurs suivantes nécessaires pour l’authentification par programmation auprès d’Active Directory. Veillez à bien les copier. 

    **TenantId**, **SubscriptionId**, **ApplicationId** et **Secret**.

    Vous utilisez la valeur TenantId `{TENANT_ID}`, la valeur ApplicationId `{CLIENT_ID}` et la valeur Secret `{CLIENT_SECRET}`.

   > [!NOTE]
   > Votre stratégie de sécurité par défaut peut vous empêche d'exécuter un script PowerShell. Dans ce cas, configurez temporairement votre stratégie d'exécution pour permettre l'exécution du script à l'aide de la commande suivante :
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Voici à quoi ressemble le jeu d’applets de commande PS.
    ![][3]
9. Dans le portail Azure, accédez à Active Directory, cliquez sur **Inscriptions des applications**, puis recherchez votre application afin de vérifier qu’elle existe  ![][4]

### <a name="steps-to-get-a-valid-token"></a>Procédure d'obtention d'un jeton valide

1. Appelez l’API avec les paramètres suivants et veillez à remplacer les valeurs TENANT\_ID, CLIENT\_ID et CLIENT\_SECRET :
   
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
    Voici un exemple de réponse :
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     Cet exemple incluait l’encodage de l’URL des paramètres POST, et la valeur `resource` est en fait `https://management.core.windows.net/`. Veillez également à encoder l’URL `{CLIENT_SECRET}` car elle peut contenir des caractères spéciaux.

     > [!NOTE]
     > Pour le test, vous pouvez utiliser un outil client HTTP comme [Fiddler](http://www.telerik.com/fiddler) ou [l’extension Postman sur Chrome](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 
     > 
     > 
2. Dans chaque appel d'API, incluez l'en-tête de requête d'autorisation :
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Si vous obtenez un code d'état 401, vérifiez le corps de la réponse car il peut vous indiquer que le jeton a expiré. Dans ce cas, récupérez un nouveau jeton.

## <a name="using-the-apis"></a>Utilisation des API
Maintenant que vous avez un jeton valide, vous êtes prêt à passer les appels d'API.

1. Dans chaque requête API, vous devez passer un jeton valide, non expiré, que vous avez obtenu dans la section précédente.
2. Vous devez spécifier certains paramètres dans l'URI de la requête qui identifie votre application. L'URI de la requête ressemble à ceci :
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Pour obtenir les paramètres, cliquez sur le nom de votre application puis sur Tableau de bord : vous verrez alors une page similaire à celle-ci, avec 3 paramètres.
   
   * **1**`{subscription-id}`
   * **2**`{app-collection}`
   * **3**`{app-resource-name}`
   * **4** Le nom de votre groupe de ressources va être **MobileEngagement** sauf si vous en avez créé un autre. 

> [!NOTE]
> <br/>
> 
> 1. Ignorez l’adresse racine de l’API car elle s’appliquait aux API précédentes.<br/>
> 2. Si vous avez créé l’application à l’aide du portail Azure, vous devez utiliser le nom de ressource de l’application, qui est différent du nom de l’application elle-même. Si vous avez créé l’application dans le portail Azure, vous devez utiliser le nom de l’application elle-même (il n’existe aucune distinction entre le nom de ressource de l’application et le nom de l’application pour les applications créées dans le nouveau portail).  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



