---
title: "Comment configurer l’authentification unique pour une application de proxy d’application | Microsoft Docs"
description: "Comment configurer rapidement l’authentification unique pour votre application de proxy d’application"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3fddcb9387017bfaf48199bdacd839bfc357a820
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Comment configurer l’authentification unique pour une application de proxy d’application

L’authentification unique (SSO) permet à vos utilisateurs d’accéder à une application sans avoir à s’authentifier plusieurs fois. Elle assure une authentification unique dans le cloud auprès d’Azure Active Directory et permet au service ou au connecteur d’emprunter l’identité de l’utilisateur pour effectuer toute autre demande d’authentification à partir de l’application.

## <a name="how-to-configure-single-sign-on"></a>Comment configurer l’authentification unique
Pour configurer l’authentification SSO, assurez-vous d’abord que votre application est configurée pour la pré-authentification par le biais d’Azure Active Directory. Pour ce faire, sélectionnez **Azure Active Directory** -&gt; **Applications d’entreprise** -&gt; **Toutes les applications** -&gt; Votre application **-&gt; Proxy d’application**. Sur cette page, assurez-vous que le champ « Pré-authentification » est défini sur « Azure Active Directory ». 

Pour plus d’informations sur les méthodes de pré-authentification, reportez-vous à l’étape 4 du [document sur la publication d’applications](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

   ![Méthode de pré-authentification dans le Portail Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configuration des modes d’authentification unique pour les applications de proxy d’application
Il convient à présent de configurer le type d’authentification unique spécifique. Les méthodes d’authentification sont classées en fonction du type d’authentification utilisé par l’application principale. Les applications de proxy d’application prennent en charge trois types d’authentification :

-   **Authentification par mot de passe** : l’authentification par mot de passe peut être utilisée pour toute application présentant des champs de nom d’utilisateur et de mot de passe pour l’authentification. Les étapes de configuration sont décrites dans notre [documentation sur la configuration de l’authentification SSO par mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#bring-your-own-password-sso-applications).

-   **Authentification Windows intégrée** : pour les applications utilisant l’authentification Windows intégrée, l’authentification unique est activée par le biais de la délégation Kerberos contrainte (KCD, Kerberos Constrained Delegation). Cela donne aux connecteurs de proxy d’application l’autorisation d’emprunter l’identité des utilisateurs dans Active Directory et d’envoyer et recevoir des jetons en leur nom. Vous trouverez plus d’informations sur la configuration de KCD dans la [documentation sur l’authentification unique avec KCD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd).

-   **Authentification basée sur l’en-tête** : l’authentification basée sur l’en-tête est activée par le biais d’un partenariat. Elle nécessite une configuration supplémentaire. Pour plus d’informations sur le partenariat et obtenir des instructions pas-à-pas sur la configuration de l’authentification unique pour une application utilisant des en-têtes pour l’authentification, consultez la [documentation sur PingAccess pour Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access).

Chacune de ces options est accessible dans votre application sous « Applications d’entreprise » (ouvrez la page **Authentification unique** dans le menu de gauche). Notez que si votre application a été créée dans l’ancien portail, vous ne verrez peut-être pas toutes ces options.

Cette page propose aussi l’option d’authentification « Authentification liée », également prise en charge par le proxy d’application. Toutefois, notez que cette option n’ajoute pas l’authentification unique à l’application. Cela étant dit, il se peut que l’authentification unique soit déjà implémentée dans l’application par le biais d’un autre service tel qu’Active Directory Federation Services. 

Cette option permet à l’administrateur de créer un lien vers une application présentée préalablement aux utilisateurs lorsqu’ils accèdent à l’application. Par exemple, s’il existe une application qui est configurée pour authentifier les utilisateurs avec Active Directory Federation Services 2.0, l’administrateur peut utiliser l’option d’authentification liée pour créer un lien vers cette application dans le panneau d’accès.

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)
