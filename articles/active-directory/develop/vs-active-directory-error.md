---
title: Comment diagnostiquer des erreurs avec le service connecté Azure Active Directory
description: Le service connecté Active Directory a détecté un type d’authentification incompatible
services: active-directory
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: ab81c3385479a96fbfa7e68c4e81129ff327ed4b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostic d’erreurs avec le service connecté Azure Active Directory

Lors de la détection du code d'authentification précédent, le serveur de connexion Azure Active Director a détecté un type d’authentification incompatible.

Afin de détecter correctement le code d’authentification précédent dans un projet, le projet doit être généré.  Si vous avez rencontré cette erreur et que vous n’avez pas de code d’authentification précédent dans votre projet, régénérez et réessayez.

## <a name="project-types"></a>Types de projet

Le service connecté vérifie le type de projet que vous développez afin de pouvoir y injecter la logique d’authentification appropriée. S’il existe un contrôleur qui dérive de `ApiController` dans le projet, ce dernier est considéré comme un projet WebAPI. S’il existe uniquement des contrôleurs qui dérivent de `MVC.Controller` dans le projet, ce dernier est considéré comme un projet MVC. Le service connecté ne prend en charge aucun autre type de projet.

## <a name="compatible-authentication-code"></a>Code d’authentification compatible

Le service connecté vérifie également les paramètres d’authentification qui ont été précédemment configurés ou sont compatibles avec le service. Si tous les paramètres sont présents, il est considéré comme un cas réentrant et le service connecté s’ouvre en affichant les paramètres.  Si seuls certains paramètres sont présents, il est considéré comme une erreur.

Dans un projet MVC, le service connecté vérifie les paramètres suivants, qui résultent de l'utilisation précédente du service :

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

En outre, le service connecté vérifie les paramètres suivants dans un projet Web API, qui résultent de l'utilisation précédente du service :

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Code d’authentification incompatible

Pour terminer, le service connecté tente de détecter les versions du code d’authentification configurées avec des versions antérieures de Visual Studio. Si cette erreur s'affiche, cela signifie que votre projet comporte un type d'authentification incompatible. Le service connecté détecte les types d'authentification suivants, provenant de versions antérieures de Visual Studio :

* Authentification Windows
* Comptes d'utilisateur individuels
* Comptes professionnels

Pour détecter l’authentification Windows dans un projet MVC, le service connecté recherche `authentication`l’élément dans votre fichier `web.config`.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Pour détecter l’authentification Windows dans un projet Web API, le service connecté recherche `IISExpressWindowsAuthentication`l’élément dans le fichier `.csproj` de votre projet :

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Pour détecter l'authentification des comptes d'utilisateur individuels, le service connecté recherche l'élément de package dans votre fichier `packages.config`.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

Pour détecter un ancien formulaire d’authentification d’un compte professionnel, le service connecté recherche l’élément suivant dans `web.config` :

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

Pour modifier le type d'authentification, supprimez le type d'authentification incompatible, puis essayez d’ajouter le service connecté à nouveau.

Pour plus d’informations, consultez la page [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).