---
title: "Microsoft Graph pour Azure Active Directory Identity Protection | Microsoft Docs"
description: "Découvrez comment interroger Microsoft Graph pour obtenir une liste d’événements à risque et des informations associées à partir d’Azure Active Directory."
services: active-directory
keywords: "azure active directory identity protection, événement à risque, vulnérabilité, stratégie de sécurité, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: df0d89fc93f1b9c19d669c29306398a8b25ee425
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Prise en main d’Azure Active Directory Identity Protection et de Microsoft Graph
Microsoft Graph est le point de terminaison d’API unifiée de Microsoft et accueille également les API [d’Azure Active Directory Identity Protection](active-directory-identityprotection.md). La première API, **identityRiskEvents**, vous permet d’interroger Microsoft Graph pour obtenir une liste [d’événements à risque](active-directory-identityprotection-risk-events-types.md) et des informations associées. Cet article vous permet de vous familiariser avec l’interrogation de cette API. Pour des informations détaillées ainsi qu’un accès à l’explorateur de Graph, consultez le [site de Microsoft Graph](https://graph.microsoft.io/).


L’accès aux données d’Identity Protection par le biais de Microsoft Graph se fait en quatre étapes :

1. Récupérer votre nom de domaine.
2. Créer une inscription d’application 
2. Utilisez cette clé secrète et d’autres éléments d’information pour vous authentifier auprès de Microsoft Graph ; ce dernier vous transmettra un jeton d’authentification. 
3. Utilisez ce jeton pour faire des demandes auprès du point de terminaison d’API et récupérer des données d’Identity Protection.

Avant de commencer, vous aurez besoin des éléments suivants :

* Des privilèges d’administrateur pour créer l’application dans Azure AD
* Le nom de domaine de votre client, par exemple contoso.onmicrosoft.com.


## <a name="retrieve-your-domain-name"></a>Récupérer votre nom de domaine 

1. [Connectez-vous](https://portal.azure.com) à votre portail Azure en tant qu’administrateur. 

2. Dans le volet de navigation gauche, cliquez sur **Active Directory**. 
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. Dans la section **Gérer**, cliquez sur **Propriétés**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Copiez votre nom de domaine.


## <a name="create-a-new-app-registration"></a>Créer une nouvelle inscription d’application

1. Dans la page **Active Directory**, dans la section **Gérer**, cliquez sur **Inscriptions des applications**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. Dans le menu du haut, cliquez sur **Nouvelle inscription d’application**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. Dans la page **Créer**, effectuez les étapes suivantes :
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. Dans la zone de texte **Nom** , tapez un nom pour votre application (par exemple : Application d’API d’événement à risque AADIP).
   
    b. Pour **Type**, sélectionnez **Application web et/ou API web**.
   
    c. Dans la zone de texte **URL d’authentification**, tapez `http://localhost`.

    d. Cliquez sur **Créer**.

4. Pour ouvrir la page **Paramètres**, dans la liste des applications, cliquez sur l’inscription d’application que vous venez de créer. 

5. Copiez **l’ID de l’application**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Autorisation d'utilisation de l'API pour votre application

1. Dans la page **Paramètres**, cliquez sur **Autorisations requises**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. Dans la page **Autorisations requises**, dans la barre d’outils en haut, cliquez sur **Ajouter**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. Dans la page **Ajouter un accès d’API**, cliquez sur **Sélectionner une API**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. Dans la page **Sélectionner une API**, sélectionnez **Microsoft Graph**, puis cliquez sur **Sélectionner**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. Dans la page **Ajouter un accès d’API**, cliquez sur **Sélectionner des autorisations**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. Dans la page **Activer l’accès**, cliquez sur **Lire toutes les informations d’événement de risque pour l’identité**, puis cliquez sur **Sélectionner**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. Dans la page **Ajouter un accès d’API**, cliquez sur **Terminé**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. Dans la page **Autorisations requises**, cliquez sur **Accorder les autorisations**, puis cliquez sur **Oui**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Obtenir une clé d’accès

1. Dans la page **Paramètres**, cliquez sur **Clés**.
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. Dans la page **Clés**, effectuez les étapes suivantes :
   
    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. Dans la zone de texte **Description de la clé**, tapez une description (par exemple, *Événement à risque AADIP*).
    
    b. Pour **Durée**, sélectionnez **Dans 1 an**.

    c. Cliquez sur **Enregistrer**.
   
    d. Copiez la valeur de la clé, puis collez-la dans un emplacement sûr.   
   
   > [!NOTE]
   > Si vous perdez cette clé, vous devrez revenir à cette section et créer une nouvelle clé. Gardez cette clé secrète : toute personne la possédant peut accéder à vos données.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Authentifiez-vous auprès de Microsoft Graph et interrogez l’API d’événements à risque concernant l’identité
À ce stade, vous devez avoir :

- le nom de domaine de votre client ;

- L’ID client 

- La clé 


Pour l’authentification, envoyez une demande POST à `https://login.microsoft.com` , avec les paramètres suivants dans le corps :

- grant_type : « **client_informationsidentification** »

-  resource: “**https://graph.microsoft.com**”

- client_id : \<votre ID client\>

- client_secret : \<votre clé\>


Si l’opération réussit, elle retourne un jeton d’authentification.  
Pour appeler l’API, créez un en-tête avec le paramètre suivant :

    `Authorization`=”<token_type> <access_token>"


Lors de l’authentification, le jeton retourné contient le type de jeton ainsi que le jeton d’accès.

Envoyez cet en-tête en tant que requête à l’URL de l’API suivante : `https://graph.microsoft.com/beta/identityRiskEvents`

La réponse, en cas de réussite, consiste en une collection d’événements à risque concernant l’identité ainsi que les données associées au format JSON OData, qui peuvent être analysés et gérés selon vos besoins.

Voici un exemple de code pour l’authentification et l’appel de l’API par le biais de PowerShell.  
Il suffit d’ajouter l’ID client, la clé secrète, ainsi que le domaine du locataire.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous venez de créer votre premier appel à Microsoft Graph !  
Vous pouvez à présent interroger les événements à risque concernant l’identité et utiliser les données comme bon vous semble.

Pour en savoir plus sur Microsoft Graph et comment créer des applications à l’aide de l’API Graph, consultez la [documentation](https://graph.microsoft.io/docs) afférente et bien plus sur le [site de Microsoft Graph](https://graph.microsoft.io/). Assurez-vous également de marquer la page [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) (API Azure AD Identity Protection) à l’aide d’un signet ; cette dernière répertorie toutes les API d’Identity Protection disponibles dans Graph. Sur cette page, vous pourrez consulter l’ensemble des nouvelles façons de travailler avec Identity Protection via API au fur et à mesure que nous les ajoutons.

Pour plus d’informations, consultez :

-  [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

-  [Types d’événements à risque détectés par Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Overview of Microsoft Graph (Vue d’ensemble de Microsoft Graph)](https://graph.microsoft.io/docs)

- [Azure AD Identity Protection Service Root (Racine de service d’Azure AD Identity Protection)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

