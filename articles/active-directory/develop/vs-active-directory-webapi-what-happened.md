---
title: "Modifications apportées à un projet WebApi quand vous vous connectez à Azure AD | Microsoft Docs"
description: "Décrit ce qui se passe dans votre projet WebApi quand vous vous connectez à Azure AD en utilisant Visual Studio"
services: active-directory
documentationcenter: 
author: kraigb
manager: mtillman
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 8a0f6e1838bcc550829c0da92dc224e1df859e9c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Qu’est-il arrivé à mon projet WebApi (service connecté Azure Active Directory de Visual Studio) ?
> [!div class="op_single_selector"]
> * [Prise en main](vs-active-directory-webapi-getting-started.md)
> * [Que s'est-il passé ?](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Des références ont été ajoutées.
### <a name="nuget-package-references"></a>Références du package NuGet
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>Références .NET
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>Modifications du code
### <a name="code-files-were-added-to-your-project"></a>Des fichiers de code ont été ajoutés à votre projet
La classe de démarrage d’authentification **App_Start/Startup.Auth.cs** a été ajoutée à votre projet. Elle contient la logique de démarrage permettant l’authentification Azure AD.

### <a name="startup-code-was-added-to-your-project"></a>Un code de démarrage a été ajouté à votre projet
Si vous disposiez déjà d’une classe de démarrage dans votre projet, la méthode **Configuration** a été mise à jour pour inclure un appel vers `ConfigureAuth(app)`. Sinon, une classe de démarrage a été ajoutée à votre projet.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Votre fichier app.config ou web.config comporte de nouvelles valeurs de configuration.
Les entrées de configuration ci-dessous ont été ajoutées.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>Une application Azure AD App a été créée
Une application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l'Assistant.

[En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Si j’ai coché *Désactiver l’authentification des comptes d’utilisateur individuels*, quelles autres modifications ont été apportées à mon projet ?
Des références du package NuGet ont été supprimées, et des fichiers ont été supprimés et sauvegardés. Selon l’état de votre projet, vous pouvez avoir besoin de supprimer manuellement d’autres références ou fichiers, ou de modifier le code le cas échéant.

### <a name="nuget-package-references-removed-for-those-present"></a>Références du package NuGet supprimées (pour celles présentes)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Fichiers de code sauvegardés et supprimés (pour ceux présents)
Chacun des fichiers suivants a été sauvegardé et supprimé du projet. Les fichiers de sauvegarde sont situés dans un dossier « Backup » à la racine du répertoire du projet.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>Fichiers de code sauvegardés (pour ceux présents)
Chacun des fichiers suivants a été sauvegardé avant d’être remplacé. Les fichiers de sauvegarde sont situés dans un dossier « Backup » à la racine du répertoire du projet.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Si j’ai coché *Lire les données de l’annuaire*, quelles autres modifications ont été apportées à mon projet ?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Des modifications supplémentaires ont été apportées à votre fichier app.config ou web.config
Les entrées de configuration ci-dessous ont été ajoutées.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Votre application Azure Active Directory a été mise à jour
Votre application Azure Active Directory a été mise à jour pour inclure l’autorisation *Lire les données de l’annuaire*, et une clé supplémentaire a été créée pour être ensuite utilisée comme *ida:Password* dans le fichier `web.config`.

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

