---
title: "Comment diagnostiquer des erreurs à l’aide de l’Assistant Connexion Azure Active Directory"
description: "L’Assistant de connexion Active Directory a détecté un type d’authentification incompatible"
services: active-directory
documentationcenter: 
author: kraigb
manager: mtillman
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/05/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 186bb1ede11c869b1352906b7ebafe57025f4f09
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connection-wizard"></a>Diagnostic d’erreurs avec l’Assistant Connexion Azure Active Directory
L'Assistant a trouvé un type d'authentification incompatible lors de l'opération de détection du code d'authentification précédent.   

## <a name="what-is-being-checked"></a>Quel est l'objet de la vérification ?
**Remarque :** afin de détecter correctement le code d’authentification précédent dans un projet, le projet doit être généré.  Si vous avez rencontré cette erreur et que vous n’avez pas de code d’authentification précédent dans votre projet, régénérez et réessayez.

### <a name="project-types"></a>Types de projet
L’Assistant vérifie le type de projet que vous développez afin d’y injecter la logique d’authentification appropriée.  S’il existe un contrôleur qui dérive de `ApiController` dans le projet, ce dernier est considéré comme un projet WebAPI.  S’il existe uniquement des contrôleurs qui dérivent de `MVC.Controller` dans le projet, ce dernier est considéré comme un projet MVC.  Les autres éléments ne sont pas pris en charge par l’assistant.

### <a name="compatible-authentication-code"></a>Code d’authentification compatible
L’Assistant vérifie également les paramètres d’authentification qui ont été précédemment configurés ou sont compatibles avec celui-ci.  Si tous les paramètres sont présents, il est considéré comme ré-entrant et l’Assistant s’ouvre en affichant les paramètres.  Si seuls certains paramètres sont présents, il est considéré comme une erreur.

Dans un projet MVC, l'Assistant vérifie les paramètres suivants, qui résultent de l'utilisation précédente de l'Assistant :

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

En outre, l'Assistant vérifie les paramètres suivants dans un projet d'API web, qui résultent de l'utilisation précédente de l'Assistant :

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>Code d’authentification incompatible
Pour terminer, l’Assistant détecte l’utilisation éventuelle de versions du code d’authentification configurées avec des versions antérieures de Visual Studio. Si cette erreur s'affiche, cela signifie que votre projet comporte un type d'authentification incompatible. L'Assistant détecte les types d'authentification suivants, provenant de versions antérieures de Visual Studio :

* Authentification Windows 
* Comptes d'utilisateur individuels 
* Comptes professionnels 

Pour détecter l’authentification Windows dans un projet MVC, l’Assistant recherche l’élément `authentication` à partir de votre fichier **web.config** .

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Pour détecter l’authentification Windows dans un projet API web, l’Assistant recherche l’élément `IISExpressWindowsAuthentication` à partir de votre fichier **.csproj** :

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

Pour détecter l'authentification des comptes d'utilisateur individuels, l'Assistant recherche l'élément package à partir de votre fichier **Packages.config** .

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

Pour détecter une ancienne forme d’authentification d’un compte professionnel, l’Assistant recherche l’élément suivant à partir de votre fichier **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Pour modifier le type d'authentification, supprimez le type d'authentification incompatible, puis réexécutez l'Assistant.

Pour plus d’informations, consultez la page [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

#<a name="next-steps"></a>Étapes suivantes
- [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md)