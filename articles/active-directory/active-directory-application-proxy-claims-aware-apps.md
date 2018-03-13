---
title: "Applications prenant en charge les revendications - Proxy d’application Azure AD | Microsoft Docs"
description: "Comment publier des applications ASP.NET locales qui acceptent les revendications ADFS pour un accès à distance sécurisé par vos utilisateurs."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: markvi
ms.reviewer: harshja
ms.openlocfilehash: a732687e47cffff6b6aae687f8e780bba630243e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Utilisation d’applications prenant en charge les revendications dans le proxy d’application
[Les applications prenant en charge les revendications](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) effectuent une redirection vers le service d’émission de jeton de sécurité (STS). Le service d’émission de jeton de sécurité demande des informations d’identification à l’utilisateur en échange d’un jeton, puis redirige l’utilisateur vers l’application. Il existe plusieurs façons d’activer le proxy d’application pour utiliser ces redirections. Utilisez cet article pour configurer votre déploiement pour les applications prenant en charge les revendications. 

## <a name="prerequisites"></a>configuration requise
Vérifiez que le service d’émission de jeton de sécurité vers lequel l’application prenant en charge les revendications effectue la redirection est disponible en dehors de votre réseau local. Vous pouvez rendre le service d’émission de jeton de sécurité disponible en l’exposant par le biais d’un proxy ou en autorisant les connexions externes. 

## <a name="publish-your-application"></a>Publication de votre application

1. Publiez votre application en suivant les instructions décrites dans [Publier des applications avec le proxy d’application](application-proxy-publish-azure-portal.md).
2. Accédez à la page de l’application dans le portail et sélectionnez **Authentification unique**.
3. Si vous choisissez **Azure Active Directory** comme **méthode de préauthentification**, sélectionnez **Authentification unique Azure AD désactivée** comme **méthode d’authentification interne**. Si vous avez choisi **Directe** comme **méthode de préauthentification**, vous n’avez rien à modifier.

## <a name="configure-adfs"></a>Configurer AD FS

Vous pouvez configurer AD FS pour les applications prenant en charge les revendications de deux façons. La première consiste à utiliser des domaines personnalisés. La seconde consiste à utiliser WS-Federation. 

### <a name="option-1-custom-domains"></a>Option 1 : domaines personnalisés

Si toutes les URL internes de vos applications sont des noms de domaines complets (FQDN), alors vous pouvez configurer des [domaines personnalisés](active-directory-application-proxy-custom-domains.md) pour vos applications. Utilisez ces domaines personnalisés pour créer des URL externes identiques aux URL internes. Lorsque vos URL externes correspondent à vos URL internes, les redirections STS fonctionnent que vos utilisateurs soient locaux ou à distance. 

### <a name="option-2-ws-federation"></a>Option 2 : WS-Federation

1. Ouvrez Gestion AD FS.
2. Accédez à **Approbations de la partie de confiance**, cliquez avec le bouton droit sur l’application que vous publiez avec le proxy d’application, puis choisissez **Propriétés**.  

   ![Approbations de partie de confiance, clic droit sur le nom de l’application - capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. Sous l’onglet **Points de terminaison**, sous **Type de point de terminaison**, sélectionnez **WS-Federation**.
4. Sous **URL approuvée**, entrez l’URL que vous avez entrée dans le proxy d’application sous **URL externe**, puis cliquez sur **OK**.  

   ![Ajouter un point de terminaison, définition de la valeur de l’URL approuvée – capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Étapes suivantes
* [Activer l’authentification unique](application-proxy-sso-overview.md) pour les applications qui ne prennent pas en charge les revendications
* [Activation d’applications clientes natives de manière à ce qu’elles interagissent avec des applications proxy](active-directory-application-proxy-native-client.md)


