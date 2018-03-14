---
title: Applications, autorisations et consentement dans Azure Active Directory | Microsoft Docs
description: "Azure AD Connect intègre vos répertoires locaux à Azure Active Directory. Cela vous permet de fournir une identité commune pour les applications Office 365, Azure et SaaS intégrées à Azure AD."
keywords: "introduction à Azure AD, applications, présentation d’Azure AD Connect, installation d’active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: d3e14c18d5e4cd77f4c68d8a5d9d0b915e896695
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Applications, autorisations et consentement dans Azure Active Directory
Dans Azure Active Directory, vous pouvez ajouter des applications à votre répertoire.  Les types d’applications peuvent varier.  Pour afficher des applications dans le portail, sélectionnez un répertoire et choisissez-en quelques-unes.

> [!IMPORTANT]
> Microsoft recommande de gérer Azure AD à l’aide du [Centre d’administration Azure AD](https://aad.portal.azure.com) dans le portail Azure au lieu d’utiliser le portail Azure référencé dans cet article.

## <a name="types-of-apps"></a>Types d’applications

1. **Applications à locataire unique** </br>
    - **Applications à locataire unique** : souvent appelées applications métiers. S’applique lorsqu’une personne de votre organisation développe sa propre application et souhaite que les utilisateurs s’y connectent.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **Applications de proxy d’application** : lorsque vous exposez une application locale avec le proxy d’application Azure AD, une application à locataire unique est inscrite dans votre client (en plus du service de proxy d’application). Cette application représente votre application locale pour toutes les interactions du cloud (par exemple, l’authentification). (Le proxy d’application nécessite Azure AD Standard ou une version ultérieure.)


2. **Applications multi-locataires**
    - **Applications multi-locataires pour lesquelles d’autres personnes peuvent donner leur consentement** : semblables aux « applications à locataire unique développées par votre organisation ». La principale différence (en dehors de la logique de l’application elle-même) est que les utilisateurs d’autres clients peuvent également donner leur consentement et se connecter à l’application.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Applications multi-locataires développées par d’autres personnes, pour lesquelles Contoso peut donner son consentement** (ou « applications consenties », en version courte). C’est l’autre face des « applications multi-locataires développées par votre organisation ». Lorsqu’une autre organisation développe une application multi-locataire, les utilisateurs de votre organisation peuvent donner leur consentement pour cette application et s’y connecter.
    - **Applications internes Microsoft** : applications représentant des services Microsoft. Le consentement se produit lorsque vous vous inscrivez au service. Des expériences utilisateur et logiques spécifiques sont parfois utilisées pour les applications internes lors de l’établissement des stratégies relatives à l’accès aux applications.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **Applications pré-intégrées** : applications disponibles dans la galerie d’applications Azure AD. Vous pouvez les ajouter à votre répertoire pour permettre l’authentification unique (et dans certains cas, l’approvisionnement) sur les applications SaaS.
    - **Authentification unique Azure AD** : authentification unique « réelle ». Concerne les applications qui peuvent être intégrées à Azure AD via un protocole de connexion pris en charge, comme SAML 2.0 ou OpenID Connect. L’Assistant vous guide tout au long du processus de configuration.
    - **Authentification unique par mot de passe** : Azure AD stocke en toute sécurité les informations d’identification de l’utilisateur pour l’application. Ces informations d’identification sont « injectées » dans le formulaire d’inscription par l’extension de navigateur d’accès aux applications Azure AD. Également appelée « mise au coffre des mots de passe ».

## <a name="permissions"></a>Autorisations

Quand une application est inscrite, l’utilisateur qui procède à l’inscription de l’application (c’est-à-dire, le développeur) définit les autorisations auxquelles l’application doit accéder, ainsi que les ressources (les ressources sont, elles-mêmes, définies en tant qu’autres applications). Par exemple, un utilisateur créant une application de lecture de courriers va indiquer que son application exige l’autorisation « Accéder aux boîtes aux lettres en tant qu’utilisateur connecté » dans la ressource « Office 365 Exchange Online » :
    
![](media/active-directory-apps-permissions-consent/apps6.png)

Pour qu’une application (le client) demande une autorisation spécifique à partir d’une autre application (la ressource), le développeur de l’application de ressource définit les autorisations existantes. Dans notre exemple, le propriétaire de l’application de ressource « Office 365 Exchange Online », Microsoft, a défini une autorisation nommée « Accéder aux boîtes aux lettres en tant qu’utilisateur connecté ».

Lorsque le développeur de l’application définit des autorisations, il doit spécifier si l’autorisation peut être consentie, ou si le consentement de l’administrateur est requis. Ainsi, les développeurs peuvent autoriser les utilisateurs à donner eux-mêmes leur consentement pour les applications exigeant uniquement des autorisations peu sensibles. Ils peuvent aussi exiger que les administrateurs donnent leur consentement pour des autorisations plus sensibles. Par exemple, l’application de ressource « Azure Active Directory » a été définie pour que les utilisateurs puissent consentir aux applications, en demandant des autorisations de lecture seule limitées.  Toutefois, le consentement de l’administrateur est requis pour les autorisations de lecture complète et toutes les autorisations d’écriture.

Dans la mesure où les clients natifs ne sont pas authentifiés, une application définie comme application cliente native peut seulement demander des autorisations déléguées. Cela signifie qu’il doit toujours y avoir un utilisateur réel impliqué lors de l’obtention d’un jeton. Les applications web et les API web (clients confidentiels) doivent toujours s’authentifier auprès d’Azure AD lors de l’obtention d’un jeton d’accès. Ce qui signifie qu’elles ont également la possibilité de demander des autorisations application seule. Par exemple, un service principal peut être obligé de s’authentifier auprès d’un autre service principal. Les applications demandant des autorisations application seule nécessitent toujours le consentement de l’administrateur.

En résumé :



- Une application (cliente) indique les autorisations dont elle a besoin pour les autres applications (ressources).
- Une application (ressource) indique quelles autorisations sont exposées à d’autres applications (clientes).
- Une autorisation peut être une autorisation application seule ou une autorisation déléguée.
- Une autorisation déléguée peut être marquée comme « autorise le consentement de l’utilisateur », ou « requiert le consentement de l’administrateur ».
- Une application peut se comporter comme un client (en déclarant qu’elle a besoin d’autorisations pour une ressource), comme une ressource (en déclarant quelles autorisations sont exposées) ou les deux.

## <a name="controls"></a>Commandes

Voici une liste des différents contrôles d’administration disponibles pour tous ces comportements.

Dans le portail Azure, sous **manage** (gérer), choisissez **user settings** (paramètres utilisateur).

![](media/active-directory-apps-permissions-consent/apps11.png)



- Vous pouvez décider si les utilisateurs peuvent donner leur consentement pour des applications :

Dans le portail Azure, sélectionnez **Les utilisateurs peuvent autoriser les applications à accéder à leurs données**.
![](media/active-directory-apps-permissions-consent/apps12.png)



- Vous pouvez contrôler l’inscription par les utilisateurs de leurs propres applications métiers à locataire unique :

Dans le portail Azure, sélectionnez **Les utilisateurs peuvent inscrire des applications**.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>Même si vous n’autorisez pas les utilisateurs à inscrire des applications métiers à locataire unique, les applications pouvant être inscrites sont limitées.  
>Par exemple, les développeurs qui ne sont pas des administrateurs de répertoires.
>
>- Les utilisateurs ne peuvent pas transformer une application à locataire unique en application multi-locataire.
>- Lorsque vous inscrivez des applications métiers à locataire unique, les utilisateurs ne peuvent pas demander d’autorisations application seule à d’autres applications.
>- Lorsque vous inscrivez des applications métiers à locataire unique, les utilisateurs ne peuvent pas demander d’autorisations déléguées à d’autres applications si ces autorisations nécessitent le consentement de l’administrateur.
>- Les utilisateurs ne peuvent pas apporter de modifications aux applications dont ils sont propriétaires.


## <a name="example"></a>Exemple

Par exemple, prenons l’application « FabrikamMail pour Office 365 » à laquelle vous avez remarqué que les utilisateurs de votre client se connectent. « FabrikamMail » est une application de lecture de courriers pour Android, publiée par « Fabrikam, Inc. ». Elle correspond à la définition « Applications multi-locataires développées par d’autres personnes, pour lesquelles Contoso peut donner son consentement ».

Si les utilisateurs sont autorisés à donner leur consentement, ils sont invités à donner leur consentement la première fois qu’ils se connectent : ![](media/active-directory-apps-permissions-consent/apps14.png)

« Accéder à vos boîtes aux lettres » est la chaîne de consentement proposée à l’utilisateur pour l’autorisation « Accéder aux boîtes aux lettres en tant qu’utilisateur connecté » exposée par « Office 365 Exchange Online » (autrement dit, Exchange).

Vous pouvez afficher les autorisations en recherchant l’objet ServicePrincipal pour Exchange (la ressource), qui a été ajouté lors de votre abonnement à Office 365. Vous pouvez considérer l’objet ServicePrincipal comme une « instance » de l’application dans votre client, utilisée pour enregistrer les différentes options et configurations.  Vous pouvez le voir en utilisant `Get-AzureADServicePrincipal` dans PowerShell.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

Le consentement est initié lorsque l’utilisateur clique sur « Accepter ». Tout d’abord, un objet ServicePrincipal pour « FabrikamMail pour Office 365 » est créé dans le client. L’objet ServicePrincipal ressemble à ce qui suit :

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

Le fait de donner un consentement pour une application crée un lien Oauth2PermissionGrant entre les éléments suivants :
  
- l’objet utilisateur
- les applications clientes ServicePrincipalName (SPN)
- les applications de ressource ServicePrincipalName (SPN)
- les autorisations dans l’application de ressource.  

Dans le cas de FabrikamMail, cela ressemble à ce qui suit :

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** est l’ID de l’objet principal du service de FabrikamMail (celui qui vient d’être créé), **PrincipalId** est l’ID de l’objet utilisateur (de l’utilisateur qui a donné son consentement), **ResourceId** est l’ID de l’objet principal du service d’Exchange, Scope est l’autorisation dans Exchange qui a été consentie).

Si les utilisateurs ne sont pas autorisés à donner leur consentement, ils verront un écran indiquant qu’une autorisation est requise.

